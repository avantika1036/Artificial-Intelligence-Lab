# Blocks World Planner – Common Lisp Implementation

## Problem Statement
This project implements a **Blocks World AI Planner** in **Common Lisp**.  
The goal is to transform an initial configuration of blocks into a desired goal configuration using valid move operations.  

The program uses **state-space search** with recursive planning to find a valid sequence of actions that achieves the goal state.

---

## Approach
1. **State Representation**  
   - Each state is a list of facts such as `(on a b)` or `(clear c)`.  
   - Example:  
     ```lisp
     ((on a table) (on b table) (on c a) (clear b) (clear c))
     ```

2. **Goal Checking**  
   - The function `goal-reached?` checks if all goal facts are present in the current state.

3. **Actions and Rules**  
   - Valid actions are moves like `(move a b c)` meaning *move block A from B to C*.  
   - The planner ensures:
     - The block to move is clear.
     - The destination is clear or the table.
     - Blocks are not moved onto themselves.

4. **State Transitions**  
   - The `apply-move` function updates the state after performing a valid move:
     - Removes old relations (`on a b`)
     - Adds new relations (`on a c`)
     - Updates `clear` facts accordingly.

5. **Recursive Depth-First Planning**  
   - The `plan` function explores possible moves recursively until the goal is reached.  
   - Keeps track of visited states to prevent loops.  

6. **Execution**  
   - Run the `solve` function to initiate planning from the initial to the goal state.  

---

## Implementation Details
- **Language:** Common Lisp  
- **Key Functions:**
  - `valid-move?` → Checks legality of a move.  
  - `apply-move` → Generates a new state after performing a move.  
  - `plan` → Recursive DFS-based planner.  
  - `solve` → Entry point that runs the planner and prints the solution.

- **Planner Type:** Depth-First Search (DFS)  
- **Knowledge Representation:** Predicate-based symbolic state representation.

---
## Code
```lisp
;;; ------------------------------------------------------------
;;; BLOCKS WORLD PLANNER - Clean Lisp Version (Tested in SBCL)
;;; ------------------------------------------------------------

(defparameter *initial-state*
  '((on a table)
    (on b table)
    (on c a)
    (clear b)
    (clear c)))

(defparameter *goal-state*
  '((on a b)
    (on b c)
    (on c table)))

;;; ------------------------------------------------------------
;;; STATE UTILITIES
;;; ------------------------------------------------------------

(defun member-state (x state)
  (member x state :test #'equal))

(defun add-state (fact state)
  (adjoin fact state :test #'equal))

(defun del-state (fact state)
  (remove fact state :test #'equal))

;;; ------------------------------------------------------------
;;; GOAL TEST
;;; ------------------------------------------------------------

(defun goal-reached? (state)
  (every (lambda (g) (member-state g state)) *goal-state*))

;;; ------------------------------------------------------------
;;; MOVE VALIDATION
;;; ------------------------------------------------------------

(defun valid-move? (state block from to)
  "Return T if moving BLOCK from FROM to TO is valid."
  (and (not (equal from to))
       (member-state `(on ,block ,from) state)
       (member-state `(clear ,block) state)
       (or (equal to 'table)
           (member-state `(clear ,to) state))))

;;; ------------------------------------------------------------
;;; APPLY MOVE
;;; ------------------------------------------------------------

(defun apply-move (state block from to)
  "Return new state after moving BLOCK from FROM to TO."
  (let ((new-state (copy-list state)))
    (setf new-state (del-state `(on ,block ,from) new-state))
    (setf new-state (add-state `(on ,block ,to) new-state))
    (when (not (equal from 'table))
      (setf new-state (add-state `(clear ,from) new-state)))
    (when (not (equal to 'table))
      (setf new-state (del-state `(clear ,to) new-state)))
    new-state))

;;; ------------------------------------------------------------
;;; RECURSIVE PLANNER
;;; ------------------------------------------------------------

(defun plan (state visited)
  "Return a sequence of actions that lead from STATE to the goal."
  (cond
    ((goal-reached? state)
     (list 'goal-reached))

    (t
     (loop for b in '(a b c)
           append
           (loop for x in '(a b c table)
                 append
                 (loop for y in '(a b c table)
                       unless (or (equal x y)
                                  (equal b x)
                                  (equal b y))
                       append
                       (when (valid-move? state b x y)
                         (let ((new-state (apply-move state b x y)))
                           (unless (member new-state visited :test #'equal)
                             (let ((rest (plan new-state (cons new-state visited))))
                               (when rest
                                 (return-from plan
                                   (cons (list 'move b x y) rest))))))))))
     nil)))

;;; ------------------------------------------------------------
;;; SOLVE ENTRY POINT
;;; ------------------------------------------------------------

(defun solve ()
  (let ((solution (plan *initial-state* (list *initial-state*))))
    (if solution
        (progn
          (format t "~%Solution found:~%")
          (dolist (step solution)
            (format t "  ~A~%" step)))
        (format t "No solution found.~%"))))
--- 

## Time Complexity
- **Worst Case:** \( O(b^d) \)  
  where  
  - \( b \): branching factor (possible moves per state)  
  - \( d \): depth of solution  

- **Space Complexity:** \( O(d) \) due to recursion and visited state tracking.

---

## Use Cases
- Demonstrates **AI Planning** and **search-based problem solving**.  
- Educational example for:
  - Knowledge representation using symbolic logic.  
  - State-space reasoning in Lisp.  
  - Depth-first search for automated planning.

---

## Limitations
- **Inefficient for large block sets** — DFS can explore redundant states.  
- **No heuristic guidance** — Lacks optimization found in A* or heuristic planners.  
- **Fixed block names** — Limited flexibility for arbitrary domain scaling.  
- **Non-interactive** — Requires code modification for new goals or initial states.
