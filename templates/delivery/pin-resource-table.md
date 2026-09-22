# Pin Resource Table

Authoritative pin allocation. Update on any GPIO, alternate-function, DMA, IRQ, or clock change; check for conflicts at the same time. Source of truth: the current `.ioc` + schematic, verified against each other.

| Pin | AF / Mode | Signal | Peripheral / Driver owner | DMA (stream/channel) | IRQ (name / priority used) | Notes (pull, speed, board net) |
| --- | --- | --- | --- | --- | --- | --- |
| `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` / none | `<TBC>` / none | `<TBC>` |

## IRQ Priority Ledger

Required in FreeRTOS projects: record `__NVIC_PRIO_BITS`, priority grouping, and `configMAX_SYSCALL_INTERRUPT_PRIORITY` once, then every IRQ's actual priority and whether it calls FromISR APIs.

| Constant | Value |
| --- | --- |
| `__NVIC_PRIO_BITS` | `<TBC>` |
| Priority grouping | `<TBC>` |
| `configLIBRARY_MAX_SYSCALL_INTERRUPT_PRIORITY` | `<TBC>` |

| IRQ | Priority (register value) | Calls FromISR? | Basis for decision |
| --- | --- | --- | --- |
| `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` |

## Conflict Checks Log

| Date | Change | Conflicts checked (AF / DMA stream / IRQ priority) | Result |
| --- | --- | --- | --- |
| `<date>` | `<TBC>` | `<TBC>` | `<TBC>` |
