# 8-Puzzle Solver using Hill Climbing

## Problem Statement
This program solves the 8-puzzle problem using the **Hill Climbing** search algorithm with the **misplaced tiles heuristic**.  
The goal is to transform the initial configuration into the goal configuration by moving the empty tile (`0`) in legal directions.

---

## Approach
1. **State Representation**:  
   - Puzzle is stored as a 3×3 matrix (vector of vectors), with `0` representing the empty tile.
2. **Heuristic**:  
   - **Misplaced Tiles** — counts tiles not in their correct position (excluding `0`).
3. **Algorithm Flow**:  
   - Start from the initial state.
   - Generate all valid neighbor states by moving the empty tile.
   - Choose the neighbor with the smallest heuristic value.
   - Repeat until:
     - Goal state is reached, or
     - No better neighbor exists.

---

## Implementation Details
- **`print_board`**: Prints the current puzzle board state.
- **`isGoal`**: Checks if the current state matches the goal state.
- **`findEmptyBox`**: Finds coordinates of the empty tile (`0`).
- **`misplaced_tiles`**: Calculates heuristic (number of misplaced tiles).
- **`get_neighbors`**: Generates all possible states by moving the empty tile in up to 4 directions.
- **`hillClimbing`**:
  - Keeps track of the current state and its heuristic.
  - Selects the neighbor with the lowest heuristic.
  - Stops if no improvement is possible.
- **`main`**:
  - Defines start and goal states.
  - Calls `hillClimbing` to solve the puzzle.

---

## Code

```cpp

#include <bits/stdc++.h>
using namespace std;

bool waterJugBFS(int jug1Cap, int jug2Cap, int target) 
{
    queue<pair<int, int>> q;
    set<pair<int, int>> visited;

    q.push({0, 0});

    while (!q.empty()) {
        auto curr = q.front();
        q.pop();

        cout << "Current state: (" << curr.first << ", " << curr.second << ")\n";

        if (curr.first == target || curr.second == target || curr.first + curr.second == target) 
        {
            cout << "Reached target: (" << curr.first << ", " << curr.second << ")\n";
            return true;
        }

        if (visited.count({curr.first, curr.second})) continue;
        visited.insert({curr.first, curr.second});

        vector<pair<int, int>> nextStates;

        nextStates.push_back({jug1Cap, curr.second});

        nextStates.push_back({curr.first, jug2Cap});

        nextStates.push_back({0, curr.second});

        nextStates.push_back({curr.first, 0});

        {
            int pour = min(curr.first, jug2Cap - curr.second);
            nextStates.push_back({curr.first - pour, curr.second + pour});
        }

        {
            int pour = min(curr.second, jug1Cap - curr.first);
            nextStates.push_back({curr.first + pour, curr.second - pour});
        }

        for (auto &s : nextStates) 
        {
            if (!visited.count({s.first, s.second})) 
            {
                q.push(s);
            }
        }
    }

    return false;
}

int main() {
    int jug1Cap, jug2Cap, target;
    cout << "Enter capacity of Jug 1: ";
    cin >> jug1Cap;
    cout << "Enter capacity of Jug 2: ";
    cin >> jug2Cap;
    cout << "Enter target amount: ";
    cin >> target;

    if (waterJugBFS(jug1Cap, jug2Cap, target))
        cout << "Solution found!\n";
    else
        cout << "No solution possible!\n";

    return 0;
}

```
---

## Time Complexity
- **Worst case**: \( O(b \times d) \)  
  where:
  - \( b \) = branching factor (≤ 4 moves possible from a state)
  - \( d \) = depth (steps until termination)
- **Average**: Much smaller due to early stopping.

## Space Complexity
- **O(1)** auxiliary space (stores only current state and immediate neighbors).

---

## Use Cases
- Teaching local search algorithms in AI.
- Comparing heuristic-based search techniques.
- Understanding the effects of local optima and plateaus.

---

## Limitations
- **Local Optima**: Can get stuck in non-goal states with no better neighbors.
- **Plateaus**: May stop when all neighbors have equal heuristic values.
- **No Backtracking**: Does not explore alternative paths once stuck.
- **Not Guaranteed Optimal**: The solution found may not be the shortest path.

