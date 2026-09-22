# Driver Contracts

Interface contracts per device/bus driver. Each new or changed driver gets a section; the contract is what business modules (and host mocks) rely on. For each field, `TBC` is not an acceptable shipped value — an explicit "not applicable" is.

## Driver: `<name>` (v`<n>`, date `<date>`)

| Contract item | Value |
| --- | --- |
| Initialization prerequisites (clocks, GPIO, bus up) | `<TBC>` |
| State / thread safety | `<TBC>` (which calls may run concurrently, locking model) |
| Timeouts (init, transfer, recovery) | `<TBC>` |
| Error returns and meaning | `<TBC>` |
| Buffer ownership and lifetime | `<TBC>` (who allocates, valid until when, DMA rules) |
| Callback execution context | `<TBC>` (ISR / task / RTOS queue handoff) |
| Bus implementation injection | `<TBC>` (interface used, how mocked on host) |
| DMA / cache strategy (if any) | `<TBC>` (clean/invalidate vs non-cacheable region, alignment) |
| Error/timeout recovery path | `<TBC>` (observable, not infinite retry) |

## Known Limitations

- `<TBC>`

## Contract Change Log

| Date | Driver | Change | Callers updated? |
| --- | --- | --- | --- |
| `<date>` | `<TBC>` | `<TBC>` | `<TBC>` |
