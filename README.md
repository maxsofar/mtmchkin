# Mtmchkin

A turn-based card game engine in C++, written for a first OOP course at the Technion. Done solo.

## How it works

The game is driven entirely by text input — a deck file defines the cards in play, stdin provides the player roster and team size. From there the engine runs rounds, tracks state, and logs every encounter until no players remain.

**Game loop:**
1. Parse the deck file into a card deque (with validation and error types for bad input)
2. Read player names and types from stdin; construct via factory
3. Each round: every active player encounters the top card; card moves to back of deck
4. Players who reach max level go to winners; players who die go to losers
5. `isGameOver()` returns true when the active player queue is empty
6. Print leaderboard: winners → active → losers

## Design

The interesting part is the object model. Cards and players are both polymorphic hierarchies with factories that map names from the input file to concrete types — so adding a new card or player class is a one-file change.

**Cards:** `Dragon`, `Goblin`, `Vampire`, `Fairy`, `Merchant`, `Pitfall`, `Treasure`, `Barfight`, `Gang`  
Each overrides `applyEncounter(Player&)` with its own behavior. `Gang` is a container card — it holds multiple battle cards and applies them in sequence, with special parsing logic in the deck reader (`EndGang` delimiter).

**Players:** `Fighter`, `Rogue`, `Wizard`  
Derived from a `Player` base that tracks name, level, force, HP, and coins. Subclasses override attack strength, healing, and coin behavior.

**Factories:** `CardFactory::CardsMap` and `PlayersFactory::PlayersMap` — both map strings to factory objects, keeping construction decoupled from the game logic.

## Building and running

```bash
g++ -std=c++11 main.cpp Mtmchkin.cpp utilities.cpp Cards/*.cpp Players/*.cpp -I. -o mtmchkin
./mtmchkin
```

Input is via stdin and a deck file. Sample decks are in `deck.txt` and the `inputs/` directory. Expected outputs for automated testing are in `tests/`.

## Course context

**234124** — Introduction to Systems Programming, Technion.  
This was the fourth and final C++ exercise in the course, building on the class hierarchy developed across earlier assignments. Written before AI coding tools were part of the workflow.
