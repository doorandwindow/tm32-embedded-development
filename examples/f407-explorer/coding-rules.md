# Coding Rules — F407 Explorer Example

Pre-filled example with recommended defaults; adapt to what the project actually does (ALIENTEK example-code style vs CubeMX style) and record deviations. `TBC` = confirm.

## Language and Warnings

| Item | Rule |
| --- | --- |
| C standard | C11 `TBC` (check CMake `C_STANDARD`) |
| Warning level (owned code) | `-Wall -Wextra -Wshadow` `TBC` (check CMake flags) |
| Warning suppression policy | per-line with rationale, never global |
| Third-party / generated code baseline | ALIENTEK BSP modules + CubeMX/HAL: separate warning flags, don't `-Werror` them |
| Static analysis | none yet `TBC` |

## Naming and Style

| Item | Rule |
| --- | --- |
| Formatting tool / config | `TBC` |
| Prefixes / namespaces | follow ALIENTEK BSP style (`xxx_init`, `xxx_read`, ...); new drivers use module prefixes |
| Integer usage | fixed-width types (`uint32_t`); units explicit (e.g. `angle_deg`, `dt_s`) |

## Project Prohibitions

- No busy-waiting where a state machine / timer event exists (LCD init delays excluded if hardware requires).
- No Madgwick/filtering/control logic inside MPU6050 or GT9147 device drivers — those are business modules.
- No direct `I2C_HandleTypeDef` use outside the bus layer; GT9147 software-I2C bus API stays behind its bus interface.
- No edits outside USER CODE regions in generated files.

## Review and Test Expectations

- Madgwick and any protocol/state-machine logic gets host-runnable tests with recorded data `TBC`.
- Map diff required for Flash/RAM-relevant changes.

## Rules Change Log

| Date | Rule added/changed | Reason | Decided by |
| --- | --- | --- | --- |
| `<date>` | example created | packaged with skill | — |
