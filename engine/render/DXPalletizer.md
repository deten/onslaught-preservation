# DXPalletizer.cpp - Function Mappings

Status: active static function map
Last updated: 2026-11-06 (names and sizes taken from the naming authority; spelling made British; console-layout claims narrowed; OID__AllocObject defined)
Summary: the nine-function octree colour quantiser that converts 32-bit textures to 8-bit palettised ones, its
node layout, nearest-colour rule and swizzle tables.
Specimen: `<maintainer evidence>/safe-copy-bea-pristine/BEA.exe.original.backup`, SHA-256 `74154bfae14ddc8ecb87a0766f5bc381c7b7f1ab334ed7a753040eda1e1e7750`.

> Colour palette quantisation for 8-bit indexed textures. The functions are found from the
> `DXPalletizer.cpp` debug source path string at `0x00651d60`.

## Interfaces

Every function except the two swizzles is a member with the receiver in ECX; the swizzles are `__cdecl`.

| Address | Function | Arguments and result |
| --- | --- | --- |
| `0x0054e500` | `CPalNode__Insert` | four colour bytes (three channels and alpha or a fourth channel) and the bit depth; returns a node pointer |
| `0x0054e670` | `CPalNode__BuildPalette` | the output palette words |
| `0x0054e6e0` | `CPalNode__AssignIndices` | a pointer to the palette counter and the minimum pixel count; returns an `int` |
| `0x0054e790` | `CPalNode__Collapse` | none |
| `0x0054e950` | `CPalNode__scalar_deleting_dtor` | a free-self flag byte; returns the node pointer |
| `0x0054e9d0` | `CDXPalletizer__Palletize` | source RGBA, width, height, requested palette size, output index and palette references, and flags for source alpha, allocating outputs, swizzling, preserving alpha, expanding a half palette and copying palette tiles |
| `0x0054ef70` | `CDXPalletizer__FindNearestColour` | four colour values; returns the palette index byte |
| `0x0054f090` | `CDXPalletizer__SwizzleBlock` | block width, block height, source block, destination block; returns an `int` |
| `0x0054f380` | `CDXPalletizer__SwizzleTexture` | width, height, source indices, destination; returns an `int` |

The exact workspace, octree-node, palette and swizzle layouts, the console texture format and runtime
texture output remain unproven.

## Overview

DXPalletizer implements an **octree-based colour quantisation** system for converting 32-bit RGBA textures to 8-bit indexed (palettised) textures. This is a common technique in game engines for reducing texture memory while maintaining visual quality.

The system uses:
- **Octree data structure** - 16-entry child nodes (2^4 for RGBA bit levels)
- **Colour frequency tracking** - Counts pixels per colour region
- **Manhattan distance** - For nearest-colour matching in palette lookup
- **Morton-order swizzling** - reorders indexed pixels into Morton (Z-order) blocks

## Functions (9 total)

| Address | Name | Size (bytes) | Purpose |
|---------|------|------|---------|
| 0x0054e9d0 | CDXPalletizer__Palletize | 1,425 | Main entry point - converts RGBA to indexed |
| 0x0054e500 | CPalNode__Insert | 361 | Insert colour into octree, creating nodes as needed |
| 0x0054e6e0 | CPalNode__AssignIndices | 176 | Recursively assign palette indices to leaf nodes |
| 0x0054e790 | CPalNode__Collapse | 445 | Collapse octree nodes to reduce colours |
| 0x0054e670 | CPalNode__BuildPalette | 102 | Extract final palette from octree |
| 0x0054e950 | CPalNode__scalar_deleting_dtor | 116 | Recursively free octree node memory |
| 0x0054ef70 | CDXPalletizer__FindNearestColour | 274 | Find closest palette entry using Manhattan distance |
| 0x0054f380 | CDXPalletizer__SwizzleTexture | 575 | Swizzle texture into Morton-order blocks |
| 0x0054f090 | CDXPalletizer__SwizzleBlock | 742 | Swizzle individual texture block (16x16 or smaller) |

---

## Function Details

### CDXPalletizer__Palletize (0x0054e9d0)

**Main quantisation entry point**

**Algorithm:**
1. Allocates octree root node (0x54 bytes)
2. Iterates all source pixels, inserting into octree
3. Collapses octree to fit target palette size
4. Builds final palette from remaining nodes
5. Maps each pixel to nearest palette entry
6. Optionally swizzles the output into Morton order

**Memory allocations:**
- Line 0xee (238): Octree root node - 0x54 bytes
- Line 0xf4 (244): Palette buffer - paletteSize * 4 bytes
- Line 0x136 (310): Index buffer - width * height bytes
- Line 0x147 (327): Temp index buffer - width * height bytes

---

### CPalNode__Insert (0x0054e500)

**Insert colour into octree**

Takes the colour bytes and the current bit depth, which starts at 7.

**Octree node layout (0x54 bytes):**

| Offset | Field |
| --- | --- |
| `0x00` | colour bytes r, g, b, a (centre of the region) |
| `0x04` | maximum bounds per channel (`0xFF` initially) |
| `0x08` | pixel count in this region |
| `0x0C` | leaf flag (1 for a leaf) |
| `0x10` | node flags |
| `0x14` | sixteen child node pointers (one per combination of the four channel bits) |

**Algorithm:**
- Walks down octree from MSB to LSB of each colour channel
- Creates child nodes as needed (16 children per level for 4-bit index)
- At depth 0 (leaf), stores exact colour and increments pixel count
- At higher depths, stores region center colour

---

### CPalNode__AssignIndices (0x0054e6e0)

**Assign palette indices to leaf nodes**

Takes a pointer to the running palette index counter and the minimum pixel count a leaf needs.

**Algorithm:**
- Recursively traverses octree
- Leaf nodes with pixelCount >= threshold get assigned a palette index
- Non-leaf nodes recurse into all 16 children
- Returns total pixels assigned

---

### CPalNode__Collapse (0x0054e790)

**Collapse octree to reduce palette size**

Takes no arguments.

**Algorithm:**
- Checks if all children are leaves
- If so, merges children into parent:
  - Sums pixel counts
  - Sets isLeaf = 1
  - Frees child nodes
- If not, recursively collapses children first
- Called repeatedly until palette fits target size

---

### CPalNode__BuildPalette (0x0054e670)

**Extract palette from octree**

Takes the palette buffer.

**Algorithm:**
- Recursively walks octree
- For each node with assigned index (index >= 0):
  - Writes RGBA colour to `paletteBuffer[index * 4]`
- Iterates all 16 children recursively

---

### CPalNode__scalar_deleting_dtor (0x0054e950)

**Free octree node and children**

Takes a flag byte saying whether to free the node itself.

**Algorithm:**
- Recursively frees all 16 children
- If shouldFree & 1, frees self via OID__FreeObject (OID_Free)
- Returns node pointer

---

### CDXPalletizer__FindNearestColour (0x0054ef70)

**Find nearest palette entry**

Takes the four colour components and returns the index byte.

**Algorithm:**
- Uses **Manhattan distance** (sum of absolute differences)
- Distance = |r1-r2| + |g1-g2| + |b1-b2| + |a1-a2|
- Iterates palette entries from ECX pointer
- Palette count at offset 0x400 from palette base
- Returns index of closest match

---

### CDXPalletizer__SwizzleTexture (0x0054f380)

**Swizzle texture into Morton order**

Takes the width, the height, the source indexed pixels and the output buffer.

**Algorithm:**
- Validates dimensions are powers of 2 (up to 1024)
- Divides texture into 128x64 blocks (or smaller for small textures)
- Calls CDXPalletizer__SwizzleBlock for each block
- Uses lookup tables at:
  - 0x00651760: Small texture swizzle table
  - 0x00651960: Large texture swizzle table
  - 0x00651c60: Block index table
  - 0x00651ce0: Morton order table

---

### CDXPalletizer__SwizzleBlock (0x0054f090)

**Swizzle individual texture block**

Takes the block width (up to 128), the block height (up to 64), the source block and the output block.

**Morton Order Tables:**
- 0x00651ce0: 4x8 lookup table for Morton indices
- Reorders the block's pixels into Morton order (the cache purpose is not established here)

---

## Data References

| Address | Type | Purpose |
|---------|------|---------|
| 0x00651d60 | string | Debug source path of `DXPalletizer.cpp` |
| 0x00651760 | int[128] | Small texture swizzle table |
| 0x00651960 | int[128] | Large texture swizzle table |
| 0x00651c60 | int[] | Block index lookup table |
| 0x00651ce0 | int[32] | Morton order lookup (4x8) |
| 0x006fbe44 | uint | Palette scaling factor 1 |
| 0x006fbe54 | uint | Palette scaling factor 2 |

---

## Technical Notes

### Octree Colour Quantisation

The octree structure efficiently represents the colour space:
- 8 bits per channel = 8 levels of tree depth
- Each node can have up to 16 children (2^4 for RGBA)
- Leaf nodes represent actual colours used
- Branch nodes represent colour regions

### Swizzling (Morton Order)

The swizzle functions reorder linear indexed textures (row by row) into Morton (Z-order) blocks, which interleave the bits of the x and y coordinates. The lookup tables under Data References show the order. Which console target, if any, these layouts serve is not established here.

### Memory Allocation Pattern

All allocations go through `OID__AllocObject`, the name these notes give retail's object allocator (the
authority table has no row under that name, so its current name is not settled here). It takes the size,
a memory type, the debug source file and the debug line:
- type 0x5f: Octree nodes
- type 0x61: Pixel/palette buffers

---

## Call Graph

```
CDXPalletizer__Palletize
    |
    +-> OID__AllocObject (octree root)
    +-> CPalNode__Insert (for each pixel)
    |       +-> OID__AllocObject (child nodes)
    |
    +-> CPalNode__AssignIndices
    +-> CPalNode__Collapse
    |       +-> CPalNode__scalar_deleting_dtor
    |
    +-> CPalNode__BuildPalette
    +-> CDXPalletizer__FindNearestColour (for each pixel)
    +-> CDXPalletizer__SwizzleTexture (optional)
            +-> CDXPalletizer__SwizzleBlock
    +-> CPalNode__scalar_deleting_dtor (cleanup)
```

---

## Related Files

- **DXTexture.cpp** - Texture loading, uses palletizer for 8-bit textures
- **tgaloader.cpp** - TGA image loading, may feed into palletizer
- **imageloader.cpp** - Base image loading class

---

## Discovery Method

Found via xref search to debug path string at 0x00651d60:
- 5 xrefs from 2 functions
- 0x0054e500: 1 xref (CPalNode__Insert)
- 0x0054e9d0: 4 xrefs (CDXPalletizer__Palletize - multiple allocations)

Helper functions discovered by analyzing call targets within the main functions.

---

## Status

- [x] All 9 DXPalletizer functions are named and their interfaces recorded.
- [x] Static call graph, xref, debug-path, and table-address evidence is documented.
- [x] Swizzle table addresses are identified.
- [ ] Swizzle table values and exact console texture format identity are not fully decoded.
- [ ] Runtime texture output, exact source identity, exact structure layouts, BEA patching, and rebuild parity remain unproven.
