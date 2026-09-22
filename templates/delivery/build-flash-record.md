# Build / Flash Record

Actual results of every build and flash session. Commands come from `toolchain-profile.md`; record what actually happened, including failures. Never invent results — a gap is recorded as a gap.

## Record

| Date | Commit | Build config | Build result (warnings/errors) | Flash size / RAM usage | Flashed? | Smoke result |
| --- | --- | --- | --- | --- | --- | --- |
| `<date>` | `<TBC>` | `<TBC>` (Debug/Release) | `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` / not run |

## Smoke Checklist (per flash)

| Check | Result | Notes |
| --- | --- | --- |
| Power-up clean | `<TBC>` | `<TBC>` |
| Clocks as configured (measured where possible) | `<TBC>` | `<TBC>` |
| Reset and repeated boot (cold + warm, x3) | `<TBC>` | `<TBC>` |
| Serial log output sane | `<TBC>` | `<TBC>` |
| Watchdog / fault paths intact | `<TBC>` | `<TBC>` |

## Fault-Injection Results (when applicable)

| Injected fault | Expected behavior | Observed | Evidence |
| --- | --- | --- | --- |
| `<TBC>` (I2C lockup, RX overflow, comm loss, storage full) | `<TBC>` | `<TBC>` | `<TBC>` |

## Timing Evidence (when applicable)

| Path | Tool (logic analyzer / scope / DWT) | Capture | Result vs requirement |
| --- | --- | --- | --- |
| `<TBC>` | `<TBC>` | `<TBC>` (file/link) | `<TBC>` |

## Unverified Items

- `<TBC>` (hardware unavailable, revision unknown, ...) — carried into `known-issues-and-decisions.md`
