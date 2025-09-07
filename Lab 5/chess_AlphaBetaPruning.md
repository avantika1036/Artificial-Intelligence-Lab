# Chess AI with Minimax + Alpha-Beta Pruning (Python)

## Problem Statement
This project implements a **Chess AI** using the **Minimax algorithm with Alpha-Beta Pruning**.  
- The AI evaluates chess positions using a material-based evaluation function.  
- Alpha-Beta pruning significantly reduces the number of explored moves, making the AI faster and stronger compared to plain Minimax.  
- The user can play as **White** or **Black**, while the AI plays the opposite side.  

---

## Approach
1. **Board Representation**  
   - Uses the [`python-chess`](https://pypi.org/project/chess/) library for move legality, board representation, and rules.

2. **Evaluation Function**  
   - Assigns material values to pieces:  
     - Pawn = 1  
     - Knight = 3  
     - Bishop = 3  
     - Rook = 5  
     - Queen = 9  
     - King = 0 (not scored).  
   - Additional cases:  
     - Checkmate → ±9999  
     - Stalemate or insufficient material → 0  

3. **Minimax with Alpha-Beta Pruning**  
   - Standard Minimax recursively explores moves.  
   - **Alpha (best guaranteed for maximizer)** and **Beta (best guaranteed for minimizer)** prune branches that cannot influence the final decision.  
   - This reduces computation without affecting correctness.

4. **Best Move Selection**  
   - Evaluates all legal moves at the current position.  
   - Chooses the move with the highest evaluation.  

5. **Game Loop**  
   - Player enters moves in **UCI format** (e.g., `e2e4`).  
   - AI responds with its calculated move.  
   - Continues until checkmate, stalemate, or draw.  

---

## Implementation Details
- **`evaluate_board(board)`** → Evaluates the board based on material count and endgame states.  
- **`minimax(board, depth, alpha, beta, maximizing)`** → Recursive Minimax search with Alpha-Beta pruning.  
- **`find_best_move(board, depth)`** → Selects the AI’s best move at given depth.  
- **`play_game()`** → Runs the interactive loop between human and AI.  

---

## Code

```python
import chess

def evaluate_board(board: chess.Board) -> int:
    if board.is_checkmate():
        return 9999 if board.turn == chess.BLACK else -9999
    if board.is_stalemate() or board.is_insufficient_material():
        return 0

    piece_values = {
        chess.PAWN: 1,
        chess.KNIGHT: 3,
        chess.BISHOP: 3,
        chess.ROOK: 5,
        chess.QUEEN: 9,
        chess.KING: 0
    }

    score = 0
    for piece_type in piece_values:
        score += len(board.pieces(piece_type, chess.WHITE)) * piece_values[piece_type]
        score -= len(board.pieces(piece_type, chess.BLACK)) * piece_values[piece_type]

    return score


def minimax(board: chess.Board, depth: int, alpha: int, beta: int, maximizing: bool) -> int:
    if depth == 0 or board.is_game_over():
        return evaluate_board(board)

    if maximizing:
        max_eval = -99999
        for move in board.legal_moves:
            board.push(move)
            eval = minimax(board, depth - 1, alpha, beta, False)
            board.pop()
            max_eval = max(max_eval, eval)
            alpha = max(alpha, eval)
            if beta <= alpha:  
                break
        return max_eval
    else:
        min_eval = 99999
        for move in board.legal_moves:
            board.push(move)
            eval = minimax(board, depth - 1, alpha, beta, True)
            board.pop()
            min_eval = min(min_eval, eval)
            beta = min(beta, eval)
            if beta <= alpha:  
                break
        return min_eval


def find_best_move(board: chess.Board, depth: int) -> chess.Move:
    best_move = None
    best_value = -99999

    for move in board.legal_moves:
        board.push(move)
        board_value = minimax(board, depth - 1, -100000, 100000, False)
        board.pop()
        if board_value > best_value:
            best_value = board_value
            best_move = move

    return best_move


def play_game():
    board = chess.Board()
    depth = 6  
    print("Welcome to Chess AI (Minimax + Alpha-Beta Pruning)")
    print(f"Search depth fixed at {depth} moves.\n")
    print(board)

    choice = input("Do you want to play as White and move first? (y/n): ").strip().lower()
    user_is_white = (choice == "y")

    while not board.is_game_over():
        if (board.turn == chess.WHITE and user_is_white) or (board.turn == chess.BLACK and not user_is_white):
            move = None
            while move not in board.legal_moves:
                user_input = input("Your move (UCI format, e.g. e2e4): ")
                try:
                    move = chess.Move.from_uci(user_input)
                except:
                    print("Invalid format. Try again.")
                    continue
                if move not in board.legal_moves:
                    print("Illegal move. Try again.")
            board.push(move)
            print("\nBoard after your move:")
            print(board)
        else:
            ai_move = find_best_move(board, depth)
            board.push(ai_move)
            print(f"\nAI plays: {ai_move}")
            print(board)

    print("\nGame Over:", board.result())


if __name__ == "__main__":
    play_game()
```
---
## Time Complexity
- Plain Minimax: **O(b^d)**  
- With Alpha-Beta Pruning: **O(b^(d/2))** (best case)  
  - **b** = branching factor (~35 legal moves in chess).  
  - **d** = search depth (set to 6).  

## Space Complexity
- **O(d)** recursion depth (stack space).  

---

## Use Cases
- Learn adversarial search algorithms in AI.  
- Understand benefits of Alpha-Beta pruning.  
- Educational tool for **game-playing AI**.  
- Can serve as the foundation for more advanced chess engines.  

---

## Limitations
- **Shallow search depth (6)** → Cannot plan long strategies.  
- **Simple evaluation function** → Ignores positional play, king safety, pawn structure, etc.  
- **No move ordering or transposition table** → Further optimizations possible.  
- **Not competitive with real chess engines** like Stockfish.  

