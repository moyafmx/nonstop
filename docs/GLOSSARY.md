# Nonstop Fantasy Leagues: Initial Vocabulary

## 1. Purpose

This document identifies the primary domain concepts used by Nonstop Fantasy Leagues.

It is a preliminary document. Terms, definitions, relationships, and boundaries will be reviewed and formalized during the requirements discovery process.

## 2. Naming Conventions

Some concepts, such as teams, players, games, and seasons, exist in multiple business domains but represent different entities.

To avoid ambiguity, the following prefixes will be used:

- **NFL:** Entities belonging to American football and the National Football League.
  - Example: NFL Team — Seattle Seahawks, Green Bay Packers.
  - Example: NFL Season — a regular season consisting of 272 games across 18 weeks.
- **Fantasy:** Entities belonging to the Fantasy Football domain.
  - Example: Fantasy Team — a team participating in a fantasy league.
  - Example: Fantasy Season — the progression of a fantasy competition.

These prefixes distinguish domain concepts, not necessarily their eventual names in code or the user interface.

## 3. NFL Domain Terms

The following terms describe the league, its participants, and its season structure.

- **NFL Team**
- **NFL Player**
- **NFL Season**
- **NFL Week**
- **NFL Week Schedule**
- **Injury**
- **NFL Game** — further elaborated in Section 4.

Definitions and relationships remain to be established.

## 4. NFL Game Simulation Terms

These concepts describe the structure, rules, state, and execution of an individual NFL game.

### NFL Game

A simulated football contest between two NFL teams.

Initial concepts:

- Four quarters.
- Overtime, when applicable.
- Final result and game completion.

### Game Rules

The rules governing game execution.

Initial concepts:

- Quarter duration.
- Clock management.
- Overtime rules.

### Game Situation

The current competitive state of an NFL game.

Initial attributes:

- Offense and defense.
- Down.
- Yards to go.
- Line of scrimmage.
- Score.
- Remaining timeouts.
- Overtime state.

### Field Position

The location of the football on the playing field.

Initial concepts:

- Yard-line representation from 0 to 100, relative to the offense.
- Field position changes following possession changes.

### Game Clock

The mechanisms controlling the progression of game time.

Initial concepts:

- Current quarter.
- Remaining time.
- Time elapsed between plays.
- Hurry-up situations.
- Clock stoppages and resumptions.

### Play Caller

The component responsible for selecting actions based on the current game situation.

Initial decisions:

- Kickoffs.
- Conversion attempts.
- Kneel-downs.
- Pass and run selection.
- Fourth-down decisions.
- Accepting or declining penalties.

### Play

An individual football action that advances the game simulation.

Initial play types:

- PassPlay
- RunPlay
- KickoffPlay
- PuntPlay
- FieldGoalPlay
- ExtraPointPlay
- TwoPointPlay

Initial responsibility:

- Resolve an individual play and return its outcome, including elapsed time.

### Penalty

The representation and resolution of rule violations.

Initial concepts:

- Foul detection.
- Penalty adjudication.
- Available enforcement options.
- Acceptance or declination.
- Effects on game state.

### Formation

The arrangement and selection of players participating in a play.

Initial concepts:

- Players on the field.
- Offensive and defensive personnel.

### Box Score

A structured summary of an NFL game's results and statistics.

Initial concepts:

- Scoring plays.
- Team scores.
- Final result.
- Team and player statistics, as applicable.

### Depth Chart

The ordering and assignment of players to positions and roles within an NFL team.

Definitions and relationships remain to be established.

## 5. Fantasy Football Domain Terms

**Explicit restriction: Do not define, elaborate, or formalize the following terms until permission is granted.**

These terms are listed solely to identify concepts for future requirements discovery.

- **Fantasy League**
- **Fantasy League Types**
  - Head-to-Head (H2H)
  - Guillotine (also known as Death)
- **Fantasy Team**
- **Fantasy Player**
- **Fantasy Match**
  - Head-to-Head (H2H) Match
  - Showcase Match (Guillotine leagues)
- **Fantasy Draft**
- **Fantasy Playoffs**

## 6. Glossary Development Guidelines

During requirements discovery:

- Treat all existing definitions and descriptions as provisional.
- Identify ambiguous terminology and overlapping concepts.
- Distinguish domain entities, attributes, rules, processes, and implementation components.
- Identify relationships and dependencies between concepts.
- Challenge assumptions rather than silently adopting them.
- Do not introduce detailed implementation decisions without justification.
- Preserve the restriction on Fantasy Football definitions until explicitly lifted.

The ultimate objective is a consistent, unambiguous domain vocabulary suitable for human developers, AI agents, requirements documents, and software specifications.