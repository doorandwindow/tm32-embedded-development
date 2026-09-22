# Hardware Profile — F407 Explorer Example

Pre-filled example; pin facts verified against the ALIENTEK official IO allocation table (Explorer V2.2) and cross-checked with the HAL example BSP sources. Remaining `TBC` = confirm in your own project.

## Clock Tree (configured)

| Item | Value | Source (schematic / code / measurement) |
| --- | --- | --- |
| HSE frequency | 8 MHz | Explorer board crystal; ALIENTEK clock init divides by PLL_M=8 |
| PLL config (M/N/P/Q) | M=8, N=336, P=2, Q=7 — `Stm32_Clock_Init(336,8,2,7)` in example code | HAL example `sys.c` / `main.c` |
| SYSCLK | 168 MHz | example code comment + PLL math (8/8×336/2) |
| AHB / APB1 / APB2 | 168 / 42 / 84 MHz (standard F4 prescalers) | consistent with USART1@115200 in examples; confirm in your `.ioc` |
| Flash wait states | 5 WS @ 168 MHz (per RM0090) | confirm in your code |
| Peripheral kernel-clock notes | FSMC on AHB3; SDIO and I2S clocking `TBC` in your project | — |

Note: Cortex-M4F — no D-Cache; cache-coherence sections of the skill do not apply to this MCU.

## Debug and Programming Access

| Item | Value |
| --- | --- |
| Debug interface | SWD (on-board ST-LINK; prefer SWD — PA13/PA14 only) |
| Programmer / debugger | ST-LINK on-board (`TBC` firmware version) |
| Serial console | USART1: PA9=TX / PA10=RX, 115200 8N1, via on-board CH340 (P6 jumpers default connected) |

## Serial Capture Channel (HIL evidence path)

| Item | Value |
| --- | --- |
| USB-UART bridge | CH340 (on-board) ↔ MCU USART1 (PA9 = TX) |
| VID:PID | `1A86:7523` (primary locator) |
| Current COM number | e.g. COM18 — **informational only, re-enumerate each session** |
| Baud rate | 115200 |
| DTR/RTS convention | not asserted (this board does not reset on DTR; reset comes from ST-Link `-rst` after flash) |
| Expected boot banner | e.g. `[boot] SystemCoreClock=168000000` + first peripheral init lines — set to your own firmware's real output |

Note: P6 jumpers wire PA9/PA10 to the CH340. Removing them frees USART1 but disables the capture channel.

## External Devices

| Device | Bus / Interface | Key pins | Address / CS | Supply | Notes |
| --- | --- | --- | --- | --- | --- |
| MPU6050 | software I2C (shared bus: 24C02, WM8978) | SCL=PB8, SDA=PB9 (on-board 4.7k pull-ups) | 7-bit 0x68 (AD0 low; WHO_AM_I=0x68) | 3.3 V | INT=PC0, active low (example init writes INTBP_CFG=0x80) |
| NT35510 / SSD1963 LCD | FSMC Bank1-NE4, 16-bit | data D0–D15 = PD/PE set (see pin table); NOE=PD4, NWE=PD5; RS=A6 (PF12) | NE4 = PG12; base `0x6C000000 \| 0x7E` | 3.3 V | BL = PB15; panel ID auto-detected by ALIENTEK BSP (9341/5310/5510/1963) |
| GT9147 | software I2C (reuses touch-port nets T_SCK/T_MOSI) | SCL=PB0, SDA=PF11 | 7-bit 0x14 (write 0x28 / read 0x29) | 3.3 V | RST=PC13, INT=PB1; bit-banged, NOT hardware I2C |

## Board Quirks and Constraints

- Jumper matrix decides pin ownership: **P6** (PA9/PA10 ↔ CH340), **P9** (PA2/PA3: RS232 COM2 vs RS485), **P10** (PB10/PB11: RS232 COM3 vs ATK-MODULE), **P11** (PA11/PA12: USB vs CAN). Check the jumper positions before allocating these pins.
- PB2 = BOOT1 (sampled at reset, network controlled by B0) and TFTLCD touch MISO at the same time.
- PB8/PB9 carry a shared software-I2C bus (24C02 + MPU6050 + WM8978, 4.7k pull-ups): bit-banged, slower than hardware I2C; keep per-device timing assumptions separate.
- PA13/PA14 = SWDIO/SWCLK (10k pull/pull-down networks). PA15 = JTDI/USB_PWR with 10k pull-up: to use it as GPIO, disable JTAG — on F4 this is a SYSCFG remap, not AFIO.
- PB3/PB4 (W25Q128 SPI1 SCK/MISO) default to JTAG JTDO/JTRST — same JTAG-disable requirement before GPIO use.
- PC14/PC15 carry the 32.768 kHz RTC crystal — never usable as GPIO.
- PF9/PF10 drive DS0 (red) / DS1 (green) LEDs, active low. PF7 (light sensor) is recommended output-only per the official table.
- LCD and touch share the board's 2x17 LCD connector; check ALIENTEK pin-multiplexing notes before reassigning touch pins (GT9147 rides on the T_SCK/T_MOSI nets).

## Hardware Profile Change Log

| Date | Item changed | Reason | Confirmed by |
| --- | --- | --- | --- |
| 2026-09-22 | Clock tree, serial channel, external-device pins filled; quirks expanded | ALIENTEK official IO table + HAL example BSP cross-check | official table + example sources |
| `<date>` | example created | packaged with skill | — |
