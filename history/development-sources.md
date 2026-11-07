# Development source index

Status: active, prioritized source/provenance index
Last updated: 2026-11-06 (private archive named `<maintainer evidence>`, local folder layout dropped, zero-normalized count added; earlier: links updated for the onslaught-preservation layout)
Summary: first-party and technical-development evidence for Battle Engine
Aquila, separated from later reporting, inferred binary behavior, and fictional
in-universe lore. Original archive material remains read-only in the maintainer's private store,
`<maintainer evidence>/` (not public); only bounded findings and hashes are promoted here.

## Evidence classes

| Class | Use |
| --- | --- |
| First-party contemporary | Architecture, production process, public assets, release terminology, credits, and stated platform decisions |
| Released/demo binary and data | Measured implementation, format, build, and authored-content evidence |
| Pinned source reference | Architecture/intent aid; must be checked against the released specimen before claiming parity |
| Third-party contemporary | Chronology, preview features, and externally observed build differences |
| Later developer recollection | Context requiring date/source attribution and technical corroboration |
| Technical inference | Explicitly bounded conclusion derived from bytes, structures, traces, or cross-build comparison |
| Fictional lore | Narrative/terminology only; never engine evidence without an independent technical join |

## Priority 1 — parsed first-party architecture source

### Lost Toys GDC deck

Location: a private copy in `<maintainer evidence>/`, identified by the SHA-256 below.

| Property | Value |
| --- | --- |
| Title | *Cross-Platform Console Development: Our Experiences With Battle Engine Aquila* |
| Presenter | Jeremy Longley, Lost Toys |
| Slides | 47 |
| Bytes | 195,072 |
| SHA-256 | `3b2e08607fd881dfefb31395b49de91a68ec02dee9554714f6a97d029165713e` |
| Supporting archive | 77 files / 751,853 bytes |
| Supporting-manifest SHA-256 | `1254ffd317766b395eef9d61473db8ce274868032eb5c663099c49df36abc0e0` |
| Analysis status | Complete ordered transcript, terminology index, and bounded PC-retail search in ignored local lab |

This is the highest-value recovered first-party development artifact. It names
BEA's shared/platform subsystem model, rendering differences, content-scaling
concerns, console preview/profiling tools, certification scheduling, and the
importance of a maintained PC build. See
[`../engine/README.md`](../engine/README.md).

The deck's code slides are edited presentation pseudocode. Exact identifiers are only
promoted after a binary/data/source-path join.

## Priority 2 — measured builds and data

### Canonical PC retail disc

The three Lore PC ISOs labelled V1.00, Bundle V299, and ASUS V299 are
byte-identical. Canonical ISO SHA-256:
`1dc0d95c778105ae3cb1b0db9afa701fc3141ed4ee467cdd227811f6f4248c57`.

The disc reproduces the pristine 2,506,752-byte `BEA.exe`, SHA-256
`74154bfae14ddc8ecb87a0766f5bc381c7b7f1ab334ed7a753040eda1e1e7750`.
It is the retail PC implementation baseline; surrounding mirror metadata may
still carry provenance, but the three disc payloads are not separate builds.

### PC demo

Location: a private copy in `<maintainer evidence>/`, identified by the hashes below.

| Artifact | Bytes | SHA-256 |
| --- | ---: | --- |
| Outer ZIP | 110,691,112 | `62e3f54a25af8049491c96123409f7ee6cc02d9326f4252d84606ffc136acd47` |
| Inner `BEA.exe` | 2,510,848 | `d8637dd755b21c720c0cb8f71923f94d2a04a184d90f5343c2e868ce8606e5c2` |
| Inner `All.gip` | 75,388,730 | `90b16dc8df5669bb1ed2dbd09b450c30864047c9a536ecc31bfc6aa55cb66975` |

This is a distinct build and a strong independent refuter for retail function
work. Its strict virtual class surface pairs all 2,127 retail virtual targets, and 2,123 of those pairs show zero normalized instruction differences once their encoded immediate and displacement fields are zeroed; see [`../engine/README.md`](../engine/README.md) and [`../platforms/demo/demo-vs-retail.md`](../platforms/demo/demo-vs-retail.md).

### Xbox, PlayStation 2, and demo discs

The archive contains Xbox USA/Europe/Korea, PS2 USA/Europe, and four PS2 demo
carriers in multiple containers. Initial read-only identity triage is complete
and promoted through `BUILD_AND_DUMP_MATRIX.md` (archived research record, not published),
the 98-row `archive source manifest` (archived research record, not published),
and the 28-row
`equivalence ledger` (archived research record, not published). Container
hashes, inner-image hashes, filesystem identity, and executable identity remain
separate. Same game/region names are not sufficient duplicate evidence.

The successor
[`platform content crosswalk`](../platforms/console/platform-content-crosswalk.md) reads all six
language members in all three Xbox regions and compares them to pristine PC by
text ID, text, audio identifier, mission-title shape, and character-profile
field. It also closes the strict 66-world-ID presence set while retaining the
known regional AYA byte differences.

## Priority 3 — first-party public material

### Official Lost Toys / BEA website archive

Root: the captures of the official site, kept privately in `<maintainer evidence>/`.

Expected value: original screenshots/thumbnails, SWF material, PC support
files, public mission/character/vehicle terminology, and contemporary public
technology descriptions. The in-universe Forseti/Muspell technology pages are
lore unless independently corroborated as real engine documentation.

Analysis status: inventoried by the archive collector; technical/asset joins
remain open. The Wayback harvest is complete: repeated passes stopped finding new material, so another pass needs a new seed.

### E3 2002 press kit

Root: the E3 2002 press kit, kept privately in `<maintainer evidence>/`.

The kit's disc images stay in the maintainer's archive and are not published or
requested here. Expected value: earlier or higher-quality screenshots/renders,
logos, fact sheets, press copy, video, filenames, and metadata. They remain read-only; filesystem manifests
and BEA-specific comparisons are the next bounded step.

### Manuals and credits

Root: the manuals and documents, kept privately in `<maintainer evidence>/`.

Use for credits/roles, release/legal data, controls, mission/vehicle/weapon and
character terminology, and regional feature differences. Manual prose proves
documented player-facing behavior, not the internal implementation by itself.
The platform content crosswalk now checks the PC English and PS2 USA setting
sections against their exact source files and corrects one OCR-only `Rensor`
misread by direct page inspection: the printed name is **Kensor**.

### Official and press visual material

Roots include the videos and trailers, box art, the official Wayback captures, and an image collection, all kept privately in `<maintainer evidence>/`.
Use dated frames to identify altered HUDs, levels, vehicles, lighting, and cut
features, then join them to a named build or publication date before making a
development-sequence claim.

## Priority 4 — source/reference material

The Stuart Gillam source under [`references/Onslaught/`](https://github.com/dlprentice/Onslaught/tree/c3d8ac464259942b83fa8aa7f867c856e77b734d), pinned at commit `c3d8ac4` (see the [README](../README.md#how-to-read-a-finding)), is a high-value
architecture and intent reference. It is not assumed to be the retail PC,
Xbox, or PS2 source tree. Every consequential behavior is checked against
retail bytes, data, or runtime evidence; cross-build demo agreement now supplies
an additional refuter for virtual functions.

## Transformation and publication boundary

- Original archive files remain unchanged in `<maintainer evidence>/`.
- Extractions, normalized comparisons, transcripts, and raw manifests stay in
  ignored analysis workspaces unless their size/licensing permits a bounded
  derived fact to be promoted.
- Every promoted claim names the input path and hash when known.
- Copyrighted executables, disc images, extracted assets, and long presentation
  transcripts are not tracked or redistributed.
- Passwords for protected archives stay with their local owner and are not written into any repository or log.
