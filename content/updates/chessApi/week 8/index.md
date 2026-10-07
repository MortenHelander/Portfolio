---
title: "Week 7"
date: 2026-10-04
draft: false
project: "chessapi"
header: "WeekEightPartOne"
externalUrl: "Blog/chessAPI/#weekeightpartone"
summary: "User stories and working with AI"
---


## User stories and working with AI

At the beginning of this week I remembered my poorly constructed and forgotten User-stories from the beggining of the project. I've almost reached a point where classic chess is fully functional and I realized I was missing some structure, planning and DoD (definition of done). Together with ClaudeAI, I laid down my criteria for the exam and also my goals for what could be nice to have, but not essential. I got a markdown file which from Claude which had a pretty neat structure and somewhat correct ideas. I've written an explanation for how to read it and edited the document to represent my own vision of the API. I could've used GitHub's Kanban, but I discovered pretty early that when working solo it (at least to me) often felt like more work than actually helping. So this was a good quick solution instead:


# Chess API – User Stories

Oct 7, 2026 · @Morten

38 user stories in six epics, covering everything from classic rules to power-ups. Status reflects the code review of ChessBackendAPI\_3 done today: classic rules are mostly done, real-time play and accounts are not started.

## How to read this

- **Format:** As a *role*, I want *goal*, so that *benefit*. Acceptance criteria are written as short testable checks, so they translate directly into JUnit or RestAssured tests.
- **Roles:** *Player* (anyone in a game), *Registered user* (has an account), *Spectator* (watches a game), *Developer* (you, for technical stories that support the others).
- **Priority (MoSCoW):** Must = needed for a playable classic game over WebSocket. Should = expected for the semester hand-in. Could = nice to have. Won't = parked for now.
- **Status:** set from the code as it stands; change the dropdowns as you go.

## Epic 1: Classic chess rules

The engine is the strongest part of the project: 5 of 9 stories are done, and the 3 with bugs each need a small, targeted fix.

| ID    | User story                                                                                                                                      | Acceptance criteria                                                                                                                                                                                                                                 | Priority | Status      |
| ----- | ----------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ----------- |
| CR-01 | As a player, I want each piece to move according to the rules, so that the game is real chess.                                                  | All six piece types only reach legal squares; pieces cannot jump except the knight; own pieces block, enemy pieces can be captured.                                                                                                                 | Must     | Done        |
| CR-02 | As a player, I want moves that leave my king in check to be rejected, so that I cannot make an illegal move by accident.                        | Move that exposes or keeps own king in check returns 400 "Illegal move"; board and turn are unchanged.                                                                                                                                              | Must     | Done        |
| CR-03 | As a player, I want to castle on both sides, so that I can use a standard opening plan.                                                         | Allowed only if king and rook have not moved, squares between are empty, and king is not in, through or into check; rook lands on the correct square. Bugs: castling allowed with an enemy piece on G1/B1, and through a square attacked by a pawn. | Must     | Has bugs    |
| CR-04 | As a player, I want to capture en passant, so that pawn play follows the full rules.                                                            | Only on the move right after the enemy double step; the captured pawn is removed. Bug: en passant that exposes own king is accepted.                                                                                                                | Must     | Has bugs    |
| CR-05 | As a player, I want to choose which piece my pawn promotes to, so that I can underpromote when it matters.                                      | Pawn on last rank requires q, r, b or n; missing letter returns 400; stored UCI ends with the lowercase letter.                                                                                                                                     | Must     | Done        |
| CR-06 | As a player, I want the game to end at checkmate, so that a winner is declared.                                                                 | Status becomes CHECKMATE, winner colour is set, further moves return 400 "Game is finished".                                                                                                                                                        | Must     | Done        |
| CR-07 | As a player, I want stalemate to end the game as a draw, so that the result is correct.                                                         | Status becomes DRAW with winner NO\_COLOR; GET /games and GET /games/{id} still return 200. Bug: winner is stored as null and the GET endpoints return 500.                                                                                         | Must     | Has bugs    |
| CR-08 | As a player, I want to be told when my king is in check, so that the frontend can warn me.                                                      | Every move response and board state includes an inCheck flag for the side to move.                                                                                                                                                                  | Must     | Not started |
| CR-09 | As a player, I want automatic draws by threefold repetition, the fifty-move rule and insufficient material, so that games cannot go on forever. | Each rule ends the game as DRAW with a reason; one test per rule.                                                                                                                                                                                   | Should   | Not started |

## Epic 2: Game lifecycle

Only move history works end to end today; creating and joining a game is the main blocker for anyone actually playing.

| ID | User story | Acceptance criteria | Priority | Status |
| --- | --- | --- | --- | --- |
| GL-01 | As a player, I want to start a new classic game, so that I can play without anyone touching the database. | POST /games creates the game and both players, returns 201 with the game id; white moves first. GameService.createGame exists but no route uses it. | Must | In progress |
| GL-02 | As a player, I want to invite a friend with a game code, so that we end up in the same game. | Creator gets a short code; second player joins with it and is assigned the free colour; a full game rejects a third player. | Must | Not started |
| GL-03 | As a player, I want to fetch the current board, so that the frontend can draw it without replaying every move. | Game response includes a FEN string (or a square-to-piece list), side to move and inCheck. | Must | Not started |
| GL-04 | As a player, I want to see the legal moves of a selected piece, so that the frontend can highlight them. | GET /games/{id}/legal-moves?from=e2 returns the squares from MoveValidator.getLegalMoves; empty list if it is not that side's turn. | Should | Not started |
| GL-05 | As a player, I want to resign, so that I can end a lost game. | Status becomes RESIGNED, the opponent is the winner, further moves return 400. Fills in the empty /finish endpoint. | Must | Not started |
| GL-06 | As a player, I want to offer a draw that my opponent can accept or decline, so that we can agree on a result. | Offer is stored until the next move; accept ends the game as DRAW; a move by the opponent counts as a decline. | Should | Not started |
| GL-07 | As a player, I want to see all moves of a game in order, so that I can review or replay it. | GET /games/{id}/moves returns moves sorted by move number with UCI and timestamp. | Should | Done |

## Epic 3: Real-time play over WebSocket

This is the semester goal and nothing exists yet, but moveAndShiftTurn can be reused as is, so most of the work is connection handling and broadcasting.

| ID | User story | Acceptance criteria | Priority | Status |
| --- | --- | --- | --- | --- |
| WS-01 | As a player, I want to connect to my game over a WebSocket, so that I get updates without refreshing. | ws://…/ws/games/{id} accepts the connection; unknown game id closes it with a reason; on connect the client receives the current board. | Must | Not started |
| WS-02 | As a player, I want my move to show up on my opponent's screen right away, so that the game feels live. | Client sends a move message (from, to, promotionLetter); after a legal move every connection in that game receives the new board, last move and whose turn it is. | Must | Not started |
| WS-03 | As a player, I want errors to go only to me, so that my opponent is not spammed by my mistakes. | Illegal move, wrong turn or bad JSON sends an error message to the sender only; the board is unchanged. | Must | Not started |
| WS-04 | As a player, I want both of us to be notified when the game ends, so that nobody keeps waiting. | Checkmate, stalemate, resignation or agreed draw broadcasts a game-over message with status, winner and reason. | Must | Not started |
| WS-05 | As a player, I want to rejoin after losing my connection, so that a network glitch does not cost me the game. | Reconnecting to the same game sends the full current board; moves made while away are not lost. | Should | Not started |
| WS-06 | As a player, I want to know when my opponent disconnects, so that I know why they are not moving. | On close, the other player receives an opponent-left message; on return, an opponent-back message. | Should | Not started |
| WS-07 | As a spectator, I want to watch a game live, so that I can follow friends' games. | Spectator connection receives all broadcasts; any move message from a spectator is rejected. | Could | Not started |
| WS-08 | As a developer, I want two moves sent at the same moment to be handled safely, so that the database never holds two moves for one turn. | The @Version conflict returns a clear error (409 over HTTP, an error message over WebSocket) instead of 500; only the first move is saved. | Must | In progress |

## Epic 4: Users and authentication

The entities and DAOs are in place, but UserController is commented out and there is no login, so today anyone can move for both sides.

| ID | User story | Acceptance criteria | Priority | Status |
| --- | --- | --- | --- | --- |
| US-01 | As a visitor, I want to create an account, so that my games and stats are saved to me. | POST /users with username, email and password (min. 8 characters) returns 201; duplicate username or email returns 409; the password is stored hashed. | Must | In progress |
| US-02 | As a registered user, I want to log in and receive a token, so that the server knows who I am. | POST /auth/login returns a token on valid credentials and 401 otherwise; protected routes and the WebSocket handshake require the token. | Must | Not started |
| US-03 | As a player, I want only myself to be able to move my pieces, so that nobody can play my side. | The server compares the user from the token with the player of that colour; a mismatch returns 403 (an error message over WebSocket). | Must | Not started |
| US-04 | As a registered user, I want to see a list of my past and ongoing games, so that I can resume or review them. | GET /users/{id}/games returns the user's games, newest first. GameDAO.getAllGamesByUserId already exists. | Should | In progress |
| US-05 | As a registered user, I want to see my wins, losses and draws, so that I can follow my progress. | Stats update automatically when a game ends; GET /users/{id}/stats returns the totals. | Should | In progress |
| US-06 | As a registered user, I want to edit or delete my profile, so that I control my own data. | PUT and DELETE /users/{id} work only for the logged-in user; deleting keeps finished games but anonymises the player. | Could | In progress |

## Epic 5: Fun mode power-ups

The data model is ready (PlayerPowerUp, PowerUpType, PowerUpStatus), but no game logic uses it yet. PU-05 matters most: random effects break the replay-based board unless they are stored.

| ID    | User story                                                                                                                         | Acceptance criteria                                                                                                                                                                                                                                     | Priority | Status      |
| ----- | ---------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ----------- |
| PU-01 | As a player, I want to start a FUN mode game, so that I can play chess with power-ups.                                             | POST /games with gameMode FUN creates the game; CLASSIC games never hand out power-ups.                                                                                                                                                                 | Should   | Not started |
| PU-02 | As a player, I want instant power-ups to take effect when I earn them, so that the game gets a surprise twist.                     | KILL\_RANDOM\_PIECE, GAIN\_RANDOM\_PIECE and RANDOMIZE\_BOARD apply at once and are saved as AUTOMATICALLY\_APPLIED; if no valid target exists, saved as AUTOMATICALLY\_DISCARDED. A power-up never removes a king or leaves a side already checkmated. | Should   | In progress |
| PU-03 | As a player, I want to hold an AI power-up and choose when to use it, so that I can save it for the right moment.                  | GOOD\_AI and EVIL\_AI are saved as HELD; using one on my turn plays the AI move and sets USED; it cannot be used twice.                                                                                                                                 | Should   | In progress |
| PU-04 | As a player, I want to see what power-up my opponent used and what it changed, so that the board change is not confusing.          | Over WebSocket both players receive a power-up message with type, player and affected squares.                                                                                                                                                          | Could    | Not started |
| PU-05 | As a developer, I want power-up effects saved as events in the move history, so that ReplayHelper rebuilds exactly the same board. | Replaying a FUN game from the database gives the same board as before the restart; covered by a test with a fixed random seed.                                                                                                                          | Should   | Not started |

## Epic 6: External APIs

The Lichess and Chess.com clients exist and have tests, but no endpoint exposes them; Stockfish is only prepared through the stored UCI strings.

| ID | User story | Acceptance criteria | Priority | Status |
| --- | --- | --- | --- | --- |
| EX-01 | As a player, I want to solve the Lichess daily puzzle in TRAINING mode, so that I can practise between games. | GET /puzzles/daily returns the puzzle; it is fetched once a day and cached in DailyPuzzle; a Lichess outage returns the cached puzzle or 503. | Should | In progress |
| EX-02 | As a registered user, I want to link my Chess.com username, so that my profile shows my real ratings. | Linking checks that the username exists; rapid, blitz and bullet ratings are shown on the profile; an unknown username returns 404. | Could | In progress |
| EX-03 | As a player, I want to play against a Stockfish AI, so that I can play when no friend is online. | A game can be created with an AI player (Player.isAi); after each human move the server sends the UCI history to Stockfish and plays its reply. GOOD\_AI and EVIL\_AI (PU-03) build on this. | Should | Not started |

## Suggested sprint order

Finish classic play over WebSocket first; power-ups and external APIs build on top of it.

1. **Fix the engine:** CR-07, CR-03, CR-04, each with a regression test.
2. **Make a game playable through the API:** GL-01, GL-03, CR-08, GL-05.
3. **Go real-time:** WS-01 to WS-04 and WS-08.
4. **Know who is who:** US-01, US-02, US-03, then add the token check to the WebSocket handshake.
5. **Polish classic play:** GL-02, GL-04, GL-06, CR-09, WS-05, WS-06, US-04, US-05.
6. **Fun and training:** PU-05 first, then PU-01 to PU-04, EX-01, EX-03.
7. **If time allows:** WS-07, US-06, EX-02.