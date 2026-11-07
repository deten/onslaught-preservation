# Kill Tracking System

Status: active reference; the top-byte notes are corrected to the byte-level rule in [save-format.md](save-format.md#kill-counters-and-slots)
Last updated: 2026-11-06 (source named as Stuart Gillam's published source; dated labels removed; code blocks replaced by prose with pinned citations; the screen-position owner names aligned with the decompilation; the goodie-unlock list is no longer described as complete; earlier: retired AppCore/WinUI tools in the past tense)
Summary: kill categories, 24-bit counts and their Goodie thresholds; the first two counters' top byte is a front-end screen offset that Load resets to 0 when out of range.
Evidence: MEASURED for the 24-bit mask and the top-byte rule (pristine bytes `0x0041C16B`, `0x0042126a`-`0x00421280`, `0x004218f0`-`0x00421960`); SOURCE for the kill categories (`Player.h`); thresholds per the cited Goodie owner.
Specimen: pristine `<maintainer evidence>/safe-copy-bea-pristine/BEA.exe.original.backup`, SHA-256
`74154bfae14ddc8ecb87a0766f5bc381c7b7f1ab334ed7a753040eda1e1e7750`.

## Kill Types (from source: EKilledType - Player.h)

**TK = "Thing Killed"** - Confirmed via `GetNumEnemyThingKilled()` and `KilledEnemyThing()` in Player.cpp. Categorizes enemy units destroyed by type. NOT "Team Kill" (no friendly fire tracking in codebase).

The kill types are the five saved categories, indices 0 to 4 (Aircraft, Vehicles, Emplacements, Infantry and Mechs, in the order of the offset table below), an end marker at index 5, and two runtime-only counters at indices 6 and 7 (A-grades and S-grades). The enumeration is in `Player.h` ([pinned source, lines 27-38](https://github.com/dlprentice/Onslaught/blob/c3d8ac464259942b83fa8aa7f867c856e77b734d/Player.h#L27-L38)).

**Only indices 0-4 are saved to the .bes file.** Indices 6-7 are "virtual" counters used at runtime for grade-based goodie unlock checks. They're calculated on the fly by counting grades in the save, NOT stored as counters. In `CGoodieData::GetMethod` ([`FEPGoodies.cpp`, lines 92-101](https://github.com/dlprentice/Onslaught/blob/c3d8ac464259942b83fa8aa7f867c856e77b734d/FEPGoodies.cpp#L92-L101), pinned source), index 6 counts A-grades, index 7 counts S-grades, and every other index is read from the save file.

---

## TK_ Confirmed: "Thing Killed" NOT "Team Kill"

**Hypothesis tested:** TK might mean "Team Kill" (friendly fire).

**Evidence confirms TK = "Type/Thing Killed":**
- The function `KilledEnemyThing(CUnit* thing)` explicitly tracks kills of **enemy** units only
- Function name says "EnemyThing" - no friendly fire tracking anywhere
- Kill attribution uses `thing->IsA(THING_TYPE_*)` to determine unit category
- The array storing counts is `mThingsKilled` (line 100 of Player.h)
- The getter is `GetNumEnemyThingKilled(EKilledType type)`

**Conclusion:** TK_ stands for "Thing Killed" categorizing enemy units destroyed by type.

---

## Memory Layout

Kill counters live at **CCareer offset `0x23F4`** (5 dwords total), with true on-disk dwords at `0x23F6`, `0x23FA`, `0x23FE`, `0x2402`, `0x2406` (file = `career + 2`).

The offset `0x23A4` lands inside reserved `CGoodie` entries (280-284 in the legacy aligned view, and 279-284 in the true view, `0x23A4`-`0x23B7`), so it must never be used for kill edits.

**Important file-vs-memory nuance**: `CCareer__Load` validates a 16-bit version word at file offset `0x0000` and then bulk-copies the CCareer bytes from `source + 2` into memory. That makes many 32-bit fields appear **shifted by 2 bytes** in the on-disk file.

For kill counters, there are two common ways you'll see offsets discussed:
- **True dword view (authoritative)**: dwords at `0x23F6`, `0x23FA`, ... which correspond to the actual in-memory dwords; these are what `BEA.exe` operates on directly.
- **Legacy aligned view (deprecated)**: dwords at `0x23F4`, `0x23F8`, ... (2 bytes earlier). In that view, values can appear as `dword >> 16`, but patching at those offsets can overlap adjacent true dwords and silently corrupt metadata.

### Canonical Interpretation (What BEA.exe Uses)

Binary checks and unlock logic use (in-memory dwords):
- `kills_payload = (kill_dword & 0x00FFFFFF)` (24-bit integer payload)
- `meta = (kill_dword >> 24)` (top byte; for the first two counters a front-end screen offset `meta - 0x80` that Load resets to 0 when outside ±0x40)

This matches `CCareer__Load` (rewrites only the first two counters' top byte and preserves the lower 24 bits) and `CCareer__UpdateGoodieStates` (masks with `& 0x00FFFFFF` before comparing to threshold ints like 25/50/75/100, 100/200/300/400, etc).

### File Offsets (On-Disk)

| Aligned Offset (Count = `>>16`) | True Dword Offset (Payload = `&0x00FFFFFF`) | Category | Goodie Unlock Thresholds |
|---------------------------------|--------------------------------------------|----------|--------------------------|
| 0x23F4 | 0x23F6 | Aircraft | 25, 50, 75, 100 kills |
| 0x23F8 | 0x23FA | Vehicles | 100, 200, 300, 400 kills |
| 0x23FC | 0x23FE | Emplacements | 25, 50 kills (75 appears in combined unlocks) |
| 0x2400 | 0x2402 | Infantry | 40, 80, 160 kills (no 120-based goodie in this build) |
| 0x2404 | 0x2406 | Mechs | 20, 40, 80 kills (40 is used by 2 goodies) |

**Encoding (true dword view)**: `stored_value = (kills_payload) | (meta << 24)`
- Typical observed meta value is `0x80` for the first two counters (i.e., neutral bias), so 95 kills often appears as `0x8000005F` at file offset `0x23F6`.

**Why patchers preserve metadata**: the binary uses the **top byte** as persistent metadata for at least the first two counters. Patchers should preserve that byte and only update the lower 24-bit payload.

---

## Binary Evidence (BEA.exe)

- `CCareer__UpdateThingsKilled` (`0x0041c180`) updates `this + 0x23f4 + i*4` and logs totals using `value & 0xFFFFFF`.
- Per-level counts live in `g_LevelKillCounts` (`0x00672e30`) and are added into CCareer totals during `CCareer__UpdateThingsKilled`.
- Helper at `0x0041c160` returns the lower 24 bits of the dword at `this + 0x23f4 + idx*4`.
- `CCareer__UpdateGoodieStates` (`0x0041c470`) reads kill-related globals (e.g., `g_Career_mThingsKilled` at `0x00662a14` and adjacent addresses) to decide goodie unlocks.
- `CCareer__Load` (`0x00421200`) rewrites the **top byte** for career offsets `0x23f4` and `0x23f8` (file `0x23F6`/`0x23FA`): an offset `byte - 0x80` outside ±0x40 becomes 0 (`0x0042126a`-`0x00421280`), preserving the lower 24 bits.
- `CCareer__GetKillCounterTopByte_23F8` (`0x00421900`) and setters at `0x00421910` / `0x00421940` manipulate only the top byte (bias 0x80).

These imply the binary treats kill totals as **lower 24-bit values**, with the first two top bytes holding a biased front-end screen offset (out-of-range values reset to `0x80`).

**Observed save values (retail .bes):**
- Aligned view @ `0x23F4`: `0x005F0000` => 95 kills (`>> 16`)
- True dword view @ `0x23F6`: `0x8000005F` => 95 kills (`& 0x00FFFFFF`)

This reconciles the earlier "shift-16 vs 24-bit" mismatch: the aligned `>> 16` view is an artifact of the 2-byte version word shifting the CCareer dump in the file.

---

## Load-Time Clamp (Top Byte)

From `CCareer__Load` (0x00421200), the code validates and adjusts values at offsets 0x23F4 and 0x23F8:

*Decompiler listing omitted.*

The load clamp only touches the **top byte** and preserves the lower 24 bits. BEA.exe uses the masked lower 24 bits as the integer kill count directly (e.g., `0x8000005F & 0x00FFFFFF == 95`). The `>> 16` interpretation only applies to the repo's historical *aligned file view* (because it is 2 bytes earlier than the true dword boundary).

---

## The "Top Byte" Is a Persisted Screen-Position Offset

The *top byte* of the first two kill counters (CCareer offsets `0x23F4` and `0x23F8`) is not part of the kill count. It is a front-end **screen-position** offset stored in excess-128 — the stored byte `0x80` is position `0` — and its owner is `CFEPScreenPos`: RTTI type descriptor `0x00629DB0` (`.?AVCFEPScreenPos@@`), complete object locator `0x00613D10`, vtable `0x005DB858`. `Blank`, `UpdateThingsKilled`, a 19-byte helper (`0x0041C160`), `Load` and `UpdateGoodieStates` (which masks the byte off) read or write the words directly. The four accessors below are the only other route to them; their callers are the `CFEPScreenPos` page and `CFEPOptions__TransitionNotification` (`0x0051F85F`, `0x0051F86C`):

- `CCareer__GetKillCounterTopByte_23F4` (`0x004218f0`) / `CCareer__SetKillCounterTopByte_23F4` (`0x00421910`)
- `CCareer__GetKillCounterTopByte_23F8` (`0x00421900`) / `CCareer__SetKillCounterTopByte_23F8` (`0x00421940`)
- `CScreenPosition__Read` (`0x0051f470`) reads both values
- `CScreenPosition__Write` (`0x0051f490`) writes both values

**Practical consequence for patchers:** when writing kill counts (true view at `0x23F6..0x2409`), preserve the existing top byte: `new = (old & 0xFF000000) | (kills & 0x00FFFFFF)`. A byte-level diff of two saves must also report that byte as a screen-position difference rather than a kill-count difference; the retired AppCore editor's [`BesFilePatcher.GetRegionName`](https://github.com/dlprentice/Onslaught-Career-Editor/blob/4ac3c9ae32d97c1293858c49bf9cc485eb814f28/OnslaughtCareerEditor.AppCore/BesFilePatcher.cs) (Onslaught-Career-Editor `4ac3c9ae`) labelled it `ScreenPos[0]`/`ScreenPos[1]`, and the companion's [`CareerSave`](https://github.com/dlprentice/Onslaught-Career-Editor/blob/51129bab45489e5e45ee1c047cbcd8072bf4cc50/companion/OnslaughtToolkit.Godot/Careers/CareerSave.cs) never authors it.

**Important:** This is *not* the Sound/Music volume system. The real audio volumes are IEEE-754 floats in CCareer at:
- `mSoundVolume` (true view `0x248E`)
- `mMusicVolume` (true view `0x2492`)

**Established so far:** the user-facing setting is the front end's own screen-position adjustment. The callers of the four accessors sit in one contiguous block, `0x0051F470`-`0x0051FD6C`: the `CFEPScreenPos` page (vtable `0x005DB858`) and `CFEPOptions__TransitionNotification`, which snapshots the values; the adjusters at `0x0051FA20`-`0x0051FAE7` are asymmetric — the `0x23F4` word steps `±4` clamped to `[-0x3F, +0x40]`, the `0x23F8` word steps `±1` clamped to `[-0x32, +0x40]` — and each one repacks through the setter and plays a UI sample. Because `CCareer::UpdateThingsKilled` stores an unmasked 32-bit sum (`0x0041C1A9`), a count carrying out of bit 23 adds one to the stored byte, changing the stored setting by one unit; that is shipped behaviour and is correctable from the game's own options page. **Open:** which of the two words is horizontal, and whether the setting moves the picture; none of the readers above applies it to the display.

---

## Goodie Unlocks by Kill Count

The per-threshold list is not yet in this repository. [goodies-system.md](goodies-system.md#unlock-ownership) gives the index ranges and categories, and the companion's [unlock service](https://github.com/dlprentice/Onslaught-Career-Editor/blob/51129bab45489e5e45ee1c047cbcd8072bf4cc50/companion/OnslaughtToolkit.Godot/Careers/GoodieUnlockRequirementService.cs) maps the rules by goodie index and marks the indices it has not mapped.

**Summary of thresholds:**

| Category | Thresholds | Max Needed |
|----------|------------|------------|
| Infantry | 40, 80, 160 | 160 |
| Aircraft | 25, 50, 75, 100 | 100 |
| Vehicle | 100, 200, 300, 400 | 400 |
| Mech | 20, 40, 80 | 80 |
| Emplacement | 25, 50 | 50 |

---

## Implementation Notes

The retired AppCore `BesFilePatcher` ([`4ac3c9ae`](https://github.com/dlprentice/Onslaught-Career-Editor/blob/4ac3c9ae32d97c1293858c49bf9cc485eb814f28/OnslaughtCareerEditor.AppCore/BesFilePatcher.cs)) took per-category kill overrides to match specific unlock requirements. If no per-category overrides are provided, a single global value is applied to all five categories.

**Minimum effective values:**
- For all infantry unlocks: 160 kills
- For all aircraft unlocks: 100 kills
- For all vehicle unlocks: 400 kills
- For all mech unlocks: 80 kills
- For all emplacement standalone unlocks: 50 kills
- For the combined Emplacements+Vehicles goodie: 75 emplacements AND 100 vehicles (Stuart Gillam's published source, `FEPGoodies.cpp`, pinned as the [README](../README.md) describes; retail behavior appears consistent so far)

**Recommended default**: 400+ to unlock all kill-based goodies.

---

## Training Level Kill Exclusion

World 100 (Training) does not add to kill counters: `CCareer::UpdateThingsKilled` returns before it adds anything when the finished world is 100 ([`Career.cpp`, lines 538-541](https://github.com/dlprentice/Onslaught/blob/c3d8ac464259942b83fa8aa7f867c856e77b734d/Career.cpp#L538-L541), pinned source; the retail behaviour is not measured here).

---

## TK_INFANTY Typo Confirmed

The infantry kill type has a typo (missing 'R') preserved throughout the codebase: the constant is `TK_INFANTY` (index 3), and the source keeps that spelling ([`Player.h`, line 32](https://github.com/dlprentice/Onslaught/blob/c3d8ac464259942b83fa8aa7f867c856e77b734d/Player.h#L32)).
