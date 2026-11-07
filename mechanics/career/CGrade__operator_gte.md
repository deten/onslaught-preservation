# CGrade__operator_gte

Status: active function note
Last updated: 2026-11-06 (Specimen line added, letter order stated as rank, the source comparison described in words and not quoted; earlier: removed private-evidence references)
Summary: `CGrade::operator >=` at `0x00420ac0`: S beats every grade, otherwise letters rank A (best) through E.
Specimen: `<maintainer evidence>/safe-copy-bea-pristine/BEA.exe.original.backup`, SHA-256 `74154bfae14ddc8ecb87a0766f5bc381c7b7f1ab334ed7a753040eda1e1e7750`.

> Address: `0x00420ac0`
>
> Source: `references/Onslaught/Career.h` line 35 (`CGrade::operator >=`)

## Status
- **Verified vs Source:** Yes

## Signature

A member function on the left-hand grade that takes the right-hand grade letter as a `char` and returns a boolean.

## Behavior (source-parity)
Equivalent to Stuart's inline operator. An `S` on the left returns TRUE. Otherwise an `S` on the right returns FALSE. Otherwise it returns TRUE when the left letter is the same as the right letter or comes earlier in the alphabet, so `A` is at least `B` and `B` is not at least `A`.

Interpretation:
- Treats `S` as strictly highest.
- Otherwise grade letters are compared lexicographically (`A` better than `B`, etc.).
- Used as "left grade is at least as good as right grade."

## Notes
- Called many times inside `CCareer__UpdateGoodieStates` to evaluate grade-gated goodies.

## Related
- CGrade__ctor_char (in the decompilation notes, not yet published)
- [CCareer__UpdateGoodieStates](../../saves/by-function/CCareer__UpdateGoodieStates.md)
