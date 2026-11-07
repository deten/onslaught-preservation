# AYA Tag Reference

Status: superseded for layouts by cmsh-fields.md and cmsh-mesh.md; kept for tag IDs — AYA tag quick reference
Last updated: 2026-11-06 (layouts and roles checked against cmsh-fields.md, cmsh-mesh.md and the extractor audit)
Summary: AYA tag IDs and mesh/texture marker lookup.

Quick reference of the CMSH mesh tag IDs; layouts and open items are in [cmsh-fields.md](cmsh-fields.md) and the [extractor audit](aya-resource-extractor-source-audit.md).

## Contents

- [Texture Tags](#texture-tags)
- [Mesh Part Tags](#mesh-part-tags)
- [Hierarchy Tags](#hierarchy-tags)
- [Bounding Tags](#bounding-tags)
- [Animation Tags](#animation-tags)
- [Bone Tags](#bone-tags)
- [Vertex Buffer Tags](#vertex-buffer-tags)
- [Special Tags](#special-tags)

---

## Texture Tags

### CMST - Compiled Material Set
Container for texture/material definitions.

| Field | Size | Description |
|-------|------|-------------|
| Material data | numTextures * 36 | Per-texture material properties |

Followed by numTextures MSHT blocks.

### MSHT - Mesh Texture
Individual texture definition block.

Always contains a TEXB child tag.

### TEXB - Texture Binding
Texture file reference and UV properties.

| Field | Size | Description |
|-------|------|-------------|
| Lanes per frame | frames * 20 | Five f32 lanes per frame: blend, U/V offset, U/V scale |
| Texture name | 128 | A NUL-terminated string in a fixed 128-byte field; nothing guarantees a terminator or UTF-8 (see [cmsh-fields.md](cmsh-fields.md)) |

---

## Mesh Part Tags

### MESP - Mesh Part
Top-level container for a mesh part. Each model has numParts MESP blocks.

Always contains a CMSP child tag.

### CMSP - Compiled Mesh Part
Core mesh part data.

| Field | Size | Description |
|-------|------|-------------|
| Current orientation | 48 | 3x4 matrix (current transform) |
| Base orientation | 48 | 3x4 matrix (bind pose) |
| Offset position | 16 | float4 (current offset) |
| Base position | 16 | float4 (bind offset) |
| Pointers | 8 | Runtime pointers (skip) |
| Part number | 4 | uint32 |
| Part type | 4 | uint32 |
| Num children | 4 | uint32 |
| Reserved | 20 | 5 x uint32 |
| Num DVert | 4 | Display vertices |
| Num PVert | 4 | Physics vertices |
| Num tris | 4 | Triangle count |
| Num AFrames | 4 | Animation frames |
| Num VFrames | 4 | Vertex frames |
| Num HFrames | 4 | Hierarchy frames |
| Num bones | 4 | Bone count |
| Pointers | 24 | Runtime pointers (skip) |
| Part name | 32 | Name (null-terminated) |
| Reserved | 64 | 16 x uint32 |

---

## Hierarchy Tags

### CHLD - Children
List of child part indices.

| Field | Size | Description |
|-------|------|-------------|
| Child indices | numChildren * 4 | uint32 array |

### PRNT - Parent
Parent part reference.

| Field | Size | Description |
|-------|------|-------------|
| Parent index | 4 | uint32 |

### NMIC - Next in Chain
Index of the next part in a chain; purpose open (see [cmsh-fields.md](cmsh-fields.md)).

| Field | Size | Description |
|-------|------|-------------|
| Next part index | 4 | uint32 |

---

## Bounding Tags

### BBOX - Bounding Box
Axis-aligned bounding box for culling.

**Note**: The tag appears twice: a wrapper, then the 40-byte record ([cmsh-fields.md](cmsh-fields.md), grammar). Why the writer repeats it is open ([cmsh-mesh.md](cmsh-mesh.md)).

| Field | Size | Description |
|-------|------|-------------|
| Origin | 16 | float4 (center point) |
| Axis | 16 | float4 (extents) |
| Valid | 4 | uint32 (1 = valid) |
| Radius | 4 | float (bounding sphere) |

---

## Animation Tags

### VHFM - Virtual-frame map
One byte per virtual frame, each the index of a hierarchy pose (0 to hFrames-1); see [cmsh-fields.md](cmsh-fields.md).

| Field | Size | Description |
|-------|------|-------------|
| Pose index per virtual frame | numVFrames | byte array |

### HORI - Hierarchy Orientation
Per-frame orientation matrices.

| Field | Size | Description |
|-------|------|-------------|
| Orientations | numHFrames * 48 | 3x4 matrix per frame |

### HPOS - Hierarchy Position
Per-frame position data.

| Field | Size | Description |
|-------|------|-------------|
| Positions | numHFrames * 16 | float4 per frame |

### HFOV - Hierarchy Field of View
Per-frame FOV (for camera parts).

| Field | Size | Description |
|-------|------|-------------|
| FOV values | numHFrames * 4 | float per frame |

### CPOS - Current Position
Derived model-space cache: one composed frame per virtual frame, or one static record; not an authoring source (see [cmsh-cpos-cori-identity.md](cmsh-cpos-cori-identity.md)).

| Field | Size | Description |
|-------|------|-------------|
| Payload | numCachedFrames * 16 | One vector per cached frame; the size comes from the tag header |

### CORI - Current Orientation
Derived model-space cache: one composed frame per virtual frame, or one static record; not an authoring source (see [cmsh-cpos-cori-identity.md](cmsh-cpos-cori-identity.md)).

| Field | Size | Description |
|-------|------|-------------|
| Payload | numCachedFrames * 48 | One 3x4 matrix per cached frame; the size comes from the tag header |

### CAMD - Animation-mode table (mesh level)
36-byte records (mode name, id, start, last, frame scaler, rate), one per animation mode. They follow the parts, so they do not end part parsing (see [cmsh-fields.md](cmsh-fields.md)).

---

## Bone Tags

### BONE - Bone Indices
Bone index array for skinning.

| Field | Size | Description |
|-------|------|-------------|
| Bone indices | numBones * 4 | uint32 array |

### BONW - Bone Weights
Per-vertex bone weights.

| Field | Size | Description |
|-------|------|-------------|
| Weights | numPVert * numBones * 4 | float array |

### BONS - Bone Skin
Per-vertex bone-slot values; see [cmsh-fields.md](cmsh-fields.md).

| Field | Size | Description |
|-------|------|-------------|
| Skin data | numPVert * 12 | three i32 values per vertex |

**Note**: BONW and BONS do not occur in the 213-file corpus ([cmsh-matrix-palette-skinning.md](cmsh-matrix-palette-skinning.md)). The sizes shown are what the retail reader requests, not measured payloads.

---

## Vertex Buffer Tags

### PMVB - Part Mesh Vertex Buffer
Container for vertex/index data. Contains CMVB.

### CMVB - Compiled Mesh Vertex Buffer
Vertex buffer header.

| Field | Size | Description |
|-------|------|-------------|
| Pointers | 8 | Runtime pointers (skip) |
| Piece slots | 256 | 64 piece-pointer slots (replaced at load) |
| Piece count | 4 | piece_count (i32, 0-12 installed) |
| Pointers | 8 | Runtime pointers |
| Vertex chunk size | 4 | 36 (standard) or 48 (skinned) |
| FVF flags | 4 | D3D flexible vertex format |
| Primitive type | 4 | 4 = triangle strip in the game's numbering (5 = triangle fan); see [cmsh-fields.md](cmsh-fields.md), vertex-buffer record |
| Reserved | 8 | Unknown |

Followed by piece_count MMPT blocks.

### MMPT - Mesh Material Part
Per-material vertex/index buffer section.

| Field | Size | Description |
|-------|------|-------------|
| VB size | 4 | Vertex buffer byte size |
| IB size | 4 | Index buffer byte size |
| Num indices | 4 | Index count |
| Num vertices | 4 | Vertex count |
| Num primitives | 4 | Primitive count |
| Built | 4 | i32, the sixth MMPT field (named *built* in [cmsh-fields.md](cmsh-fields.md)); meaning open |

Followed by IBUF, VBUF, TEXR.

### IBUF - Index Buffer
Triangle strip indices.

| Field | Size | Description |
|-------|------|-------------|
| Indices | numIndices * 2 | uint16 array |

### VBUF - Vertex Buffer
Vertex data array.

| Field | Size | Description |
|-------|------|-------------|
| Vertices | numVertices * vertexChunkSize | Vertex array |

For secondary materials (materialIndex > 0), VBUF references the same vertices:
- Read 4-byte size from tag header
- Skip that many bytes

### TEXR - Texture Reference
Material texture binding.

| Field | Size | Description |
|-------|------|-------------|
| Texture IDs | 24 | 6 x uint32 (multi-texture slots) |

Slot 0 is the primary diffuse texture. Other slots for multi-texturing (rarely used).

---

## Special Tags

### REFR - Reference Part
Points at another part's geometry: reference parts (type 6) draw the part this index names (see [cmsh-fields.md](cmsh-fields.md)).

| Field | Size | Description |
|-------|------|-------------|
| Reference part | 4 | uint32 (source part index) |

When encountered, copy vertex/index data from referenced part using current part's transform.

### PBKT - Part Bucket
Wrapper of the part's collision grid, entered rather than read: the next header is the CPBT grid record (184 bytes), followed by the grid's arrays ([layout in cmsh-fields.md](cmsh-fields.md#collision-grid-cpbt-184-bytes-and-its-arrays)). The consumer join for the PB* payload is open (audit P2 item 3 in the [extractor audit](aya-resource-extractor-source-audit.md)).

| Field | Size | Description |
|-------|------|-------------|
| Wrapper | header only | No payload of its own |

### CCUS - Camera customization (extractor-only)
Extractor-only: the pinned extractor accepts it as a post-part marker, but it is not observed in the measured corpus, so its role is unknown (see the [extractor audit](aya-resource-extractor-source-audit.md)).

---

## Tag Parsing Order

Tags within MESP appear in this order (all optional except CMSP):

1. CMSP (required)
2. CHLD
3. PRNT
4. NMIC
5. BBOX (appears twice)
6. VHFM
7. HORI
8. HPOS
9. HFOV
10. BONE
11. BONW
12. BONS
13. PBKT
14. CPOS
15. CORI
16. REFR
17. PMVB (contains CMVB, MMPT, IBUF, VBUF, TEXR)

A part ends at the next MESP. After the last part, the mesh-level records follow: CAMD (if any), the mesh BBOX, CEMT (if any) and PMS2 (if any); see [cmsh-fields.md](cmsh-fields.md).

---

## Implementation Notes

1. **Tag header**: Always 8 bytes (4-char name + 4-byte length)
2. **Alignment**: No padding between tags
3. **Optional tags**: Check tag name before reading; if unexpected, rewind 8 bytes
4. **Material loop**: MMPT/IBUF/VBUF/TEXR repeat for each material in CMVB
5. **REFR handling**: Must have already parsed the referenced part
