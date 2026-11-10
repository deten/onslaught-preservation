# Walker-to-jet raw-state timing

Status: accepted measurement — its Core transition mapping is a superseded reference (see below)
Last updated: 2026-11-06 (header completed; Core mapping labelled superseded with its current constant; timing record cited)
Summary: one walker-to-jet transition, measured in two runs with the early-flight archive change, went from raw state 1 to raw state 3 in 535.359–537.249 ms; the timing record is `walker-transform-morph-timing-v1.json` in this folder.
Evidence: MEASURED — copied-runtime sampling; the runtime captures are not retained, so the JSON record is what can be checked.
Specimen: Steam `BEA.exe`, SHA-256 `74154bfae14ddc8ecb87a0766f5bc381c7b7f1ab334ed7a753040eda1e1e7750` (as recorded in the JSON specimen field)
Schema: `battleengine-walker-transform-state-timing.v2` (the file name keeps the series name `v1`)

One clean Level 100 copy delivered Transform action `0x21` to player one's
BattleEngine, entered `Morph` (`CBattleEngine::Morph`, the transform routine that switches between walker and jet; `references/Onslaught/BattleEngine.cpp`), hit the flight-disabled rejection, and remained in raw state `2`. Two fresh copies with the early-flight archive change (the retired AppCore editor's one-byte change of Level 100's flight setting from `DisableFlightMode` to `EnableFlightMode`) repeated raw states `2 → 1 → 3`.

An uninterrupted read-only sampler measured raw state `1` through raw state `3`
at **535.359–535.393 ms** and **537.208–537.249 ms**. Debugger markers proved
the input/Morph identities and detached before state `1`; they did not provide
timing. The superseded Core reference project recorded this bounded transition as 16 intervals at 30 Hz (533.333 ms); its current constant, `WalkerToJetTransitionTicks` in `rebuild/OnslaughtRebuild.Core/SimulationConstants.cs` in onslaught-godot, is 10, and the comment beside it cites the 535–537 ms observations.

The timing record is [`walker-transform-morph-timing-v1.json`](walker-transform-morph-timing-v1.json) in this folder: schema v2, the specimen hash, the setup, the control run and the two modified attempts. Its integrity record states that the runtime captures are not retained.

The old 4.67–5.17 second result did not use these identified state endpoints and
is not a transform duration. This measurement does not establish animation,
camera, jet-to-walker, energy, shield, weapon, or flight-dynamics parity.
