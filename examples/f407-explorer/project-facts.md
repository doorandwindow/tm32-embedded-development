# Project Facts — F407 Explorer Example

Pre-filled example for the STM32F407ZGT6 Explorer (正点原子探索者 V2.2) attitude-terminal project. Pin-level facts below were verified against ALIENTEK's official IO allocation table (Explorer V2.2) and cross-checked with the HAL example BSP sources; remaining values are marked `TBC`. Copy into `docs/stm32-ai/` and verify every value against your own schematic/code before treating it as authoritative.

## Identity

| Field | Value |
| --- | --- |
| Project name | STM32F407 attitude terminal (MPU6050 + LCD) |
| MCU full part number | STM32F407ZGT6 (1 MB Flash; ALIENTEK's official IO table and the chip datasheet both designate ZGT6 for this board — earlier ZET6 mentions were a typo) |
| Package | LQFP144 |
| Silicon revision | `TBC` (read from chip markings / debugger) |
| Board / hardware version | ALIENTEK Explorer (探索者) V2.2 |
| Execution model | bare-metal (explicit state machines; Madgwick attitude solver runs in a business module) |
| Code generator | STM32CubeMX (`TBC` version) |
| HAL / CMSIS package version | `TBC` (check `.ioc` / Drivers folder) |
| Build system | CMake + Ninja |
| Compiler and version | arm-none-eabi-gcc 13.3.1 |
| Primary reference manual | RM0090 (local copy — see Reference Library below) |
| Errata sheet | STM32F407/417 errata sheet, not stored locally; `TBC` target revision (download from ST) |

## Entry Points and Generation Boundaries

| Item | Path / Value |
| --- | --- |
| CubeMX `.ioc` file | `TBC` |
| Startup file | `TBC` |
| Linker script | `TBC` |
| User-code convention | USER CODE BEGIN/END regions in generated files; drivers under `TBC` (e.g. `Drivers/BSP/`, ALIENTEK-style modules) |
| Middleware in use | none (bare-metal) |

## Key Peripherals (summary)

| Peripheral | Role | Bus / Interface |
| --- | --- | --- |
| MPU6050 | attitude sensor (Accel/Gyro) for Madgwick solver | software I2C: SCL=PB8 / SDA=PB9 (shared with 24C02 & WM8978), INT=PC0, 7-bit addr 0x68 |
| NT35510 / SSD1963 LCD | 480x800 portrait display | FSMC Bank1-NE4, base `0x6C000000 \| 0x7E` (RS=A6), 16-bit data bus |
| GT9147 touch | capacitive touch controller | bit-banged (software) I2C: SCL=PB0 / SDA=PF11 — not hardware I2C |

## Local Reference Library (authoritative docs on this machine)

| Document | Path | Use for |
| --- | --- | --- |
| RM0090 (F405/407/417/427/429 families, English, full) | `D:\video_my\stm32_ai\资料\rm0090-stm32f405415-stm32f407417-stm32f427437-and-stm32f429439-advanced-armbased-32bit-mcus-stmicroelectronics.pdf` | register definitions, DMA/IRQ/clock details |
| STM32F4xx 中文参考手册 | `D:\video_my\stm32_ai\资料\8，STM32参考资料\STM32F4xx中文参考手册.pdf` | register lookup in Chinese |
| Cortex-M3/M4 权威指南 | `D:\video_my\stm32_ai\资料\8，STM32参考资料\` | core, faults, NVIC |
| GT9147 datasheet + programming guide | `D:\video_my\stm32_ai\资料\2，芯片资料\GT9147数据手册.pdf`, `GT9147编程指南.pdf` | touch registers, configuration |
| MPU6050 寄存器表（中文）+ spec + DMP/eMPL | `D:\video_my\stm32_ai\资料\5，MPU6050资料\` | IMU registers, motion driver |
| NT35510 datasheet | `D:\video_my\stm32_ai\资料\3，液晶资料\NT35510(ID5510)\` | LCD panel registers |
| Explorer V2.2 schematic | `D:\video_my\stm32_ai\资料\3，ALIENTEK探索者STM32F4开发板原理图\Explorer STM32F4_V2.2_SCH.pdf` | board nets, final pin arbitration |
| Official IO allocation table (V2.2) | `D:\video_my\stm32_ai\资料\3，ALIENTEK探索者STM32F4开发板原理图\探索者IO引脚分配表.xlsx` | pin reuse / conflict source of truth |
| HAL example BSP (60 experiments) | `D:\video_my\stm32_ai\资料\3，标准例程-HAL库版本\` | BSP reference only — Keil/MDK projects, NOT CMake-buildable; port sources, never point devloop at them |

## Facts Change Log

| Date | Fact changed | Reason | Confirmed by |
| --- | --- | --- | --- |
| 2026-09-22 | MCU corrected ZET6 → ZGT6; clock tree, serial channel, MPU6050/GT9147/LCD pin facts filled; reference library registered | ALIENTEK official IO table + HAL example BSP cross-check | official table + example sources |
| `<date>` | example created | packaged with skill | — |
