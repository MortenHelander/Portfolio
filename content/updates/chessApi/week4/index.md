---
title: "Week 4"
date: 2026-09-12
draft: false
project: "chessapi"
header: "WeekFour"
externalUrl: "Blog/chessAPI/#weekfour"
summary: "Concurrency and clean up"
---

## Concurrency and clean up

Integration and concurrency
This week we learned about concurrency which was a very interesting topic mostly because it's been one of the more technically heavy we've had so far. It's also been a bit more difficult to instantly know where I should use it in the project. For now I'm hoping to be able use it effectively when players makes a move or have to load games.
I also expect when I get further into the process that I will have to use threads to manage two players communicating to the server when using websocket but honestly I have no idea how it works yet.

For this weeks work, I've begun implementing my existing chess game-engine into my school project, along the way I'm refactoring quite a few things.
- SVG images existing directly on the Piece inheritor classes, this was fine in my for fun single game program but for an online game it makes no sense that the backend.
- Board class clean-up, the current state of the board class is a little bit of a mess and it contains too many responsibilities, I'm creating a few services to split some seperation of concern, for example a MoveExecutor.
