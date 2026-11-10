# CCareer__Save

Status: mixed — current byte serialization rechecked; older source/callsite receipts retain their limits
Last updated: 2026-10-22 (research-campaign records and decompiler-style code removed)
Summary: serializes career, active bindings and runtime tail; tail construction also refreshes the packed display-mode global.
Source File: `references/Onslaught/Career.cpp` (partial-source comparison) | Binary: pristine `BEA.exe.original.backup`; September 19 byte/control evidence is linked below.
Specimen: pristine `BEA.exe.original.backup`, SHA-256 `74154bfae14ddc8ecb87a0766f5bc381c7b7f1ab334ed7a753040eda1e1e7750`.

> Address: 0x00421350 | Source: `references/Onslaught/Career.cpp`

## Status
- **Verified vs Source:** Behavior-level verified (2026-02-23): matches XBOX-style struct dump flow, with retail PC options-entries + tail serialization and caller-side file-write behavior verified.

## Purpose
Serializes career to a buffer for saving as a `.bes` file: writes the 16-bit version stamp, dumps a fixed-size CCareer region, appends the options entries block, then appends a fixed 0x56-byte tail snapshot (`CCareer__WriteOptions`).

In the Steam retail build observed by this repo, `.bes` saves are fixed-size **10,004 bytes** and the options entries block is **16 entries** (`0x20 * 16` bytes).

## Signature
A `CCareer` member (receiver in ECX) taking a destination buffer pointer; it returns nothing and pops
4 bytes (one argument) on return.

## On-Disk Layout (Steam Build Fixed 10,004 Bytes)
This matches the struct-dump save strategy in Stuart’s XBOX path, with additional PC-port “options + tail” appended. While the implementation counts “active” options entries, the Steam build observed here always produces a fixed 16-entry block.

| Offset | Size | Content |
|--------|------|---------|
| 0x0000 | 2 | Version word (`current_version_stamp()`) |
| 0x0002 | 0x24BC | Fixed CCareer dump (CCareer starts at file+0x0002; includes the pending extra-Goodies dword at 0x0002..0x0005, so nodes begin at 0x0006) |
| 0x24BE | 0x200 | Options entries (16 entries × `0x20` bytes; populated from global table at `0x008892d8`) |
| 0x26BE | 0x56 | Tail globals/options snapshot (`CCareer__WriteOptions` @ `0x00420b10`) |

Internal size formula is `0x2514 + 0x20*N`. Retail Steam saves observed here have `N=16` => `0x2714` (10,004 bytes), and this project treats `0x2714` as invariant (do not resize).

## Notes

- Writes version as a 16-bit word then bulk-copies `0x24BC` bytes into `dest + 2` (`rep movsd` with `ECX=0x92F`).
- Iterates the options definition table at `0x008892d8` until the sentinel (entry+4 == -1) and writes only “active” entries (byte[0] != 0). In the Steam retail build observed here, this yields 16 entries.
- Calls `CCareer__WriteOptions` (`0x00420b10`) with the pointer to the end of the options block to append the final 0x56 bytes.
- Does NOT set `mCareerInProgress` (see CCareer__SaveAndMarkInProgress).
- Companion to CCareer__Load
- September 19 [original-code controls](../save-options-startup-compatibility.md#september-19-independent-recheck) compare both serializer variants' complete 10,004-byte outputs and guards in 17 scenarios. The entire tail writer and both display helpers execute with controlled device records; no actual display or file is used.

## Side Effects and Boundaries (Verified)

- `CCareer__Save` itself is a **buffer serializer**:
  - writes the serialized output to caller-provided `dest`,
  - reads from `this` (`CAREER`) and the options descriptor table at `0x008892d8`,
  - appends options-tail bytes via `CCareer__WriteOptions`,
  - performs no direct disk I/O.

The earlier “writes only to dest” claim omitted a callee side effect:
`CCareer__WriteOptions` calls `00420d10` with receiver `0066061c`, refreshing that
global packed display-mode key before serializing it. This was reproduced by
the September 19 controls, including after reader mode lookup cleared the key's
low 16 bits. Ordinary Save does not set the live career-progress word;
CCareer__SaveAndMarkInProgress does.

- File-write side effects occur in callers, not inside `CCareer__Save`:
  - `CFEPOptions__SaveDefaultOptions` (`0x0051f500`) allocates `size = CCareer__SizeOfSaveGame()`, calls `CCareer__Save`, then writes `defaultoptions.bea` via `fopen/fwrite/fclose`.
  - `CPlatform__Flip` (`0x004d2580`) on `0x0082b5b0 == 2` allocates/save-serializes and calls `CFEPOptions__WriteDefaultOptionsFile(dest, size)`, then frees.
  - `0x004d06e0` (PauseMenu path) serializes with `CCareer__Save`, conditionally writes selected save via `CPCMemoryCard__WriteSave`, then writes `defaultoptions.bea` via `CFEPOptions__WriteDefaultOptionsFile`.
  - `CFEPMain__Process` (`0x00462640`) calls `CCareer__Save` at `0x00462893`, then (same branch) calls `CFEPOptions__WriteDefaultOptionsFile` at `0x004628df` and conditionally writes slot data via `CPCMemoryCard__WriteSave`.

## Related Functions
- [CCareer__Load](CCareer__Load.md) - Reverse operation
- [CCareer__SaveAndMarkInProgress](CCareer__SaveWithFlag.md) - Save with progress flag
- [CCareer__SizeOfSaveGame](CCareer__GetSaveSize.md) - Returns buffer size needed
