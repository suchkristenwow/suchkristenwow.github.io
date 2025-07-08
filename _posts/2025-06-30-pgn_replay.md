---
layout: post
title: "PGN Replay"
date: 2025-06-30 10:16:23
description: "Using actual games to check move validation"
tags: [chess-bot, cpp, unit-testing]
categories: side-projects
---

I was right to be suspicious, dear reader.

To really put my move validation and end-game condition checker to the test I wanted to run some actual games.

Chess games are recorded in PGN files which contain a list of moves played in SAN (standard algebraic notation) as well as the result.

First, I parse these files and use regex to extract the moves. Then I create a GameManager instance and begin iterating over the moves. I check the move validity and the call movePiece. Then I check the game result status. First, I tried on a random game (Garry Kasparov vs. Jorden van Foreest 2021) from chess.com and found substantial problems with the disambiguation logic. In particular, the move bxc6 parsed as **bishop** takes on c6 which of course is not a valid move.

I ended up solving this by changing parseSAN to look for candidate pieces - most moves come up with only one candidate but for ones that have more than one candidate, it checks which candidate piece could legally move to the target square.

The other problem I found is that movePiece didn't actually move the rook and the king during castling, so a special case had to be added to handle that. I similarly had to implement pawn promotion in this function as well.

Additionally, isValidPassant had to be changed to allow en passant to **only** when the capturing pawn moved two squares and when the capturing pawn attempts a diagonal capture to an empty square.

With these changes, I was able to replay several famous games (including Ivanchuk vs. Wolff 1995 a famous example of en passant) as well as some home-brewed examples of special cases using the analysis board on chess.com

Now that I've successfuly passed the unit tests and replayed a handful of games, I have more confidence that the bones of the chess engine are good.

Next, we can get into actually working on the part where the computer makes moves!

👉 Follow along with my progress on GitHub: [chessBot](https://github.com/suchkristenwow/chessBot)
