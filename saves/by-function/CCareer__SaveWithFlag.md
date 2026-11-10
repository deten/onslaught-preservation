# CCareer__SaveAndMarkInProgress

Status: active bounded serializer contract; earlier source/callsite receipts remain dated evidence
Last updated: 2026-11-06 (Goodie 228 offset wording corrected; uncited patcher sentence removed; heading uses the decompilation's name)
Summary: sets the live progress word before serialization; Load does not clear that word.
Source File: `references/Onslaught/Career.cpp` (partial-source comparison) | Binary: pristine `BEA.exe.original.backup`; September 19 byte/control evidence is linked below.
Specimen: pristine `BEA.exe.original.backup`, SHA-256 `74154bfae14ddc8ecb87a0766f5bc381c7b7f1ab334ed7a753040eda1e1e7750`.

> Address: 0x004213c0 | Source: `references/Onslaught/Career.cpp`

## Status
- **Verified vs Source:** Behavior-level verified (2026-02-23): retail implementation sets `mCareerInProgress = TRUE` then executes the same save body inline.

## Purpose
Save with `mCareerInProgress` set. In retail Steam, this is a dedicated routine that sets the progress flag and then performs the full save serialization body directly.

## Signature
A `CCareer` member (receiver in ECX) taking a destination buffer pointer; it returns nothing and pops
4 bytes (one argument) on return.

## Notes
- Sets `mCareerInProgress` to `1` in-memory (`this+0x2488 = 0x00000001`), which appears as `0x00010000` in the repo’s historical aligned file view.
- Serializes the same fields as `CCareer__Save` (version word + fixed CCareer dump + variable options entries + `CCareer__WriteOptions`) inlined in this function body.
- Deep-pass verified callsite (2026-02-23): `0x00465045` in `CFEPSaveGame__CreateSave` calls `CCareer__SaveAndMarkInProgress`.
- No `CALL CCareer__Save` occurs inside `CCareer__SaveAndMarkInProgress`; parity is by duplicated logic, not wrapper call-through.
- September 19 [original-code controls](../save-options-startup-compatibility.md#september-19-independent-recheck) compare its complete output and guards alongside ordinary Save, with the complete original tail writer. It changes live `+2488` to one before copying. The two calls are authored controls after startup, not proof that startup saves automatically.
- September 20 [native reload controls](../save-options-startup-compatibility.md#original-save-reload-after-reinitialization) add real-fixture derivatives whose progress word starts at zero. Plain Save preserves zero; this variant writes one into live state and its output before that buffer reaches the second original Load. Setting the flag only on the second save changes exactly file byte `0x248a`. Cases whose source flag was already one provide only the separate idempotence check.
- WARNING: In the legacy aligned view, Goodie 228 sits at `0x22D4`, two bytes before its true offset, and is not the progress flag. The [UpdateGoodieStates note](CCareer__UpdateGoodieStates.md) gives the true offset.

## Related Functions
- [CCareer__Save](CCareer__Save.md) - Core save function
- [CCareer__Load](CCareer__Load.md) - Copies the flag; the separate boot-time Blank clears it
