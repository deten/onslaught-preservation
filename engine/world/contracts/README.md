# World contracts

Status: active — folder index
Last updated: 2026-11-06 (truncated summary cells completed; a retail address removed from one cell)
Summary: Per-function contracts for the event scheduler and the monitor deletion list.

## Documents

| Document | Summary |
| --- | --- |
| [CEventManager__AddEvent_AtTime](CEventManager__AddEvent_AtTime__0044b370.md) | specimen-bound static scheduler contract plus two retained Level-100 runtime queue envelopes. Two gap-free calls with distinct caller/payload identities insert equal-time reused events into one priority-0 ring list in FIFO order. |
| [CMonitor__AddDeletionEvent](CMonitor__AddDeletionEvent__00401040.md) | `CMonitor__AddDeletionEvent` lazily creates the monitor's deletion set and adds a reader cell at its head; two recordings show the lazy-allocation path and the insertion executing, but no retained trace ties one call's receiver, argument and result together. |
