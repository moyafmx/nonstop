# Simulate Game requirements

Status: requirements discovery complete at the agreed scope. The exclusions and deferred decisions below remain open for later work. Domain terms are defined in [the glossary](GLOSSARY.md).

## Scope

Simulate Game produces a complete regular-season Game in one pass, including Overtime when required. The simulation is not interactive. It uses fixed Game Rules. The initial rules follow relevant settings in the Reference Data where their meanings are clear. Rules absent from the data may be simplified and must be documented. The first capability covers common football situations; unsupported exceptional situations must be documented.

The Game includes holding, offside, false start, pass interference, face mask, and illegal block in the back. It enforces the corresponding Penalties. Each supported Flag has a defined enforcement rule covering its effect on yards, Down, Possession, and whether Play results count. A Penalty can be accepted or declined where the choice changes the outcome. Flags outside the supported set do not occur. The design must allow more Flag and Penalty types to be added.

At most one Flag occurs on a Play. The initial Penalties are:

| Flag | Penalty |
| --- | --- |
| False start | Five yards against the offense. The Play does not start. |
| Offside | Five yards against the offending Team. The Down is replayed if accepted. |
| Holding | Ten yards against the offense, or five yards against the defense. Defensive holding awards a first down. |
| Pass interference | Defensive interference places the ball at the spot of the Flag and awards a first down. Offensive interference costs ten yards and replays the Down. |
| Face mask | Fifteen yards against the offending Team. A defensive face mask awards a first down. |
| Illegal block in the back | Ten yards against the offending Team. |

If an accepted yardage Penalty moves the ball far enough for a first down, the offense receives a new set of downs. Penalty enforcement cannot itself award points. If a yardage Penalty would move the ball more than halfway from its enforcement spot to the offending Team's goal line, it moves the ball half that distance instead. Defensive pass interference in the end zone places the ball at the one-yard line.

Penalties are normally enforced from the spot where the Play began. Defensive pass interference is enforced at the Flag's spot. An illegal block during a return is enforced at the Flag's spot. An accepted Penalty normally cancels the Play's football results and statistics, but does not cancel an Injury that occurred during the Play.

For an illegal block during a kick return, the kick and change of Possession stand. The return from the Flag onward is canceled, and the Penalty moves the ball back from the Flag's spot. The engine chooses whether to accept or decline a Penalty in favor of the Team entitled to the decision, taking the Game Situation into account.

When a Team declines a Penalty, the Play Outcome stands, including any change of Possession or Score. The Flag and decision remain in the Play-by-Play.

Regular-season Overtime lasts at most 10 minutes. Both Teams have an opportunity to possess the ball, including on a kickoff return, if time remains. If the first possession ends in a touchdown, the other Team has an opportunity to possess the ball if time remains. The second Team may continue its first possession while behind. If that Team scores a touchdown while behind, it attempts an extra point or two-point conversion before the Game can end. Otherwise, the Game ends if that possession ends while the Team is still behind, if the Team takes the lead, or if time expires. If the second Team ties the Score and time remains, the next Score ends the Game. A defensive touchdown or safety ends Overtime immediately. The Game ends in a tie if the Score is tied when Overtime expires, even if a Team has not had an opportunity to possess the ball.

When regulation ends tied, the engine selects a Team to receive an Overtime kickoff. This choice is independent of the opening kickoff recipient.

When regulation ends with unequal Scores, the Game ends without Overtime.

A touchdown scores six points, a field goal three, a safety two, an extra point one, and a two-point conversion two. The engine selects the Team that receives the opening kickoff. The other Team receives the kickoff at the start of the second half.

After a touchdown and the following extra-point or two-point attempt, or after a successful field goal, the scoring Team kicks off to the other Team. If a touchdown occurs on a Play that began before a Quarter's clock expired, the scoring Team may attempt an extra point or two-point conversion before the period ends, unless the Game has already ended under the Overtime rules.

After a safety, the Team that conceded it kicks the ball to the scoring Team from its own 20-yard line. After a missed field goal, the defending Team takes Possession at the spot of the kick or its own 20-yard line, whichever is farther from its goal line. A blocked field goal follows the blocked-kick recovery rule instead. Field goal and extra-point kicks take place seven yards behind the line of scrimmage.

A normal kickoff starts at the kicking Team's 35-yard line. This includes the opening, second-half, and post-score kickoffs. The kick after a safety is the exception.

An extra-point attempt starts at the defense's 15-yard line; a two-point attempt starts at its two-yard line. A blocked extra point is a failed attempt. The ball becomes dead and neither Team scores on a recovery. If the defense gains Possession during a two-point attempt, the attempt ends without points and the defense cannot return the ball for a Score.

The field is 100 yards long between the goal lines. Possession and Field Position are measured relative to the two Teams' goal lines.

Regulation has four 15-minute Quarters. The Game Clock runs between Plays when appropriate and stops for incomplete passes, a ball carrier going out of bounds, Timeouts, scores, a change of Possession, the two-minute warning in each half, and the end of a Quarter. A Play that starts before the clock reaches zero finishes and its outcome counts. Possession and Down carry from the first to second Quarter and from the third to fourth Quarter. Each Team has three Team Timeouts per half and two in Overtime. Unused Team Timeouts expire at the end of each half and before Overtime. A kickoff touchback places the ball at the receiving Team's 35-yard line. A punt touchback or safety-kick touchback places it at that Team's 20-yard line.

The engine determines time consumed by Plays and time between Plays, taking the Game Situation into account. The Game Clock never goes below zero. Exact timing distributions remain to be calibrated.

If the opening Overtime possession ends in a touchdown, the scoring Team attempts an extra point or two-point conversion before the kickoff to the other Team.

The offense normally has four Downs to gain ten yards and receive a new set of downs. If the goal line is less than ten yards away, the offense must reach it. An unsuccessful fourth Down transfers Possession.

The engine chooses Plays and other decisions, including whether to go for a first down, punt, attempt a field goal, or use a Timeout. Its choices take the Game Situation and available Players into account.

Play Outcomes depend on chance, the Game Situation, and relevant Player Attributes. The formulas remain to be defined.

The Game supports runs, completed and incomplete passes, sacks, interceptions and interception returns, fumbles and recoveries, kickoffs and kickoff returns, punts and punt returns, field goal attempts, extra-point attempts, two-point attempts, kneel-downs, and spikes. Handoffs and dropbacks can be recorded as parts of Plays. Touchdowns and safeties can occur as Play Outcomes.

The Game supports blocked punts, field goal attempts, and extra-point attempts. Either Team can recover a blocked punt or field goal attempt and return it for a touchdown. The Play-by-Play identifies the block and the Players involved.

The Game supports onside kicks. The engine chooses whether to attempt one and records the kick and recovery. The kicking Team can recover an onside kick after it travels at least ten yards, or sooner if the receiving Team touches it first. Otherwise, the receiving Team takes Possession. The recovering Team takes Possession where it recovers the ball and cannot advance it on that Play. If the kicking Team touches the ball too early and the receiving Team has not touched it, the receiving Team takes Possession at that spot. Laterals are outside the initial scope.

The Game also supports turnovers on downs, Flags, Penalties, Injuries, Timeouts, quarter changes, the two-minute warning, and the end of the Game.

Laterals, multiple Flags on one Play, and defensive scoring on a blocked extra point or failed two-point attempt are outside the initial scope.

Each Team normally has 11 distinct eligible Players on the field for a Play. No special check prevents a Team from having too few eligible Players after Injuries.

## Starting information

The starting information identifies the Game, its home and away Teams, their Players, Player Attributes, and Depth Charts. Each Team's entire Roster forms its Game Roster. All Players on those Rosters are eligible at the start, regardless of availability or designation in the source data.

The starting information supplies football data, but need not use the Reference Data's JSON structure or include its historical, financial, or presentation data. The initial capability assumes distinct Team identities, a Player on only one of the two Rosters, and Depth Charts that refer to Players on their own Team. It does not require weather, stadium effects, or coaching tendencies.

Each Player has an identity, name, primary Position, and Player Attributes. The starting Attributes include the physical, passing, ball-carrying, receiving, blocking, defensive, and kicking Attributes available in the 2026 ratings records. The engine uses relevant Attributes for each situation. Not every Attribute must affect every Play. Handling malformed or incomplete starting information remains for later design.

The simulation uses each Team's Depth Chart to choose participating Players and automatically selects replacements when Players are injured. The Game Roster stays fixed while a Player's eligibility can change during the Game. If no eligible Player remains at a Position, an eligible Player from another Position may fill it, according to the Depth Chart.

An optional seed controls reproducibility. The same starting information and supplied seed produce the same complete Game, including Play-by-Play, statistics, and Injuries, within the same simulator version. Without a supplied seed, reproducibility is not required. The same outcome is not guaranteed across simulator versions.

## Results

The completed Game includes a Game Result, a Box Score, and a chronological Play-by-Play. The Box Score includes team and individual Player Statistics for passing, rushing, receiving, defense, kicking, punting, and returns. The exact statistics remain to be defined. The Game Result, Box Score, and Play-by-Play must describe the same Game.

The Box Score includes at least:

- Passing attempts, completions, yards, touchdowns, interceptions, and sacks.
- Rushing attempts, yards, and touchdowns.
- Receiving targets, receptions, yards, and touchdowns.
- Tackles, sacks, interceptions, forced fumbles, and fumble recoveries.
- Field goal and extra-point attempts and successes, punts and punt yards, and kickoff and punt returns and return yards.
- Two-point attempts and conversions, return touchdowns, defensive touchdowns, safeties, fumbles, and fumbles lost.
- Blocked punts, blocked field goal attempts, onside-kick attempts, and onside-kick recoveries.

Team totals agree with the contributing Player Statistics where those measures are directly comparable.

The Box Score and Play-by-Play identify Players by identity, not just by name. The final Score matches the scoring Plays, the Game Result matches that Score, and Player and Team Statistics agree with the Plays that count.

The engine decides whether to accept or decline a Penalty without User interaction. The fixed rules define the yardage and enforcement for each supported Flag, including whether the Down is replayed or a new set of downs is awarded.

Each recorded Play identifies the participating Teams and relevant Players and records the Game Situation before the Play. The Play Outcome explains changes in Score, Possession, Down, Field Position, and Game Clock. The Play-by-Play also records non-Play events, including false starts, Injuries, Timeouts, quarter changes, the two-minute warning, and the end of the Game. The supplied sample Game guides the level of detail, without prescribing its format.

Any participating Player on either Team can suffer an Injury during a Play, including kicking Plays. The injured Player cannot participate in subsequent Plays in that Game. Earlier Plays and their statistics remain valid and recorded. If an accepted Penalty cancels a Play's football results, an Injury that occurred during that Play still counts. The result identifies each injured Player and when the Injury occurred. The effect on availability after the Game, including Injury duration, remains to be defined.

## Plausibility

Results and Player performances must be broadly plausible across many Games. Better-rated Players should generally perform better. Exact measures and calibration targets remain to be defined.

Plausibility is assessed across many Games, not by treating an individual unusual Game as invalid. The assessment considers scores, major statistics, and differences in Player performance.

Numerical plausibility targets, the formulas relating Player Attributes to outcomes, malformed-input handling, and the effect of Injuries on later Games are deferred.
