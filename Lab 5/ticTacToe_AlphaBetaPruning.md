# Tic-Tac-Toe AI with Minimax + Alpha-Beta Pruning (C++)

## Problem Statement
This project implements a **Tic-Tac-Toe AI** using the **Minimax algorithm with Alpha-Beta Pruning**.  
- The game allows a human to play against the computer.  
- The AI uses optimal decision-making and will never lose.  
- Alpha-Beta pruning ensures faster computation compared to plain Minimax.  

---

## Approach
1. **Board Representation**  
   - The board is stored as a `vector<char>` of size 9.  
   - Indices 0–8 represent the 3×3 grid.  

2. **Evaluation Function**  
   - Returns **+10** if AI wins.  
   - Returns **-10** if human wins.  
   - Returns **0** for draw or no winner yet.  

3. **Minimax Algorithm with Alpha-Beta Pruning**  
   - AI (maximizer) tries to maximize score.  
   - Human (minimizer) tries to minimize score.  
   - **Alpha** = best score AI can guarantee.  
   - **Beta** = best score human can guarantee.  
   - Branches that cannot affect final outcome are pruned.  

4. **Game Loop**  
   - Human can choose to play first or second.  
   - Player inputs moves as numbers (1–9).  
   - AI chooses optimal moves using Minimax + pruning.  
   - The game continues until **win/draw**.  

---

## Implementation Details
- **`checkWinner(board)`** → Checks for a winning line.  
- **`isFull(board)`** → Checks if the board is full (draw).  
- **`minimax(board, maximizing, ai, human, alpha, beta)`** → Recursive Minimax with pruning.  
- **`bestMove(board, ai, human)`** → Finds the optimal move for AI.  
- **`printBoard(board)`** → Displays board in a human-readable format.  
- **`main()`** → Handles user interaction, AI moves, and game loop.  

---
## Code

```cpp
#include <iostream>
#include <vector>
#include <limits>
using namespace std;

char checkWinner(const vector<char>& board) 
{
    int winLines[8][3] = 
    {
        {0,1,2},{3,4,5},{6,7,8}, 
        {0,3,6},{1,4,7},{2,5,8}, 
        {0,4,8},{2,4,6}         
    };
    for (auto &line : winLines) 
    {
        char a = board[line[0]], b = board[line[1]], c = board[line[2]];
        if (a != ' ' && a == b && b == c) return a;
    }
    return ' ';
}

bool isFull(const vector<char>& board) 
{
    for (char c : board) if (c == ' ') return false;
    return true;
}

int minimax(vector<char>& board, bool maximizingPlayer, char ai, char human, int alpha, int beta) 
{
    char winner = checkWinner(board);
    if (winner == ai) return 10;
    if (winner == human) return -10;
    if (isFull(board)) return 0;

    if (maximizingPlayer) 
    {
        int bestScore = numeric_limits<int>::min();
        for (int i = 0; i < 9; i++) 
        {
            if (board[i] == ' ') 
            {
                board[i] = ai;
                int score = minimax(board, false, ai, human, alpha, beta);
                board[i] = ' ';
                bestScore = max(bestScore, score);
                alpha = max(alpha, score);
                if (beta <= alpha) break; // pruning
            }
        }
        return bestScore;
    } 
    else 
    {
        int bestScore = numeric_limits<int>::max();
        for (int i = 0; i < 9; i++) 
        {
            if (board[i] == ' ') 
            {
                board[i] = human;
                int score = minimax(board, true, ai, human, alpha, beta);
                board[i] = ' ';
                bestScore = min(bestScore, score);
                beta = min(beta, score);
                if (beta <= alpha) break; // pruning
            }
        }
        return bestScore;
    }
}

int bestMove(vector<char>& board, char ai, char human) 
{
    int bestScore = numeric_limits<int>::min();
    int move = -1;
    for (int i = 0; i < 9; i++) 
    {
        if (board[i] == ' ') 
        {
            board[i] = ai;
            int score = minimax(board, false, ai, human,
                                numeric_limits<int>::min(), numeric_limits<int>::max());
            board[i] = ' ';
            if (score > bestScore) 
            {
                bestScore = score;
                move = i;
            }
        }
    }
    return move;
}

void printBoard(const vector<char>& board) 
{
    cout << "\n";
    for (int i = 0; i < 9; i++) 
    {
        cout << " " << board[i] << " ";
        if (i % 3 != 2) cout << "|";
        else if (i != 8) cout << "\n-----------\n";
    }
    cout << "\n\n";
}

int main() 
{
    cout << "Welcome to Tic-Tac-Toe (Minimax with Alpha-Beta)\n";
    char choice;
    cout << "Would you like to start first? (y/n): ";
    cin >> choice;

    char human = (choice == 'y' || choice == 'Y') ? 'X' : 'O';
    char ai = (human == 'X') ? 'O' : 'X';

    vector<char> board(9, ' ');
    bool humanTurn = (human == 'X');

    printBoard(board);

    while (true) 
    {
        if (humanTurn) 
        {
            int move;
            cout << "Your move (1-9): ";
            cin >> move;
            move--; // 0-based
            if (move >= 0 && move < 9 && board[move] == ' ') 
            {
                board[move] = human;
                humanTurn = false;
            } 
            else 
            {
                cout << "Invalid move. Try again.\n";
                continue;
            }
        } 
        
        else 
        {
            int move = bestMove(board, ai, human);
            cout << "AI chooses square " << (move+1) << "\n";
            board[move] = ai;
            humanTurn = true;
        }

        printBoard(board);

        char winner = checkWinner(board);
        if (winner == human) 
        {
            cout << "You (" << human << ") win!\n";
            break;
        } 
        
        else if (winner == ai) 
        {
            cout << "AI (" << ai << ") wins!\n";
            break;
        } 
        
        else if (isFull(board)) 
        {
            cout << "It's a draw!\n";
            break;
        }
    }

    return 0;
}

```
---

## Time Complexity
- **Without pruning**: O(b^d)  
- **With Alpha-Beta pruning**: O(b^(d/2)) in best case.  
  - b = branching factor (~9 at start).  
  - d = depth of search (≤ 9 moves in Tic-Tac-Toe).  
- In practice, AI always evaluates quickly because Tic-Tac-Toe is small.  

## Space Complexity
- **O(d)** recursion depth.  
- Max depth = 9 (full board).  

---

## Use Cases
- Educational tool to demonstrate Minimax and Alpha-Beta pruning.  
- Understand game-tree search in AI.  
- Show why Tic-Tac-Toe is a **solved game**.  
- Extendable to larger games (e.g., Connect-4, Chess).  

---

## Limitations
- Works only for **3×3 Tic-Tac-Toe**.  
- Not scalable to large games without further optimizations.  
- Evaluation is simple (win/lose/draw only).  


