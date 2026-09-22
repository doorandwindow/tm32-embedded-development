# Architecture Brief

One-page map of the system architecture. Keep it current when architecture or MCU choices change; deep details belong in the referenced records, not here.

## System Overview

- Product function in one sentence: `<TBC>`
- Execution model: `<TBC>` (bare-metal main loop / RTOS task set — see task-isr-dma-matrix.md)

## Layer Map

| Layer | Contents | May depend on |
| --- | --- | --- |
| Application / task orchestration | `<TBC>` | business modules |
| Business modules | `<TBC>` | device drivers only (never HAL/registers/RTOS internals) |
| Device drivers | `<TBC>` | bus interfaces |
| Bus drivers | `<TBC>` | HAL/CMSIS, owns DMA/IRQ/locks |
| BSP / platform | `<TBC>` | concrete board wiring, reset/CS GPIOs |

Note: a stable existing project does not need to be forced into this structure; record its actual equivalent layers here instead.

## Key Architecture Decisions

| Date | Decision | Rationale | Alternatives rejected |
| --- | --- | --- | --- |
| `<date>` | `<TBC>` | `<TBC>` | `<TBC>` |

## Related Records

- Chip selection: `chip-selection-matrix.md`
- Resources: `pin-resource-table.md`, `resource-budget.md`, `task-isr-dma-matrix.md`
- Interfaces: `driver-contracts.md`
