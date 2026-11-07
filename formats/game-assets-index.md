# Game Assets And Mission Data

Status: active index
Last updated: 2026-11-06 (the installed file count stated with its source and the walker record's differing count; the historical entry's counts marked superseded; earlier: retired AppCore/WinUI tools in the past tense)
Summary: where the asset-format, extraction and mission-data documents are, and which of them are historical.

These documents describe formats and relationships observed from user-supplied
game data and the pinned AYA reference extractor. Current source and app
packages do not bundle retail asset payloads; see
[`../history/attribution.md`](../history/attribution.md) for the
project's attribution and distribution boundary.

The specimen-bounded narrative census of the installation is `../installed-corpus-census.md` (archived research record, not published). The measured count is 5,464 source files in the mirror index census ([README.md](README.md)); the integrity block of the [walker timing record](../mechanics/battleengine/walker-transform-morph-timing-v1.json) counts 5,515 installed files hashed, and the public tree does not explain the 51-file difference. The narrative census is a dated synthesis, not a replacement for the installed bytes or the narrower format findings below.

## Asset formats and extraction

- [Historical game folder structure](game-folder-analysis.md) (December 2025; historical, so its counts are superseded by the [level inventory](../levels/level-inventory.md) and the format contracts listed here)
- [AYA asset format](aya-asset-format.md)
- [AYA resource tag contract](aya-resource-tag-family-static-contract.md)
- Guarded extraction pipeline (archived research record, not published)
- [Modding reference](../platforms/command-line/modding-reference.md)

The reusable extractor and catalog tools are documented in
`tools/README.md` in Onslaught-Career-Editor. Generated catalogs use bundle-root-
relative paths and remain local; the retired WinUI Asset Library read an existing
catalog and did not extract the installed game in place.

## Mission and script references

- [Level inventory](../levels/level-inventory.md): which of the 95 level folders are playable, how the game reaches each,
  and what the others hold
- [MSL scripting](../levels/scripting/msl-scripting.md)
- [MissionScript / IScript static contract](../levels/scripting/missionscript-iscript-static-contract.md)

The MSL reference retains language conventions and representative examples.
The level inventory is a checked map of which levels exist and load, not a mirror of their files.
Generated per-level inventories and count tables are intentionally not tracked;
readers with the game can query or regenerate them when a reconstruction task needs
them. Static identities do not establish runtime source selection, command
effects, renderer fidelity, or rebuild parity.
