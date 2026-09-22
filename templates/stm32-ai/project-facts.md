# Project Facts

Single source of truth for project identity. Read first at the start of every task; treat every value here as authoritative over assumptions, prior memory, and MCU-family generalizations. Unconfirmed values stay `TBC` — do not infer them.

## Identity

| Field | Value |
| --- | --- |
| Project name | `<TBC>` |
| MCU full part number | `<TBC>` (e.g. STM32F407ZETx — use the exact orderable part, not the family) |
| Package | `<TBC>` (e.g. LQFP144) |
| Silicon revision | `<TBC>` (from datasheet order code, debugger, or chip markings) |
| Board / hardware version | `<TBC>` |
| Execution model | `<TBC>` (bare-metal / FreeRTOS <version> / other RTOS) |
| Code generator | `<TBC>` (e.g. STM32CubeMX <version>) |
| HAL / CMSIS package version | `<TBC>` |
| Build system | `<TBC>` (CMake / Makefile / IDE project) |
| Compiler and version | `<TBC>` (e.g. arm-none-eabi-gcc 13.3.1) |
| Primary reference manual | `<TBC>` (RM number + revision) |
| Errata sheet | `<TBC>` (document number + target revision) |

## Entry Points and Generation Boundaries

| Item | Path / Value |
| --- | --- |
| CubeMX `.ioc` file | `<TBC>` |
| Startup file | `<TBC>` |
| Linker script | `<TBC>` |
| User-code convention | `<TBC>` (USER CODE BEGIN/END regions, extension layer path, ...) |
| Middleware in use | `<TBC>` (FreeRTOS, FatFS, LwIP, ...) |

## Local Reference Library (optional but recommended)

Register the authoritative documents available on this machine (reference manual, errata sheet, datasheets, schematic, official pin tables) so register/errata checks are executable, not aspirational. Use absolute paths; note when a document is NOT stored locally.

| Document | Path | Use for |
| --- | --- | --- |
| Reference manual (RM number + revision) | `<TBC>` | register definitions |
| Errata sheet (target revision) | `<TBC>` | silicon bug screening |
| Board schematic | `<TBC>` | nets, final pin arbitration |
| Key device datasheets (one row per device) | `<TBC>` | device registers, timing |

## Facts Change Log

| Date | Fact changed | Reason | Confirmed by |
| --- | --- | --- | --- |
| `<date>` | `<TBC>` | `<TBC>` | `<TBC>` |
