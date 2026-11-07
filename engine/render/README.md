# Rendering

Status: active — folder index
Last updated: 2026-11-06 (glossary of the static-contract terms added; truncated index summaries completed; earlier: summaries filled, titles in sentence case)
Summary: Render resources, trees, patches, palettes, particles, view distance, LOD constants and the default Direct3D state.

## Folders

| Folder | Covers |
| --- | --- |
| [terrain/](terrain/README.md) | Terrain draw-stage flags, the shade plane and per-node colour |

## Documents

| Document | Summary |
| --- | --- |
| [DXBattleLine.cpp Function Mappings](DXBattleLine.md) | the HUD battle-line renderer: its texture-backed object, terrain heightmap sampling, mesh triangulation, marker vertices and three-pass render. |
| [DXPalletizer.cpp - Function Mappings](DXPalletizer.md) | the nine-function octree colour quantiser that converts 32-bit textures to 8-bit palettised ones, its node layout, nearest-colour rule and console swizzle tables. |
| [DXParticleTexture.cpp Function Mappings](DXParticleTexture.md) | the particle-texture objects, their global list, and the engine-side particle bundle helpers that create, restore, render and destroy them. |
| [DXPatchManager.cpp](DXPatchManager.md) | retained patch-manager analysis; the Engine shutdown receiver is re-derived from pristine bytes, while other historical claims remain to audit. |
| [DXTrees.cpp - Function Mappings](DXTrees.md) | the fast tree batch: two card buffers built from the map, how a destroyed tree is hidden, and the fast/close selection facts the rebuild uses. |
| [The default render-state block — `0x004EB1E0`, re-derived from bytes](d3d-default-render-state-block.md) | what the default render-state block 0x004EB1E0 sets on the device, re-derived from bytes; it fixes the default state, not the state at any particular later draw. |
| [Mesh, resource, and render static contract](mesh-resource-render-static-contract.md) | specimen-bound rendering and collision contracts, with bounded corrections to arithmetic association, float stores and ordered admission. |
| [The player camera, the cockpit attach, and what `HFOV` means](player-camera-attach-and-mesh-hfov.md) | original camera/projection findings, with a bounded recheck of position identities and aggregate result transport appended below. Other earlier claims were not reverified by that recheck. |
| [Render/resource static bridge](render-resource-bridge-static-contract.md) | Routes AYA `MESH` and `TEXT` vocabulary to the mesh, texture and render evidence; static only. |
| [View distance, cull, and LOD constants — recovered from the retail binary](view-distance-and-lod-constants.md) | The world far plane (700), object, mesh and tree cull distances, fog defaults and LOD constants read from the retail binary. |

## Glossary

The terms below appear in the static contracts of this folder. The mesh contract links the first
use of each term here.

- **PC24, PC53, PC64**: the x87 precision-control settings that round results to a 24-bit (single),
  53-bit (double) or 64-bit (extended) significand. Where a check depends on the setting, the text names it.
- **Nearest-even rounding**: round to the nearest representable value, with a tie going to the value
  whose last bit is even. It is the IEEE 754 default; some sections write it as RN.
- **Relocation**: an object-file record that tells the linker which code or data location must
  receive the final value of a symbol or an address. The checks compare relocation records as well
  as code bytes.
- **FPO record**: the Windows x86 debug record (frame-pointer-omission data) that gives a function's
  local and parameter sizes, prolog length and saved registers, so that stack unwinding works
  without a frame pointer.
- **VC6**: Microsoft Visual C++ 6, the compiler that the decompilation's check builds the source
  with before comparing it with the retail code.
- **LIBCMT**: Microsoft's static multithreaded C runtime library from the VC6 install. Its object
  files, such as `asin.obj`, are compared with the retail code.
- **Callable graph**, **exact control**, **focused match**: terms of the decompilation's byte-match
  checker. They are used as counts in the notes, and no document in this repository or in the
  checker notes of
  [onslaught-decomp](https://github.com/dlprentice/onslaught-decomp/blob/main/docs/check/README.md)
  defines them yet, so this glossary does not define them either.
