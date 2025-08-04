# 8-Puzzle Solver using DFS

## Problem Statement

Implement a program that solves the 8-puzzle problem using **Depth First Search (DFS)**.  
The puzzle consists of a 3x3 grid with tiles numbered from 1 to 8 and one empty space (0).  
The goal is to move the tiles to reach the goal state.

---

## Approach

- Use a **stack** for DFS traversal.
- At each state:
  - Locate the blank (0) tile.
  - Generate all valid moves (up, down, left, right).
  - Push each new state onto the stack if it hasn’t been visited.
- Continue until the goal state is reached or all states are exhausted.

---

## Implementation Details

- The puzzle state is stored as a `vector<vector<int>>`.
- We use a `set` to track visited states to avoid cycles.
- Each move swaps the blank tile with one of its neighbors.
- The `dfs()` function performs the core search logic.

---

## Code

```cpp
# include<bits/stdc++.h>
using namespace std;

void print_board(vector<vector<int>>& board) 
{
    for(int i = 0; i < 3; i++)
    {
        for(int j = 0; j < 3; j++)
        {
            cout << board[i][j] << " ";
        }
        cout << endl;
    }
}

bool isGoal(vector<vector<int>>& puzzle, vector<vector<int>>& goal)
{
    for(int i = 0; i < 3; i++)
    {
        for(int j = 0; j < 3; j++)
        {
            if(puzzle[i][j] != goal[i][j]) return false;
        }
    }

    return true;
}

void findEmptyBox(vector<vector<int>>& puzzle, int& x, int& y)
{
    for(int i = 0; i < 3; i++)
    {
        for(int j = 0; j < 3; j++)
        {
            if(puzzle[i][j] == 0)
            {
                x = i;
                y = j;
            }
        }
    }
}

bool dfs(vector<vector<int>>& puzzle, vector<vector<int>>& goal)
{
    stack<vector<vector<int>>> st;
    set<vector<vector<int>>> visited;
    st.push(puzzle);

    while(!st.empty())
    {
        vector<vector<int>> curr_puzzle = st.top();
        st.pop();

        if(isGoal(curr_puzzle, goal)) return true;
        if(visited.count(curr_puzzle)) continue;
        visited.insert(curr_puzzle);

        int x = -1, y = -1;
        findEmptyBox(curr_puzzle, x, y);

        vector<pair<int, int>> dirs = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};

        for(auto dir: dirs)
        {
            int nx = x + dir.first;
            int ny = y + dir.second;
            if(nx >= 0 && ny >= 0 && nx < 3 && ny < 3)
            {
                vector<vector<int>> new_puzzle = curr_puzzle;
                swap(new_puzzle[nx][ny], new_puzzle[x][y]);
                if(!visited.count(new_puzzle)) st.push(new_puzzle);
            }
        }
    } 
    
    return false;
}

int main() {
    vector<vector<int>> puzzle = {{1, 2, 3}, {4, 5, 6}, {0, 7, 8}};
    vector<vector<int>> goal = {{1,2, 3}, {4, 5, 6}, {7, 8, 0}};
    cout << "Start state:" << endl;
    print_board(puzzle);

    if (dfs(puzzle, goal)) cout << "Solution found!" << endl;
    else cout << "No solution found!" << endl;

    return 0;
}

```
---

## Time and Space Complexity

### Time Complexity:
- **Worst-case:** O((9!)²)  
  - Each state can lead to up to 4 moves.
  - Total possible states = 9! = 362,880.
  - In DFS, due to recursion and lack of optimal pruning, worst-case exploration can be exponential.

### Space Complexity:
- O(9!) for the visited set.
- O(H) for recursion stack in the worst-case path depth H (up to 9 levels deep).


## Use Cases
- Useful for understanding how uninformed search algorithms work.
- Demonstrates limitations of DFS (e.g., may not find shortest path, may go deep unnecessarily).
- Forms the basis for solving AI problems with state-space search.

