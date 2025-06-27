---
layout: post
title: "Why ChessBot?"
date: 2025-04-14 16:40:16
description: "Starting a side project to explore C++ best practices and RL"
tags: [chess-bot, cpp, reinforcement-learning]
categories: side-projects
---

After wrapping up my PhD in March 2025, I found myself with some downtime. I wanted to take on an ambitious side project to keep me entertained while I looked for a job - something that would challenge my understanding of C++ and give me space to explore best practices outside the context of research or systems engineering.

The idea came from a classical technical interview prompt: _build a tic-tac-toe game in C++_. That basic idea sparked a much bigger one—why not take it (several steps) further and build a full chess engine?

### Why Chess?

Chess offers a rich design space:

- A complex set of rules to encode
- Real-time interaction between a backend and a GUI
- Opportunities to implement search and reinforcement learning
- A way to practice interfacing between different components of a software stack

### Why Now?

During the Restorebot project, we initially tried using reinforcement learning to guide seeding decisions in degraded rangelands. But we quickly hit a wall: **none of the microsites we seeded grew that first season**, which meant there was no reward signal to explore. That failure led us to pivot hard toward perception, vision, and long-term SLAM. See our ISER paper if you don't know what I'm talking about (https://arxiv.org/abs/2312.07724).

Even though RL has questionable applicability in some industrial contexts—clients generally aren't interested in black-box solutions—I'm still interested in its potential. And besides, chess is a really rich space to explore some other ML techniques as well. We'll see where it takes me!

### What I Hope to Learn

- Solid C++ software design, with an emphasis on interfaces and separation of concerns
- Real-time communication between a backend engine and a frontend GUI
- Learning more about RL and other ML techniques for decision-making in game environments
- Managing a medium-size side project like a real software engineer would

---

I’m hoping this series will be a useful log of what I’m building, and what I learn along the way. If nothing else, I hope it can be helpful or interesting to someone out there.

👉 Follow along with my progress on GitHub: [chessBot](https://github.com/suchkristenwow/chessBot)
