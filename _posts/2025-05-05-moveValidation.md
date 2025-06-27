---
layout: post
title: "The Rules of the Game"
date: 2025-05-05 18:06:12
description: "First pass at move validation in my C++ chess bot"
tags: [chess-bot, cpp]
categories: side-projects
---

So we have our pieces, we have our board, and we can play a game but how do we enforce the rules?

This part of the project seemed trickier. I figured there are 3 parts of validty; valid notation, valid pattern, and move legality.

## 1. Notation

Since I chose to make the simplest, dumbest possible interface the user would need to input their desired move by typing on the command line. Rather than invent some way for them to type in their move using matrix coordinates, I decided to use SAN notation.

This is probably the 2nd most accessible way for a human to input their desired move - 2nd only to a graphic interface where you click/drag like chess.com. But I can't be bothered right now to do that... maybe later. I figured we'd need SAN parsing down the line anyway, maybe to implement some learning from existing databases of famous chess games.

### SAN Chess Notation

#### Example SAN moves:

- `e4` — pawn to e4
- `Nf3` — knight to f3
- `O-O` — kingside castling
- `Qxe5+` — queen captures on e5 with check

My validator uses a regular expression to confirm the move matches SAN format.

## 2. Move Pattern

This part seemed more fun to me - how can each piece move? How do we encode those rules? This is where the function comes in `pieceCanReach`

### Pawn

- Can move 1 or 2 squares forward from the starting row
- Captures diagonally

Later, I went back and tried to manage _en passant_ ... I also need to handle pawn promotion at some point.

Does anyone really remember _en passant_? I don't. But I suck at chess. That's why I'm making a robot to beat my husband for me. I tried staring at the Wikipedia for a while but eventually it seemed to come down to this:

- The most important thing is that it can only happen after the _opponent_ moves a pawn 2 squares forward. This is helpful to limit the number of chances for _passant_
- Furthermore, the pawn (the target) that has moved 2 squares must also land beside your pawn.
- You can capture the target as if it had only moved one square

To encode this is simple. You need only check that

- The attacker (your pawn) is capturing
- There is a target pawn next to it
- The target pawn has exactly two moves in its history, jumping up two rows

The rest of the pieces are more simple

### Bishop

- Moves diagonally, must not be blocked
- Loops outward along diagonals until it hits something

### Knight

- Classic L-shaped moves — always 2+1 in any direction
- Doesn't care about obstructions

### Rook

- Moves horizontally or vertically
- Must not be blocked

### Queen

- Combines bishop and rook rules

### King

- Moves one square in any direction
- Castling is handled specially in the legality check

## 3. Move Legality

There are a few rules that come to mind ...

- You can't move yourself into check
- If the player is currently in check, they must get themselves out of check _that_ turn
- You can't capture a piece of the same color
- You can't castle yourself out of check

👉 Follow along with my progress on GitHub: [chessBot](https://github.com/suchkristenwow/chessBot)
