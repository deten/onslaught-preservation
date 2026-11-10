# Game Folder Analysis

Status: historical snapshot — the file layout of the Battle Engine Aquila Steam release as surveyed in December 2025; the format documents linked below hold the current measurements
Last updated: 2026-11-06 (header added; level, video and resource counts, the level 800 category, the BEA.exe size, the speculative notes and the Tool cell corrected to the format documents)
Summary: the files, folders and sizes of the Steam release as read in December 2025, with pointers to the level and format documents that now measure each family.

> Historical snapshot (December 2025) of the Battle Engine Aquila Steam release file layout
> Generated: December 2025

## Overview

| Property | Value |
|----------|-------|
| **Total Size** | 680 MB |
| **Platform** | Windows PC (Steam) |
| **Steam App ID** | 1346400 |
| **Game Executable** | BEA.exe (2.4 MB, PE32 Intel 80386) |

---

## Root Directory Files

### Executables & Libraries

| File | Size | Type | Purpose |
|------|------|------|---------|
| `BEA.exe` | 2.4 MB | PE32 GUI | Main game executable |
| `Message.exe` | 36 KB | PE32 GUI | Error dialog utility |
| `binkw32.dll` | 367 KB | PE32 DLL | Bink video codec (RAD Game Tools) |
| `ogg.dll` | 48 KB | PE32 DLL | Ogg container format |
| `vorbis.dll` | 952 KB | PE32 DLL | Vorbis audio codec |
| `zlib.dll` | 62 KB | PE32 DLL | Compression library |

### Configuration & Data Files

| File | Size | Purpose |
|------|------|---------|
| `defaultoptions.bea` | 10,004 bytes | **Global options baseline/snapshot** - Same retail envelope as .bes saves |
| `cardid.txt` | 18 KB | GPU compatibility database (by Jan Svarovsky) |
| `setuphistory.txt` | 8.7 KB | Runtime log (D3D init, sound setup) |
| `OnslaughtException.txt` | 0 bytes | Crash log (empty) |
| `INSTALL.LOG` | 502 KB | Ghost Installer manifest |
| `steam_appid.txt` | 9 bytes | "1346400" |

### Key Discovery: defaultoptions.bea

`defaultoptions.bea` is the global-options baseline/snapshot (same 10,004-byte retail envelope as `.bes`), not just a one-time template.

- Boot loads it with `CCareer::Load(flag=0)` to apply options entries + tail.
- Frontend load/save flows can overwrite it from loaded/current buffers.
- Career `.bes` loads (`flag=1`) skip immediate options/tail apply.
- Version word is `0x4BD1` (16-bit; the first 4 bytes often *look* like `0x00004BD1` in 4-byte aligned hex dumps).

---

## Directory Structure

```
game/
├── BEA.exe                     # Main executable
├── defaultoptions.bea          # Global options baseline/snapshot (.bea; same 10,004-byte envelope as .bes)
├── Manuals/                    # HTML documentation (2.8 MB)
│   ├── English/
│   ├── French/
│   ├── German/
│   ├── Italian/
│   ├── Spanish/
│   └── Images/
├── data/                       # Game assets (673 MB)
│   ├── MissionScripts/         # Level scripts (4.1 MB)
│   ├── Music/                  # Soundtrack (38 MB)
│   ├── ParticleSets/           # VFX definitions (712 KB)
│   ├── language/               # String tables (1.7 MB)
│   ├── resources/              # Models, textures (149 MB)
│   ├── sounds/                 # Audio files (144 MB)
│   ├── textures/               # Loose textures (776 KB)
│   └── video/                  # FMVs and cutscenes (337 MB)
└── savegames/                  # User save directory (empty)
```

---

## data/MissionScripts/ (4.1 MB)

### Level Numbering System

**95 numbered level folders** (`level000` to `level958`; five are spelt `Level`), grouped by number. [Level inventory](../levels/level-inventory.md) says which load: 66 are playable and 29 cannot load.

| Range | Folders | What the folders are |
|-------|---------|----------------------|
| 000-030 | 21 | 15 empty shells and 6 early prototypes (003, 004, 010, 020, 021, 022); none loads |
| 100-110 | 3 | campaign levels 100 and 110; 105 is an empty shell |
| 200-232 | 8 | campaign episode 2, and 201, the playable demo's version of 200 |
| 300-332 | 8 | campaign episode 3; 301 is an empty shell |
| 400-432 | 7 | campaign episode 4 |
| 500-524 | 8 | campaign episode 5; 520 is an empty shell |
| 530 | 1 | an unfinished level; it cannot load |
| 600-622 | 5 | campaign episode 6 |
| 700-742 | 7 | campaign episode 7 |
| 800 | 1 | campaign episode 8 |
| 850-866 | 17 | two-player levels (co-operative, skirmish and versus); 856 and 858 are on no menu |
| 888 | 1 | a flagship duel test; it cannot load |
| 900-905 | 6 | 900 is an empty shell; 901-905 are the race levels |
| 956, 958 | 2 | copies of 856's and 858's scripts; they cannot load |

### Level Directory Contents

Each level folder typically contains:

```
level###/
├── LevelScript.msl       # Main mission script
├── English.txt           # English mission text
├── Global.txt            # Language-neutral text
├── text.stf              # String table file
└── [additional .msl]     # Support scripts
```

See [MSL scripting](../levels/scripting/msl-scripting.md) for the retained language reference and
[MissionScript / IScript static contract](../levels/scripting/missionscript-iscript-static-contract.md)
for binary-side identities. Per-level scripts, text, message, speaker, and
object-reference queries are run directly against the local source corpus when
needed; generated inventory mirrors are not tracked.

### MSL Scripting Language

Mission scripts use a custom `.msl` format. Key functions observed:

```msl
// Level completion
LevelWon()
LevelLost()

// Objective system
ObjectiveComplete(id)
ObjectiveFailed(id)

// Special scripts found:
hack.msl                  # In level741, level742 - hacking gameplay?
LapMonitor.msl            # In level901 - race/checkpoint logic
Level500script.msl        # Special script for branching mission
```

### Special Files

| File | Purpose |
|------|---------|
| `onsldef.msl` | Master script definitions |
| `text/english.txt` | Global English strings |
| `text/global.txt` | Language-neutral strings |
| `text/text.stf` | Compiled string table |
| `text/textlist.h` | String ID header from the text converter, with 16 IDs ([localization text](localization-text.md)) |

---

## data/resources/ (149 MB)

### AYA Resource Format

Proprietary archive format, extractable with AYAResourceExtractor.

| Pattern | Count | Purpose |
|---------|-------|---------|
| `###_res_PC.aya` | 66 | Level-specific resources ([LVLR archive](lvlr-archive.md)) |
| `goodie_##_res_PC.aya` | 232 | Gallery item resources |
| `base_res_PC.aya` | 1 | Base game resources |
| `Frontend_res_PC.aya` | 1 | Menu resources |
| `Loading_res_PC.aya` | 1 | Loading screen resources |

### Subdirectories

| Directory | Count | Contents |
|-----------|-------|----------|
| `dxtntextures/` | 800 | DXT-compressed textures |
| `meshes/` | 213 | Model files (.msh.aya) |
| `textures/` | 47 | UI elements, fonts, impostors |

### Goodie Resource Files

232 files (`goodie_00` through `goodie_231`) - matches our documentation:
- 300 save file slots, 233 displayable goodies in retail (indices 0-232)
- Remaining slots (233-299) are reserved in save storage
- Slot 232 maps to FMV cutscene 33 (displayable without a `goodie_232_res_PC.aya` resource)

---

## data/sounds/ (144 MB)

### Audio Archives

| File | Size | Contents |
|------|------|----------|
| `sounds.sfx` | 14 KB | Sound effect definitions |
| `sounds_english_pc.xap` | 5.4 MB | English voice pack |
| `sounds_french_pc.xap` | 5.6 MB | French voice pack |
| `sounds_german_pc.xap` | 5.6 MB | German voice pack |
| `sounds_italian_pc.xap` | 5.6 MB | Italian voice pack |
| `sounds_spanish_pc.xap` | 5.6 MB | Spanish voice pack |

### XAP Format

The supported PC archive begins with `PCXP`, followed by a record count and
fixed `PSMP` records containing a 64-byte name, decoded byte count, and raw
high-nibble-first IMA-ADPCM payload. The rebuild materializer ([onslaught-godot `rebuild/tools/materialize_retail_assets.py`](https://github.com/dlprentice/onslaught-godot/blob/f44e31e6ff2c80dda1078fb2ab2a59bee66ecb29/rebuild/tools/materialize_retail_assets.py) at `f44e31e`, checked 2026-11-06) decodes 41 exact records from this English bank (44 table entries, each checked against its WAV SHA-256), for the frontend, Level 100 and Aquila sound-effect outputs. It reads no other bank; other banks and broader format semantics remain unverified.

### MessageBox Voice Files

`sounds/english/MessageBox/` contains **619 .ogg files**:

Naming pattern: `{level}_{event}.ogg`
- `110_enemy_engaged.ogg`
- `200_objective_complete.ogg`
- etc.

---

## data/video/ (337 MB)

### Video Format

All videos use `.vid` extension (Bink video format).

### Root Videos

| File | Size | Purpose |
|------|------|---------|
| `OpeningFMV.vid` | 20.3 MB | Intro cinematic |
| `UsTheMovie.vid` | 5.2 MB | Credits/outro |
| `LTLogo.vid` | 1.8 MB | Lost Toys logo |
| `FEBack128.vid` | 1.4 MB | Menu background |
| `TWIMTBP_GefFX_640x480_Audio.vid` | 1.6 MB | NVIDIA logo |
| `gill_m_on_a_fork.vid` | 2.9 MB | Goodie 75's gallery video (see below) |

### Briefings (28 files)

Pattern: `PC_###_exact.vid` (1.4-4 MB each)

Mission briefing videos for campaign levels.

### Cutscenes (32 files)

Pattern: `##.vid` (1-32 MB each)

Story cutscenes numbered 01 to 31 and 33; there is no 32 ([bink-video.md](bink-video.md)).

---

## data/Music/ (38 MB)

10 Ogg Vorbis tracks by **Richard Jacques**:

| File | Size |
|------|------|
| BEA_01(Master).ogg | 4.4 MB |
| BEA_02(Master).ogg | 2.8 MB |
| BEA_03(Master).ogg | 4.4 MB |
| BEA_04(Master).ogg | 4.6 MB |
| BEA_05(Master).ogg | 4.4 MB |
| BEA_06(Master).ogg | 4.6 MB |
| BEA_07(Master).ogg | 2.8 MB |
| BEA_08(Master).ogg | 2.9 MB |
| BEA_09(Master).ogg | 3.1 MB |
| BEA_10(Master).ogg | 4.8 MB |

---

## data/language/ (1.7 MB)

Localization string tables (binary .dat format):

| File | Size |
|------|------|
| american.dat | 280 KB |
| english.dat | 280 KB |
| french.dat | 300 KB |
| german.dat | 309 KB |
| italian.dat | 303 KB |
| spanish.dat | 295 KB |

---

## data/ParticleSets/ (712 KB)

| File | Size | Purpose |
|------|------|---------|
| Frontend.par | 28 KB | Menu particle effects |
| MainSet.par | 685 KB | In-game particle effects |
| ModelViewer.par | 3.5 KB | Model viewer effects |

---

## data/ Root Files

| File | Size | Purpose |
|------|------|---------|
| `Dial.raw` | 8 KB | HUD dial graphics? |
| `battle engine configurations.dat` | 1.5 KB | Battle engine parameters |
| `default physics.dat` | 172 KB | Physics configuration |
| `worldheaders.dat` | 4.8 KB | World/level header definitions |

---

## cardid.txt - GPU Compatibility Database

Credit: **Jan Svarovsky** (originally from StarTopia at Mucky Foot)

Comprehensive GPU compatibility database with per-card and per-driver tweaks:

**Supported Vendors:**
- SiS
- ATI (Radeon, Rage series)
- 3Dlabs
- 3dfx (Voodoo series)
- Matrox
- NVIDIA (Riva, GeForce series)
- PowerVR
- S3
- Intel

Contains rendering workarounds and capability flags for era-specific graphics issues.

---

## RE Implications

### Save System Confirmation

1. `defaultoptions.bea` is consistent with the BES envelope described in [saves/](../saves/README.md)
2. New games copy this template and modify it
3. Same 10,004 byte format, same version stamp

### Level Numbering for Node Mapping

Each of the 43 campaign nodes stores its level number as `mWorldNumber` (for example 100, 110 and 200), and the level folder carries that same number. This section once guessed node IDs from folder numbers; the measured mapping is in the [career graph](../saves/career-graph.md) and the [save structures](../saves/save-structs.md).

### Mission 500 Branching

`Level500script.msl` is in the level 500 folder, beside `Submarine.msl` and `rocketbase.msl` ([MSL scripting](../levels/scripting/msl-scripting.md)). The career sends level 500 on to 511 or 512 by save slot: slot 61 (`SLOT_500_ROCKET`) for the rocket path and slot 62 (`SLOT_500_SUB`) for the submarine path ([level inventory](../levels/level-inventory.md); [MSL commands](../levels/scripting/msl-commands.md)). The script that tests the slot is not identified here.

### hack.msl Scripts

`hack.msl` is in the level 741 folder, where [MSL scripting](../levels/scripting/msl-scripting.md) lists it as a door-unlock trigger. This snapshot also listed it in level 742, which is not checked here. Open question: does level 742's `hack.msl` do more than unlock a door? Cheapest falsifier: read that script in the shipped level 742 folder.

### Gallery video `gill_m_on_a_fork.vid`

Goodie 75 plays this video from the gallery ([goodie-resources.md](goodie-resources.md)), so it is a gallery item rather than a stray file. Whether it is cut content is open. Open question: is it played from anywhere other than Goodie 75? Cheapest falsifier: search the shipped scripts and tables for its name.

---

## File Format Summary

| Extension | Format | Tool |
|-----------|--------|------|
| `.bes` / `.bea` | Shared 10,004-byte CCareer envelope (`.bes` career saves; `defaultoptions.bea` global options baseline/snapshot) | [Onslaught Companion](https://github.com/dlprentice/Onslaught-Career-Editor) (the AppCore patcher is retired) |
| `.aya` | Resource archive | AYAResourceExtractor |
| `.vid` | Bink video | RAD Video Tools |
| `.ogg` | Vorbis audio | Standard players |
| `.xap` | PCXP/PSMP sound bank; bounded IMA-ADPCM decode | [onslaught-godot `rebuild/tools/materialize_retail_assets.py`](https://github.com/dlprentice/onslaught-godot/blob/f44e31e6ff2c80dda1078fb2ab2a59bee66ecb29/rebuild/tools/materialize_retail_assets.py) at `f44e31e` |
| `.msl` | Mission script | Text-based, readable |
| `.stf` | String table | Binary |
| `.dat` | Various binary data | Per-file analysis needed |
| `.par` | Particle definitions | Unknown |
| `.sfx` | Sound definitions | Unknown |

---

*Snapshot of December 2025, from automated analysis of the Steam folder.*
