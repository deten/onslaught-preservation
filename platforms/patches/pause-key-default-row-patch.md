# Pause-key default-row patch

Status: experimental copied-profile patch with one bounded pause/resume observation.
Last updated: 2026-11-06 (function name from the public naming table; terms glossed; specimen and evidence lines added; earlier: research-campaign records removed)
Summary: one immediate in the default single-binding table changes the default pause key; one bounded observation.
Evidence: MEASURED — one bounded copied-profile session, with the copied patch-site byte read and the scoped input and pause dispatch it observed.
Specimen: pristine `BEA.exe.original.backup`, SHA-256 `74154bfae14ddc8ecb87a0766f5bc381c7b7f1ab334ed7a753040eda1e1e7750`.

`pause_o_scan_initializer_experiment` changes the retail default single-binding initializer for `BUTTON_PAUSE` in a copied executable.

## Byte contract

| Item | Value |
| --- | --- |
| Retail function | `0x00514210 CONTROL_BINDINGS__init`, the default single-binding table initializer |
| Runtime table row | `0x00889718`, button `0x38` |
| Patch VA / file offset | `0x005144CD` / `0x1144CD` |
| Original / patched byte | `01` / `18` |

The patch site is one of five immediate pushes (`0`, `1`, `8`, `0x38`, `0`) that
set up the row before `MOV ECX,0x00889718` and the call that writes it.

The patch changes only the `PUSH 1` immediate payload to DirectInput scan-code candidate `0x18` (`O`), consistent with the pinned source mapping hypothesis.

## Bounded observation

An exact-PID copied-profile session (every observation tied to the one process ID the run launched) observed:

- the copied byte at `0x005144CD` as `0x18`;
- live mapping row `34` with `entryId=56`, `pushType=8`, and `keyArg=0x18`;
- an ordered scoped `O` query and button-`56` dispatch;
- `CGame__Pause` and pause-menu initialization in the first input window;
- pause-menu resume and `CGame__UnPause` after `ENTER` in the second window.

The session used the clean-backup-derived copied executable (`BEA.exe.original.backup`, SHA-256 `74154bfae14ddc8ecb87a0766f5bc381c7b7f1ab334ed7a753040eda1e1e7750`) with only `resolution_gate` (the resolution-gate row), `force_windowed` (the windowed-startup row), and this experiment. Free-camera flags remained off. A separate second-`O` attempt did not prove normal-gameplay unpause, so the supported statement is specifically `O` to open and `ENTER` to resume in the observed context.

## Boundary

The patch does not modify `defaultoptions.bea`, saves, configurable options rows, the installed executable, or the clean backup. The persisted options model covers 16 configurable UI rows and does not contain `BUTTON_PAUSE`; this experiment must not be folded into that format.

It does not prove broad pause/menu safety, every controller configuration, long-session behavior, online behavior, rendering parity, or rebuild parity. The row remains custom-only, outside default presets, and restricted to app-owned copied profiles.
