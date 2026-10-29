---
title: "Week 8"
date: 2026-10-18
draft: false
project: "chessapi"
header: "WeekNine"
externalUrl: "Blog/chessAPI/#weeknine"
summary: "Free time and lots of coding"
---


## Security: Free time and lots of coding

This week we had nothing at school, so plenty of time to catch up before the exam in just a little over two weeks time. I implemented a working layer of security with JWT and Javalins accessroles, so some endpoints are now protected by only specific roles. I've also cleaned up my configuration of the javalin application and split my access and exception handling into seperate classes. Along with that I also upgraded to Javalin 7, because I might as well keep updated in case version 6 gets outdated or the features in 7 are more robust. I decided to create a TokenService class with a constructor for the issuer, expiration time and secret key instead of a static class. This made it much easier to test, because any test can create their own instance of the class with whatever values I needed for example testing the expiration time, by adding a very short expiration time or in the past. And also making two instances of the service with a different secret and then trying to verify a user with the wrong key, which immitates how a hacker could try and forge a token.

I've also really began enjoying exception handling, even though it was probably my very least favorite thing a long with tests the last two semesters. It's funny how it's completely flipped, I doubt I'm doing it perfectly or if that is even possible but when we're building the backend only, it's been extremely satisfying to add exceptions and seeing the tests correctly showing them when they're supposed to.

I also began working on websocket which has been something I've both looked forward too and dreaded a little bit. I'll honestly say that it was pretty confusing but at the same time very simple, I think the hardest part is that pretty much all of it is new syntax. I had to ask Claude to make my a step by step assignment with pseudo-code and hints to be able to solve it. It's pretty cool that I've know had to add some thread safe datatypes, like a concurrent hashmap, a @Version to my Game entity and also adding a OptimisticLockException to my exception handler. This is all to make sure that the game entity doesn't get updated simoutaniously by two players thus conflicting the game state.

I tested the websocket connection with a .http file and made two users join the same game and try and make moves. And it's probably been the best moment of the project so far, I had to fix a lot of issues, but now when white player makes a move request on black's turn it rejects it correctly along with a lot of other issues. Seeing two players simmulate a chess game is a big win, now I just hope I can add some of the other features I was planning to do.
