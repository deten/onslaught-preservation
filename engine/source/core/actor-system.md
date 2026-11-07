# Actor System

Status: historical source analysis (December 2025)
Last updated: 2026-11-06 (header added; water-death and god-mode statements reworded as source observations; emplacement flag name corrected; CComplexThing row aligned with thing-system.md; footer date removed)
Summary: the CActor movement, physics timing and kill-category layer as written in Stuart's source; its water-death and god-mode statements are source observations, not retail findings.
Evidence: SOURCE — the pinned Onslaught source (actor.cpp/h, Player.cpp, game.cpp); no retail behaviour is established here.

> Analysis of actor.cpp/h - December 2025

## Overview

The Actor system is the core movement component in the game's entity hierarchy. It manages physics, position, velocity, and orientation for all moving game objects. **This is runtime-only and has NO relevance to save files**, but understanding it explains kill categorization and the in-water state that the pinned source checks for water death.

---

## Purpose

CActor provides the foundational movement and physics layer for all dynamic game entities. Every mech, vehicle, aircraft, and infantry unit inherits from CActor to gain movement capabilities.

---

## Class Hierarchy

```
CThing -> CComplexThing -> CActor -> CUnit -> CBattleEngine
```

| Class | Adds |
|-------|------|
| `CThing` | Base entity - position, type flags, lifecycle |
| `CComplexThing` | Rotation, animation, scripting |
| `CActor` | Movement, velocity, physics, ground/water state |
| `CUnit` | AI, health, damage, weapons |
| `CBattleEngine` | Player-controlled mech with jet/walker modes |

---

## Key Members

| Member | Type | Purpose |
|--------|------|---------|
| `mVelocity` | FVector | Current velocity vector |
| `mOldPos` | FVector | Previous frame position (for render interpolation) |
| `mOldOrientation` | FMatrix | Previous frame orientation |
| `mLastTimeOnGround` | float | Timestamp of last ground contact |
| `mLastTimeInWater` | float | Timestamp of last water contact |
| `mLastTimeOnObject` | float | Timestamp of last object contact |

---

## Physics System

The game runs physics at a fixed 20 FPS tick rate with render interpolation for smooth visuals:

```cpp
// From game constants
#define GAME_FR     20.0f    // Physics frames per second
#define CLOCK_TICK  0.05f    // Seconds per physics tick (1/20)
```

**LOD Optimization**: The actor system provides two movement fidelity levels:

| Method | Fidelity | Used For |
|--------|----------|----------|
| `Move()` | Full | On-screen actors, player's mech |
| `LowFidelityMove()` | Simplified | Off-screen actors, distant units |

Low fidelity movement skips expensive calculations (terrain collision, animation updates) for performance.

---

## Render Interpolation

To display smooth 60+ FPS visuals from 20 FPS physics:

In outline (a paraphrase of `actor.cpp`, not a quotation): the render position is the previous position
`mOldPos` blended towards the current `mPos` by how far the frame is through the current physics tick, and
the render orientation is blended the same way between the old and current orientation.

This is why the game looks smooth despite the 20 FPS physics rate.

---

## State Timing System

The actor uses time-based state tracking rather than boolean flags:

```cpp
// From actor.h - state checks use time thresholds
BOOL IsOnGround() { return (GetTime() - mLastTimeOnGround < 0.15f); }
BOOL IsInWater()  { return (GetTime() - mLastTimeInWater < 0.25f); }
BOOL IsOnObject() { return (GetTime() - mLastTimeOnObject < 0.15f); }
```

**Why this matters for god mode and water death (pinned source):**

In the pinned source, water contact is declared through state: `DeclareInWater()` sets `mLastTimeInWater` to the current time, and the water game-over check reads `IsInWater()` (`references/Onslaught/game.cpp:2523`), not health damage. God mode guards **damage**, so on the source's own terms it is a different code path. Whether god mode bypasses water death on the retail executable is an open question ([god mode, Open questions](../../../mechanics/cheats/god-mode.md#open-questions)).

```
Water Contact
    │
    ▼
DeclareInWater()
    │
    ├── mLastTimeInWater = GetTime()
    │
    ▼
IsInWater() returns TRUE
    │
    ▼
Water death logic triggered (state-based, NOT damage-based)
    │
    ▼
Unit destroyed (no damage step on this path)
```

In the pinned source the water decision is made by that state check, not by the damage code, so a god mode that only guards damage would not be expected to stop it. Whether it does on the retail executable is an open question.

---

## Kill Tracking Connection

Kill categorization is determined by `THING_TYPE_*` flags on the actor:

```cpp
// From Player.cpp - called when player kills something
void CPlayer::ThingKilledBy(CThing* thing) {
    if (thing->IsA(THING_TYPE_AIR_UNIT))
        mThingsKilled[TK_AIRCRAFT]++;
    if (thing->IsA(THING_TYPE_VEHICLE))
        mThingsKilled[TK_VEHICLES]++;
    if (thing->IsA(THING_TYPE_EMPLACEMENT))
        mThingsKilled[TK_EMPLACEMENTS]++;
    if (thing->IsA(THING_TYPE_INFANTRY))
        mThingsKilled[TK_INFANTY]++;  // typo preserved in source/binary
    if (thing->IsA(THING_TYPE_MECH))
        mThingsKilled[TK_MECHS]++;
}
```

The `THING_TYPE_*` flags are set during entity initialization based on the actor subclass:

| Entity Type | THING_TYPE Flag | Kill Category |
|-------------|-----------------|---------------|
| Helicopters, fighters | `THING_TYPE_AIR_UNIT` | TK_AIRCRAFT |
| Tanks, APCs | `THING_TYPE_VEHICLE` | TK_VEHICLES |
| Turrets, SAM sites | `THING_TYPE_EMPLACEMENT` | TK_EMPLACEMENTS |
| Soldiers | `THING_TYPE_INFANTRY` | TK_INFANTY (typo) |
| Enemy Battle Engines | `THING_TYPE_MECH` | TK_MECHS |

---

## Relevance to Save Editing

**NONE directly** - CActor data is NOT saved to .bes files. Actor state exists only in RAM during gameplay.

| Aspect | Save System | Actor System |
|--------|-------------|--------------|
| **Persistence** | Written to .bes file | Never serialized |
| **Lifetime** | Survives game restart | Lost on level change |
| **Purpose** | Track player progress | Runtime movement/physics |
| **Data Location** | File offsets 0x0000-0x2714 | Only in RAM |

**However**, understanding the actor system explains:

1. **Kill categorization** - The `THING_TYPE_*` flags determine which kill counter increments
2. **Water death mechanism** - state-based, not damage-based in the pinned source; whether god mode covers it is an open question
3. **Physics timing** - the 20 FPS tick rate explains movement granularity.

---

## Files Analyzed

| File | Purpose |
|------|---------|
| `actor.cpp` | Movement implementation, physics tick, state declarations |
| `actor.h` | CActor class definition, state timing methods |
| `Player.cpp` | Kill tracking integration with actor type flags |

---

## See Also

- [thing-system.md](thing-system.md) - Base Thing/CComplexThing hierarchy
- [../gameplay/battle-system.md](../gameplay/battle-system.md) - CBattleEngine specifics
- [../../../mechanics/cheats/god-mode.md](../../../mechanics/cheats/god-mode.md) - open question: whether god mode covers water death
