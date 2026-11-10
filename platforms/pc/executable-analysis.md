# Executable Analysis (BEA.exe)

Status: active reference
Last updated: 2026-11-06 (save-map names follow the decompilation, named attributions removed, function count method stated)
Summary: the retail `BEA.exe`: PE identity, hashes, size, DLL imports, Direct3D 9 and branding.
Specimen: pristine `BEA.exe.original.backup`, SHA-256 `74154bfae14ddc8ecb87a0766f5bc381c7b7f1ab334ed7a753040eda1e1e7750`.

## Executable Metadata

**File**: `BEA.exe` (Steam version)

| Property | Value |
|----------|-------|
| **MD5** | `3b456964020070efe696d2cc09464a55` |
| **SHA256** | `74154bfae14ddc8ecb87a0766f5bc381c7b7f1ab334ed7a753040eda1e1e7750` |
| **File Size** | 2,506,752 bytes (~2.4 MB) |
| **Loaded Memory** | ~6.1 MB (includes BSS/uninitialized data) |
| **Architecture** | x86 32-bit |
| **Functions** | 5,482 game functions, the count of the byte-matching decompilation ([onslaught-decomp](https://github.com/dlprentice/onslaught-decomp)). Its function table (`config/functions.tsv`, in that repository) has 6,684 rows; its correction list (`config/function-corrections.tsv`) adds 16 functions and removes one non-function block, giving 6,699. After 1,185 compiler unwind funclets and 32 import thunks, which it counts apart, are removed, 5,482 game functions remain. Earlier counts quoted in dated notes came from automatic function recovery and are not properties of the binary. |
| **Format** | PE / D3D9 |

**Notes**: CompanyName says "Lost Toys" (retail build still branded Lost Toys). The Windows retail release was published by Encore. Who did the retail Windows work is not established here. D3D9 confirms retail/Steam build (not Stuart's D3D8 internal dev build).

---

## Key DLL Dependencies

| DLL | Purpose |
|-----|---------|
| BINKW32.DLL | Bink Video (cutscenes) |
| D3D9.DLL | Direct3D 9 graphics |
| DINPUT8.DLL | DirectInput 8 controls |
| DSOUND.DLL | DirectSound audio |
| OGG/VORBIS.DLL | Ogg Vorbis codec |
| ZLIB.DLL | Compression |
| AVIFIL32.DLL | AVI handling |
| WSOCK32.DLL | Networking |

---

## Save/Load Function Map

| Address | Function | Description |
|---------|----------|-------------|
| 0x00421350 | CCareer__Save | Serializes career to buffer |
| 0x004213c0 | CCareer__SaveAndMarkInProgress | Serializes career to buffer (retail save path helper; flag behavior still being mapped) |
| 0x00421200 | CCareer__Load | Deserializes buffer to career (`flag=0`: boot/defaultoptions path, applies Sound/Music and options entries/tail globals; `flag!=0`: career `.bes` load, preserves pre-load Sound/Music and skips options entries/tail apply) |
| 0x00421430 | CCareer__SizeOfSaveGame | Calculates dynamic save size |
| 0x00420b10 | CCareer__WriteOptions | Writes `0x56` bytes of globals/options tail state |
| 0x00420d70 | CCareer__ReadOptions | Reads `0x56` bytes of globals/options tail state |
| 0x00514f80 | CPCMemoryCard__WriteSave | Generic file write wrapper (savegames folder) |
| 0x00515080 | CPCMemoryCard__ReadSave | Generic file read wrapper (savegames folder) |
| 0x00464c50 | CFEPSaveGame__CreateSave | Save-menu handler (serializes CAREER and writes `savegames\<name>.bes`) |
| 0x00461e20 | CFEPLoadGame__StartLoad | UI handler for load menu |

Names follow the decompilation, the naming authority ([onslaught-decomp](https://github.com/dlprentice/onslaught-decomp)). Older names still used in other notes for some of these addresses: `CCareer__SaveWithFlag`, `CCareer__GetSaveSize`, `OptionsTail_Write`, `OptionsTail_Read`, `PCPlatform__WriteSaveFile`, `PCPlatform__ReadSaveFile` and `CFEPLoadGame__DoLoad`.

---

## File Mode Strings

| Address | String | Usage |
|---------|--------|-------|
| 0x00629038 | "rb" | Binary read (load) |
| 0x0063316c | "wb" | Binary write (save) |
| 0x0063df7c | "savegames\\*.bes" | Save file pattern |
| 0x0063df94 | "savegames\\" | Save directory |

---

## Other Interesting Strings

**Debug string**: the executable contains one profane debug string, which this document does not quote.

**Source file debug paths**: the executable embeds the developers' build paths (`<build root>\ONSLAUGHT2\<file>.cpp`) in its assertion and allocator strings; the documents that use one quote it with its address.

**PC Version History**: Whether the original 2003 Windows release was bundled with Nvidia cards is unverified here, and no source is cited for it; the date 26/5/03 is found in the file. The Steam version is a later retail build, and whether it has a console-port lineage is unverified here. It differs from Stuart's internal PC dev snapshot, and the exact lineage between console/retail variants is not fully proven yet.
