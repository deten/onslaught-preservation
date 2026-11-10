# Retail save format

Status: active — the saves folder index
Last updated: 2026-11-06 (every top-level saves document now has a row in the Documents table; earlier: retired AppCore/WinUI tools in the past tense)
Summary: the fixed 10,004-byte `.bes` career format and the documents that describe it.

This directory documents the observed fixed 10,004-byte (`0x2714`) `.bes`
career format used by the supported retail/Steam specimen. An editor starts from
a real baseline, preserves unknown bytes and file size, and writes only fields
selected by the user; the Onslaught Companion in Onslaught-Career-Editor (`CareerSave`) does so.

## Documents

| Document | Owner |
| --- | --- |
| [`save-format.md`](save-format.md) | Authoritative file layout and preservation rules |
| `struct-layouts.md` (archived research record, not published) | Career node, link, and Goodie structures |
| [`career-graph.md`](career-graph.md) | Campaign graph semantics |
| [`career-links.md`](career-links.md) | Link index and world mapping |
| [`career-unlock-recipes.md`](career-unlock-recipes.md) | Evidence-backed unlock edits |
| [`grade-system.md`](grade-system.md) | Ranking floats and grade calculation |
| [`goodies-system.md`](goodies-system.md) | Displayable Goodie slots and states |
| [`kill-tracking.md`](kill-tracking.md) | Lower-24-bit counters and metadata preservation |
| [`career-save-format-semantics.md`](career-save-format-semantics.md) | Released serializer/load modes, dynamic size law, and demo cross-build proof |
| [`career-progression-static-bridge-contract.md`](career-progression-static-bridge-contract.md) | Where each career-progression concern is documented, from source vocabulary to retail save bytes |
| [`cpcmemorycard-pc-save-backend-semantics.md`](cpcmemorycard-pc-save-backend-semantics.md) | How the released PC build maps the console-shaped memory-card interface onto ordinary `.bes` files |
| [`frontend-save-load-semantics.md`](frontend-save-load-semantics.md) | The released PC frontend save and load transaction, and where it diverges from the source |
| [`options-control-bindings.md`](options-control-bindings.md) | Control bindings: what preset execution establishes, with remapping and key capture bounded |
| [`public-save-options.md`](public-save-options.md) | Public rules for `.bes` saves and `defaultoptions.bea`: fixed size, unknown bytes preserved, edits on copies only |
| [`save-goodies.md`](save-goodies.md) | Where the Goodie states live in a `.bes` save, and the corrections an editor must keep |
| [`save-kills.md`](save-kills.md) | Kill counter and metadata lookup |
| [`save-ranks.md`](save-ranks.md) | Rank float and bit-pattern lookup |
| [`save-structs.md`](save-structs.md) | `.bes` and CCareer struct layout lookup |
| [`save-options-startup-compatibility.md`](save-options-startup-compatibility.md) | Startup and serialization behaviour independently rechecked, and the save and settings compatibility boundaries that remain |

## Quick layout

```text
0x0000  version word 0x4BD1 (uint16)
0x0002  CCareer fixed copy (0x24BC bytes)
0x0006  CCareerNode[100]
0x1906  CCareerNodeLink[200]
0x1F46  CGoodie[300]
0x23F6  five packed kill-counter dwords
0x240A  mSlots[32]
0x248A  career/options scalar fields
0x24BE  16 observed options entries (0x20 bytes each)
0x26BE  0x56-byte options tail
0x2714  end of supported file
```

Integers are little-endian dwords beginning at offsets where
`file_offset % 4 == 2`; floats are raw IEEE-754. Options entries and the tail are
applied by the retail boot path for `defaultoptions.bea`, but skipped by the
career-load path. Preserve them unless intentionally editing that format.

Static retail evidence is summarized in
[`save-options-startup-compatibility.md`](save-options-startup-compatibility.md),
with the complete serializer semantics and PC-demo twin comparison in the
[`CCareer` crosswalk](career-save-format-semantics.md).
Stuart's source informs structures and logic but is not the retail on-disk
authority.
