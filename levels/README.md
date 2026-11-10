# Levels

Status: active — folder index
Last updated: 2026-11-06 (index summaries completed; four had been cut mid-word; function-name forms explained; earlier: titles in sentence case)
Summary: The level inventory, how level 100 and world 110 are built, and the mission-script language.

The documents here name functions in two forms. The decompilation's C++ source writes them with scope (`CWorld::Load`),
and its function table in [onslaught-decomp](https://github.com/dlprentice/onslaught-decomp) writes the same functions
with a double underscore (`CWorld__Load`). Either form names one function; the
[front page](../README.md) explains where the names come from.

## Folders

| Folder | Covers |
| --- | --- |
| [scripting/](scripting/README.md) | The MSL language, its command registry, the IScript interface and the script VM's data types |

## Documents

| Document | Summary |
| --- | --- |
| [Level inventory: the 95 MissionScripts folders](level-inventory.md) | which of the 95 numbered `data/MissionScripts` folders are playable levels, how the retail game reaches each one, and what the 29 folders that cannot load contain. |
| [Level 100 construction order: shared draws and queued events](level100-construction-order.md) | the order in which Level 100's construction consumes the shared gameplay random stream and queues events, from the base-world pines to the last level-world row, and what each event's first delivery draws. |
| [Level 100 final drone wave, Help Player turrets and abort](level100-final-drone-wave.md) | the abort after one kill is a designed retail branch, but retail gives the player two helps the rebuild lacks: four friendly turrets that come online after the first poll below 80 % health, and the jet Missile Pod. Activated turrets can see the enemy drones (original-code control, 16/16 cases). Every base-world Building and Cannon owns an AI that draws shared RNG from level start, and all four turrets run a fire-control refresh that draws once per event 4001 from construction onward (original-code control, 21/21 cases). |
| [World 110 construction order, transition and first frames](world-110-construction-order.md) | how a Level 100 win leads to World 110, the order in which World 110's construction consumes the shared gameplay stream and queues events, what the first flush delivers and draws, and when the player gains control. |
| [World 110 serialized initial-object seed admission](world-110-initial-constructor-seeds.md) | The 40 serialized initial-object rows of world 110 and the constructor inputs they carry. |
| [World 110 authored player-start admission](world-110-player-start-admission.md) | World 110's single authored player-1 start and its construction. |
