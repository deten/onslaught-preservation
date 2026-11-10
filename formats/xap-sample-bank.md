# XAP sample banks: layout and decode (PC)

Status: active format contract — complete PC bank layout and sample decode
Date: 2026-10-16
Last updated: 2026-11-06 (function names in the decompilation's spelling and cited to onslaught-decomp, decompilation linked as public, reference build described as maintainer-only, run location and GAME_FOLDER defined, IMA reference named; earlier: links updated for the onslaught-preservation layout)
Verdict: all five installed banks parse to their exact final byte, 164 samples each (820 in all), and every sample
decodes. The decode is IMA ADPCM, four bits per sample, high nibble first, from a zero prediction and step index,
to 16-bit mono PCM at 44,100 Hz. It is byte-identical, for all 820 samples, to a maintainer-only reference build of the
game's matched decoder, which is not published; a reader can compare the per-sample digests of `tools/xap_bank_walk.py --pcm-digests` with another IMA ADPCM decoder.
Evidence: SOURCE + MEASURED. The layout and decode were verified against the matched loader and decoder in the
byte-matching decompilation ([onslaught-decomp](https://github.com/dlprentice/onslaught-decomp)): `CPCSoundManager__LoadSampleBank`, `0x00517d00`;
`CPCSoundManager__LoadSampleFromBuffer`, `0x005172a0`; `CPCSoundManager__DecodeADPCM`, `0x00517fa0`. The banks
were walked and decoded with `tools/xap_bank_walk.py` in Onslaught-Career-Editor.
Specimen: retail addresses cite `BEA.exe.original.backup`, SHA-256
`74154bfae14ddc8ecb87a0766f5bc381c7b7f1ab334ed7a753040eda1e1e7750`; the corpus is the installed
`data/sounds/sounds_<language>_pc.xap`, five files, manifest digest (SHA-256 over sorted `name sha256` lines)
`93a0e1feb16d8c1b9a9ebaa29b5f7aa7f6039f0850da37aa881dce6cdf22ba7d`.

The game's sound effects and spoken HUD lines live in one bank per language: English, French, German, Italian and
Spanish. Music and mission voices are Ogg Vorbis files; [ogg-audio.md](ogg-audio.md) covers those, the bank
census and the `sounds.sfx` catalog that names the bank samples.

## Which bank is read

The game reads one bank at startup, chosen by the language setting: `data\sounds\sounds_<language>_pc.xap`. It
reads it again when the sound options change. A missing bank is logged and skipped, and the game runs without
those sounds.

## Layout

All integers are little-endian.

| Offset | Size | Meaning |
| ---: | ---: | --- |
| `0` | 4 | magic, the ASCII bytes `PCXP` (the loader reads and does not check it) |
| `4` | 4 | `count`: the number of samples, a signed 32-bit integer |
| `8` | … | `count` samples, back to back |

Each sample:

| Offset | Size | Meaning |
| ---: | ---: | --- |
| `+0` | 4 | the ASCII bytes `PSMP` (read and not checked) |
| `+4` | 64 | the sample's name, NUL-terminated, for example `Atmospheres\N_A_building_on` |
| `+68` | 4 | `pcm_bytes`: the size of the decoded sample in bytes, unsigned (16-bit samples, so twice the sample count) |
| `+72` | `ceil(pcm_bytes / 4)` bytes, computed as `(pcm_bytes + 3) / 4` in integer division | the ADPCM data: four bits per decoded sample |

The bank ends where the last sample's ADPCM data ends; all five installed banks have no trailing bytes. Every
installed `pcm_bytes` is even.

The name is how the game finds the sample. The `sounds.sfx` catalog refers to samples by these names, and when the
bank is read again after the sound options change, each sample replaces the loaded sample of the same name. Each installed bank holds 164
distinct names in the same order. The census in [ogg-audio.md](ogg-audio.md) has which samples are identical across
languages.

## Decoding a sample

The ADPCM data is IMA ADPCM with no header and no blocks: one running state for the whole sample.

- **Start.** The predicted value is 0 and the step index is 0.
- **Order.** Each byte holds two codes: the **high** four bits first, then the low four bits. That is the
  opposite of the IMA ADPCM used in WAV files. The number of codes decoded is `pcm_bytes / 2`.
- **Each code, in order:**
  1. Take the step size for the current step index from the standard IMA step table (89 entries, 7 to 32767).
  2. Move the step index by the standard IMA index adjustment for the code (−1 for codes 0–3 and 8–11; +2, +4,
     +6, +8 for codes 4–7 and 12–15), and keep it within 0–88.
  3. The difference is `step / 8`, plus `step` if the code's bit 2 is set, `step / 2` if bit 1 is set, and
     `step / 4` if bit 0 is set; each division drops the fraction.
  4. If the code's bit 3 is set, subtract the difference from the predicted value; otherwise add it. Keep the
     predicted value within −32768 to 32767.
  5. That value is the next 16-bit output sample. The step for the next code comes from the index as updated in
     step 2.

The decode follows the IMA reference algorithm for 4-bit ADPCM to 16-bit linear decompression (section 6.2 of the IMA Digital Audio Focus and Technical Working Groups' [*Recommended Practices for Enhancing Digital Audio Compatibility in Multimedia Systems*, Revision 3.00, 21 October 1992](https://www.cs.columbia.edu/~hgs/audio/dvi/IMA_ADPCM.pdf)); the game's tables are the reference tables.

## Playback format

Decoded samples are 16-bit signed mono PCM at **44,100 Hz**. That is the game's highest sound-quality setting,
where the loader uses the decoded samples unchanged. At the lower settings it converts them to 22,050 Hz 16-bit or
11,025 Hz 8-bit for playback. The banks themselves hold only the 44,100 Hz stream.

## Corpus

| Bank | Samples | Decoded audio |
| --- | ---: | ---: |
| `sounds_english_pc.xap` | 164 | 244.2 s |
| `sounds_french_pc.xap` | 164 | 251.9 s |
| `sounds_german_pc.xap` | 164 | 254.9 s |
| `sounds_italian_pc.xap` | 164 | 255.3 s |
| `sounds_spanish_pc.xap` | 164 | 253.4 s |

From a checkout of Onslaught-Career-Editor at [`51129bab`](https://github.com/dlprentice/Onslaught-Career-Editor/tree/51129bab45489e5e45ee1c047cbcd8072bf4cc50), run `python tools/xap_bank_walk.py --game GAME_FOLDER`, where `GAME_FOLDER` is the installed game folder that contains `data/sounds/`. It prints these counts and the manifest digest. With
`--pcm-digests` it prints a SHA-256 of every decoded sample, so another decoder can be compared sample by sample.
The companion decodes the banks the same way and saves samples as WAV files.

## Open questions

- **Sample naming.** Whether the per-sample `PSMP` tag ever differs, and what other values the tools that wrote
  the banks used there. The loader never reads it. Falsifier: a bank with another value that still plays.
- **Lower-quality conversion.** The exact conversion to 22,050 and 11,025 Hz. Falsifier: the matched conversion
  routine against a decoded sample.
