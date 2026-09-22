# Coding Rules

Project-specific rules that bind every code change. Keep the list short and enforceable; anything not listed follows the existing codebase. Record exceptions with scope and expiry instead of silently deviating.

## Language and Warnings

| Item | Rule |
| --- | --- |
| C standard | `<TBC>` (e.g. C11) |
| Warning level (owned code) | `<TBC>` (e.g. `-Wall -Wextra -Wshadow`) |
| Warning suppression policy | `<TBC>` (per-line with rationale, never global) |
| Third-party / generated code baseline | `<TBC>` (separate flags, pinned versions) |
| Static analysis | `<TBC>` (tool + scope, or "none yet") |

## Naming and Style

| Item | Rule |
| --- | --- |
| Formatting tool / config | `<TBC>` (clang-format file path) |
| Prefixes / namespaces | `<TBC>` (module prefixes, `bsp_` / `drv_` / `app_`, ...) |
| Integer usage | Fixed-width types (`uint32_t`), explicit units in names or comments |

## Project Prohibitions

- No busy-waiting to hide timing problems; use events, state machines, or RTOS primitives.
- No business logic in ISRs or device drivers; keep filtering/control in business modules.
- No direct HAL handle use in business modules; depend on owned bus interfaces.
- No edits outside USER CODE regions in generated files.
- `<TBC>` (project-specific additions)

## Review and Test Expectations

- `<TBC>` (e.g. every new driver ships with host-runnable mock tests; map diff required for Flash/RAM-relevant changes)

## Rules Change Log

| Date | Rule added/changed | Reason | Decided by |
| --- | --- | --- | --- |
| `<date>` | `<TBC>` | `<TBC>` | `<TBC>` |
