# Rank floats quick reference

Status: active — quick reference for rank float and bit-pattern values
Last updated: 2026-11-06 (A to D floats no longer labelled verified; grade code block replaced by a sentence; NONE caveat tied to grade-system.md; header moved below the title)
Summary: Rank float and bit-pattern lookup.

## True View Encoding

In the retail/Steam build, rank is stored as raw IEEE-754 float bits in `CCareerNode.mRanking` at `node + 0x3C` (true dword view). Older docs that described a “split-float” were looking at a legacy 4-byte-aligned view of the same bytes.

## Rank Values

| Rank | Float | IEEE-754 Bits |
|------|-------|--------------|
| S | 1.0 | 0x3F800000 |
| A | 0.8 | 0x3F4CCCCD |
| B | 0.6 | 0x3F19999A |
| C | 0.35 | 0x3EB33333 |
| D | 0.15 | 0x3E19999A |
| E | 0.0 | 0x00000000 |
| NONE | -1.0 | 0xBF800000 |

The bit patterns are the binary32 encodings of the listed values. S, E and NONE follow the converter's boundaries and the -1.0 that `CCareerNode::Blank` writes (`references/Onslaught/Career.cpp:1178` and `:110` at [`c3d8ac4`](https://github.com/dlprentice/Onslaught/tree/c3d8ac464259942b83fa8aa7f867c856e77b734d)). The A to D floats are not in that source file, and no capture here establishes them; each lies inside its grade's range under Threshold Ranges.

## Grade Calculation (Source: Career.cpp:1178)

An exact 1.0 gives S, zero or below gives E, and otherwise the letter comes from `floor(f*4)`, giving D, C, B and A for 0 to 3 (`references/Onslaught/Career.cpp:1178` at [`c3d8ac4`](https://github.com/dlprentice/Onslaught/tree/c3d8ac464259942b83fa8aa7f867c856e77b734d)). [grade-system.md](grade-system.md) states the thresholds.

## Threshold Ranges

| Grade | Float Range | floor(f*4) |
|-------|-------------|------------|
| S | f == 1.0 | Exact match |
| A | 0.75 <= f < 1.0 | 3 |
| B | 0.50 <= f < 0.75 | 2 |
| C | 0.25 <= f < 0.50 | 1 |
| D | 0.0 < f < 0.25 | 0 |
| E | f <= 0.0 | Explicit |

## NONE Caveat

The uncompleted sentinel (-1.0) converts to E in the converter. How the level-select map draws it is a static reading only ([grade-system.md](grade-system.md#none--10), the NONE section); no capture has confirmed it.

## Node Rank Patching

For synthetic S-rank: set `mRanking = 0x3F800000` and ensure the node is marked complete (`mComplete=1`).
