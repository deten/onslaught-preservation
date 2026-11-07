# Game Mechanics

Status: active — index of accepted mechanics measurements and contracts
Last updated: 2026-11-06 (the folder indexes and the top-level static contracts routed, cheat codes marked not yet published, shorthand glossary added; earlier: links updated for the onslaught-preservation layout)
Summary: routes to each accepted game-mechanics owner; every owner states its own
specimen, procedure and limits.

This area keeps accepted measurements and source/static context that materially
support product or deterministic [Core](../INDEX.md#terms) behavior.

- [God mode](cheats/god-mode.md)
- Cheat codes (not yet published)
- Campaign scalar status (archived research record, not published)
- [Released spawner squad-cycle contract](spawner-squad-cycle.md)
- [Level 100 final drone wave, Help Player turrets and abort](../levels/level100-final-drone-wave.md)
- [Battle Engine weapon stores, charge and firing state](battleengine/battle-engine-weapon-stores.md)
- [Battle Engine auto-aim, launch position and Gun emitters](battleengine/battle-engine-aiming.md)
- [Level 100 construction order: shared draws and queued events](../levels/level100-construction-order.md)
- [World 110 construction order, transition and first frames](../levels/world-110-construction-order.md)
- [Named waypoint paths: loading, start node and following](waypoint-paths.md)
- [Dropships: flight](dropship-flight.md)
- [Dropships: landing, unloading and leaving](dropship-landing.md)
- [Walker dash: the retail timing window](battleengine/walker-dash.md)
- [World 110 serialized initial-object seed admission](../levels/world-110-initial-constructor-seeds.md)
- [World 110 authored player-start admission](../levels/world-110-player-start-admission.md)
- [Target acquisition static contract](battleengine/battleengine-target-acquisition-static-contract-v1.md)
- [Walker-to-jet timing](battleengine/walker-transform-morph-timing-v1.md)
- Jet forward response (archived research record, not published)
- Jet energy response (archived research record, not published)
- [Battle Engine configuration values](battleengine/battleengine-config-values.md)
- [Battle Engine folder index](battleengine/README.md)
- [Unit AI folder index](ai/README.md)
- [Career rules folder index](career/README.md)
- [Weapons folder index](weapons/README.md)
- [Cheats folder index](cheats/README.md)
- [Destroyable segments static contract](destroyable-segments-static-contract.md)
- [Unit, Battle Engine and gameplay static contract](unit-battleengine-gameplay-static-contract.md)
- [`CWorldPhysicsManager::CreateExplosion` caller family](cexplosion-factory-callers.md)
- [`CController` shared mapping and dispatch](controller-shared-semantics.md)
- [`CRound::Hit`, configured explosion creation and `CExplosion::Hit`](cround-hit-damage-path.md)

Each measurement is limited to its stated specimen and procedure. Core mapping
does not claim broader parity. Superseded measurements and plans live in Git
history.

## Shorthand used in these notes

Short forms the mechanics notes use without a full expansion. Where a term has a home document, its entry links to it.

- **6000 and 6001**: the Battle Engine event numbers `BECOME_JET` and `BECOME_WALKER` in the pinned source (`references/Onslaught/BattleEngine.h`, lines 22-23).
- **.text**: the code section of the executable image, where the function bodies are.
- **MapWho**: the spatial partition of Things into sectors that collision queries scan; each Thing's registration is its `mMapWhoEntry` ([thing system](../engine/source/core/thing-system.md)).
- **PC24, PC53, PC64**: the x87 precision-control settings, with 24-bit, 53-bit and 64-bit significands ([walker dash, precision assumption](battleengine/walker-dash.md)).
- **RN**: round to nearest, ties to even, the x87 rounding-control setting.
- **ring bucket**: one slot of the event manager's scheduling ring, which runs at 20 Hz ([event scheduler semantics](../engine/world/event-manager-scheduler-semantics.md)).
- **TTD**: a time-travel debugging trace, a recorded debugger session that can be replayed.
