Status: active — quick reference for the source class hierarchy and thing types
Last updated: 2026-11-06 (the absent CMonitor and CUnit definitions and the Direct3D 9 import stated; the IsA correction reduced to one sentence; the fork fetch step linked; earlier: links updated for the onslaught-preservation layout)
Source: class relationships read from Stuart Gillam's published Onslaught source (pin c3d8ac4, see engine/source/README.md).
Summary: Source class hierarchy and thing-type lookup.
# Class Hierarchies

## Core Object Chain

```
CMonitor (observer pattern)
  └── CThing (base game object)
        └── CComplexThing (rotation, animation)
              └── CActor (movement, physics)
                    └── CUnit (health, damage)
                          └── CBattleEngine (player mech)
```

`CMonitor` and `CUnit` have no definition in the pinned drop: `Monitor.h` and a unit header are absent, and `event.h:9` only forward-declares `CMonitor`. The `CUnit` link under `CActor` and the `CMonitor` link above `CThing` follow the synthesis's inheritance spine ([synthesis](stuart-source-synthesis.md#gameplay-inheritance-spine)). `thing.h:63` names no `CMonitor` base for `CThing` (only `IAudibleThing` and `IRenderableThing`), and `BattleEngine.h:72` names `CUnit` as the base of `CBattleEngine` through a macro whose definition is absent. The tree shows the intended hierarchy, not a checked one.

## Engine Chain

```
CEngine (abstract)
    ├── CDXEngine (DirectX 8)     -- DXEngine.h:22
    └── CPCEngine (PC)            -- PCEngine.h:19
```

The `DirectX 8` labels describe the source headers, which are Direct3D 8. The pristine Steam binary imports and uses Direct3D 9 ([executable analysis](../../platforms/pc/executable-analysis.md); [D3D8 source versus D3D9 release](stuart-source-synthesis.md#d3d8-source-versus-d3d9-release)).

**CORRECTED 2026-07-28 — CPCEngine is a sibling of CDXEngine, not its child.**
This tree previously drew `CPCEngine (PC)` nested one level under
`CDXEngine (DirectX 8)`. That nesting is false in the pinned source:
`references/Onslaught/PCEngine.h:19` is `class CPCEngine : public CEngine` and
`references/Onslaught/DXEngine.h:22` is `class CDXEngine : public CEngine`. An
exhaustive sweep of `class C… : public …` over `references/Onslaught/*.h` finds no
derivation of `CPCEngine` from any DX class anywhere in the corpus.

Two things the corrected tree deliberately does **not** say:

- `CPCEngine` is not the engine the pinned drop selects for PC. Selection is
  compile-time at `references/Onslaught/engine.h:229-239`, and the `_DIRECTX` arm
  picks `CDXEngine` (`extern class CDXEngine ENGINE;`). `CPCEngine` is referenced
  by no other file in the corpus.
- `CPS2Engine` is **INFERRED, not SOURCE.** It appears once, as
  `extern class CPS2Engine ENGINE;` at `engine.h:237`; `PS2Engine.h` is not in the
  pinned drop, so its base class is not established here.

## Frontend Chain

```
CFrontEnd : IController          -- Frontend.h:100
    ├── CDXFrontEnd               -- DXFrontend.h:9
    └── CPCFrontEnd               -- PCFrontend.h:11
```

**CORRECTED 2026-07-28 — CPCFrontEnd is a sibling of CDXFrontEnd, not its child.**
This tree previously drew `CPCFrontEnd` nested one level under `CDXFrontEnd`.
`references/Onslaught/PCFrontend.h:11` is `class CPCFrontEnd : public CFrontEnd`
and `references/Onslaught/DXFrontend.h:9` is
`class CDXFrontEnd : public CFrontEnd`; both derive from the abstract base
declared at `references/Onslaught/Frontend.h:100`.

`CPS2FrontEnd` is **INFERRED and weaker even than CPS2Engine**: the identifier
appears nowhere in the pinned corpus. All that exists is
`#include "PS2Frontend.h"` under the `TARGET == PS2` arm at `Frontend.h:299-301`,
and that header is not in the drop.

## Storage Chain

```
CMemoryCard (abstract)            -- MemoryCard.h:27
    ├── CPCMemoryCard (STUB!)     -- PCMemoryCard.h:8
    └── CXBoxMemoryCard (full)    -- XBoxMemoryCard.h:15
```

Note added 2026-07-28: `CPS2MemoryCard` is **INFERRED**, not SOURCE — the
identifier does not appear anywhere in `references/Onslaught/`. The two PC/Xbox
leaves above it are source-backed at the lines cited.

## Thing Type Checking

```cpp
// references/Onslaught/thing.h:174 — the sole declaration in the pinned corpus
const BOOL IsA(EThingType type) const { return (type & mThingType); }
```

**CORRECTED 2026-07-28 — `IsA` is not virtual, and that was the whole point of
citing it.** The earlier version of this section showed a virtual `IsA` that took a
`ULONG`, returned a normalised 0/1 result and had no `const`. The declaration at
`references/Onslaught/thing.h:174` is non-virtual, const-qualified, takes an
`EThingType` and returns the raw masked value.
`grep -rn 'virtual.*IsA' references/Onslaught/` finds no virtual `IsA`; its only hit is
`BattleEngine.h:250`, `virtual BOOL IsAThreat()`, a different name (how to fetch the fork:
[engine/source README](README.md#fetching-the-pinned-source)).

It is declared exactly once, on `CThing`, and is **not** created by
`DECLARE_THING_CLASS` — that macro has three use sites in the drop
(`thing.h:257`, `actor.h:13`, `BattleEngine.h:72`) and no `#define` anywhere, so
its body is outside the pinned corpus. See
[`core/thing-system.md`](core/thing-system.md),
corrected the same day.

## Thing Type Flags → Kill Categories

| Flag | Kill Category |
|------|---------------|
| THING_TYPE_AIR_UNIT | TK_AIRCRAFT |
| THING_TYPE_VEHICLE | TK_VEHICLES |
| THING_TYPE_INFANTRY | TK_INFANTY |
| THING_TYPE_MECH | TK_MECHS |
| THING_TYPE_EMPLACEMENT | TK_EMPLACEMENTS |
