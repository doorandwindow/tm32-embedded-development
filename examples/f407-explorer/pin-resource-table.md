# Pin Resource Table — F407 Explorer Example

Pre-filled from ALIENTEK's official Explorer V2.2 IO allocation table, cross-checked with the HAL example BSP sources (2026-09-22). Update on any GPIO, alternate-function, DMA, IRQ, or clock change; check for conflicts at the same time. Final arbitration source: the V2.2 schematic + your `.ioc`.

Groups with a spanning range collapse repetitive lines (e.g. `PE7–PE15 = FSMC_D4–D12` means pins PE7 through PE15 in pin order).

## Onboard Basics

| Pin | AF / Mode | Signal | Peripheral / Driver owner | DMA | IRQ | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| PF9 | GPIO output | LED0 (DS0, red) | led module | none | none | active low |
| PF10 | GPIO output | LED1 (DS1, green) | led module | none | none | active low |
| PF8 | GPIO output (TIM AF possible) | BEEP | beeper | none | none | not recommended as generic IO |
| PE4 / PE3 / PE2 | GPIO input | KEY0 / KEY1 / KEY2 | key module | none | EXTI optional | fully independent when keys idle |
| PA0 | GPIO input / WKUP | KEY_UP (WK_UP) | key module / PWR wake | none | EXTI0 / WKUP | fully independent when key idle |
| PF7 | GPIO (output-only recommended) | light sensor (LS1) | ADC/IO sample | ADC optional | none | official table: output-only recommended |
| PA8 | TIM AF / GPIO | HS0038 IR receiver (REMOTE_IN), DCMI XCLK | IR remote / camera | — | — | 4.7k pull-up, IR pulls the line; not recommended as generic IO |
| PG9 | GPIO | 1-wire DQ (DHT11 / DS18B20, U12), DCMI PWDN | one-wire drivers | none | none | 4.7k pull-up |

## Serial / USB-UART

| Pin | AF / Mode | Signal | Peripheral / Driver owner | DMA | IRQ | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| PA9 | USART1_TX (AF7) | CH340 RXD via **P6** | usart module (printf console) | DMA optional | USART1 optional | remove P6 caps to free PA9 — kills capture channel |
| PA10 | USART1_RX (AF7) | CH340 TXD via **P6** | usart module | DMA optional | USART1 optional | see P6 note above |
| PA2 / PA3 | USART2 AF / GPIO | RS232 COM2 or RS485 via **P9**; PA2 also = LAN8720 MDIO | per jumper setup | — | — | allocate only after checking P9 + LAN8720 usage |
| PB10 / PB11 | USART3 AF / GPIO | RS232 COM3 or ATK-MODULE via **P10** | per jumper setup | — | — | allocate only after checking P10 |
| PA11 / PA12 | USB or CAN AF | USB D−/D+ or CAN RX/TX via **P11** | USB/CAN stack | — | — | fully independent if P11 caps removed |

## MPU6050 (software I2C bus #1, shared)

| Pin | AF / Mode | Signal | Peripheral / Driver owner | DMA | IRQ | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| PB8 | GPIO open-drain | IIC_SCL (software) | myiic bus module | none | none | 4.7k pull-up on board; shared with 24C02 + WM8978 |
| PB9 | GPIO open-drain (dir-switched) | IIC_SDA (software) | myiic bus module | none | none | direction toggled in code; 4.7k pull-up |
| PC0 | GPIO input | MPU6050 INT (3D_INT), ATK-MODULE LED | mpu6050 driver | none | EXTI0 optional | also drives ATK-MODULE LED net |

## GT9147 Capacitive Touch (software I2C bus #2)

| Pin | AF / Mode | Signal | Peripheral / Driver owner | DMA | IRQ | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| PB0 | GPIO open-drain | CT_IIC_SCL (software) | ctiic bus module | none | none | rides on touch-port net T_SCK; only valid with LCD module plugged |
| PF11 | GPIO open-drain (dir-switched) | CT_IIC_SDA (software) | ctiic bus module | none | none | rides on T_MOSI net |
| PC13 | GPIO output | GT9147 RST | gt9147 driver | none | none | same net as resistive-touch T_CS |
| PB1 | GPIO input | GT9147 INT (T_PEN) | gt9147 driver | none | EXTI1 optional | touch interrupt |
| PC13 note | — | resistive XPT2046 T_CS | — | — | — | GT9147 RST and resistive T_CS share PC13 — do not drive during touch SPI ops |

## LCD — FSMC Bank1-NE4, 16-bit

| Pin | AF / Mode | Signal | Peripheral / Driver owner | DMA | IRQ | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| PG12 | FSMC NE4 (AF12) | LCD chip select | lcd (FSMC) driver | — | — | fully independent if LCD absent |
| PF12 | FSMC A6 (AF12) | LCD RS (data/cmd select) | lcd driver | — | — | base `0x6C000000 \| 0x7E`, HADDR right-shifted |
| PD4 / PD5 | FSMC NOE / NWE (AF12) | LCD RD / WR | lcd driver | — | — | shared with SRAM |
| PD14, PD15, PD0, PD1, PE7–PE15, PD8–PD10 | FSMC D0–D15 (AF12) | 16-bit data bus | lcd + SRAM shared | — | — | full set: D0=PD14, D1=PD15, D2=PD0, D3=PD1, D4–D12=PE7–PE15, D13–D15=PD8–PD10 |
| PB15 | GPIO output | LCD_BL backlight | lcd driver | none | none | fully independent if LCD absent |

## External SRAM IS62WV51216 — FSMC Bank1-NE3

| Pin | AF / Mode | Signal | Peripheral / Driver owner | DMA | IRQ | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| PG10 | FSMC NE3 (AF12) | SRAM chip select | xram driver | — | — | not recommended as generic IO |
| PF0–PF5 | FSMC A0–A5 (AF12) | SRAM address | xram driver | — | — | SRAM-dedicated |
| PG0–PG5 | FSMC A10–A15 (AF12) | SRAM address | xram driver | — | — | SRAM-dedicated |
| PD11–PD13 | FSMC A16–A18 (AF12) | SRAM address (HADDR A17–A19) | xram driver | — | — | SRAM-dedicated |
| PE0 / PE1 | FSMC NBL0 / NBL1 (AF12) | SRAM byte masks | xram driver | — | — | SRAM-dedicated |

## SPI / Wireless / Storage

| Pin | AF / Mode | Signal | Peripheral / Driver owner | DMA | IRQ | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| PB3 | SPI1_SCK (AF5) / JTAG JTDO | W25Q128 + WIRELESS SCK | spi flash driver | DMA optional | — | JTAG must be disabled (SYSCFG remap) before GPIO use |
| PB4 | SPI1_MISO (AF5) / JTAG JTRST | W25Q128 + WIRELESS MISO | spi flash driver | DMA optional | — | same JTAG note |
| PB5 | SPI1_MOSI (AF5) | W25Q128 + WIRELESS MOSI | spi flash driver | DMA optional | — | — |
| PB14 | GPIO output | W25Q128 CS (F_CS) | spi flash driver | none | none | not recommended as generic IO |
| PG6 / PG7 / PG8 | GPIO | NRF24L01 CE / CS / IRQ | nrf24l01 driver | none | EXTI5 optional (PG8) | PG8 also = RS485 RE |
| PC8–PC12, PD2 | SDIO AF12 | SDIO D0–D3, SCK=PC12, CMD=PD2 | sdio driver | DMA2 recommended | SDIO optional | 47k pull-ups; shared with DCMI D2/D3/D4 (PC8/PC9/PC12) |

## Audio (WM8978) and Camera (DCMI/OLED port)

| Pin | AF / Mode | Signal | Peripheral / Driver owner | DMA | IRQ | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| PB12 / PB13 | I2S AF (or GPIO) | WM8978 LRCK / SCLK | i2s driver | — | — | free as GPIO when WM8978 unused |
| PC2 / PC3 | I2S AF (or GPIO) | WM8978 ADCDAT / DACDAT | i2s driver | DMA recommended | — | — |
| PC6 | I2S MCLK / DCMI D0 | WM8978 MCLK + camera D0 | i2s / camera | — | — | dual function |
| PB6 / PB7, PA4, PA6, PE5 / PE6 | GPIO/DCMI AF | camera D5/D1... see official table: D5=PB6, VSYNC=PB7, HREF=PA4, PCLK=PA6, D6=PE5, D7=PE6 | camera driver | DCMI DMA | DCMI optional | fully independent when camera port unused |
| PD6 / PD7, PG15 | GPIO | camera SCL / SDA / RESET | camera driver | none | none | fully independent when camera port unused |
| PA15 | GPIO / JTAG JTDI | USB_PWR (USB host VBUS control) | usb host power | none | none | 10k pull-up; JTAG disable needed for GPIO |

## IRQ Priority Ledger

Required in FreeRTOS projects: record `__NVIC_PRIO_BITS`, priority grouping, and `configMAX_SYSCALL_INTERRUPT_PRIORITY` once, then every IRQ's actual priority and whether it calls FromISR APIs. Bare-metal projects: record EXTI/IRQ priorities in use.

| Constant | Value |
| --- | --- |
| `__NVIC_PRIO_BITS` | 4 (F4 family default — confirm in your project) |
| Priority grouping | `TBC` |
| `configLIBRARY_MAX_SYSCALL_INTERRUPT_PRIORITY` | n/a (bare-metal) / `TBC` |

| IRQ | Priority (register value) | Calls FromISR? | Basis for decision |
| --- | --- | --- | --- |
| `TBC` (USART1) | `TBC` | `TBC` | `TBC` |

## Conflict Checks Log

| Date | Change | Conflicts checked (AF / DMA stream / IRQ priority) | Result |
| --- | --- | --- | --- |
| 2026-09-22 | Initial import from official V2.2 IO allocation table + HAL example BSP cross-check | all onboard peripherals vs official table; jumper-dependent nets flagged (P6/P9/P10/P11); GT9147↔XPT2046 shared PC13; SDIO↔DCMI shared PC8/9/12; PB8/PB9 shared bus | no unmanaged conflicts in default jumper configuration |
