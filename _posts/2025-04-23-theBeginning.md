---
layout: post
title: "The Beginning Begins"
date: 2025-04-23 12:26:16
description: "Starting a side project to explore C++ best practices and RL"
tags: [chess-bot, cpp]
categories: side-projects
---

# Project Design: Building a Chess Bot in C++

When I set out to build a chess bot to learn C++ better, I knew I had to start with the foundations: representing the **pieces**, the **board**, and the **game manager** which would be responsible for checking for the winner and managing turns.

This post walks through how I structured the project in its first phase, and why.

---

## 1. The `Piece` Class

The first building block was the `Piece` class. Each piece in chess has a name (like "pawn"), a color ("white" or "black"), and a position on the board (both in algebraic notation and row/col index).

Besides piece name and color I also added:

- `hasMoved`: to help handle castling and initial pawn moves.
- `moveHistory`: a vector of past positions, I only did this after I considering special rules like **en passant**.
- `point_val`: rough valuation of the piece, for heuristic determination in the future.

Here's the basic constructor:

```cpp
class Piece {
public:
    std::string name;
    std::string color;
    std::string displayName;
    int point_val;
    bool hasMoved;
    std::vector<std::pair<int, int>> moveHistory;

    Piece(const std::string& name,
          const std::string& color,
          const std::string& displayName,
          char file,
          int rank,
          int point_val,
          bool hasMoved,
          std::vector<std::pair<int, int>> moveHistory);

    Piece(const std::string& name,
          const std::string& color,
          const std::string& displayName,
          char file,
          int rank,
          int point_val);
};
```

One thing I learned here is how to write constructors and the utility of convenience constructors. Basically allowing you not have to re-write long constructor calls every time you init a piece.

## 2. The `Board` Class

This one is easy. You need only a grid of size 8x8. Then we iterate through, assigning pieces where they're needed to spots in the grid. The main thing is being able to visualize it. I thought about implementing a cute interface in a window like chess.com but I can't be bothered at this point so I went with a much more utilitarian approach where it's just printed out on the command line after each turn (hence the display name property in the piece class, which is "w_N" for white knight). This required a lot of fussing with spacing and printing out the rank/file such that they aligned with the rows/columns.

I use shared pointers in the grid. What does that mean? Piece objects live on the heap and lets each piece maintain its own state (move history, color, etc.) without copying them over and over. It also means that, while the board owns the piece in the grid square, I can pass it to other functions, like a move validator wihout being concerned about ownership. And I don't think I will have to worry about memory management either.

## 3. The `Game Manager` Class

The GameManager handles the game loop: alternating turns, taking input, making computer moves, and printing the board. It manages whose turn it is and passes moves to the board for validation and execution.

Initially, I just implemented a few functions; `start()`, `makeMove`, and the public properties; `currentTurn`, and `board`. Later, I implemented the necessary structure to check for a winner or stalemate, which is more complicated.

---

Once these were implemented, we could build and start printing out the board. At this point my main.cpp was just

```cpp
int main(int argc, char* argv[]) {
    std::string playerColor;
    while (true){
        std::cout << "Choose your color (white/black) " << std::endl;
        std::cin >> playerColor;
        if (playerColor == "white" || playerColor == "black"){break;}
        std::cout << "Invalid color. Please write white or black.\n" << std::endl;
    }

    GameManager game(playerColor);
    game.start();

    return 0;
}
```

Which simply yields:

{% include figure.liquid
    loading="eager"
    path="assets/img/basicInterface.jpg"
    title="GUI"
    caption="For now, this is our chess board."
    class="img-fluid rounded z-depth-1"
%}

We have a chess board. We have some pieces. Let's play! Next we can work on coding up the rules...

👉 Follow along with my progress on GitHub: [chessBot](https://github.com/suchkristenwow/chessBot)
