# Battle Engine System

Status: active — source analysis of the Battle Engine classes, with the god-mode and save links
Last updated: 2026-11-06 (class files, state names, damage order, water rule, god-mode persistence and save-version wording corrected; header added)
Summary: the Battle Engine classes, transformation states, god-mode flags, damage flow and configuration data as written in Stuart's pinned source; runtime claims stay with their dated notes and open questions.
Evidence: SOURCE — the pinned Battle Engine source files, cited by name and line; the god-mode runtime notes linked below are dated and have no archived capture.

> Analysis from BattleEngine.cpp/h, BattleEngineConfigurations.cpp/h, BattleEngineDataManager.cpp/h, BattleEngineJetPart.cpp/h, and BattleEngineWalkerPart.cpp/h - December 2025

## Overview

The Battle Engine system implements the player-controlled mech, representing the titular Battle Engine in the game. This system includes dual-mode (walker/jet) mechanics, transformation, combat systems, and god mode implementation.

---

## BattleEngine Core Class (BattleEngine.cpp/h)

The `CBattleEngine` class is the core player-controlled mech implementation.

### Class Inheritance Hierarchy

```
CThing
  └── CComplexThing
        └── CActor
              └── CUnit (definition absent from the pinned drop; inferred from uses)
                    └── CBattleEngine
```

The deep inheritance chain reflects the game's object-oriented design, with each level adding capabilities:
- `CThing` - Base game object
- `CComplexThing` - Multi-part objects
- `CActor` - Animated entities
- `CUnit` - base class named by `CBattleEngine`; its definition is absent from the pinned drop, so its role is inferred from uses
- `CBattleEngine` - Player mech specifics

### Composition Pattern

The Battle Engine uses composition for its dual-mode (walker/jet) mechanics:

```cpp
CBattleEngineWalkerPart mWalkerPart;  // Ground movement component
CBattleEngineJetPart    mJetPart;      // Flight physics component
CActiveReader<CPlayer>  mPlayer;       // Player association
```

This allows mode-specific code to be cleanly separated while sharing the core CBattleEngine state.

### Key Constants

| Constant | Value | Purpose |
|----------|-------|---------|
| `kBattleEngineStores` | 6 | Number of energy stores |
| `kMaxConfigurations` | 20 | Maximum mech configurations |
| `MAX_AUG_VALUE` | 10.0 | Weapon augmentation threshold |
| `BATTLE_ENGINE_COFGHEIGHT` | 1.9f | Center of gravity height |
| `BATTLE_ENGINE_TRANSFORM_TIME` | 0.5 | Transformation duration (seconds) |
| `AUG_DECREASE_RATE` | 0.01 | Augmentation drain per simulation update (20 Hz tick; `BattleEngine.cpp` line 1351) |

### Transformation System

The Battle Engine has four states for walker/jet mode transitions:

| State | Description |
|-------|-------------|
| `BATTLE_ENGINE_STATE_MORPHING_INTO_WALKER` | Jet → Walker transition |
| `BATTLE_ENGINE_STATE_MORPHING_INTO_JET` | Walker → Jet transition |
| `BATTLE_ENGINE_STATE_WALKER` | Ground mode active |
| `BATTLE_ENGINE_STATE_JET` | Flight mode active |

**Transformation Requirements:**
- Walker → Jet requires `mMinTransformEnergy` (sufficient energy reserve)
- Transformation takes 0.5 seconds (`BATTLE_ENGINE_TRANSFORM_TIME`)
- **"MASSIVE HACK"** comment in code: mesh swapping between `f_be1.msh` (walker) and `f_be2.msh` (jet)

### God Mode Implementation Details

Two independent flags control invincibility:

| Flag | Effect |
|------|--------|
| `mVulnerable` | When FALSE, immune to damage |
| `mInfinateEnergy` | When TRUE, unlimited energy |

**SetVulnerable(FALSE) Behavior:**
- Makes BE immune to damage
- Visual damage effects still play (sparks, sounds)
- At line 2234 in `Damage()`: if `mVulnerable==FALSE`, life/shields/energy are restored after damage calculation

**SetInfinateEnergy(TRUE) Behavior:**
- Refills energy to maximum when enabled
- Prevents energy drain during gameplay

**CRITICAL: Water Death Exception (Line 1261)**

Source-only reading, not runtime-tested. In outline (`BattleEngine.cpp` line 1261), the Battle Engine survives water contact only when `mVulnerable` is FALSE and developer mode is on; otherwise it dies at once. Whether water and other hazards bypass the god-mode restore at runtime is an open question (see the open questions in [god-mode.md](../../../mechanics/cheats/god-mode.md)).

On this reading, god mode does not protect against water unless developer mode is also on.

### Damage System

The `Damage()` function (`BattleEngine.cpp` lines 2127-2240) implements the following flow for positive damage:

1. Increase the player's damage-taken statistic (`PS_DAMAGETAKEN`) by the damage times 256
2. If life is nonnegative, absorb the damage against shields: the shield share is the damage times `mShieldEfficiency`, divided by 100
3. Suppress shield damage when the damage-shields flag is false
4. Charge the walker augment from absorbed shield damage when the augment is inactive and the walker has an augment weapon
5. Pass damage beyond the remaining shields to life, draining the shields to zero
6. Start death only when life becomes negative and `mVulnerable` is on
7. Stamp the last-damage time and add a directional damage flash

Then the augment value is clamped to 10, shields are mirrored into energy in walker state, negative damage repairs life and then energy, and shock and controller vibration follow life loss. When `mVulnerable==FALSE`, the original life, shields and energy are restored (god mode).

This means damage is fully calculated and reported for statistics even in god mode - only the final restoration prevents actual harm.

### Weapon Augmentation System

The augmentation system powers up weapons based on absorbed shield damage:

```cpp
mAugValue += shieldDamageAbsorbed;
if (mAugValue >= MAX_AUG_VALUE) {  // >= 10.0
    // Primary weapon becomes augmented (more powerful)
}
// Augmentation drains at AUG_DECREASE_RATE (0.01/frame)
```

Augmentation provides a reward loop: taking shield damage charges your weapon for more powerful attacks.

### Debug Features

| Feature | Access Method |
|---------|---------------|
| `cg_battleenginevisible` | Console variable - toggle BE visibility |
| Developer mode water bypass | Requires `mDeveloperMode` flag |

### Disabled Features Discovered

Remnants of cut features found in the source code:

- **Afterburner**: `BUTTON_MECH_JET_AFTERBURNER` handler is commented out
- **Cloak**: `BUTTON_MECH_CLOAK` exists and calls `mBattleEngine->HandleCloak()`

These were apparently cut from the final game but the button mappings and hooks remain in the code.

### Hardcoded Level Hacks

These levels have special handling for carrier-based leg animation (when the BE starts on a ship):

```
Levels: 231, 232, 331, 221, 222, 524, 523, 332
```

These correspond to "Special Pan Camera Levels" - levels where the player starts on a moving platform.

---

## Battle Engine Configurations (BattleEngineConfigurations.cpp/h, BattleEngineDataManager.h)

Defines the mech configuration system for stats and weapons. The configuration-name table is `UBattleEngineConfigurations` (BattleEngineConfigurations.h); the stats and weapon lists are `CBattleEngineData` (BattleEngineDataManager.h).

### Key Constants

| Constant | Value | Purpose |
|----------|-------|---------|
| `kMaxConfigurations` | 20 | Maximum mech configurations |
| `kCurrentBattleEngineDataFormat` | 12 | Current data format version |
| `kBattleEngineStores` | 6 | Number of energy stores |

### CBattleEngineData Class (BattleEngineDataManager.h)

```cpp
class CBattleEngineData {
    float mMaxAirVelocity;       // Jet mode max speed
    float mLife;                  // Health points
    float mEnergy;                // Energy capacity
    float mShieldEfficiency;      // Shield effectiveness
    // ... weapon lists
};
```

### Default Weapons

- Walker mode: "Vulcan Cannon 1", "Pulse Cannon Pod"
- Jet mode: "Vulcan Cannon 1", "Missile Pod"

### Format Version History

12 format versions of Battle Engine configuration data, read with backward compatibility; they do not version Career saves ([stuart-source-synthesis.md](../stuart-source-synthesis.md#configuration-data-and-versioning)).

---

## Battle Engine Data Manager (BattleEngineDataManager.cpp/h)

Static manager class for mech configurations.

### Class Structure (BattleEngineDataManager.h)

```cpp
class UBattleEngineDataManager {
    static void Load(CMEMBUFFER &inFile);
    static CBattleEngineData* GetConfiguration(const char* name);
    // Version 1-12 field additions
};
```

**Default Configuration Name:** "Standard"

---

## Battle Engine Jet Part (BattleEngineJetPart.cpp/h)

Jet mode flight physics and controls.

### Aerobatics System

| Maneuver | Activation | Duration |
|----------|------------|----------|
| Barrel Roll | Double-tap left/right within 0.2s | 26 frames |
| Loop | Double-tap forward/back within 0.2s | Variable |

### Stall Detection

- Auto-morph to walker mode if velocity < 0.15 for 2.5 seconds
- Prevents players from hovering in jet mode

### Water Skimming

- Damage applied when altitude < 0.5 units
- Encourages high-altitude flight over water

**Typo Preserved:** "barrell roll" (line 441) - should be "barrel roll"

---

## Battle Engine Walker Part (BattleEngineWalkerPart.cpp/h)

Walker mode movement and ground combat.

### Dash System

| Parameter | Value |
|-----------|-------|
| Activation | Double-tap direction |
| `mDashVelocity` | 25.0 |
| `mDashLength` | 15 frames |

Walk cycle animation is tied to movement velocity for realistic locomotion.

---

## Typos Preserved in Source

| Typo | Location | Should Be |
|------|----------|-----------|
| `barrell roll` | BattleEngineJetPart.cpp:441 | barrel roll |
| `GetCurrentAccleration` | Method name | GetCurrentAcceleration |
| `sepecial` | Comment | special |
| `mInfinateEnergy` | Member variable | mInfiniteEnergy |

---

## Relevance to Save Editing

**INDIRECT** - Battle Engine state is runtime-only and NOT saved to .bes files.

However, understanding the Battle Engine system explains:

1. **God mode persistence**: The internal build has a per-player `mIsGod[]` array in CCareer, and the Steam save persists per-player god flags ([save-format.md](../../../saves/save-format.md) rows 37-38; [god-mode.md](../../../mechanics/cheats/god-mode.md)). Whether a saved flag without the cheat name changes combat at runtime is an open question (the open questions in [god-mode.md](../../../mechanics/cheats/god-mode.md)). The pause-menu item that toggles the flag is gated by `IsCheatActive(3)` (the `Maladim` name); the dated runtime notes in god-mode.md report a visible toggle and a combat-damage effect, with no archived capture.
2. **Water death exception**: On the source-only reading (not runtime-tested), god mode does not prevent water death unless `mDeveloperMode` is set
3. **Kill tracking**: Battle Engine damage reports feed into kill counters saved at CCareer `+0x23F4` (file `0x23F6`, true view)
4. **Configuration system**: Mech stats are loaded from config files, not save files

---

## Files Analyzed

| File | Purpose |
|------|---------|
| `BattleEngine.cpp` | Core mech implementation, god mode, damage, transformation |
| `BattleEngine.h` | CBattleEngine class definition, constants |
| `BattleEngineConfigurations.cpp` | `UBattleEngineConfigurations` configuration-name table |
| `BattleEngineConfigurations.h` | `UBattleEngineConfigurations` class |
| `BattleEngineDataManager.cpp` | Static configuration manager |
| `BattleEngineDataManager.h` | `CBattleEngineData` and `UBattleEngineDataManager` class definitions, format version |
| `BattleEngineJetPart.cpp` | Flight physics, aerobatics |
| `BattleEngineJetPart.h` | Jet part class definition |
| `BattleEngineWalkerPart.cpp` | Ground movement, dash system |
| `BattleEngineWalkerPart.h` | Walker part class definition |

---

## See Also

- [../core/actor-system.md](../core/actor-system.md) - Base movement and physics
- [../../../mechanics/cheats/god-mode.md](../../../mechanics/cheats/god-mode.md) - God mode investigation
- [../../../saves/save-format.md](../../../saves/save-format.md) - God mode offsets in save files
