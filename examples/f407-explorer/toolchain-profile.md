# Toolchain Profile — F407 Explorer Example

Pre-filled example. Commands below are typical for CubeMX + CMake + Ninja + GCC on Windows; **run each once, then register your exact working commands** (paths, generator flags) before treating them as authoritative. `TBC` = confirm in this project.

## Environment

| Item | Value |
| --- | --- |
| OS / shell | Windows (CMD/PowerShell; note codepage — use UTF-8-safe logging if scripts print non-ASCII) |
| Toolchain | arm-none-eabi-gcc 13.3.1 (`TBC` full `--version` string) |
| CMake / Ninja version | `TBC` |
| Code generator export flow | CubeMX → CMake project (`TBC`: "CMake" via CubeMX or imported from Makefile) |

## Registered Commands

| Purpose | Command | Working directory | Verified on |
| --- | --- | --- | --- |
| Configure | `cmake -B build -G Ninja -DCMAKE_BUILD_TYPE=Debug` `TBC` | project root | `TBC` |
| Build | `cmake --build build` `TBC` | project root | `TBC` |
| Clean build | delete `build/` then Configure + Build | project root | `TBC` |
| Flash | `TBC` (ST-LINK via STM32CubeProgrammer CLI / OpenOCD / PyOCD — register the exact working command) | `TBC` | `TBC` |
| Debug launch | `TBC` (OpenOCD + GDB / IDE launch config) | `TBC` | `TBC` |
| Erase / unlock (guarded) | `TBC` | `TBC` | `TBC` |

## Build Artifacts and Baselines

| Item | Path / Value |
| --- | --- |
| ELF / map / bin-hex output | `build/<project>.elf`, `TBC` map path |
| Size report method | `arm-none-eabi-size` on ELF `TBC` |
| Flash baseline | `TBC` bytes |
| RAM baseline | `TBC` bytes |

## Known Toolchain Pitfalls

- `TBC` (e.g. non-ASCII paths breaking CMake/Ninja, codepage issues with UTF-8 script output)

## Toolchain Change Log

| Date | Tool / command changed | Reason | Verified by |
| --- | --- | --- | --- |
| `<date>` | example created | packaged with skill | — |
