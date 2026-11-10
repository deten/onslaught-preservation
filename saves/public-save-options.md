# Public Save And Options Overview

Status: active — public overview of the `.bes` save and options rules
Last updated: 2026-11-06 (Summary added; editor-code scope named; true-view offsets point to save-format.md; earlier: links updated for the onslaught-preservation layout)
Summary: the fixed-size `.bes` career save and `defaultoptions.bea` options files, the rules for changing them, and the detailed layout documents to read first.

Battle Engine Aquila `.bes` saves and `defaultoptions.bea` files are fixed-size
binary buffers. Editor code in Onslaught-Career-Editor must preserve unknown bytes and operate on
copies.

## Rules

- Do not synthesize `.bes` files from scratch.
- Start from a real baseline save/options file.
- Preserve file size, padding, reserved bytes, and unknown regions unless the
  task is explicitly investigating those bytes.
- Use the true-view offsets in [save-format.md](save-format.md) (summary tables
  are in [QUICK-REFERENCE.md](../QUICK-REFERENCE.md)), not shifted aligned-view
  guesses.
- Keep public PRs free of real saves and private game data.

## Useful References

- [Save Format](save-format.md)
- [Save Structs](save-structs.md)
- [Save Ranks](save-ranks.md)
- [Save Goodies](save-goodies.md)
- [Save Kills](save-kills.md)
