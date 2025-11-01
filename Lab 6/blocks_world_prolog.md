# Blocks World – Working Planner (Prolog)

## Problem Statement
The **Blocks World problem** is a classic AI planning problem that simulates a robot rearranging blocks on a table.  
The goal is to transform an **initial state** of stacked blocks into a specified **goal configuration** using a series of valid moves.  

This implementation uses a **Depth-First Search (DFS) planner** to find a sequence of actions that achieves the goal state.

---

## Approach
1. **State Representation**  
   - States are represented as lists of facts such as:
     - `on(Block, Surface)` — a block is on another block or the table.
     - `clear(Block)` — a block has nothing on top of it.
   - Example:  
     ```prolog
     [on(a, table), on(b, table), on(c, a), clear(b), clear(c)]
     ```

2. **Actions**  
   - Two types of moves:
     - `move(B, X, Y)` → Move block B from X to Y.  
     - `move_to_table(B, X)` → Move block B from X to the table.  
   - Valid actions are determined using logical constraints (`valid_action/2`).

3. **Planner (DFS Search)**  
   - Recursively explores possible actions.  
   - Avoids revisiting states using a **Visited list**.  
   - Stops when the goal state is achieved (`goal_reached/1`).  

4. **Goal Checking**  
   - The goal is reached when all goal conditions are satisfied (subset of current state).  

5. **Execution**  
   - Run the planner with the `solve.` predicate.  
   - The solution plan (list of moves) is printed once the goal is reached.

---

## Implementation Details
- **`initial_state/1`** → Defines the starting configuration.  
- **`goal_state/1`** → Defines the desired configuration.  
- **`valid_action/2`** → Checks whether a move is possible in the current state.  
- **`apply_action/3`** → Updates the world after performing an action.  
- **`goal_reached/1`** → Checks if the goal has been achieved.  
- **`plan/3`** → Performs DFS search with backtracking to build a plan.  
- **`solve/0`** → Entry point that runs the planner and prints the resulting plan.  

---

## Code
👉 Paste your Prolog code here:

```prolog
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% BLOCKS WORLD – Working Planner
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

initial_state([
    on(a, table),
    on(b, table),
    on(c, a),
    clear(b),
    clear(c)
]).

goal_state([
    on(a, b),
    on(b, c)
]).

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% ACTIONS
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

% Move a block from X to Y
valid_action(State, move(B, X, Y)) :-
    member(on(B, X), State),
    member(clear(B), State),
    member(clear(Y), State),
    B \= X, B \= Y, X \= Y.

apply_action(State, move(B, X, Y), NewState) :-
    delete(State, on(B, X), S1),
    delete(S1, clear(Y), S2),
    append([on(B, Y), clear(X)], S2, Temp),
    sort(Temp, NewState).

% Move a block from X to the table
valid_action(State, move_to_table(B, X)) :-
    member(on(B, X), State),
    member(clear(B), State).

apply_action(State, move_to_table(B, X), NewState) :-
    delete(State, on(B, X), S1),
    append([on(B, table), clear(X)], S1, Temp),
    sort(Temp, NewState).

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% GOAL CHECK
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

goal_reached(State) :-
    goal_state(Goal),
    subset(Goal, State).

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% PLANNER (DFS)
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

plan(State, [], _) :-
    goal_reached(State),
    !,
    write('Goal reached!'), nl.

plan(State, [Action|Rest], Visited) :-
    valid_action(State, Action),
    apply_action(State, Action, NewState),
    \+ member(NewState, Visited),
    plan(NewState, Rest, [NewState|Visited]).

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
% SOLVER
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

solve :-
    initial_state(Init),
    plan(Init, Plan, [Init]),
    nl, write('Solution plan:'), nl,
    forall(member(A, Plan), (write('  '), write(A), nl)).
```
---

## Time Complexity
- **Worst Case:** O(b^d)  
  - b = branching factor (number of possible actions per state).  
  - d = depth (number of steps to reach the goal).  
- As this uses **DFS**, performance can degrade for deep or cyclic search spaces.

## Space Complexity
- **O(d)** in recursion depth (stack-based search).  
- Additional space for visited states.

---

## Use Cases
- Demonstrating **AI planning** and **state-space search** concepts.  
- Foundation for understanding **STRIPS**, **Goal Regression**, and **Automated Planning**.  
- Educational use in **Artificial Intelligence** or **Logic Programming** courses.

---

## Limitations
- Uses **DFS**, which is **not optimal** — may not find the shortest plan.  
- **No heuristic guidance** — exhaustive search may take longer for large problems.  
- State representation is simplified; scaling to many blocks is computationally expensive.  
