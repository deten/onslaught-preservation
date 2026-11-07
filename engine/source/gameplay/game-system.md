# Game System

Status: historical source analysis (December 2025), with bounded retail corrections to the debug-key description
Last updated: 2026-11-06 (god flags, level-loss path, frame timing, state and lives names, multiplayer and FMV ranges, grade bounds, volume curve and Music.cpp citation checked against the pinned source; earlier: the debug-key table distinguished from the retail PC default bindings; research-campaign records removed)
Summary: the game loop, level lifecycle, end-level data, music and sound managers as written in Stuart's source; the
keyboard bindings below are distinguished from the selected retail PC table.
Evidence: SOURCE — the historical analysis of Stuart's source. Rechecked against the pinned source in the 2026-11-06 correction: the god-flag account, the level-loss path, the frame timing, the game-state and lives names, the multiplayer and FMV ranges, the secondary-objective bounds, the Music.cpp assignment and the master-volume curve. The debug-key table, the rest of the cheat text, the sound-channel and localisation notes and the career-integration tables are historical and were not individually rechecked.
Specimen: `<maintainer evidence>/safe-copy-bea-pristine/BEA.exe.original.backup`, SHA-256
`74154bfae14ddc8ecb87a0766f5bc381c7b7f1ab334ed7a753040eda1e1e7750`.

> Analysis from game.cpp/h, DXGame.cpp/h, PCGame.cpp/h, EndLevelData.cpp/h, Music.cpp/h, and SoundManager.cpp/h - December 2025

## Overview

The game loop system provides the core execution framework through a class hierarchy that abstracts platform-specific main loops.

---

## Class Hierarchy

```
CGame (base class - abstract interface)
    ├── CDXGame (DirectX implementation; the selected GAME under _DIRECTX)
    ├── CPCGame (alternate PC lane; excluded from the selected source lane)
    └── CPS2Game (PlayStation 2 - not in provided source)
```

**Platform Selection:**
- The source's global `GAME` is `CDXGame` under `_DIRECTX` and `CPS2Game` otherwise ([synthesis](../stuart-source-synthesis.md), the global owners table). `CPCGame` derives from `CGame`, not `CDXGame` (`references/Onslaught/PCGame.h:9` at the pin). It is an alternate lane: whether the historical build excluded `PCGame.cpp` is unproven without a project file.
- Xbox builds use `CDXGame` directly
- PS2 builds use `CPS2Game` (not provided)

---

## Key Constants

| Constant | Value | Purpose |
|----------|-------|---------|
| `MAX_UPDATE_TIME` | 1.0f/40.0f | Defined as a 40 FPS ceiling; only commented-out code uses it |
| `CLOCK_TICK` | 0.05f | 50ms per tick = 20 ticks/second |
| `MAX_PLAYERS` | 4 | Maximum simultaneous players |
| `GAME_COUNT_WHEN_LOST_OR_DRAW` | 5.0f | Delay before returning to menu on loss |
| `GAME_COUNT_WHEN_WON` | 5.0f | Delay before returning to menu on win |
| `TIMERECORDS` | 1000 | Profiler sample buffer size |

### Timing Architecture

- **Game logic updates**: one model update per 20 Hz interval (`CLOCK_TICK`, 0.05 s). `MAX_UPDATE_TIME` (1/40 s, 0.025 s) is defined, but its only uses in the pinned source are commented out (`references/Onslaught/DXGame.cpp:131-147`, `references/Onslaught/PCGame.cpp:283-299`).
- **Fixed time step events**: `CLOCK_TICK` intervals (50ms = 20 FPS physics)
- **Rendering**: repeats between model updates until the current interval ends (`mFrameLength`, 0.05 s by default; `references/Onslaught/game.cpp:2223`).

---

## Game States (EGameState)

| State | Value | Description |
|-------|-------|-------------|
| `GAME_STATE_NOT_RUNNING` | 0 | Game not initialized |
| `GAME_STATE_PRE_RUNNING` | 1 | Loading/initializing |
| `GAME_STATE_PANNING` | 2 | Intro camera pan |
| `GAME_STATE_PLAYING` | 3 | Normal gameplay |
| `GAME_STATE_LEVEL_LOST` | 4 | Single-player: mission failed |
| `GAME_STATE_LEVEL_WON` | 5 | Single-player: mission completed |
| `GAME_STATE_PLAYER_1_WON` | 6 | Multiplayer: Player 1 victory |
| `GAME_STATE_PLAYER_2_WON` | 7 | Multiplayer: Player 2 victory |
| `GAME_STATE_GAME_DRAWN` | 8 | Multiplayer: draw |
| `GAME_STATE_QUIT` | 9 | Exiting level |

---

## Quit Types (EQuitType)

| Type | Value | Description |
|------|-------|-------------|
| `QT_NONE` | 0 | No quit requested |
| `QT_QUIT_TO_FRONTEND` | 1 | Return to main menu |
| `QT_QUIT_TO_SYSTEM` | 2 | Exit to OS (PC only) |
| `QT_LOAD_ERROR` | 3 | Asset loading failure |
| `QT_RESTART_LEVEL` | 4 | Restart current mission |
| `QT_QUIT_TIMEOUT` | 5 | Inactivity timeout |
| `QT_USER_QUIT_TO_FRONTEND` | 6 | User-initiated menu exit |
| `QT_USER_QUIT_TO_TITLE_SCREEN` | 7 | User-initiated title exit |

---

## Main Loop Architecture

The game uses a nested loop structure:

```
RunLevel()
    ├── Init()                 // One-time level initialization
    ├── InitRestartLoop()      // Per-attempt initialization
    ├── LoadResources()        // Asset loading
    └── RestartLoopRunLevel()  // Main gameplay
            └── MainLoop()     // Frame-by-frame execution
                    ├── ProcessInput()
                    ├── Update(deltaTime)
                    └── Render()
```

**Key Methods:**
- `RunLevel()` - Entry point, handles level lifecycle
- `InitRestartLoop()` - Reset state for level restart
- `MainLoop()` - Core frame processing with timing management

---

## Career Integration Points

The game loop directly interfaces with the career system at several critical points:

| Location | Code | Purpose |
|----------|------|---------|
| Line 319 | `mSlots = CAREER.GetSlots()` | Load tech slot state |
| Line 737 | `CAREER.GetControllerConfigurationNum(i)` | Per-player controller config |
| Lines 1139-1143 | FMV goodie unlocking | Auto-unlock goodies when watching cutscenes |

### Tech Slot Loading (Line 319)

```cpp
mSlots = CAREER.GetSlots();
// Used to check mission branching (SLOT_500_ROCKET, SLOT_500_SUB)
```

### Controller Configuration (Line 737)

```cpp
for (int i = 0; i < MAX_PLAYERS; i++) {
    int config = CAREER.GetControllerConfigurationNum(i);
    // Apply per-player control scheme
}
```

---

## B4K42 Cheat Mechanism

**Scope note:** This section documents the **internal/source build** cheat strings. The Steam PC port uses different codes (`MALLOY`, `TURKEY`, `Maladim`). Later Steam-build testing confirmed that `Maladim` exposes a `God OFF` / `God ON` toggle under `Controller Options` and changes real combat-damage behavior; see [`god-mode.md`](../../../mechanics/cheats/god-mode.md) for the retail-specific runtime posture.

**Source/internal finding:** The B4K42 god mode cheat check uses `strstr()` against the **save game NAME** at runtime (substring match):

```cpp
// From FEPSaveGame.cpp - IsCheatActive()
if (strstr(saveName, "B4K42") != NULL) {
    // God mode enabled - "MyB4K42Save" would also work
}
```

### Why B4K42 Cheat Works

- The game checks if the save NAME **contains** "B4K42" (substring match, not exact)
- If found, god mode is enabled for Player 1
- In the internal build, god mode is runtime state (`CPlayer::mIsGod`) and the career also has a per-player `CCareer::mIsGod[2]` array in the source.
- **Steam build (checked against [god-mode.md](../../../mechanics/cheats/god-mode.md)):** the per-player god flags, `CCareer::mIsGod[0]` and `mIsGod[1]`, are saved in the career at the player 1 and player 2 rows of [save-format.md](../../../saves/save-format.md). The earlier 'invert-Y reuse' and 'not persisted' claims were wrong. The name `g_bGodModeEnabled` in older notes is unresolved and is not relied on here.

---

## FMV Goodie System

FMV (Full Motion Video) goodies are automatically unlocked when watching cutscenes:

| Goodie ID Range | Content |
|-----------------|---------|
| 200-231 | The 32 default/movie descriptor rows, which the FMV grid cells select (valid descriptor indices run 0-231) |
| 232 | Not a valid descriptor: the grid emits it for its last FMV cell, one past the 232-entry arrays |

### Auto-Unlock Logic (Lines 1139-1143)

When an FMV finishes, the source sets the goodie id to the FMV number plus 200, with FMV 33 mapped to id 232. If that goodie is neither new nor old, it sets the goodie to `GS_NEW` and increments the pending extra-goodies counter (`references/Onslaught/game.cpp:1132-1143` at the pin). The grid's id mapping is in [the synthesis](../stuart-source-synthesis.md).

So FMV-unlocked goodies are set by the FMV path rather than by kill counts or grades.

---

## Debug Keys

This historical internal-source table includes controller mappings such as the two camera bindings in
[PCController.cpp](https://github.com/dlprentice/Onslaught/blob/c3d8ac464259942b83fa8aa7f867c856e77b734d/PCController.cpp)
and game-level shortcuts; the other rows have not been individually rechecked here.
The [retail-matching PC defaults](https://github.com/dlprentice/onslaught-decomp/blob/6c434449dcc595a3ab7a99c4154d666e49ba8695/src/PCController.cpp#L20-L68)
instead map `F` to free-camera toggle and `A` to free-camera strafe left; default `Space` transforms the Battle
Engine when gameplay permits. The retail [free-camera action](https://github.com/dlprentice/onslaught-decomp/blob/6c434449dcc595a3ab7a99c4154d666e49ba8695/src/game.cpp#L2671-L2697)
checks cheat 4, normally enabled by `Aurore` in the career name. The internal rows below are not a general retail key list.

| Key | Function | Notes |
|-----|----------|-------|
| `~` (tilde) | Toggle console | Debug console overlay |
| `T` | MAP_WHO | Unknown debug display |
| `C` | Toggle cockpit view | First-person camera |
| `P` | Toggle profiler | Performance overlay |
| `R` | Restart level | Immediate level restart |
| `ESC` | Quit to menu | Standard quit |
| `V` | Toggle god mode | Via `BUTTON_TOGGLE_GOD_MODE` |
| `U` | Win level | Debug instant-win |
| `I` | Lose level | Debug instant-lose |
| `7` | Complete all objectives | Debug skip |
| `A` / `F` | Free camera | Two key bindings |
| `8` / `9` | Debug squad cycling | Forward/backward |
| `4` / `5` | Debug unit cycling | Forward/backward |
| `Space` | Skip cutscene | During cinematics |
| `O` | Pause | Keyboard alternative |

**Scope:** The remaining rows are historical descriptions that were not individually rechecked; do not infer their retail availability from this table.

---

## God Mode Toggle

God mode is toggled via `BUTTON_TOGGLE_GOD_MODE` (virtual button 0):

```cpp
// When V key pressed in dev mode:
if (BUTTON_TOGGLE_GOD_MODE pressed) {
    for (int n = 0; n < num_players; n++) {
        BOOL current = mPlayer[n]->IsGod();
        mPlayer[n]->SetIsGod(!current);
    }
}
```

**Important:** This is the runtime toggle path. In the internal/source build it calls `SetIsGod`, which also writes career state. In Steam/retail, the per-player god flags are saved in the career (the player 1 and player 2 rows of [save-format.md](../../../saves/save-format.md)), and current evidence points to a split runtime toggle path that still produces real combat-damage protection when `Maladim` is active.

---

## Multiplayer Detection

Levels are identified as multiplayer based on their world number:

| Range | Type |
|-------|------|
| 100-800 | Single-player campaign, including the final level 800 |
| 850-899 | Multiplayer levels covered by `CGame::IsMultiplayer` |
| 900-905 | Special race/frontend modes; outside the range `CGame::IsMultiplayer` checks |

### Detection Logic

```cpp
BOOL IsMultiplayerLevel(int worldNumber) {
    return (worldNumber >= 850 && worldNumber <= 899);
}
```

In retail, `CGame__IsMultiplayer` at `0x004725d0` reads the current-level field at `CGame+0x2a0` and checks the exact `850..899` range. This explains why multiplayer modes (and per-player state) are handled differently from single-player, while leaving race/frontend mode behavior as separate evidence.

---

## Player Death and Respawn System

The respawn system handles player death differently based on game mode:

### DeclarePlayerDead(int number)

Called when a player's battle engine is destroyed:

```cpp
void CGame::DeclarePlayerDead(int number) {
    CBattleEngine* engine = mPlayer[number-1]->GetBattleEngine();
    FVector pos = engine->GetPos();

    // Disable free camera if active
    if (mFreeCameraOn[number])
        ToggleFreeCameraOff(number);

    // Create death view camera
    SetCurrentCamera(number-1, new CViewPointCamera(pos, 0.5f, 0.0f, 6.0f, 3.0f));

    // Stop message box sounds in single-player
    if (!GAME.IsMultiplayer() && mMessageBox)
        mMessageBox->StopCurrentPlayingSound();

    switch (WORLD.GetType()) {
        case CWorld::kSinglePlayer:
            if (engine->IsInWater())
                DeclareLevelLost(GAME_OVER_WATER, TRUE);
            else
                DeclareLevelLost(GAME_OVER_DEATH, TRUE);
            break;

        case CWorld::kCooperativeMultiplayer:
        case CWorld::kVersusMultiplayer:
            // Schedule respawn after 5 seconds
            EVENT_MANAGER.AddEvent(5.0f, number == 1 ? RESPAWN_PLAYER_1 : RESPAWN_PLAYER_2, this, START_OF_FRAME);
            break;
    }
}
```

### Level Lost Reasons

| Constant | Trigger |
|----------|---------|
| `GAME_OVER_DEATH` | Player health reached zero |
| `GAME_OVER_WATER` | Player fell into water |
| `P1_OUT_OF_LIVES` | Player 1 has no lives remaining (co-op) |
| `P2_OUT_OF_LIVES` | Player 2 has no lives remaining (co-op) |

### RespawnPlayer(SINT inNumber)

Handles player respawn in multiplayer modes:

```cpp
void CGame::RespawnPlayer(SINT inNumber) {
    // Check lives in different modes (inNumber 0 is player 1, 1 is player 2)
    switch (WORLD.GetType()) {
        case CWorld::kCooperativeMultiplayer:
            if (inNumber == 0 && mPlayer1Lives == 0) {
                DeclareLevelLost(P1_OUT_OF_LIVES, TRUE);
                return;
            }
            if (inNumber == 1 && mPlayer2Lives == 0) {
                DeclareLevelLost(P2_OUT_OF_LIVES, TRUE);
                return;
            }
            break;

        case CWorld::kVersusMultiplayer:
            if (inNumber == 0 && mPlayer1Lives == 0) {
                MPDeclarePlayerWon(2);  // Other player wins
                return;
            }
            if (inNumber == 1 && mPlayer2Lives == 0) {
                MPDeclarePlayerWon(1);  // Other player wins
                return;
            }
            break;
    }

    // Decrement lives
    if (inNumber == 0 && mPlayer1Lives > 0)
        mPlayer1Lives--;
    if (inNumber == 1 && mPlayer2Lives > 0)
        mPlayer2Lives--;

    // Find spawn point (CSpawnPoint preferred, CStart fallback)
    // ...spawn logic...

    // If spawn fails, restore the life and retry after 1 second
    if (!spawned) {
        if (inNumber == 0) {
            mPlayer1Lives++;
            EVENT_MANAGER.AddEvent(1.0f, RESPAWN_PLAYER_1, this, START_OF_FRAME);
        } else {
            mPlayer2Lives++;
            EVENT_MANAGER.AddEvent(1.0f, RESPAWN_PLAYER_2, this, START_OF_FRAME);
        }
    }
}
```

### Player Lives

- **Default lives per player**: 2 (`mPlayer1Lives = 2`, `mPlayer2Lives = 2`)
- Lives decrement on each death/respawn
- When lives reach 0:
  - **Co-op**: Triggers `DeclareLevelLost()` with `P1_OUT_OF_LIVES` or `P2_OUT_OF_LIVES`
  - **Versus**: Triggers `MPDeclarePlayerWon()` for the surviving player

### Spawn Point Types

| Class | Priority | Purpose |
|-------|----------|---------|
| `CSpawnPoint` | Primary | Level-designed respawn locations |
| `CStart` | Fallback | Initial spawn locations (if no CSpawnPoint available) |

Spawn points are player-specific (`GetPlayerNumber()` returns 1 or 2) and can be marked as unavailable.

---

## World Types (CWorld)

| Type | Constant | Description |
|------|----------|-------------|
| Single Player | `kSinglePlayer` | Standard campaign missions |
| Co-op Multiplayer | `kCooperativeMultiplayer` | 2 players vs AI |
| Versus Multiplayer | `kVersusMultiplayer` | 1v1 PvP combat |

---

## PC Game Implementation (PCGame.cpp/h)

### Frame Timing System

PCGame.cpp is the alternate lane (see Platform Selection). The main loop that drives it is `CGame::MainLoop` in the base class (`references/Onslaught/game.cpp:2076`).

From the main loop (`references/Onslaught/game.cpp:2130-2223` at the pin) and the constants above:

| Feature | Value |
|---------|-------|
| Model update | One per 20 Hz interval (`CLOCK_TICK` = 0.05 s; `mFrameLength` defaults to 0.05 s, `references/Onslaught/game.cpp:309`) |
| Frame cap | `MAX_UPDATE_TIME` (1/40 s) is defined; its only uses (`references/Onslaught/DXGame.cpp:131-147` and `references/Onslaught/PCGame.cpp:283-299`) are commented out |
| Render | Repeats within each model interval until the interval ends |
| Physics tick | 20 FPS fixed (CLOCK_TICK = 0.05s) |

### Screen Capture System

F8 key captures screenshots:
- Saves to `grabs\scrNNNN.tga` (sequential numbering)
- TGA format (uncompressed)
- Full resolution (game's current resolution)

### Debug Features (DrawGameStuff)

Debug overlay showing:
- FPS counter
- Memory usage
- Profiler data
- Event queue statistics

### Time Recording System (DEBUG_TIMERECORDS)

Performance profiling via `TIME_THIS_FUNCTION()` macro:
- 1000 sample buffer (`TIMERECORDS`)
- Tracks function execution time
- Displayed via profiler overlay (P key)

---

## EndLevelData Runtime System (EndLevelData.cpp/h)

### CRITICAL: Runtime-Only Structure

**In the source/internal path, and consistent with current Steam tracing, EndLevelData is treated as runtime-only (not directly serialized to `.bes`).** It is a struct that:
1. Gets populated when a level ends (win or lose)
2. Is consumed by `CCareer::Update()` to update the persistent career
3. Is discarded after career update completes

This invalidates earlier hypotheses that the historically labeled "mystery region" at 0x24CC-0x2714 contained "cached EndLevelData". That region contains remaining CCareer members (per-player settings, configuration).

### Struct Members

| Member | Type | Saved? | Destination |
|--------|------|--------|-------------|
| `mBaseThingsLeft` | BOOL[288] | ✅ Yes | `CCareerNode::mBaseThingsExists` (compressed to 36 bytes) |
| `mPrimaryObjectives` | CMissionObjective[10] | ❌ No | Runtime display only |
| `mSecondaryObjectives` | CMissionObjective[10] | ❌ No | Runtime display only |
| `mWorldFinished` | int | ❌ No | Used for routing (100, 211, 500, etc.) |
| `mFinalState` | EGameState | ❌ No | WIN/LOSE determination |
| `mRanking` | float (0.0-1.0) | ✅ Yes* | `CCareerNode::mRanking` (only if better) |
| `mScore` | SINT | ❌ No | Displayed on results screen |
| `mTimeTaken` | float (seconds) | ❌ No | Displayed on results screen |
| `mThingsKilled` | int[5] | ✅ Yes** | **Added to** `CCareer::mKilledThings` |
| `mSlots` | int[32] | ✅ Yes*** | **Copied to** `CCareer::mSlots` |

\* Rankings only update if the new ranking is BETTER than the existing saved ranking.
\*\* Kill counts are **cumulative** - added to existing totals, not replaced.
\*\*\* Tech slots are **overwritten** - the level's slot state replaces career slots entirely.

### Ranking Calculation System

The ranking (grade) is calculated based on score relative to level-specific thresholds:

In outline (a paraphrase of `CGame::FillOutEndLevelData` in game.cpp at the pin, not a quotation): a score at or above the level's S-grade
score gives a ranking of 1.0; a score between the D-grade and S-grade scores is interpolated linearly between
them; anything lower gives 0.0. A time multiplier first scales the score, and the ranking is clamped to 0.0-1.0.

**CRITICAL: Secondary Objectives Gate Ranking**

For levels that have secondary objectives, `CGame::FillOutEndLevelData` (`references/Onslaught/game.cpp:1028-1042` at the pin) raises a ranking below 0.4 to 0.4 when `IsAllSecondaryObjectivesComplete()` is true, and caps it at 0.6 when it is false. The source's own comments call 0.4 a C and 0.6 a B. `IsAllSecondaryObjectivesComplete()` (`references/Onslaught/EndLevelData.cpp:21-49`) returns false when any secondary objective has failed.

| Secondary Status | Minimum Rank | Maximum Rank | Notes |
|------------------|--------------|--------------|-------|
| None failed (function returns true) | 0.4 (C) | 1.0 | Full ranking range available |
| Any failed (function returns false) | 0.0 | 0.6 (B) | **The ranking cannot exceed 0.6** |

### Data Flow: EndLevelData → Career

```
Level Ends
    │
    ▼
EndLevelData populated
    │
    ▼
CCareer::Update() called
    │
    ├─── Check mFinalState
    │         │
    │         ├─── GAME_STATE_LEVEL_WON: Process updates
    │         │
    │         └─── GAME_STATE_LEVEL_LOST: goodie state only (no progression updates)
    │
    ├─── Update mBaseThingsExists (base objectives)
    │
    ├─── Update mRanking (only if better)
    │
    ├─── Add mThingsKilled to mKilledThings (cumulative)
    │
    ├─── Copy mSlots to mSlots (overwrite)
    │
    ├─── Update node links (unlock next missions)
    │
    └─── Save career to .bes file
```

**Key Behaviors:**

1. **GAME_STATE_LEVEL_WON only**: progression updates trigger only on level victory. A loss runs only the goodie-state recalculation (`references/Onslaught/Career.cpp:382-385` at the pin), with no change to slots, kill totals, rankings or node completion. Whether a loss changes the saved goodie bytes is not measured here.

2. **Rankings only improve**: If current save has A-rank (0.8) and you finish with B-rank (0.6), the save retains A-rank. Rankings never decrease.

3. **Kill counts are cumulative**: Every kill adds to your total. Replaying missions continues to add to kill counts (for goodie unlocks).

4. **Slots are overwritten**: Unlike kills, tech slots are replaced entirely. This is used for branching (SLOT_500_ROCKET vs SLOT_500_SUB).

### Confirmation: Reserved/Unmapped Region is NOT EndLevelData

The historically labeled mystery region at file offsets 0x24CC-0x2714 was previously hypothesized to contain cached EndLevelData. For the Steam build analyzed here, current evidence argues against that theory:

- EndLevelData is never serialized to disk
- EndLevelData is a transient runtime structure
- The reserved/unmapped region contains remaining CCareer members:
  - Per-player settings (invert Y-axis, vibration, controller config)
  - Unknown padding/reserved fields
  - Possibly platform-specific data

---

## Music System (Music.cpp/h)

Music playback with playlist support.

### Playback Types (EMusicPlayType)

| Type | Behavior |
|------|----------|
| `MPT_SINGLE` | Play once |
| `MPT_LINEAR` | Play in order |
| `MPT_RANDOM` | Random shuffle |
| `MPT_SELECTION` | Player-selected |

### Music Selections (EMusicSelection)

| Selection | Context |
|-----------|---------|
| `MUS_FRONTEND` | Menu music |
| `MUS_CREDITS` | Credits roll |
| `MUS_TUTORIAL` | Tutorial levels |
| `MUS_STEALTH` | Stealth missions |
| `MUS_INGAME` | General gameplay |

### Volume Persistence

In the Steam build analyzed for this repo, `CAREER.GetMusicVolume()` maps to retail `.bes` offset **0x2492** (true-view mapping); re-verify on other retail variants.

### Platform Audio Formats

- PC: MP3/WAV
- Xbox: WMA

### Bug Found

`CMusic::PlayFromList` (`references/Onslaught/Music.cpp:448` at the pin) assigns `mPlayType` inside its condition, so the null-selection branch forces random mode. That reads as a typo for a comparison, which is an inference from the source text. The released build behaves the same way ([cmusic-shared-semantics.md](../../audio/cmusic-shared-semantics.md)).

---

## Sound System (SoundManager.cpp/h, pcsoundmanager.cpp/h)

The sound system handles 3D positional audio, volume management, and platform-specific audio output.

### Class Hierarchy

| Class | Platform | Backend |
|-------|----------|---------|
| `CSoundManager` | Base class | Abstract interface |
| `CPCSoundManager` | PC | DirectSound |
| `CPS2SoundManager` | PlayStation 2 | Sony SPU |
| `CXBOXSoundManager` | Xbox | DirectSound/Xbox Audio |

### Key Constants

| Constant | Value | Purpose |
|----------|-------|---------|
| `FAR_SOUND` | 50 | Max audible distance (units) |
| `NEAR_SOUND` | 3 | Full volume distance (units) |
| `MAX_SOUND_EVENTS` | 256 | Max concurrent sound events |
| `MAX_SOUND_BUFFERS` | 32 | PC DirectSound buffers |
| Default sample rate | 44100 Hz | All platforms |
| Default format | 16-bit mono | Standard audio format |

### Volume Categories

Multiple volume channels with independent control:

| Member | Purpose | Default |
|--------|---------|---------|
| `mMasterVolume` | Global volume multiplier | 1.0 |
| `mGameSoundsMasterVolume` | In-game effects | 1.0 |
| `mMenuSoundsMasterVolume` | Menu/UI sounds | 1.0 |
| `mRadioMessageVolume` | Radio voice comms | PS2: 0.70, PC: 0.42 |
| `mHUDMessageVolume` | HUD feedback | 0.45 |

**Note:** the retained source sets radio message volume to 0.70 on PS2 and 0.42 on the other targets (`references/Onslaught/SoundManager.cpp:98-104` at the pin).

### Sound Tracking Types (ESoundTrackingType)

Controls how 3D sounds follow their sources:

| Type | Behavior | Use Case |
|------|----------|----------|
| `ST_NOTRACKING` | Fixed position | Camera-relative sounds |
| `ST_SETINITIALPOSITION` | Set once, don't follow | Explosion at location |
| `ST_FOLLOWANDDIE` | Follow source, stop when source dies | Engine sounds |
| `ST_FOLLOWDONTDIE` | Follow source, continue after death | Ambient loops |

### Career Integration

Volume settings persisted in .bes save files:

| Setting | Career Offset | File Offset | Format |
|---------|---------------|-------------|--------|
| Sound Volume | `mSoundVolume` | 0x248E | Raw IEEE-754 float |
| Music Volume | `mMusicVolume` | 0x2492 | Raw IEEE-754 float |

Note: Retail file offsets use the true view mapping `file_off = 0x0002 + career_off`. In the legacy 4-byte-aligned view, these floats can look scrambled due to 2-byte misalignment.

### PC Volume Curve (CPCSoundManager)

The retained source converts the master volume in `CSoundManager::SetMasterVolume` (`references/Onslaught/SoundManager.cpp:156-173` at the pin). On non-PS2 targets it maps a linear 0-1 setting `v` to `1 - tan((1 - v) * 1.38) / tan(1.38)`; the career keeps the linear setting. On PS2 the value is used unchanged. The music volume (`CMusic::SetVolume`, `references/Onslaught/Music.cpp:557-572`) uses the same expression, scaled to 0-127.

- The mapping is concave: its output lies above the linear line across (0, 1), about 0.43 at a 0.1 setting and 0.84 at 0.5.
- The pin has no `CPCSoundManager::SetVolume()`. The PC channel volume comes from the attenuated event volume in `pcsoundmanager.cpp`.
- The released retail and demo builds store the supplied float directly and do not apply this conversion ([csoundmanager-shared-semantics.md](../../audio/csoundmanager-shared-semantics.md)), so the curve is source-only.

### Localization System

Language-dependent voice samples loaded from subdirectories:

```
sounds\
  english\     - English voice samples
  french\      - French voice samples
  german\      - German voice samples
  spanish\     - Spanish voice samples
  italian\     - Italian voice samples
```

Sample names are language-agnostic; the system prepends the language path.

### Debug Console Commands

| Command/Variable | Purpose |
|------------------|---------|
| `playsound <name>` | Play named sound sample |
| `snd_visible` | Toggle sound debug visualization |
| `snd_frozen` | Freeze sound system for debugging |

---

## Special Pan Camera Levels

8 levels have the player starting "on something" requiring a different intro camera:
- 221, 222, 231, 232, 331, 332, 523, 524

These levels use a panning camera intro instead of the standard fixed camera, as the player starts on a moving platform (carrier ship).

---

## Files Analyzed

| File | Purpose |
|------|---------|
| `game.cpp` | Main game loop, career integration, B4K42 cheat, player death/respawn |
| `game.h` | Game states, quit types, constants |
| `DXGame.cpp` | DirectX game loop implementation |
| `DXGame.h` | CDXGame class definition |
| `PCGame.cpp` | Alternate PC game lane, not the selected class (its 40 FPS frame cap is commented out; F8 screenshots) |
| `PCGame.h` | CPCGame class definition |
| `EndLevelData.cpp` | Runtime level completion data (NOT saved) |
| `EndLevelData.h` | EndLevelData struct definition |
| `Music.cpp` | Music playback system |
| `Music.h` | Music enums, playback types |
| `SoundManager.cpp` | Base sound manager implementation |
| `SoundManager.h` | Sound system interface, tracking types |
| `pcsoundmanager.cpp` | PC DirectSound implementation |
| `pcsoundmanager.h` | PC-specific sound declarations |

---

## See Also

- [career-system.md](career-system.md) - Career integration details
- [battle-system.md](battle-system.md) - Battle Engine mechanics, god mode implementation
- [../frontend/fep-systems.md](../frontend/fep-systems.md) - Cheat code implementation
- [../../../mechanics/cheats/god-mode.md](../../../mechanics/cheats/god-mode.md) - God mode investigation
