# Vocabulary decisions

This document records the naming conventions and scope decisions agreed during vocabulary discovery. [GLOSSARY.md](../GLOSSARY.md) contains the definitions. These decisions refine the provisional language in [the initial vocabulary](0002-initial-vocabulary.md).

## Naming conventions

- Nonstop is the canonical product name. Simulator is descriptive shorthand; FFS describes its category. Game refers to a football contest.
- NFL refers exclusively to the National Football League. General football concepts, such as Play, Down, and Formation, belong to American Football.
- The NFL prefix is reserved exclusively for real-life entities. Unqualified Team, Player, and Game refer to simulated entities when context is clear; Simulated and Real-world qualifiers make distinctions explicit when needed.
- Glossary terms use human-readable names, such as Pass Play. Code identifiers and interface labels remain undecided.
- Software component names, including Play Caller and Simulation Engine, belong in architecture documentation rather than the domain glossary.

## Scope decisions

- The glossary preserves real-life football definitions and adds simulation vocabulary as it is agreed.
- Every NFL Player belongs to an NFL Team at all times within this project's scope.
- NFL Season and NFL Regular Season are equivalent. Preseason and postseason are excluded.
- Formation describes counts of On-field Players at each Position. Exact alignment is excluded.
- Position is included; play-specific Role is excluded.
- Injury prevents participation within this project's scope. A separate Availability concept was not adopted.
- Field Position is included; Line of Scrimmage is excluded.
- Quarter and Overtime are included; Period is not a separate glossary term.
- Flag names a rule violation; Penalty names its prescribed consequence.
- Simulated League names an independently progressing football world; League is shorthand when context is clear. Simulator names the Nonstop software.
- Reference Data names the football information used as the starting basis for Simulated Leagues.
- Sim is an accepted prefix for any simulated entity, including SimTeam, SimPlayer, and SimLeague. Unqualified simulation names remain valid when context is clear.
- Existing Reference Data is modeled after real-life entities. SimPlayers evolve independently from their real-life references during a SimLeague.
- Season, Week, Franchise, and Week Schedule name simulated counterparts of the corresponding NFL-prefixed real-life concepts.
- Each SimLeague is scoped to one Season. Persistence across seasons is out of scope because it substantially increases complexity.
- Changes to Reference Data do not automatically affect existing SimLeagues.
- League State names the complete current circumstances of a League, including its progress, teams, players, and accumulated results.
- Snapshot names a preserved record of League State at a particular point. Save is the verb for preserving a Snapshot; Resume names continuation from a Snapshot.
- A Snapshot remains fixed as a League progresses. Later saving creates a new Snapshot rather than changing the earlier point it represents.
- Restore makes a Snapshot's League State current without necessarily advancing it; Resume continues progression from restored state.
- Independent alternate continuations from a Snapshot are outside the initial scope. Each League has one current progression.
- League progression includes automatic saving after a Week is completed. Once a Week has been played, the user cannot restore an earlier Snapshot in which that Week was unplayed.
- Games run from beginning to end in one pass. Game State remains distinct from Game Situation, although Game State is expected to be used infrequently.
- Play-by-Play is needed to score individual plays when Fantasy Football functionality is added. This does not authorize defining Fantasy Football vocabulary yet.
- User names the human operating Nonstop; Player and SimPlayer name simulated football participants.
- Current Snapshot names the latest saved League State permitted for restoration. Retained earlier Snapshots are not selectable as playable restore points after Week Completion.
- Week Completion requires every scheduled Game to finish and its results and Play-by-Play to be recorded.
- The User manually advances to the next Week. Week Completion alone does not change the Current Week; the manual advance is the transition boundary.
- Recorded Play-by-Play remains readable independently of running a Game. Later scoring reads recorded Play Outcomes without replaying or changing the football outcomes.
- Week Number identifies a fixed Week within its Season, independent of progression or the Week being viewed.
- Current Week identifies the League's progression position and remains current after its Games finish until the User advances. Previous Week and Next Week are its immediate neighbors within the Season.
- Browsing past or future Weeks does not change Current Week or the meanings of Previous Week and Next Week.
- Current, previous, and next qualify named concepts; they are not universal time labels. Current Snapshot may change while Current Week remains the same.
- Advance Week changes Current Week to Next Week and is permitted only after Current Week Completion. It does not itself play Games.
- Week 1 is the initial Current Week. Future navigation labeled Previous Week from Week 1 will show Fantasy draft results rather than a football Week. Draft terminology remains deferred.
- An internal Week 0 may represent the draft for navigation purposes, but it is not user-facing vocabulary or a scheduled football Week.
- The final scheduled Week has no Next Week or Advance Week action. Season Completion occurs when every Week has reached Week Completion, without an additional advance action.
- League Completion marks the end of a League's required activities and disables further progression. Its exact relationship to Season Completion awaits definition of the additional Fantasy Football activities.
- SimLeague End of Season Report names the final presentation of accumulated results, distinct from League Completion. League Summary is not the adopted name.
- A Completed League remains available for inspecting results and Play-by-Play.
- Advance Week triggers an Autosave that preserves the new Current Week in the Current Snapshot.
- Future Fantasy Football functionality will introduce intermediate states within a Week. Current Week must not be treated as a complete description of League progression.
- Previous Week retains its football meaning. The interface's Previous Week navigation has a Week 1 exception for viewing draft results, without restoring earlier League State.
- Simulate Game names the operation that produces a Game's results and Play-by-Play in one pass, distinct from reading or scoring recorded plays.
- Player Attributes describe football characteristics used in simulation; Player Statistics describe recorded performance. Specific attributes remain undecided.
- The initial simulation vocabulary baseline was confirmed before the separate Turn discussion.
- Turn belongs to the Fantasy Football context and names a stage within a Week governing permitted User actions. It is not synonymous with Week or an individual action.
- A Week contains six named Turns: Monday, Tuesday, Wednesday, Thursday, Friday, and Weekend. Weekend combines Saturday and Sunday.
- All simulated Games are played during Weekend. The weekday analogy excludes Thursday Night and Monday Night games.
- Fantasy vocabulary authorization is limited to Turn and its permitted-action boundaries. The actions permitted during each Turn remain undefined.
- Turns proceed in order: Monday, Tuesday, Wednesday, Thursday, Friday, Weekend. Advance Week moves from Weekend to the following Week's Monday, subject to the existing completion and final-Week boundaries.
- Weekend remains Current Turn while the User examines the completed Games, until Advance Week.
- Current Turn identifies the League's active stage within Current Week, independently of the Week or results being viewed.
- Turn names describe simulated stages, with no connection to real-world weekdays or elapsed time and no deadlines implied by their names.
- Advance Turn ends Current Turn and enters the next stage. Monday through Friday advance within the same Week; Weekend uses Advance Week to enter the following Monday, subject to completion and final-Week boundaries.
- Every Turn transition triggers an Autosave preserving the new Current Week and Current Turn.
- Earlier Turns cannot be restored as playable stages or revisited to change decisions; historical results remain inspectable.
- Turn progression and action permissions are separate concepts. An action may be permitted across multiple Turns, so ending a Turn does not imply that every action available during it becomes unavailable.
- The first regular-season Week begins on Monday of Week 1. Any preceding draft stage is outside the six weekly Turns and remains to be defined.

## Deferred topics

- Fantasy Football definitions other than the narrowly authorized Turn vocabulary remain deferred pending explicit authorization.
- League Progress and intermediate-stage terminology are deferred to the Fantasy League discussion.
- Snapshot retention and failure recovery are deferred to later design work rather than expanded into operational glossary terms.
- The exact League Completion boundary awaits the additional Fantasy Football activities.
- The actions permitted during individual Turns, including permissions spanning multiple Turns, remain to be defined.
- Numeric field coordinates, detailed rule values and exceptions, code conventions, and architecture remain outside this vocabulary exercise.
