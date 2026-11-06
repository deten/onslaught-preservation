## What changed and why

<!-- One subject per pull request. Name the documents you edited. -->

## Evidence

<!-- For a new or changed claim: the capture, the byte comparison against a named file with its SHA-256, the focused test,
     or the pinned source file and line. If the receipt holds material CONTRIBUTING.md excludes, say it is not public and
     give only its description and hash. -->

## Checks

There is no hosted CI. List the exact commands you ran and what they printed.

- [ ] `git diff --check` is clean and I read the whole `git diff`
- [ ] `python3 tools/check_docs.py --changed` reports no problem (links, anchors, headers, machine paths)
- [ ] A finding that quotes a retail address also has `Evidence:` and a `Specimen:` line with the file's SHA-256

## Boundaries

- [ ] I added no game files or anything converted from them, no decompiler output, no disassembly with raw instruction
      bytes, no wholesale string or data dumps, no screenshots of the game
- [ ] I added no credentials, personal contact details, machine-local paths or links to private repositories
- [ ] I contribute my changes under the licence of the files they change (see CONTRIBUTING.md), and I have the right to
      do so
- [ ] I did not add workflow files or hosted CI

Checks I could not run, and why:
