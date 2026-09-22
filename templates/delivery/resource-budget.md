# Resource Budget

Flash/RAM/stack/heap/real-time budget with baselines and margins. Compare the map file against these baselines on every material change; record power numbers only when a power target actually exists.

## Memory Baseline

| Metric | Baseline | Limit | Margin | Last measured (commit) |
| --- | --- | --- | --- | --- |
| Flash (text+data) | `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` |
| RAM (data+bss) | `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` |
| Heap | `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` |

## Stack Budget

| Task / context | Size | High-water mark (measured) | Margin | Method |
| --- | --- | --- | --- | --- |
| `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` (uxTaskGetStackHighWaterMark / analysis) |
| ISR (main stack) | `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` |

## Real-Time Budget

| Path | Requirement | Measured | Margin | Method (timer/DWT/logic analyzer) |
| --- | --- | --- | --- | --- |
| `<TBC>` (ISR latency, task period/jitter, DMA completion, wake time) | `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` |

## Power Budget (only if a power target exists)

| State | Target | Measured | Conditions (clock, peripherals, measurement setup) |
| --- | --- | --- | --- |
| Active | `<TBC>` | `<TBC>` | `<TBC>` |
| Sleep / low-power | `<TBC>` | `<TBC>` | `<TBC>` |
| Wake latency | `<TBC>` | `<TBC>` | `<TBC>` |

## Threshold Exceedances and Explanations

| Date | Metric | Value vs baseline | Explanation |
| --- | --- | --- | --- |
| `<date>` | `<TBC>` | `<TBC>` | `<TBC>` |
