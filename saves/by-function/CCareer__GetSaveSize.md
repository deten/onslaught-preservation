# CCareer__SizeOfSaveGame

Status: active bounded retail size contract
Last updated: 2026-10-22 (research-campaign records and decompiler-style code removed)
Summary: counts initialized active binding rows; this is allocation sizing, not loader validation.
Source File: `references/Onslaught/Career.cpp` (partial-source comparison) | Binary: pristine `BEA.exe.original.backup`; September 19 byte/control evidence is linked below.
Specimen: pristine `BEA.exe.original.backup`, SHA-256 `74154bfae14ddc8ecb87a0766f5bc381c7b7f1ab334ed7a753040eda1e1e7750`.

> Address: 0x00421430 | Source: `references/Onslaught/Career.cpp`

## Status
- **Verified vs Source:** No (source has `SizeOfSaveGame()` but the PC port adds an options entries block + fixed tail; Steam retail observed as 16 entries)

## Purpose
Calculate the save buffer size. Base is fixed, plus `0x20` bytes for each enabled options entry, plus a fixed `0x56`-byte tail. The ordinary initialized retail table yields **10,004 bytes** (`N=16`); changed active/sentinel metadata can change the result.

## Signature
A `CCareer` member with no arguments that returns the save size in bytes as an `int` (`CCareer::GetSaveSize()` in the source).

## Return Value
- Internally returns `0x2514 + 0x20 * N`, where `N` is the count of “active” option entries in the global table at `0x008892d8` (byte[0] != 0), up to the sentinel (entry+4 == -1).
- Typical retail Steam saves observed here have `N=16` => `0x2714` (10,004 bytes).

## Breakdown (As Implemented)
| Component | Size |
|-----------|------|
| Version word | 2 |
| Fixed CCareer dump | 0x24BC |
| Options entries | 0x20 * N |
| Tail snapshot (`CCareer__WriteOptions`) | 0x56 |
| **Total** | **0x2514 + 0x20 * N** |

## Notes
- Used by save system to allocate buffers
- September 19: the original single-binding initializer and this complete 52-byte body execute in the [startup controls](../save-options-startup-compatibility.md#september-19-independent-recheck), producing 10,004 bytes. Raw PE zero-filled storage is not an initialized table. Traversal uses 32-byte stride, not a linked list.
- Saves of the observed Steam retail build are `0x2714` bytes. A tool that rewrites one keeps that size and every byte of the options and tail regions.

## Related Functions
- [CCareer__Save](CCareer__Save.md) - Uses this size
- [CCareer__Load](CCareer__Load.md) - Has no size parameter; callers check their read count before invoking it
