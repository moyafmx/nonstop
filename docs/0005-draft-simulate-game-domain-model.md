# Simulate Game domain model

Status: draft for discussion. Builds on [the requirements](0004-simulate-game-requirements.md) without changing them. Terms are defined in [the glossary](GLOSSARY.md). Open questions are collected in [Decisions](#6-decisions). This document does not choose code structure.

## 1. The shape of one Game

Simulate Game turns **starting information** and an optional **seed** into a **completed Game**. Inside, a Game repeats one cycle until the Game Rules declare it over:

1. The engine reads the current **Game State**.
2. It makes **decisions**, such as the next Play, a Team Timeout, or an onside kick.
3. Chance and **Player Attributes** **resolve** the Play into what happened on the field.
4. The **Game Rules** turn what happened into a Play Outcome. This includes Penalty enforcement and the Penalty decision.
5. The Play Outcome and any non-Play events are appended to the **Play-by-Play**, which produces the next Game State.

Three kinds of knowledge are involved and should stay distinct:

| Kind | Nature | Examples |
| --- | --- | --- |
| Game Rules | Deterministic, fixed, auditable | Downs, scoring, kickoff spots, Penalty enforcement, Overtime ending, clock stoppages |
| Play resolution | Probabilistic, calibrated | Yards gained, completion, Fumble, Injury, Flag occurrence, time consumed |
| Decisions | Strategic, situation-aware | Play choice, fourth down, Timeouts, accept/decline, onside kick, kickoff receiver |

## 2. Concepts and relationships

### Starting information (input, immutable)

- **Game**: identity, home Team, and away Team.
- **Team**: identity, Roster, and Depth Chart.
- **Roster**: the Players. The requirements make each whole Roster the **Game Roster**, so every Player is eligible at the start.
- **Player**: identity, name, primary Position, and Player Attributes.
- **Depth Chart**: an ordering of players per slot. The Reference Data has a slot for each of the 11 Positions plus two return slots, KR and PR (see [D1](#6-decisions)). Each slot ranks the Team's entire 52-player Roster, which makes the cross-Position fallback concrete.

### Game State (changes during the Game)

Game State is everything needed to continue the Game:

- **Game Situation**: Score, Possession, Down, Yards to Go, Field Position, Quarter or Overtime, Game Clock, and Remaining Team Timeouts. This is what decisions and resolution consult.
- **Pending action**: what must happen next. This is a snap from scrimmage, a Kickoff (normal, or the kick after a Safety), or a Try after a Touchdown. A Touchdown is followed by a Try, then a Kickoff. A Safety is followed by the kick after a Safety from the conceding Team's 20-yard line. "Down and distance" means nothing while a Kickoff or Try is pending.
- **Half structure**: which Team receives the second-half kickoff and the two-minute warnings already given.
- **Overtime progress**: whether each Team has had its possession opportunity, and whether the next Score ends the Game.
- **Player eligibility**: the Game Roster minus Players injured so far.
- **Play-by-Play so far**.

### Play and Play Outcome

- A **Play** records its kind, its participating Teams (offense or kicking Team, defense or receiving Team), its relevant Players, and the **Game Situation before the Play**.
- A Play's **raw result** is what happened on the field before the rules judge it. It includes yards, completion, Fumble, Recovery, Return, block, Injuries, and at most one Flag with its spot and offending Player.
- The **Play Outcome** is the raw result after the Game Rules apply. This includes the Penalty decision and enforcement. It explains every change in Score, Possession, Down, Field Position, and Game Clock.
- **Non-Play events** are false starts (the Play never starts), Injuries, Team Timeouts, Quarter changes, the two-minute warning, and the end of the Game.

### Flag, Penalty, and Penalty decision

- **Flag**: a violation type, an offending Team and Player, and a spot.
- **Penalty**: the prescribed consequence, which is yards, an enforcement spot, Down effect (replay or new set of downs), and whether Play results count.
- **Penalty Decision**: accept or decline, made by the Team entitled to the decision. It is not made when the choice makes no difference, as with a false start.
- Accepted: the Play's football results and statistics are cancelled, except Injuries and kick-return partial results. Declined: the Play Outcome stands, including a change of Possession or Score. The Flag and the decision stay in the Play-by-Play either way.

### Results (output)

- **Play-by-Play**: the chronological record. It is the source of truth.
- **Box Score**: team and Player Statistics, with the Game Result included.
- **Game Result**: the final Score and the outcome (home win, away win, or tie).
- **Injuries**: each injured Player and when the Injury happened. This can be read from the Play-by-Play.

Recommended stance: treat the Game Result and Box Score as derived from the Play-by-Play, never built alongside it. The requirement that all three "describe the same Game" then holds by construction instead of needing a separate check.

## 3. Invariants

**Situation**

- I1. Field Position always lies between the goal lines (0 to 100 from the possessing Team's goal line). A ball that ends beyond a goal line has already become a Touchdown, Safety, or touchback.
- I2. During a snap from scrimmage, the Down is between 1 and 4. Yards to Go is at least 1 and never more than the distance to the goal line ("& goal").
- I3. The Game Clock is between 0 and the period length (15:00, or 10:00 in Overtime).
- I4. Remaining Team Timeouts is between 0 and the allowance: 3 per half, or 2 in Overtime. Timeouts reset at each half and before Overtime.
- I5. The Score never decreases. Each change equals 6, 3, 2, 1, or 2 points from a scoring Play Outcome, and never from Penalty enforcement.

**Sequencing**

- I6. Every Touchdown is followed by a Try. The only exception is a Game already ended by an Overtime rule. A Try happens even when the clock reached 0 during the scoring Play.
- I7. Every successful Field Goal and every Try is followed by a Kickoff by the scoring Team. A Safety is followed by a kick from the conceding Team's 20-yard line. The exception in both cases is the end of a half or of the Game.
- I8. Possession and Down carry over from Q1 to Q2 and from Q3 to Q4. Q3 opens with a Kickoff to the Team that did not receive the opening kickoff.
- I9. A Play that starts with time remaining finishes and counts. An accepted defensive Penalty on a Play that expires a half or Overtime gives one untimed down. At the end of Q1 or Q3, the situation carries over instead.
- I9a. Field Position and Yards to Go are whole yards.
- I10. Overtime is played only when regulation ends tied. The Game ends exactly when an Overtime ending condition from the requirements is met.

**Players**

- I11. Each Play's On-field Players are 11 distinct eligible Players per Team, drawn from that Team's Game Roster. Fewer than 11 are allowed only when eligible Players run out.
- I12. An injured Player appears in no later Play. Earlier Plays and statistics are unaffected.
- I13. Every Player referenced in a Play belongs to one of the two Game Rosters and is referenced by identity.

**Flags**

- I14. A Play has at most one Flag, and only from the supported set.
- I15. Enforcement never moves the ball more than half the distance to the offending Team's goal line. It never scores. Defensive pass interference in the end zone places the ball at the 1-yard line.

**Results**

- I16. The final Score equals the sum of counting scoring Play Outcomes. The Game Result agrees with the final Score.
- I17. Player Statistics come only from Plays that count. Team totals equal the sum of Player totals where the measures are comparable.
- I18. Determinism: the same starting information, seed, and simulator version produce an identical completed Game.

## 4. Scenarios used to test the model

| # | Scenario | Expected by the model | Status |
| --- | --- | --- | --- |
| S1 | 3rd & 8 at own 40. Defensive holding, 5-yard enforcement. | Accepted gives 1st & 10 at own 45 (automatic first down). | Clear |
| S2 | 1st & 10 at opponent 6. Offensive holding. | Half the distance: the ball goes back 10 yards to the 16, 1st & goal from 16. Not limited by half-distance, because the offense's own goal line is far away. | Clear |
| S3 | 2nd & 5 at own 3. Offensive holding. | Half the distance gives the 1.5-yard line, 2nd & 6.5. No Safety. | Resolved (D7, D7a): the 2, 2nd & 6. |
| S4 | Interception returned for a Touchdown. Offside on the defense. | The offense would accept, cancel the Interception, replay the Down, and move 5 yards. Declining keeps the defense's Touchdown. The decision belongs to the offense. | Clear |
| S5 | Punt return. Illegal block in the back by the receiving Team at its own 30. | The kick and change of Possession stand. The receiving Team starts at its own 20. | Clear |
| S6 | Punt return. Face mask by the **kicking** Team. The sample Game shows this. | Enforced from the end of the Return. The Receiving Team gets a first down (D2). | Resolved |
| S7 | Interception return with an illegal block by the intercepting Team. | Possession stands. Enforced from the Flag's spot (D3). | Resolved |
| S8 | A Q2 Play starting at 0:02 ends at 0:00 with an accepted defensive Penalty. | Untimed down (D4). | Resolved |
| S9 | Overtime. The first Team kicks off, recovers a fumbled return, and scores. | Ends Overtime immediately (D5). | Resolved |
| S10 | Overtime. The first possession ends in a Field Goal. The second Team scores a Touchdown with time left. | The second Team takes the lead and the Game ends at once, with no Try. | Resolved |
| S11 | Overtime. The first possession ends with a Touchdown at 0:03. The Try is good. Time expires during the Kickoff. | The Game ends with the first Team ahead. "If time remains" applies. | Clear |
| S12 | Starting QB and backup are injured. The QB slot falls through to the Depth Chart's next eligible Player, such as a WR. | Covered by the requirement and the full-Roster Depth Chart. | Clear |
| S13 | A Player is injured on a Play cancelled by an accepted Penalty. | The Injury counts. The Player is ineligible from the next Play. | Clear |
| S14 | A two-point attempt is intercepted. | It ends without points and there is no return. | Clear |
| S15 | Offside by the offense. | Does not occur (D6, simplification). | Resolved |
| S16 | A 4th-down conversion fails at the opponent's 35. | Turnover on Downs. The opponent gets 1st & 10 at its own 35. | Clear |
| S17 | A missed 52-yard Field Goal from the opponent's 35. | The kick spot is the 42 (7 yards back). The defending Team takes Possession at its own 42, which is farther from its goal line than the 20. | Clear |

## 5. Player Attributes in the Reference Data

The ratings record keys, the `skills` codes, and settings such as `footballOvertime: "bothPossess"`, `foulRateFactor`, and `injuryRate` match the export format of Football GM (ZenGM). That identification is an **interpretation** from the structure. The meta name says only "default football settings".

**Confirmed from the data**

- All 1,664 Players have exactly one ratings record (season 2026). Its `pos` matches the 11 Positions: QB, RB, WR, TE, OL, DL, LB, CB, S, K, and P.
- Ratings are integers from 0 to 100, except `fuzz`, which ranges from −3 to 3.
- The `hgt` rating correlates with Player height in inches at r = 0.98. Strength correlates with body weight at r = 0.89.
- Mean ratings by Position follow the expected pattern. For example, `thv`, `thp`, and `tha` are high only for QBs. `kpw`, `kac`, `ppw`, and `pac` are high only for Ks and Ps. `pcv` is high for CBs and Ss, `rbk` and `pbk` for OL, and `prs` and `rns` for DL.
- `ovr`, `pot`, `ovrs`, and `pots` are per-Position summaries: overall and potential. `ovrs` also has KR and PR entries.
- `skills` codes are drawn from {A, X, H, Bp, Br, PR, RS, L, Ps, Pd, Pa}.

**Interpretation (high confidence, from data plus Football GM conventions)**

| Key | Meaning | Group |
| --- | --- | --- |
| hgt | Height | Physical |
| stre | Strength | Physical |
| spd | Speed | Physical |
| endu | Endurance | Physical |
| thv | Throwing vision | Passing |
| thp | Throwing power | Passing |
| tha | Throwing accuracy | Passing |
| bsc | Ball security | Ball-carrying |
| elu | Elusiveness | Ball-carrying |
| rtr | Route running | Receiving |
| hnd | Hands (catching) | Receiving |
| rbk | Run blocking | Blocking |
| pbk | Pass blocking | Blocking |
| pcv | Pass coverage | Defensive |
| tck | Tackling | Defensive |
| prs | Pass rushing | Defensive |
| rns | Run stopping | Defensive |
| kpw | Kicking power | Kicking |
| kac | Kicking accuracy | Kicking |
| ppw | Punting power | Kicking |
| pac | Punting accuracy | Kicking |

The 21 keys map onto exactly the seven groups named in the requirements.

Skills (interpretation): A = athlete, X = explosive runner, H = hands, Bp = pass blocker, Br = run blocker, PR = pass rusher, RS = run stopper, L = lockdown coverage, Ps = smart passer, Pd = deep passer, Pa = accurate passer.

**Decided (D8)**

- All ratings are Player Attributes, including the derived summaries, `skills`, and `fuzz`. The set may be reviewed later.
- Height is the 0–100 `hgt` rating.
- `endu` is carried as an Attribute even though nothing in the Game uses it yet, because the requirements have no fatigue.
- The meanings above remain interpretations until a primary source confirms them.

## 6. Decisions

Resolved with the game expert (2026-11-18):

- **D1. Depth Chart Slots.** A Depth Chart ranks Players per Depth Chart Slot: the 11 Positions plus KR and PR. Any slot may rank the whole Roster, so an eligible Player from another Position can fill in. The 52-player Rosters are a property of this input, not a rule.
- **D2. Sides on kicks.** The kick itself is between the Kicking Team and the Receiving Team. Once the Receiving Team has Possession, the Return is between the possessing Team and its opponent. A Kicking-Team face mask during the Return is a foul by the opponent. If accepted, it is enforced from the end of the Return and gives the Receiving Team a first down.
- **D3. Non-kick Returns.** An illegal block in the back during an Interception or Fumble Return is enforced like a kick Return: the change of Possession stands, the Return from the Flag onward is cancelled, and enforcement is from the Flag's spot. This extends the requirements, which named only kick Returns.
- **D4. Untimed down.** An accepted defensive Penalty on a Play that ends with the clock expired at the end of a half or of Overtime gives an untimed down. At the end of Q1 or Q3, the resulting situation carries into the next Quarter instead.
- **D5. Overtime kicks.** A Touchdown by the Kicking Team on a Kickoff or Punt ends Overtime immediately. The rule is stated by team and Play, not as "defensive touchdown".
- **D6. Offside.** Offside occurs only against the defense. This is a documented simplification.
- **D7. Whole yards.** Field Position is always whole yards. Half-the-distance results are rounded up.

- **D7a. Rounding direction.** Half-the-distance results are rounded *away from* the offending Team's goal line, so the ball at the 1.5 goes to the 2. Enforcement therefore never reaches a goal line (I15).
- **D8. Attribute set.** Every rating in the ratings record is a Player Attribute: the 21 base ratings and `ovr`, `pot`, `ovrs`, `pots`, `skills`, and `fuzz`. The set may be reviewed later. Height is the 0–100 `hgt` rating, not inches.
- **S10.** In Overtime, after a first-possession Field Goal, a second-Team Touchdown ends the Game immediately, with no Try.

No domain decisions are open.

## 7. Deep-module opportunities (for discussion)

These candidates come from the knowledge split in section 1. They are framed by what they hide, not by code shape.

1. **Game Rules.** Given a Game State and a raw Play result, produce the Play Outcome and the next Game State, including the Penalty options. It hides downs, scoring, Tries, kickoff spots, touchbacks, enforcement and half-distance, Overtime termination, Timeout reset, and clock-stop rules. It is deterministic and pure, and it is the densest concentration of football knowledge. Scenario tables like section 4 become its tests directly.
2. **Play Resolution.** Given a Game Situation, a chosen Play, and On-field Players with their Attributes, plus randomness, produce a raw result: yards, events, Injuries, at most one Flag, and time consumed. It hides the formulas and calibration. It is the likeliest place for a real second adapter, such as a baseline formula and a calibrated one, or a scripted resolver for rules tests.
3. **Decisions (the coach).** Given a Game Situation and eligible Players, produce the Play choice, fourth-down choice, Timeout, onside kick, Overtime receiver, or Penalty Decision. It hides strategy. It can be swapped later for User control or for coaching tendencies.
4. **Lineup.** Given a Depth Chart, Player eligibility, and the needed Formation, produce On-field Players. It hides the slot fallback, return slots, and the never-the-same-Player-twice rule.
5. **Results derivation.** Given the Play-by-Play, produce the Box Score and Game Result. It hides every statistic definition and which results count. This makes I16 and I17 hold by construction.
6. **Simulate Game** itself. Given starting information and an optional seed, produce a completed Game. It is the outermost small interface, a loop over modules 1 to 5 that owns the randomness so that I18 holds.

Settled: **time consumed by a Play belongs to Play Resolution.** Play Resolution reports the duration as part of the raw result, and the Game Rules never decide how long a Play lasts. The Game Rules decide only when the Game Clock stops, the two-minute warning, the end of a period, and untimed downs, applying them to that duration.

Settled: **time between Plays** is split three ways. Decisions chooses the tempo, such as hurrying up or running down the clock. Play Resolution produces the time that actually passes. Game Rules decide whether the Game Clock was running, for example not after an incomplete pass.

Still to discuss: whether Penalty enforcement lives inside Game Rules, for the most locality, or apart from it, which makes each module shallower but easier to extend per Flag. The recommendation is inside, with each Flag's enforcement kept as internal data. Which candidates have real seams versus internal seams only remains to be settled.

## 8. Penalty growth (design horizon)

The game expert expects the supported Flags to grow to more than 30, some with separate offensive and defensive versions. Yardage can be adjusted by enforcement near the goal line. The first capability's requirements still list six Flags. Decided: the first capability keeps its scope and assumptions. It supports the six Flags, and at most one Flag per Play. The growth is a design horizon: the model must not prevent it, but it is not built now.

Consequences for the model:

- **A Flag type is one violation. Its Penalty depends on the offending side and the kind of Play.** Holding is one Flag type with an offensive and a defensive Penalty, not two Flag types. This keeps the glossary's distinction between the violation (Flag) and its consequence (Penalty).
- **Each Penalty is described by facts drawn from a closed set of enforcement kinds.** These are: who can commit it, yards, enforcement spot (where the Play began, the Flag's spot, or the end of the Return), Down effect (none, replay, automatic first down, or loss of down), and what is cancelled (the whole Play, the Return from the Flag onward, or nothing). New Flags are added as descriptions using these facts. The set of facts grows only when a Flag cannot be described with the existing ones.
- **Rules shared by every Flag apply once:** the half-the-distance limit near the goal line, rounding away from the goal line, never scoring by enforcement, first downs earned by yardage, untimed downs, and Injuries that survive a cancelled Play.
- At 30 or more Flags, per-Flag enforcement code (Option C) would copy the shared rules about 30 times. A table of Penalty descriptions inside Game Rules (Option A) remains the recommendation.
