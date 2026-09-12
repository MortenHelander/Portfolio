---
title: "Week 3"
date: 2026-09-06
draft: false
---


## Baby steps

Today I realized that my Game entity had to have an eager fetchtype of it’s moves, which also makes sense. As soon as a game is either started or loaded by any player and at all times, you need to keep track of all the moves of the game, to either show the board state or later feed the AI the board state and history.

I’ve also added some logic in the Game class to make a move and shift turn I was considering putting it into a service class, but at least for now I feel it makes the most sense to keep it in the entity class, since it only handles pure java entities and hopefully makes sure a move can’t be falsely added somewhere else by a mistake.

I really banged my head into a wall when trying to test a method that gets all games by a specific userID. Firstly it took me a bit to figure out I couldn’t just instantiate the user object and use my java add methods on it, because what I’m testing is the database… After a long while however I thought I managed to fix it by changing my test populator a little, but using the correct id (1), still didn’t yield any results until I realized I had added two populators a game one and a user one. However frustrating it can be to work on something new it’s always when you finally figure out why it doesn't work that you begin to understand the concepts better in my opinion.

I’ve also finished working on the last couple of DAO’s (at least for now they are the last) and have decided to not use a generic interface for all of them, since I believe it’s better practice to not always be able to update or delete everything. For a powerup for example, it doesn’t make sense that you can delete it since it’s always attached to a player which is always attached to both a user and a game, so if I ever wanted to delete a player or a game from the database then the attached moves and powerups should be deleted.