# Task / ISR / DMA Matrix

Who does what, at which context, with which synchronization. Keep in sync with `pin-resource-table.md` (IRQ priorities) and `driver-contracts.md` (callbacks). Update on any ISR, DMA, or task change.

## Execution Contexts

| Context | Type | Priority | Notes |
| --- | --- | --- | --- |
| `<TBC>` (ISR name) | ISR | `<TBC>` (register value) | `<TBC>` (FromISR-safe calls allowed?) |
| `<TBC>` (task name) | RTOS task | `<TBC>` | `<TBC>` (stack, period) |
| Main loop | bare-metal | — | `<TBC>` |

## Ownership Matrix

| Resource | ISR action (ack/sample/clear only) | Task owner | Sync object (queue/notification/mutex) | DMA involved (owner driver) |
| --- | --- | --- | --- | --- |
| `<TBC>` peripheral | `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` |

## DMA Register

| DMA stream/channel | Peripheral | Direction | Buffer location (linker region) | Completion/error callbacks | Cache strategy (if cached core) |
| --- | --- | --- | --- | --- | --- |
| `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` |

## Shared Data Protocol (multicore or bus-master shared memory only)

- Shared regions and MPU/cache attributes: `<TBC>`
- Notification and handshake: `<TBC>`
- Barriers and ownership rules: `<TBC>`
