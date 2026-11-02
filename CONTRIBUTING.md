# Contributing

Status: active — how contributions are accepted
Last updated: 2026-11-02 (public contribution steps and documentation checks added)
Summary: how to propose and check a documentation change, the licence contributions come in under, the evidence
a finding needs, and the material this repository never accepts.

## Propose a change

For a correction or new finding, open an [issue](https://github.com/dlprentice/onslaught-preservation/issues)
or a pull request in this repository. Use [`INDEX.md`](INDEX.md) to find the document that owns the subject;
keep the finding there and update its folder index or [`QUICK-REFERENCE.md`](QUICK-REFERENCE.md) if needed.
Ordinary text and link corrections need no retail files or private research workspace.

1. Fork [onslaught-preservation](https://github.com/dlprentice/onslaught-preservation), clone your fork, and create
   a branch from this repository's current `main`.
2. Make a focused edit, following the header and evidence rules below. Keep existing heading text stable when
   possible, because other documents may link to its anchors.
3. Run `git diff --check` and review `git diff`. Open each changed relative link from the document that contains
   it, check heading anchors, and check that external source links identify the intended file and revision.
   This repository contains documentation and tables only; there is no application to build or test here.
4. Commit the scoped files, push your branch to your fork, and open a pull request against this repository's
   `main`. Explain what changed, cite the supporting evidence, and list the checks you made. Preserve the
   contribution's attribution and any applicable `Co-Authored-By` trailers.

For a new measured finding, describe the specimen and its SHA-256, the setup and steps, the observed result,
and the limits of the claim in your own words. Link public evidence at an exact revision where possible.
If the underlying receipt contains material prohibited below, state that the receipt is unavailable publicly
and include only the permitted description and identifying hash; do not attach the receipt to an issue or PR.
An unavailable receipt limits what another contributor can independently verify.

## Licence: inbound = outbound

By opening a pull request you agree that your contribution is licensed under the same terms as the file it changes:
Creative Commons Attribution-ShareAlike 4.0 International ([`LICENSE`](LICENSE)), plus GPL-3.0-only for the notes in
`engine/source/` and MIT for the `formats/aya-resource-extractor-*` notes, as [`REUSE.toml`](REUSE.toml) records.
Only contribute text you wrote or may license on those terms. Credit for substantial contributions goes in
[`history/attribution.md`](history/attribution.md).

## What a finding needs

- A header: `Status:`, `Last updated:` (or `Date:`) and `Summary:` in the first lines of every new or edited
  document. A finding that quotes a retail address also names its specimen in a `Specimen:` line with the file's
  SHA-256.
- Evidence for every behaviour claim: a controlled runtime capture, a byte comparison against a named retail file,
  a focused test, or a pinned source file and line. A plausible name or a tool's opinion proves nothing. Write
  unknowns as open questions, with the cheapest check that would settle them.
- Describe what the code does in your own words. Cite function names and addresses; do not paste code.
- Relative links between documents, and no paths from your own machine.

## Never commit

- Game files of any kind: executables, DLLs, archives, levels, textures, models, audio, video, saves, or anything
  converted from them. Screenshots of the game are not accepted either.
- Decompiler output (pseudo-C, decompiled function bodies) from any tool.
- Disassembly listings with raw instruction bytes. A patch document may give the few original and patched bytes of
  the patch site itself; it does not reproduce surrounding code.
- Wholesale dumps of the game's strings or data tables.
- Debugger logs, traces or captures that contain retail bytes.
- Credentials, personal contact details or machine-specific paths.
- Links to disc images or other copies of the game.
