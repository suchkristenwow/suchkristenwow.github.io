---
layout: post
title: "A chess bot is born"
date: 2025-07-09 11:10:09
description: "It does make legal moves... could be groovier."
tags: [chess-bot, cpp]
categories: side-projects
---

Ladies and gentleman, we have a chess engine. Here are some lessons learned since my last post.

### Step 1: Making the Engine Optional

I modified the GameManager constructor to take an engineType argument with "heuristic" as the default. That way, nothing else breaks and I can still easily add new engines in the future:

 ```cpp
GameManager(std::string playerColor, std::string engineType = "heuristic");
```

### Step 2: Link Errors... of Course

Then I realized it wouldn’t build. The compiler couldn’t find Engines::Heuristic — unless I explicitly added engines/heuristic.cpp to every executable. Which is gross.
The Better Way: CMake Libraries

Instead of manually adding .cpp files everywhere, I did this:

```cpp
add_library(chess_core
    board.cpp
    move_validator.cpp
    piece.cpp
    ...
)

add_library(chess_engines
    engines/heuristic.cpp
    engines/engine_utils.cpp
)

target_link_libraries(chess_engines PUBLIC chess_core)
``` 

What does this do?

    add_library(chess_core ...): "Compile these files into a reusable library."

    target_link_libraries(chess_engines PUBLIC chess_core): "When building chess_engines, also link chess_core. And any executable that links to chess_engines will automatically inherit chess_core too."

PUBLIC vs PRIVATE
    - PUBLIC: Link B into A, and let anything using A also see B.
    - PRIVATE: Link B into A, but don't expose B to A’s users.

Since chess_engines depends on chess_core, and anything using engines also needs the core types (e.g. Board, Piece), this is a case for PUBLIC.

Why separate engines into their own library at all?
    - I thought it would allow for independent testing of the future engines 
    - I want to unit test chess_core without dragging in evaluation code  

### Building the Heuristic Engine

The core of the engine is standard alpha-beta pruning over a 3-ply tree. I moved getAllLegalMoves() into a shared engine_utils.cpp and added evaluateBoard() plus alphaBeta(...).

int alphaBeta(Board& board, int depth, int alpha, int beta, bool maximizing, std::string color);

I also threw in some simple piece-square tables and material weights — nothing fancy yet, just enough for the engine to not blunder pawns immediately.

Then I found it wasn't letting me make legal moves. All the pawn move histories had 1 move or more in them.

Turns out, doing:

```cpp
Board simulatedBoard = board;
simulatedBoard.movePiece(...);
``` 

was not safe — because Board::grid holds shared_ptr<Piece> and the Piece objects were being mutated (e.g., their moveHistory updated).
## The Fix: Deep Copy Constructor
```cpp
Board::Board(const Board& other) {
    grid.resize(8, std::vector<std::shared_ptr<Piece>>(8));
    for (int r = 0; r < 8; ++r) {
        for (int c = 0; c < 8; ++c) {
            if (other.grid[r][c]) {
                grid[r][c] = std::make_shared<Piece>(*other.grid[r][c]);
            }
        }
    }
}
``` 

Now Board simulatedBoard = board; creates a full deep copy, and the engine stops breaking the actual game.

Now it runs but slow. Really slow.

Evaluation works. The bot plays legal moves. The engine chooses ... interesting choices. But it’s slow. Like 100 seconds per move slow. Most of that time is probably spent inside alphaBeta recomputing the same things.

Ideas for improvements 
- Transposition table 
- Iterative deepening 
- Further move pruning  

It does make moves though. We're on our way. 

{% include figure.liquid
    loading="eager"
    path="assets/img/chessbot1.jpg"
    title="screenshot1"
    caption="I move first as white."
    class="img-fluid rounded z-depth-1"
%}


{% include figure.liquid
    loading="eager"
    path="assets/img/chessbot2.jpg"
    title="screenshot2"
    caption="After 17 minutes of contemplation, the bot moves Knight to c6 - the Nimzowitsch defense!"
    class="img-fluid rounded z-depth-1"
%}

Here I set the alpha-beta depth to 3 and the evaluation time is highly variable, but on the order of 5-15 minutes. I'm actually not sure where the non-deterministic behavior comes from...

My first guess is the order of the legal moves changes each time but it could be a bug. We'll see. 

👉 Follow along with my progress on GitHub: [chessBot](https://github.com/suchkristenwow/chessBot)
