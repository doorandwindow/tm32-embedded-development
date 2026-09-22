# Test Matrix

Risk-based verification depth per change. One row per test; every "pass" must point at recorded evidence (log, capture, CI artifact). Host tests never replace target verification — both are tracked here.

## Test Environment

| Item | Value |
| --- | --- |
| Host test runner / framework | `<TBC>` |
| Mock strategy | `<TBC>` (bus interface mocks: success, NACK, short transfer, busy, timeout, DMA error) |
| Target board ID / fixture | `<TBC>` (firmware version, peripherals attached, measurement method) |

## Matrix

| ID | Test / check | Type (static / host / build / target / regression) | Covers (risk) | Method | Evidence | Result |
| --- | --- | --- | --- | --- | --- | --- |
| `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` | pass / fail / blocked / not run |

## Regression Watchlist (re-run after material changes)

| Trigger change | Required re-tests |
| --- | --- |
| Clock / cache / memory-region change | cold boot + repeated resets `<TBC>` |
| Regeneration (.ioc) | user-code regions, IRQ/DMA assignments, rebuild diff `<TBC>` |
| Low-power change | repeated entry/exit, wake sources `<TBC>` |
| `<TBC>` | `<TBC>` |

## Open Failures / Blocked Items

| ID | Item | Blocker | Owner |
| --- | --- | --- | --- |
| `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` |
