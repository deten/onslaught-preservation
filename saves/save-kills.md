# Kill counters quick reference

Status: active — quick reference for kill counter offsets, the encoding, unlock thresholds and the killed-type enum
Last updated: 2026-11-06 (unit-goodie list removed; unlock-source, goodie-timing and killed-type enum statements corrected; header moved below the title)
Summary: Kill counter and metadata lookup.

## Kill Counter Offsets (True View)

| Index | Offset | Category | Enum |
|-------|--------|----------|------|
| 0 | 0x23F6 | Aircraft | TK_AIRCRAFT |
| 1 | 0x23FA | Vehicles | TK_VEHICLES |
| 2 | 0x23FE | Emplacements | TK_EMPLACEMENTS |
| 3 | 0x2402 | Infantry | TK_INFANTY (typo!) |
| 4 | 0x2406 | Mechs | TK_MECHS |

## Encoding

```
stored = (meta << 24) | (kills & 0x00FFFFFF)
```

Preserve `meta` when patching. Clamp `kills` to 24-bit (`0..0x00FFFFFF`).

## Goodie Unlock Thresholds

| Category | Thresholds | Patch Value |
|----------|------------|-------------|
| Aircraft | 25, 50, 75, 100 | 100+ |
| Vehicles | 100, 200, 300, 400 | 400+ |
| Emplacements | 25, 50 (75 in combos) | 75+ |
| Infantry | 40, 80, 160 (100 appears in a combined unlock: Aircraft+Infantry 50+100) | 160+ |
| Mechs | 20, 40, 80 (40 unlocks 2 goodies) | 80+ |

**Recommendation**: Patch all to 400+ to unlock everything.

## Kill-Based Goodie Unlocks (Unit Goodies)

The per-threshold unlock rules are not yet tabulated in this repository. [goodies-system.md](goodies-system.md) gives the index ranges and categories, and the companion's [unlock service](https://github.com/dlprentice/Onslaught-Career-Editor/blob/51129bab45489e5e45ee1c047cbcd8072bf4cc50/companion/OnslaughtToolkit.Godot/Careers/GoodieUnlockRequirementService.cs) maps the rules by goodie index and marks the indices it has not mapped. Unit-goodie names are not listed here until they are sourced.

## Runtime Behavior

Kill counts alone don't immediately unlock goodies. Goodie states are recomputed after a mission completes and kill counts update ([by-function/CCareer__UpdateGoodieStates.md](by-function/CCareer__UpdateGoodieStates.md)).

## EKilledType Enum

The enum is in the pinned reference source at [`references/Onslaught/Player.h`](https://github.com/dlprentice/Onslaught/blob/c3d8ac464259942b83fa8aa7f867c856e77b734d/Player.h), lines 27 to 38 (Stuart's source at [`c3d8ac4`](https://github.com/dlprentice/Onslaught/tree/c3d8ac464259942b83fa8aa7f867c856e77b734d)). The source gives no explicit values, so each index is the member's position in the list.

| Index | Name | Saved |
| ---: | --- | --- |
| 0 | `TK_AIRCRAFT` | yes |
| 1 | `TK_VEHICLES` | yes |
| 2 | `TK_EMPLACEMENTS` | yes |
| 3 | `TK_INFANTY` (the spelling is the source's) | yes |
| 4 | `TK_MECHS` | yes |
| 5 | `TK_TOTAL` (a marker, not a counter) | no |
| 6 | `TK_HACK_AGRADES` | no; runtime only |
| 7 | `TK_HACK_SGRADES` | no; runtime only |
