# 8-Puzzle Solver Using Greedy Best-First Search

## Problem Statement
Implement a solver for the classic 8-puzzle game using Greedy Best-First Search algorithm with the Manhattan distance heuristic. The program finds a sequence of moves from a given start state to the goal state.

---
## Approach
- Represent the puzzle as a 3x3 grid with `0` indicating the empty slot.
- Use Greedy Best-First Search where states are prioritized based on the Manhattan distance heuristic.
- Manhattan distance calculates how far each tile is from its goal position.
- Keep track of visited states to avoid cycles.
- Generate neighboring states by sliding the empty slot up/down/left/right.
- Continue until the goal state is reached or no solution exists.

---
## Implementation Details
- `print_board`: Prints the current board state.
- `isGoal`: Checks if the current state matches the goal.
- `findEmptyBox`: Finds coordinates of the empty slot (`0`).
- `manhattan`: Calculates the heuristic by summing Manhattan distances for all tiles.
- `boardToString`: Converts the board to a string for efficient visited state tracking.
- `greedyBestFirst`: Core algorithm that uses a priority queue ordered by heuristic value.
- Main function initializes puzzle and goal states and starts the search.

---

## Code (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

void print_board(const vector<vector<int>>& board) 
{
    for (auto &row : board) 
    {
        for (int x : row) cout << x << " ";
        cout << "\n";
    }
}

bool isGoal(const vector<vector<int>>& puzzle, const vector<vector<int>>& goal) 
{
    return puzzle == goal;
}

pair<int,int> findEmptyBox(const vector<vector<int>>& puzzle) 
{
    for(int i=0; i<3; i++)
        for(int j=0; j<3; j++)
            if(puzzle[i][j] == 0)
                return {i,j};
    return {-1,-1};
}

int manhattan(const vector<vector<int>>& puzzle, const vector<vector<int>>& goal) 
{
    int dist = 0;
    for(int i=0; i<3; i++)
    {
        for(int j=0; j<3; j++)
        {
            int val = puzzle[i][j];
            if(val != 0)
            {
                for(int x=0; x<3; x++)
                {
                    for(int y=0; y<3; y++){
                        if(goal[x][y] == val)
                        {
                            dist += abs(i - x) + abs(j - y);
                        }
                    }
                }
            }
        }
    }
    return dist;
}

string boardToString(const vector<vector<int>>& board) 
{
    string s;
    for(auto &row : board)
        for(int x : row) s += to_string(x);
    return s;
}

bool greedyBestFirst(vector<vector<int>> start, vector<vector<int>> goal)
{
    auto cmp = [&](const vector<vector<int>>& a, const vector<vector<int>>& b) 
    {
        return manhattan(a, goal) > manhattan(b, goal);
    };

    priority_queue<vector<vector<int>>, vector<vector<vector<int>>>, decltype(cmp)> pq(cmp);
    set<string> visited;

    pq.push(start);

    vector<pair<int,int>> directions = {{1,0},{-1,0},{0,1},{0,-1}};

    while(!pq.empty())
    {
        auto current = pq.top(); pq.pop();

        if(isGoal(current, goal))
        {
            cout << "Solution found!\n";
            print_board(current);
            return true;
        }

        string key = boardToString(current);
        if(visited.count(key)) continue;
        visited.insert(key);

        auto [x,y] = findEmptyBox(current);

        for(auto [dx,dy] : directions)
        {
            int nx = x + dx, ny = y + dy;
            if(nx>=0 && ny>=0 && nx<3 && ny<3)
            {
                auto new_board = current;
                swap(new_board[x][y], new_board[nx][ny]);
                string newKey = boardToString(new_board);
                if(visited.count(newKey)) continue;
                pq.push(new_board);
            }
        }
    }

    cout << "No solution found!\n";
    return false;
}

int main() 
{
    vector<vector<int>> puzzle = {{1,2,3},{4,5,6},{0,7,8}};
    vector<vector<int>> goal = {{1,2,3},{4,5,6},{7,8,0}};

    cout << "Start state:\n";
    print_board(puzzle);

    greedyBestFirst(puzzle, goal);

    return 0;
}
```
--- 

## Time Complexity
- Worst case: exponential in the number of states (`O(b^d)`), where `b` is branching factor and `d` is depth.
- Greedy heuristic helps reduce exploration but no guarantee of optimality.

## Space Complexity
- Depends on the number of states stored in the priority queue and visited set.
- Potentially exponential in worst case.

---

## Use Cases
- Solving sliding puzzles (like 8-puzzle) automatically.
- Demonstrating heuristic search algorithms.
- Educational tool for understanding Greedy Best-First Search.
- AI pathfinding and problem-solving exercises.

--- 
## Limitations
- Does not guarantee the shortest solution path (non-optimal).
- May get stuck or perform poorly if heuristic is misleading.
- Limited to 8-puzzle (3x3); needs modification for larger puzzles.
- Memory intensive due to storing states and visited set.


