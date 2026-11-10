# DXParticleTexture.cpp Function Mappings

Status: active function reference; retained evidence limits apply
Last updated: 2026-11-06 (names matched to the naming authority; the GetAnimatedFrame alias renamed to GetTexture and its purpose line corrected; path offset, blend factors, return wording and capacity note corrected; specimen and evidence lines added; earlier: decompiler-style signatures and research-campaign records removed)
Summary: the particle-texture objects, their global list, and the engine-side particle bundle helpers that
create, restore, render and destroy them.
Evidence: MEASURED — static reads of the pristine instructions (identities, offsets, callsites and render-state values); the usage labels are INFERRED and runtime behaviour is UNKNOWN, as the sections say.
Specimen: `<maintainer evidence>/safe-copy-bea-pristine/BEA.exe.original.backup`, SHA-256 `74154bfae14ddc8ecb87a0766f5bc381c7b7f1ab334ed7a753040eda1e1e7750`.
Source File: `DXParticleTexture.cpp` (named by its debug path string; absent from the pinned source) | Binary: BEA.exe

> Debug source path string for `DXParticleTexture.cpp`: `0x00651dcc`.

## Names

Eight helpers this note uses were unnamed in earlier analysis and now carry names:
`CVBufTexture__Release` (`0x00501310`), `PCLTShell__SSS_NoCache` (`0x00513930`),
`PCLTShell__D3D_SetTexture` (`0x00513a50`), `PCLTShell__SetVertexShader` (`0x00513e20`),
`PCLTShell__D3D_CreatePixelShader` (`0x00513f20`), `PCLTShell__D3D_Release` (`0x00514010`),
`CPCPlatform__KeyOn` (`0x00515970`) and `CDXTexture__GetTexture` (`0x00558690`). Two were renamed:
`0x00549220` (earlier `OID__FreeObject`) is `CDXMemoryManager__Free`, and `0x00550380` (earlier
`CDXPatch__Constructor`) is `CLandscapeVB__ctor`; text below written for the old names has not been
re-measured against the new ones.

---

## Overview

DXParticleTexture is a DirectX rendering class that manages particle system textures and their associated vertex/index buffers. It maintains a global linked list of particle texture objects for efficient batch rendering and resource management.

## Interfaces

The four engine-side particle texture helpers and the nine DXParticleTexture global and object helpers:

| Address | Interface | Evidence summary |
| --- | --- | --- |
| `0x0054f6e0` | `CParticleManager__dtor`: `particle_bundle` (pointer) in ECX; no stack arguments; returns nothing | Callsite `0x0054f6d5` jumps here with ECX set to particle bundle `0x009c63e8`; body destroys particles, clears particle-manager owner links, shuts down manager storage, and frees the manager. |
| `0x0054f740` | `CParticleManager__ctor`: `particle_bundle` (pointer) in ECX; no stack arguments; returns nothing | Callsite `0x0054f6b5` reaches the reset helper; body clears bundle fields at `+0x00/+0x04/+0x08/+0x0c/+0x18`. |
| `0x0054f760` | `CParticleManager__SetRenderStates`: `__cdecl`; no stack arguments; returns nothing | Callsite `0x004c8fba` invokes this no-argument render-state preset before particle texture drawing. |
| `0x0054f7e0` | `CParticleManager__RenderOther`: `particle_bundle` (pointer) in ECX; no stack arguments; returns nothing | `CDXEngine__Render` callsite `0x0053ebe3` and frontend matrix setup callsite `0x00540f55` set ECX to bundle `0x009c63e8`; body sets the render states and camera basis, dispatches the other particle descriptors (the dispatch calls `CDXParticleTexture__RenderAll` at `0x0054f9ec`), and restores the states. |
| `0x0054fbc0` | `CDXParticleTexture__Get`: `__cdecl`; takes `texture_path` (string), `texture_type` (int); returns a pointer | `CPDPMesh__Load` callsite `0x004c57b9` pushes path/type, calls, cleans `0x8`, and stores the returned node pointer. The body scans `0x009c64d0`, allocates `0x1a4` bytes when absent, configures CTexture/CVBufTexture, and links the node. |
| `0x0054fd80` | `CDXParticleTexture__FreeAllSurfaces`: `__cdecl`; no stack arguments; returns nothing | Device/reset callsite `0x00512c1d`; body walks `0x009c64d0`, releases node resources at `+0x194/+0x198`, and releases/nulls `0x009c6468`. |
| `0x0054fde0` | `CDXParticleTexture__RecreateAllSurfaces`: `__cdecl`; no stack arguments; returns nothing | Device-restore callsite `0x0051290e`; body recreates CTexture/CVBufTexture resources and conditionally creates particle shaders, including the one named by the string `particleshader` at `0x00651ed0`. |
| `0x0054fee0` | `CDXParticleTexture__DestroyAll`: `__cdecl`; no stack arguments; returns nothing | Teardown callsites `0x0046923c` and `0x0046c9c5`; body walks `0x009c64d0`, calls `CDXParticleTexture__dtor`, frees nodes, and clears the global list. |
| `0x0054ff20` | `CDXParticleTexture__RenderAll`: `__cdecl`; no stack arguments; returns nothing | Called by `CParticleManager__RenderOther` at `0x0054f9ec` and the particle render path at `0x004c8cb3`; body sets render/shader state and calls `CDXParticleTexture__Render` for every list node. |
| `0x00550110` | `CDXParticleTexture__dtor`: member with the receiver in ECX; no stack arguments; returns nothing | `CDXParticleTexture__DestroyAll` callsite `0x0054fefd` passes the current node in ECX before freeing it. |
| `0x00550180` | `CDXParticleTexture__AddTriangle`: member with the receiver in ECX; takes `index0` (unsigned 16-bit int), `index1` (unsigned 16-bit int), `index2` (unsigned 16-bit int); returns nothing | Six particle sprite/descriptor callsites prove ECX plus three 16-bit indices and `RET 0x0c`; body writes three index words from `CVBufTexture__GetIndexPtr(this+0x198, 3)`. |
| `0x005501b0` | `CDXParticleTexture__GetIndices`: member with the receiver in ECX; takes `index_count` (int); returns a pointer | `CPDSimpleSprite__RenderSprites` callsite `0x004c782c` uses EAX as the writable index-buffer pointer returned from `CVBufTexture__GetIndexPtr`. |
| `0x00550220` | `CDXParticleTexture__Render`: member with the receiver in ECX; no stack arguments; returns nothing | `CDXParticleTexture__RenderAll` callsite `0x00550082` passes each global-list node in ECX; body resolves texture frame, sets blend mode from node type `+0x190`, resets batch index `+0x19c`, and renders the CVBufTexture. |

This is static evidence only. Exact particle bundle, DXParticleTexture, CParticleDescriptor, CTexture, CVBufTexture, shader/global, and render-state layouts, runtime particle output, BEA patching, and rebuild parity remain unproven.

## Class Structure

### Object Size

- Size: 0x1a4 bytes (420 bytes)
- Allocated via OID__AllocObject with object type 0x23

### Global Linked List

- Head pointer: `0x009c64d0`
- Objects linked via offset 0x1a0 (next pointer)

### Member Offsets

| Offset | Type | Name | Notes |
|--------|------|------|-------|
| 0x00 | char[200] | m_szFullPath | Full texture path (copied from the path argument) |
| 0xC8 (200) | char[200] | m_szShortName | Short name (filename after last backslash) |
| 0x190 (400) | int | m_nType | Texture type (the second argument: 1, 2 or 3; the usage labels in the Texture Type Meanings table are inferred, not measured) |
| 0x194 | CTexture* | m_pTexture | Texture object from CDXTexture__FindTexture |
| 0x198 | CVBufTexture* | m_pVBufTexture | Vertex buffer texture from CVBufTexture__GetOrCreate |
| 0x19C | int | m_nLastBatchIndex | Last batch index (reset to -1 each render) |
| 0x1a0 | DXParticleTexture* | m_pNext | Next in global linked list |

## Functions

### CDXParticleTexture__Get (0x0054fbc0)

**Interface:** `__cdecl`; takes the texture path (string) and the texture type (int); returns a pointer to the particle texture object

Factory function that retrieves an existing particle texture or creates a new one. Implements a cache pattern using a global linked list.

**Key Operations:**
1. Iterates through linked list at 0x009c64d0
2. For each node, compares texturePath (case-insensitive) and textureType
3. If found, returns existing object
4. If not found:
   - Allocates 0x1a4 bytes via OID__AllocObject
   - Initializes all fields to 0
   - Sets m_nLastBatchIndex to -1 (0xFFFFFFFF)
   - Copies full path to offset 0x00
   - Copies short name (after last '\\') to offset 0xC8
   - Determines texture format based on type (2 or 5)
   - Calls CDXTexture__FindTexture to get texture
   - Calls CVBufTexture__GetOrCreate for vertex buffer
   - Sets VB format: 0x142, 8 entries (the creation-time capacity), stride 0x18, 4 components
   - Sets IB format: 0x65, 8 entries (the creation-time capacity), stride 2
   - Adds to head of linked list
5. Returns pointer to object

**Called by:**
- CPDSimpleSprite__Load (0x004c0682)
- CPDTrail__Load (0x004c3185)
- CPDPMesh__Load (0x004c57b9)

---

### CDXParticleTexture__FreeAllSurfaces (0x0054fd80)

**Interface:** `__cdecl`; no stack arguments; returns nothing

Releases GPU resources for all particle textures without destroying the objects. Called during device lost scenarios.

**Key Operations:**
1. Iterates through linked list at 0x009c64d0
2. For each object:
   - If m_pTexture (0x194) exists, releases it via CTexture__Release, sets to NULL
   - If m_pVBufTexture (0x198) exists, releases it via 0x00501310, sets to NULL
3. Cleans up pixel shader at 0x009c6468 via 0x00514010

**Called from:** Device reset handler (0x00512c1d)

---

### CDXParticleTexture__RecreateAllSurfaces (0x0054fde0)

**Interface:** `__cdecl`; no stack arguments; returns nothing

Restores GPU resources for all particle textures after device reset. Recreates textures and shaders.

**Key Operations:**
1. Logs "CPT__RAS__" marker via `CDebugLog__AddMessage` (`0x00441740`)
2. Iterates through linked list at 0x009c64d0
3. For each object:
   - Determines texture format (2 or 5) based on type
   - Calls CDXTexture__FindTexture with the full path at this+0x00 (m_szFullPath)
   - Calls CVBufTexture__GetOrCreate
   - Sets VB format: 0x142, 0x208 entries (520, the restore-time capacity), stride 0x18, 4 components
   - Sets IB format: 0x65, 0x208 entries (520, the restore-time capacity), stride 2
4. If 0x009c648c and hardware supports (0x00888b20 > 0xffff01ff):
   - Creates vertex shader "particleshader" at 0x009c646c
   - Creates pixel shader with ps_2_0 code at 0x009c6468
5. Logs "CPT__RAS___end" marker

The restore capacity (520 entries) is larger than the creation capacity (8 entries). This note does not establish why; the cheapest falsifier is a runtime capture of the buffers' size after a device restore.

**Called from:** Device restore handler (0x0051290e)

---

### CDXParticleTexture__DestroyAll (0x0054fee0)

**Interface:** `__cdecl`; no stack arguments; returns nothing

Destroys all particle texture objects and frees memory. Called during shutdown.

**Key Operations:**
1. While linked list head (0x009c64d0) is not NULL:
   - Gets current head
   - Gets next pointer from offset 0x1a0
   - Calls CDXParticleTexture__dtor to release GPU resources
   - Frees object memory via CDXMemoryManager__Free
   - Updates head to next
2. Sets 0x009c64d0 to NULL

**Called from:**
- Shutdown handler (0x0046923c)
- Level unload (0x0046c9c5)

---

### CDXParticleTexture__RenderAll (0x0054ff20)

**Interface:** `__cdecl`; no stack arguments; returns nothing

Main render function that draws all particle textures with optional shader support.

**Key Operations:**
1. Saves linked list head
2. Sets render state `0x1b` to `1` via `PCLTShell__SRS` (`0x00513bc0`)
3. Clears 0x009c64d4 flag
4. Checks various conditions: `CGame__IsMultiplayer` (0x004725d0) and the render pass (`CPCPlatform__KeyOn`, 0x00515970)
5. If shader rendering enabled:
   - Sets pixel shader (0x009c6468) via D3D call (vtable+0x1ac)
   - Sets vertex shader (0x009c646c) via 0x00513e20
   - Configures texture sampler state at slot 1
   - Sets various render states (6, 5, 7, 10)
   - Sets 0x009c64d4 flag to 1
6. Iterates through all particle textures:
   - Calls CDXParticleTexture__Render for each
7. Restores render states
8. Clears pixel shader (sets to 0)
9. Sets render state 0x1b to 0

**Called from:**
- Particle system render (0x004c8cb3)
- Alternative particle render (0x0054f9ec)

---

### CDXParticleTexture__dtor (0x00550110)

**Interface:** Member with the receiver in ECX; no stack arguments; returns nothing

Instance method that releases GPU resources for a single particle texture.

**Key Operations:**
1. Sets up exception handler
2. If m_pTexture (0x194) exists:
   - Releases via CTexture__Release
   - Sets to NULL
3. If m_pVBufTexture (0x198) exists:
   - Releases via 0x00501310
   - Sets to NULL

**Called from:** CDXParticleTexture__DestroyAll (0x0054fee0)

---

### CDXParticleTexture__AddTriangle (0x00550180)

**Interface:** Member with the receiver in ECX; takes `index0` (16-bit int), `index1` (16-bit int), `index2` (16-bit int); returns nothing

Adds three indices for a triangle to the index buffer.

**Key Operations:**
1. Gets CVBufTexture from this+0x198
2. Calls CVBufTexture__GetIndexPtr(3) to get pointer for 3 indices
3. Writes indices index0, index1, index2 as shorts

**Called from:** Multiple particle rendering locations (0x004c8bec, 0x004c8c05, 0x004ca201, etc.)

---

### CDXParticleTexture__GetIndices (0x005501b0)

**Interface:** Member with the receiver in ECX; takes `count` (int); returns a pointer

Gets a pointer to index buffer space for the specified number of indices.

**Key Operations:**
1. Gets CVBufTexture from this+0x198
2. Calls CVBufTexture__GetIndexPtr(count)
3. Returns the pointer from `CVBufTexture__GetIndexPtr` to the caller (the sprite callsite in the Interfaces table uses it)

**Called from:** Particle batch rendering (0x004c782c)

---

### CDXParticleTexture__Render (0x00550220)

**Interface:** Member with the receiver in ECX; no stack arguments; returns nothing

Instance method that renders this particle texture's geometry.

**Key Operations:**
1. Returns early if m_pVBufTexture (0x198) is NULL
2. Gets texture surface via 0x00558690
3. Sets texture at slot 0 via 0x00513a50
4. Based on texture type (m_nType at offset 400):
   - Type 1: Sets blend mode (0x13=2, 0x14=2)
   - Type 2: Sets blend mode (0x13=5, 0x14=6)
   - Type 3: Sets blend mode (0x13=2, 0x14=6)
5. Resets m_nLastBatchIndex to -1
6. Renders indexed geometry:
   - If 0x009c64d4 (shader mode): CVBufTexture__RenderIndexedNoValidate
   - Otherwise: CVBufTexture__RenderIndexed

**Called from:** CDXParticleTexture__RenderAll (0x0054ff20) loop

## Related Strings

| Address | String | Usage |
|---------|--------|-------|
| 0x00651dcc | debug source path of `DXParticleTexture.cpp` | Debug path |
| 0x00651ed0 | "particleshader" | Vertex shader name |
| 0x00651ee0 | "vs_1_1 dcl_position v0 dcl_texco..." | Vertex shader source |
| 0x00651e04 | "ps_2_0 dcl_2d s0 dcl_2d s1 dcl_v..." | Pixel shader source |
| 0x00652110 | "CPT__RAS__" | RecreateAllSurfaces start marker |
| 0x00651df4 | "CPT__RAS___end" | RecreateAllSurfaces end marker |
| 0x00651db4 | "GEFORCE_PARTICLE_FOG" | Hardware capability string |

## Related Globals

| Address | Type | Name | Notes |
|---------|------|------|-------|
| 0x009c64d0 | DXParticleTexture* | g_pParticleTextureList | Linked list head |
| 0x009c64d4 | char | g_bShaderMode | Shader rendering enabled flag |
| 0x009c6468 | void* | g_pPixelShader | Pixel shader handle |
| 0x009c646c | void* | g_pVertexShader | Vertex shader handle |
| 0x009c648c | int | g_bUseShaders | Shader support flag |
| 0x009c647c | int | — | Unknown render flag |
| 0x00888b20 | int | — | Hardware capability bits |
| 0x00854dec | int | — | Unknown hardware flag |
| 0x0089c9b0 | void* | — | Texture manager/device |
| 0x00888a50 | void* | — | D3D device pointer |

## Related Functions (External)

| Address | Name | Purpose |
|---------|------|---------|
| 0x004f27e0 | CTexture__Release | Release CTexture refcount helper |
| 0x00501310 | CVBufTexture__Release | Release CVBufTexture |
| 0x00514010 | PCLTShell__D3D_Release | Release pixel shader |
| 0x00513f20 | PCLTShell__D3D_CreatePixelShader | Create pixel shader |
| 0x00513e20 | PCLTShell__SetVertexShader | Set vertex shader |
| 0x00513bc0 | PCLTShell__SRS | Cached render-state setter |
| 0x00513930 | PCLTShell__SSS_NoCache | Set sampler state |
| 0x00513a50 | PCLTShell__D3D_SetTexture | Set texture |
| 0x00549220 | CDXMemoryManager__Free | Free object memory |
| 0x00558690 | CDXTexture__GetTexture | Returns the texture pointer (the existing GetTexture implementation; see mesh-resource-render-static-contract.md) |
| 0x00568390 | stricmp (`0x00568390`) | Case-insensitive string compare |
| 0x00441740 | CDebugLog__AddMessage (`0x00441740`) | Debug log marker |
| 0x004725d0 | CGame__IsMultiplayer | Check multiplayer/current-level render condition |
| 0x00515970 | CPCPlatform__KeyOn | Check render pass |

## Texture Type Meanings

The blend factors in this table are the states each type sets (see the Render section); the usage labels are inferred from them, not measured.

| Type | Blend Source | Blend Dest | Usage (inferred) |
|------|-------------|------------|-------|
| 1 | 2 (ONE) | 2 (ONE) | Additive particles |
| 2 | 5 (SRC_ALPHA) | 6 (INV_SRC_ALPHA) | Standard alpha blend |
| 3 | 2 (ONE) | 6 (INV_SRC_ALPHA) | Mixed blend mode |

## Architecture Notes

1. **Factory Pattern**: CDXParticleTexture__Get implements a factory with caching via linked list
2. **Device Lost Handling**: FreeAllSurfaces/RecreateAllSurfaces pattern for D3D device reset
3. **Batch Rendering**: Multiple particles share textures, rendered in batches
4. **Shader Support**: Optional pixel/vertex shaders for enhanced effects (ps_2_0, vs_1_1)
5. **Memory Management**: Uses OID__AllocObject for allocation and CDXMemoryManager__Free for release
6. **String Storage**: Full path and short name stored for texture lookup

## Relationship to Other Classes

```
CParticleManager
  |
  +-- CParticleDescriptor
        |
        +-- DXParticleTexture (via CDXParticleTexture__Get)
              |
              +-- CTexture (m_pTexture)
              +-- CVBufTexture (m_pVBufTexture)
```
