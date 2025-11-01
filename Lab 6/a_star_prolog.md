# A* Search Algorithm – Prolog Implementation

## Problem Statement
This project implements the **A\*** (**A-Star**) search algorithm in **Prolog**.  
The algorithm finds the **shortest path** between a start and goal node in a weighted graph using both actual path cost and heuristic estimates.

---

## Approach
1. **Graph Representation**  
   - The graph is defined using `edge(Node1, Node2, Cost)` facts.  
   - Example: `edge(a, b, 2).` means there is a path from `a` to `b` with cost 2.

2. **Heuristic Function**  
   - Defined using `h(Node, HeuristicValue)` to estimate the remaining distance to the goal.  
   - Ensures informed search and faster convergence.

3. **Algorithm Logic**  
   - Uses an **open list** (nodes to explore) and a **closed list** (visited nodes).  
   - At each step:
     - The node with the **lowest total cost (f = g + h)** is selected.
     - Expanded to generate successor nodes.
     - Successors are added to the open list if not already visited.
   - Continues until the goal node is reached.

4. **State Representation**  
   - Each node is represented as:
     ```
     node(CurrentNode, G, F, Path)
     ```
     where  
     - `G` = cost so far  
     - `F` = total estimated cost (G + H)  
     - `Path` = list of visited nodes in reverse order.

5. **Sorting and Expansion**  
   - Nodes are sorted using their **F-values** (lowest first).
   - Prevents loops and redundant visits using a closed node list.

---

## Implementation Details
- **Language:** Prolog  
- **Core Predicates:**
  - `astar(Start, Goal, Path, Cost)` – Entry point for A* search.  
  - `astar_search(OpenList, ClosedList, Goal, Path, Cost)` – Recursive A* exploration.  
  - `sort_nodes/2` – Sorts open nodes by total estimated cost.  
  - `f_val/2` – Extracts F-value for sorting.  

- **Algorithm Type:** Informed search (A*)  
- **Search Direction:** Forward from Start to Goal.  

---
## Example Query
```prolog
% ============================
%  A* Search Implementation
% ============================

% ----- Graph definition -----
edge(a,b,2).
edge(a,c,4).
edge(b,d,7).
edge(b,e,3).
edge(c,f,4).
edge(d,g,1).
edge(e,g,6).
edge(f,g,2).

% ----- Heuristic values -----
h(a,10).
h(b,8).
h(c,7).
h(d,3).
h(e,4).
h(f,2).
h(g,0).

% ----- A* entry point -----
astar(Start, Goal, Path, Cost) :-
    h(Start, H),
    astar_search([node(Start, 0, H, [Start])], [], Goal, RevPath, Cost),
    reverse(RevPath, Path).

% ----- Goal reached -----
astar_search([node(Node, G, _, Path)|_], _, Node, Path, G) :- !.

% ----- Expand best node -----
astar_search([node(Node, G, _, Path)|RestOpen], Closed, Goal, FinalPath, Cost) :-
    findall(node(Next, G1, F1, [Next|Path]),
        ( edge(Node, Next, Step),
          \+ member(Next, Closed),    % avoid revisiting
          \+ member(Next, Path),      % avoid cycles
          G1 is G + Step,
          h(Next, H),
          F1 is G1 + H ),
        Children),
    append(RestOpen, Children, TempOpen),
    sort_nodes(TempOpen, NewOpen),
    astar_search(NewOpen, [Node|Closed], Goal, FinalPath, Cost).

% ----- Helper: sort nodes by F -----
sort_nodes(Open, Sorted) :-
    map_list_to_pairs(f_val, Open, Pairs),
    keysort(Pairs, SortedPairs),
    pairs_values(SortedPairs, Sorted).

f_val(node(_, _, F, _), F).

% ----- Example query -----
% ?- astar(a, g, Path, Cost).

```

---
## Time and Space Complexity
- **Time Complexity:** \( O(b^d) \) in the worst case  
  (where \( b \) = branching factor, \( d \) = depth of the optimal solution).  
- **Space Complexity:** \( O(b^d) \), as all generated nodes are stored in memory.

---

## Use Cases
- Pathfinding in AI (maps, games, robotics).  
- Network routing and navigation systems.  
- Educational demonstration of informed search algorithms.

---

## Limitations
- **Heuristic quality impacts performance** — poor heuristics reduce efficiency.  
- **Memory-intensive** — A* stores all visited and frontier nodes.  
- **Not suitable for very large graphs** due to space complexity.  
- **Static graph only** — does not handle dynamic edge cost changes.

