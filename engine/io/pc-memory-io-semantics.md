# PC memory-buffer and allocation-service semantic recovery

Status: active, bounded semantic recovery
Last updated: 2026-11-06 (retail sidecar, munge and ReadString claims aligned with DXMemBuffer.md; writer buffer difference enumerated; Summary label)
Evidence: SOURCE — pinned `DXMemBuffer` and `DXMemoryManager` implementations;
MEASURED — complete pristine retail bodies, Win32/zlib calls, heap routing,
constants, headers, and normalized-identical PC demo twins; UNKNOWN
— live filesystem faults, allocator fragmentation, and console parity.
Summary: the PC buffered file service and typed heap router are recovered,
including released compression behavior and two material buffer-size
differences from the retained source; the one-byte check is a sum modulo 256,
and no active sidecar validation is shown.

Specimen: pristine PC retail `BEA.exe`, SHA-256
`74154bfae14ddc8ecb87a0766f5bc381c7b7f1ab334ed7a753040eda1e1e7750`;
PC demo `BEA.exe`, SHA-256
`d8637dd755b21c720c0cb8f71923f94d2a04a184d90f5343c2e868ce8606e5c2`.

## Result

The fifteen `CDXMemBuffer` and ten `CDXMemoryManager` functions cover 5,378
retail bytes and 1,775 decoded instructions. The fifteen `CDXMemBuffer`
functions are the twelve bodies that [DXMemBuffer.md](DXMemBuffer.md) decodes,
the two direct-transfer thunks that document describes, and one adjacent
wrapper that it did not recheck. Every body has an independently
linked demo twin with zero normalized instruction differences; 368 raw bytes
differ only in encoded address or displacement spans. The machine-readable
result is `pc-memory-io-semantics-2026-08-11.tsv` (in the decompilation notes, not yet published).
That 6,558-byte table has SHA-256
`7ed6c00658136481416365769fe679377e3bcf11dc0b919352a1812d39ee66c9`.

The retained buffer implementation is
[`references/Onslaught/DXMemBuffer.cpp`](https://github.com/dlprentice/Onslaught/blob/c3d8ac464259942b83fa8aa7f867c856e77b734d/DXMemBuffer.cpp),
14,555 bytes, SHA-256
`c12a95cdf2f423239d2d298846f89d14554f550cb5ec68619b1be9e418ad505a`;
its 1,602-byte header has SHA-256
`6f28f9cffa0217bf48d18c6845620ea687d69c6227b34f6eb8416b5176e874ee`.
The retained manager is
[`references/Onslaught/DXMemoryManager.cpp`](https://github.com/dlprentice/Onslaught/blob/c3d8ac464259942b83fa8aa7f867c856e77b734d/DXMemoryManager.cpp),
12,307 bytes, SHA-256
`990dd061c1daccc15b507f29fc78a1720ecd1b93ae136bc17ab4bc92daebf33a`;
its 2,827-byte header has SHA-256
`d7b91718358fa0ebca22c063c77b0889fae435a29de5b5ffa856737e25034938`.

## Buffered file state and modes

`CDXMemBuffer` owns a Win32 file handle, data block, check-byte block/cursor,
current pointer, buffered byte count, logical position, read/write mode, and EOF
state. Construction zeroes four pointer and slot words (the data buffer, the
check-byte buffer and cursor, and the sidecar slot) and does not clear file,
EOF or position state ([DXMemBuffer.md](DXMemBuffer.md#receiver-fields-and-initialization));
destruction frees data and check-byte
storage through the global typed memory manager. Two direct-transfer thunks
serve particle-loader close and unwind paths.

The released read-buffer size law differs materially from source. Retained
`SetNextReadBufferSize(0)` chooses 64 KiB and otherwise stores the caller's
cluster-aligned size. Retail/demo instead choose 1 MiB for zero and round every
nonzero request upward to a whole 1 MiB boundary. Mission asynchronous loading
is an observed caller of this released policy.

Write-open allocates a 1 MiB buffer, creates/truncates the output, and formats a
`.crc` name without opening or writing that sidecar. The released writer's buffer
is smaller than the source's PC build, which allocates 2 MiB
([`references/Onslaught/DXMemBuffer.cpp`](https://github.com/dlprentice/Onslaught/blob/c3d8ac464259942b83fa8aa7f867c856e77b734d/DXMemBuffer.cpp#L22-L23),
lines 22-23). Read-open allocates the current configured size, formats a `.crc`
name without opening it, ignores its munge argument (the PC body does not consume
it; [DXMemBuffer.md](DXMemBuffer.md#method-identities-and-transport)), initializes
cursor/EOF state, and can begin after a byte skip. A tiny particle-set wrapper
passes memory type `0x11`, path munging on (an argument the PC body does not read),
and initial skip zero.

## Raw, compressed, and check-byte I/O

`Skip` and `Read` consume the current block, refill as needed, update logical
position, return actual transferred bytes, and preserve short-read/EOF
semantics. The released path recognizes a compressed filename suffix, reads a
four-byte compressed byte count, decompresses into the active block with zlib,
and, when a check buffer is present, compares the output-byte sum modulo 256
with the current check byte. The retail initializers do not populate that
buffer, so no sidecar validation is shown
([DXMemBuffer.md](DXMemBuffer.md#read-skip-and-compression-boundaries--static-findings)).
The exact suffix token is measured
but intentionally not generalized into a file-format name here.

`Write` fills the current block and flushes precise block boundaries. Raw mode
writes the block directly; compressed mode calls zlib `compress`, writes the
four-byte compressed length followed by compressed bytes, and uses shared
scratch storage. Neither path writes sidecar records: the retail writer formats
the sidecar name but does not open or write it. `Close` flushes the final
partial block under the same law before closing; read close instead releases
the open data and check-byte buffers. This compression path is a released extension
over the retained raw `Write`/`Close` bodies.

The retail `ReadString` identity is more precise than the retained source
`ReadString`: stop
at newline, EOF, or `max-1`, normalize CRLF to a newline followed by NUL, and
maintain the same refill, decompression and check-byte state. `GetFileSize`
is the direct Win32 wrapper and `EndOfFile` returns the stored flag
([DXMemBuffer.md](DXMemBuffer.md#method-identities-and-transport)).

## Typed memory routing

The manager constructs four embedded heaps and initializes all 129 memory-type
slots to the default heap before applying the retained type-name and size-limit
table. PC initialization configures default, dump, thing, and sound heaps,
including a `0x4B000` tiny allocation arena, then routes sound/sample and
thing-family types to their specialized heaps. Xbox-only texture and vertex
buffer heap paths in retained conditional source are absent from these PC
bodies.

`Alloc` carries size, memory type, source filename, and source line into the
selected heap. Failure distinguishes default, dump, thing, and sound heaps with
separate localized fatal-error IDs. The owner has many current call references, so this is the central
allocation fan-out rather than an isolated
wrapper.

`ReAlloc` first attempts tiny-block handling in default then thing heaps. A
non-tiny allocation has a 16-byte header immediately before the user pointer,
with memory type read at user `-8`; reallocation and free use that type to find
the owning heap. `Free` accepts null and follows the same tiny-before-header
order.

The remaining bodies are debug policy rather than allocation guesses:
selectable default/thing on-screen stats, default-heap file output, delta
reports for default/dump/thing, and detailed default/dump/thing logs with peak
and size summaries.

## Architecture and boundary

Jeremy Longley's GDC deck
([development sources](../../history/development-sources.md#lost-toys-gdc-deck))
lists both file access and memory management as
cross-platform service boundaries. These PC bodies show the concrete Win32 and
zlib implementation plus the PC heap selection, while retained conditionals
show where Xbox-specific heaps diverged. They do not prove the PS2/Xbox file
containers or allocation internals.

Open boundaries are the compressed suffix's full container taxonomy, check-byte
helper internals outside these bodies, file-sharing/error behavior
under live faults, allocator coalescing and fragmentation in `CMemoryHeap`,
console service implementations, performance, and rebuild parity. No
executable or archive input is mutated.
