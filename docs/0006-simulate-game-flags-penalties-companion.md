# Simulate Game: Flags and Penalties companion

Status: draft. This document supports [the Simulate Game domain model](0006-simulate-game-domain-model.md). The domain model's [section 4.3](0006-simulate-game-domain-model.md#43-flags-and-penalties) states the Flag and Penalty behavior that a football expert could check from a Game's Play-by-Play and Box Score. This companion describes internal mechanisms, not public module interfaces.

Terms are defined in [the glossary](GLOSSARY.md). Where this document and the domain model disagree, the domain model wins.

## 1. Mechanisms

### 1.1 Flag checks

A **Flag check** is a moment in a Play at which Flags may be drawn. It is a simulation term, not a football term: the domain model names only the parts of a Play during which a Penalty can occur.

| Flag check | Part of the Play (domain model) | When it happens |
| --- | --- | --- |
| Pre-snap | Before the snap | Before a Pass Play, Run Play, Punt, or Field Goal Attempt |
| Pass | Pass Play | On the pass path |
| Run | Run Play | On the run path |
| Punt | Punt | Before the kick |
| Punt Return | Punt Return | After the kick and change of Possession, before the Return. Only when a Return happens. |
| Kickoff Return | Kickoff Return | After the Kickoff and change of Possession, before the Return. Only when a Return happens. |
| Field Goal | Field Goal Attempt | Before the kick, on an ordinary Field Goal Attempt |

No Flag check happens on a Try, on an Onside Kick, on a Kickoff itself, on a touchback, after a kick is blocked, or during an Interception or Fumble Return.

Game Rules owns the Flag checks, which Penalties are eligible at each one (the "Can occur during" column of the domain model's Penalty table), and the limit of two Flags per Play. Game Rules determines which Flags are eligible; Play Resolution draws them. Game Rules provides the eligible Flag descriptions to Play Resolution, avoiding duplicated rule knowledge.

### 1.2 Drawing Flags

Play Resolution owns the draws.

- At each Flag check, each eligible Penalty is drawn independently.
- A draw's probability depends on neither earlier events in the Play nor Player Attributes. The rates are calibration data, still to be decided.
- If more draws succeed than the remaining two-Flag allowance permits, the successes are shuffled and only as many as fit are kept. A later Flag check on the same Play can fill a remaining slot.
- A Flag at the pre-snap check stops the intended Play, which records zero live-play seconds.
- Every draw, including the shuffle, the spot, the offending Player, and the tie-break in 1.6, comes from the Game's seeded randomness (domain I25). The order in which draws are consumed is not normative.

### 1.3 The Flag's spot

When a Penalty is enforced from the Flag's spot (defensive pass interference, and offensive Penalties during a Kickoff or Punt Return), Play Resolution draws the spot in whole yards along the Play's gain:

- for an offensive Penalty, from 1 to the yards gained;
- for defensive pass interference, from 0 to the spot of the flag, independently of completed yardage;
- the spot never goes behind the offense's own 1-yard line or beyond the opponent's 1-yard line.

The draw ranges are always valid: the lower endpoint never exceeds the upper one, and a non-positive gain gives the Play-start spot.

### 1.4 The offending Player

When a Penalty names an offending Player, Play Resolution draws one from the offending Team's On-field Players:

- First, a Position is chosen using the Penalty's position weights from among the Positions on the field, then a Player at that Position.
- If no weighted Position is on the field, any On-field Player of the offending Team is drawn.
- Offensive Holding excludes the ball carrier.
- The position weights are calibration data, still to be decided.

### 1.5 Enforcement by rollback

The raw result is an ordered sequence of events, with each Flag at its position. Some events are **enforcement-spot events**: the snap, a kick being received, and a change of Possession. To enforce an accepted Penalty, Game Rules:

1. restores the Play's starting state;
2. replays the events up to the last enforcement-spot event before the Flag, and marks everything after it as not counting;
3. applies the Flag's spot, if its Penalty uses one, then the yards, subject to half the distance in whole yards;
4. applies the first-down rules: automatic first down, yards reaching the line to gain, or a change of Possession among the replayed events.

Tack-on enforcement is the exception. The run or Return counts up to its end, and the yards are added from there. When a Penalty is enforced from the Flag's spot during a Return, the Return counts up to the spot, so the returner is credited with those yards.

For clean hands (domain section 4.3), Game Rules records whether each Team fouled before or after each change of Possession and consults that record when opposing Flags meet a change of Possession.

The live-play time of a cancelled Play is never rolled back.

A rollback cancels applicable football results but does not cancel Injuries, even when they occur after the enforcement point.

The rollback algorithm is an implementation choice and should remain invisible to callers of Game Rules.

### 1.6 Penalty Decision strategy

Coaching Decisions' initial strategy for the Penalty Decision compares Penalty Options one factor at a time, moving to the next factor only on a tie:

1. Overtime outcome
2. Touchdown or two-point score
3. missed extra point
4. late Field Goal
5. situation at an expiring half, including whether an untimed down results
6. Possession change
7. first down
8. other scoring
9. fourth-and-long
10. Field Position
11. Down
12. distance

Equal best Penalty Options are broken by a seeded random choice. The strategy can be replaced without changing Game Rules. When the rules force a result, Coaching Decisions is not consulted.

### 1.7 Recording in Play-by-Play

- Each Play's entry holds its raw result, its Flags with their positions, the Penalty Options, the Penalty Decision, and the Play Outcome.
- Each event of the raw result carries a **counting status**: whether it counts after enforcement. Don't call this marker a "flag": that word means a rule violation.
- Offsetting Penalties are recorded like any other result.
- The entry is complete, including the Play's starting state.
- GameStats Reporting reads the counting status and never re-applies Penalty rules.

### 1.8 Injured-Player shortfall

When fewer than 11 eligible Players remain on a Team's Game Roster, Lineup Selection fills the shortfall with injured Players, taken in Depth Chart order for the slot being filled. Injured players may return only when fewer than 11 eligible Players remain. This is the only exception to the normal injury-eligibility invariant.

### 1.9 Module ownership

| Module | Flag and Penalty responsibilities |
| --- | --- |
| Game Rules | Flag checks and eligibility, the two-Flag limit, Penalty Options, all enforcement (rollback, tack-on, half the distance, first downs), the two-Flag rules, the clock and the untimed down, counting status |
| Play Resolution | Flag draws, the shuffle, the Flag's spot, the offending Player |
| Coaching Decisions | The Penalty Decision strategy |
| Lineup Selection | The injured-Player shortfall |
| GameStats Reporting | Penalty statistics derived from the counting status |
| Simulate Game | Ownership of the seeded randomness |
