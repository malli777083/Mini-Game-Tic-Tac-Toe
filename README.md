/*
    ===========================================================
     TIC TAC TOE (Console-based Mini Game, C++)
    ===========================================================
    Concepts demonstrated:
      - 2D Arrays: the 3x3 board is stored as a char array and
        updated/read every turn
      - Loops: input loop (re-ask on invalid move), the game
        loop (turn by turn), and the replay loop (play again)
      - Conditional logic: move validation, win detection
        (rows, columns, diagonals) and draw detection

    Features:
      1. Two-player game (Player X vs Player O) on the same
         console
      2. Board is redrawn after every move
      3. Win detection for all 8 possible winning lines
      4. Draw detection when the board fills up with no winner
      5. Replay option at the end of each game
    ===========================================================
*/

#include <iostream>
#include <limits>

using namespace std;

const int SIZE = 3;

// ---------------------------------------------------------
// Initialize the board with position numbers (1-9) so the
// player can see which number corresponds to which cell.
// ---------------------------------------------------------
void initBoard(char board[SIZE][SIZE]) {
    int num = 1;
    for (int i = 0; i < SIZE; i++) {
        for (int j = 0; j < SIZE; j++) {
            board[i][j] = '0' + num; // stores '1' .. '9' as characters
            num++;
        }
    }
}

// ---------------------------------------------------------
// Print the current board state in a readable grid.
// ---------------------------------------------------------
void displayBoard(char board[SIZE][SIZE]) {
    cout << "\n";
    for (int i = 0; i < SIZE; i++) {
        cout << " ";
        for (int j = 0; j < SIZE; j++) {
            cout << board[i][j];
            if (j < SIZE - 1) cout << " | ";
        }
        cout << "\n";
        if (i < SIZE - 1) cout << "---+---+---\n";
    }
    cout << "\n";
}

// ---------------------------------------------------------
// Convert a 1-9 move number into row/col indices.
// ---------------------------------------------------------
void toRowCol(int move, int& row, int& col) {
    row = (move - 1) / SIZE;
    col = (move - 1) % SIZE;
}

// ---------------------------------------------------------
// Check whether a chosen cell is a free (unoccupied) spot.
// A free cell still holds its original position digit.
// ---------------------------------------------------------
bool isValidMove(char board[SIZE][SIZE], int move) {
    if (move < 1 || move > 9) return false;
    int row, col;
    toRowCol(move, row, col);
    return board[row][col] != 'X' && board[row][col] != 'O';
}

// ---------------------------------------------------------
// Place the current player's symbol on the board.
// ---------------------------------------------------------
void makeMove(char board[SIZE][SIZE], int move, char symbol) {
    int row, col;
    toRowCol(move, row, col);
    board[row][col] = symbol;
}

// ---------------------------------------------------------
// Check all 8 possible winning lines (3 rows, 3 columns,
// 2 diagonals) for three matching symbols.
// ---------------------------------------------------------
bool checkWin(char board[SIZE][SIZE], char symbol) {
    for (int i = 0; i < SIZE; i++) {
        if (board[i][0] == symbol && board[i][1] == symbol && board[i][2] == symbol)
            return true; // row i
        if (board[0][i] == symbol && board[1][i] == symbol && board[2][i] == symbol)
            return true; // column i
    }
    if (board[0][0] == symbol && board[1][1] == symbol && board[2][2] == symbol)
        return true;
    if (board[0][2] == symbol && board[1][1] == symbol && board[2][0] == symbol)
        return true;

    return false;
}

// ---------------------------------------------------------
// Board is full when no cell still holds its position digit.
// ---------------------------------------------------------
bool isBoardFull(char board[SIZE][SIZE]) {
    for (int i = 0; i < SIZE; i++)
        for (int j = 0; j < SIZE; j++)
            if (board[i][j] != 'X' && board[i][j] != 'O')
                return false;
    return true;
}

// ---------------------------------------------------------
// Clear bad input left in the stream buffer.
// ---------------------------------------------------------
void clearInputBuffer() {
    cin.clear();
    cin.ignore(numeric_limits<streamsize>::max(), '\n');
}

// ---------------------------------------------------------
// Play a single round of Tic Tac Toe. Returns nothing;
// prints the result (win/draw) at the end of the round.
// ---------------------------------------------------------
void playGame() {
    char board[SIZE][SIZE];
    initBoard(board);

    char currentSymbol = 'X';
    bool gameOver = false;

    cout << "\n===================================\n";
    cout << "          TIC  TAC  TOE\n";
    cout << "===================================\n";
    cout << "Positions are numbered 1-9 as shown below.\n";
    displayBoard(board);

    while (!gameOver) {
        int move;
        cout << "Player " << currentSymbol << ", enter your move (1-9): ";

        while (!(cin >> move)) {
            cout << "Invalid input. Enter a number between 1-9: ";
            clearInputBuffer();
        }

        if (!isValidMove(board, move)) {
            cout << "That cell is invalid or already taken. Try again.\n";
            continue; // re-loop without consuming a turn
        }

        makeMove(board, move, currentSymbol);
        displayBoard(board);

        if (checkWin(board, currentSymbol)) {
            cout << "*** Player " << currentSymbol << " wins! Congratulations! ***\n";
            gameOver = true;
        } else if (isBoardFull(board)) {
            cout << "*** It's a draw! Well played, both players. ***\n";
            gameOver = true;
        } else {
            currentSymbol = (currentSymbol == 'X') ? 'O' : 'X';
        }
    }
}

int main() {
    char playAgain;

    do {
        playGame();

        cout << "\nPlay again? (y/n): ";
        cin >> playAgain;
        clearInputBuffer();

    } while (playAgain == 'y' || playAgain == 'Y');

    cout << "\nThanks for playing Tic Tac Toe!\n";
    return 0;
}
