---
layout: post
title: "A Big-Picture History of Chess Bots"
date: 2025-06-18 11:30:59
description: "I delve into the world of chess bots, beginning with the classics."
tags: [chess-bot, cpp]
categories: side-projects
---

We can finally begin on the fun part of writing the part of the engine that actually makes moves!

### Side Tangent about my Interest in the History of Chess Bots

If you're here to follow the technical side of my chess bot development please skip to _Early Chess Engines_.

I really like reading about old chess bots, the people behind them, and how we got to where we are now. There is something so inspiring and endearing to me about nascent computer scientists and engineers between the 50s-80s in particular. It would seem computer science as a field these days has lost some of that mystique and been somewhat perverted by the bro-ey, stone-cold souless capitalist culture that is associated with robotics and AI in industry.

I feel that sparkle of romanticism when I watch movies like _Hidden Figures_ or the _The Imitation Game_. I feel it standing in the Space Center in Houston and see the desks of the CapCom just as they were in July 1969, littered with cigarette butts. They did these things _because_ they were hard (or as I would like to think, useful or good).

At least from what I've observed in the academic world, the incentives reward what’s publishable—not necessarily what’s meaningful or important. It's often a better strategy to repackage an existing result into multiple papers than to aim to find where our skills and resources can be expanded on to meet big, important challenges. That’s why I think academic robotics tends to focus on what we can _already_ do rather than what we _should_ do. Not always, but often.

As for industry, I’m both amazed and disillusioned by the scale of models coming out of companies like Meta and Google. The results are impressive, but the reliance on massive private datasets and million dollar compute budgets feels disconnected from the heart of open scientific inquiry. It lacks panache for me.

Lest this devolves into one of those recipe blogs that expounds the developer's entire family history only to tell you how to hard-boil an egg, I'll leave it at that. For now I want to learn more about how they did what they did in those supposed ``glory days".[^1]

### Early Chess Engines (1950s-1980s)

Technically, the first chess bot was a mechanical automaton called El Ajedrecista built by Leonardo Torres Quevedo in 1912 which could play an endgame with the white pieces in which white has a king and a rook, and black only has a king. The machine was capable of mating the black king (played by a human). While mechanically interesting, I think it's fair to say that it lacks the kind of intelligence I'm seeking to develop.

The invention of the computer after WWII caused an explosion in chess engines. The foundation was laid by Alan Turing, Claude Shannon and Dietrich Prinz although these early algorithms could not play entire games, mostly due to hardware constraints.

That would change in 1957 when IBM engineer Alex Bernstein created the first fully automated chess engine capable of playing an entire game, although it would take about 8 minutes per move on the IBM 704 mainframe. It's algorithm followed from the one detailed by Turing, Shannon and Prinz.

Chess Engines in this era were based on minimax search, used static evaluation functions, and or alpha-beta pruning. There was also a lot of reliance on handcrafted rules by grandmasters or programmers.

The basic idea of classical chess bots is to search the game tree. What that means is, we want to simulate all possible sequences of moves, score each resulting position and then choose the move that leads to the best outcome.

#### Minimax

- Max node: On your turn, pick the move that gives you the highest evaluation
- Min node: On your opponents turn, assume they'll pick the move that gives you the lowest evaluation
- Repeat to some fixed depth (called the number of plies, where 1 ply = 1 move by either player). This is constrained by your hardware and time, as there are usually about 35 legal moves per position, and the number of positions explodes exponentially with depth. So the work becomes, how do I guide or limit the search to make it run in reasonable time?

The main thing I want to understand more here is how exactly they evaluate each position. Positioning in chess is essential, and when humans say "white has a better position here", we usually are referring to the idea that white has stronger potential for attacking/defense, that is, we evaluate how good ones' position is based on possible future moves. But doing it that way seems a bit circular if we use position evaluation to do minimax searching.

Obviously, material plays in a part in it, but it is only one aspect, an arguably not the most important. Famously, even sacrificing a queen for a superior position can win very high level chess games - _see Kasparov's Immortal_. I'll circle back on this ...

#### What is Alpha-Beta Pruning

Alpha-beta pruning remains key to chess programming to this day so it's important to understand. It's an optimization on minimax that works by eliminating branches that don't affect the final decision.

In the best case, alpha-beta reduces the number of nodes from $O(b^d)$ to $\sqrt{b^d}$ where b is the branching factor (35 for the number of legal moves/turn) and d is the search depth (how many turns or plies we want to evaluate in the future). Simply put, we don't waste time on considering downstream decisions of bad moves. If move A results in our position eval increasing by 1, and move B results in our position eval increasing by 5 but the subsequent result of move B is check mate, we don't need to evaluate outcomes from playing move B.

#### Mac Hack

The first bot to play in a tournament was Mac Hack VI in 1966. Its name comes from Project MAC (Multi-Level Access Computer), a research program at MIT. The number in its name is not because it's the sixth of its kind, but because it was written for a PDP-6, a mainframe computer released in 1964.

It's notable for being the first chess program to be widely distributed and the first chess bot to get a rating. In its first tournament, the Massachusetts Amateur Championship in 1966 it was rated 1243. The peak of its performance was in 1967 when it achieved a rating of 1510 at the same tournament.

#### Transposition Tables

What made Mac Hack so good? It was the first chess bot to use a transposition table. This is another way to reduce the search space of the chess tree. You can imagine that, when searching over plies of subsequent possible moves, you encounter the same positions over and over again from different sequences of moves. That is, to achieve a unique arrangement of pieces on a board, there are many sequences of moves that could result in that arrangement. These different sequences are called ``transpositions".

A transposition table is merely a massive hash table which stores information about positions previously searched, how deeply they were searched, and the resulting evaluation. That is, given a board object, we convert it into a unique scalar signature using a hash function that allows us to easily store it in a big sorted dictionary along with the properties; previously searched positions and previous board evaluations.

The most commonly used hash function for this purpose is called _Zobrist Hashing_. Ultimately the board layout, castling rights, and en passant targets are mapped to a unsigned 64-bit integer.

##### Zobrist Hashing

1. Precompute random 64-bit integers for every possible piece on every square
   - 12 pieces x 64 squares = 768 entries
   - Plus random numbers for castling rights (16 combinations)
2. Initialize the hash to 0
3. XOR in the values:
   - For each piece on the board, XOR the corresponding value
   - XOR in the side-to-move value
   - XOR in castling rights and en passant square

What does XOR mean for chess pieces? You probably know XOR returns true if _only_ 1 of two items are true. What does that mean for chess pieces?

Let's say Z[piece][square] gives you a random 64-bit integer for a given piece on a specific square. When you place a piece on the board or remove one we need only do: hash ^= Z[piece][square]

That is, suppose we want to put a white pawn on e2:

```cpp
hash ^= 0xABCD1234567890FF;
```

Then we want to move it:

```cpp
hash ^= 0xABCD1234567890FF;  //hash becomes 0 again
```

This works because XOR-ing the same value twice cancels it out.

The way we avoid hash collision is fascinating and worth a read but I don't want to get into it further here ...

In my next post I'll share my first pass at a late 60s inspired chess bot. Stay tuned.

👉 Follow along with my progress on GitHub: [chessBot](https://github.com/suchkristenwow/chessBot)

[^1]: When I refer to glory days, please know I refer to when it seems like people in my field were more likely to take on big important challenges in AI in a way that _seems_ at least naively to have more integrity, and I do not mean to glorify a time when anyone who was not a white, straight cis-man would be lucky to gain even basic respect in these circles, much less be afforded any one of the many opportunities I have enjoyed in my career.
