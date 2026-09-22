# Known Issues and Decisions — F407 Explorer Example

Pre-filled board-level quirks from ALIENTEK official documentation and HAL example sources (2026-09-22). Project-specific issues get appended as the debug loop finds them.

## Known Issues

| ID | Issue | Impact | Workaround / mitigation | Status | Related records |
| --- | --- | --- | --- | --- | --- |
| KI-01 | Jumper matrix P6/P9/P10/P11 decides ownership of PA9/PA10, PA2/PA3, PB10/PB11, PA11/PA12 (CH340 / RS232 / RS485 / USB / CAN) | Serial capture channel and UART allocation break silently if jumper state is assumed instead of checked | Before any UART/USB/CAN change, record jumper positions in this file; HIL capture assumes P6 connected | mitigated (documented) | hardware-profile.md serial channel |
| KI-02 | PB2 is BOOT1 (sampled at reset) and touch MISO at the same time | Driving PB2 during touch ops is fine, but its boot-time level is set by the B0 pull network — never rewire it | Keep PB2 in input mode during boot; do not allocate as generic output | open | pin-resource-table.md |
| KI-03 | PB8/PB9 is a shared bit-banged I2C bus (24C02 + MPU6050 + WM8978, 4.7k pull-ups) | One device's stuck SDA locks up all three; software I2C timing is slower than hardware I2C | Bus-lockup recovery (9 clock pulses + STOP) in the bus module; per-device timeout, never unbounded retry | open | driver contract for the I2C bus module |
| KI-04 | GT9147 software I2C rides on resistive-touch nets (SCL=T_SCK PB0, SDA=T_MOSI PF11) and its RST shares PC13 with XPT2046 T_CS | Touch driver must know which panel type is mounted; driving PC13 during XPT2046 ops corrupts either device | Panel type detection first (ALIENTEK BSP pattern); serialize touch operations | open | gt9147 driver contract |
| KI-05 | ALIENTEK example code redirects printf via Keil `fputc` (MDK semihosting bypass); GCC/newlib needs `_write` instead | Porting BSP code to the CMake+GCC project compiles but printf goes nowhere (or hardfaults via semihosting stubs) | Implement `int _write(int fd, char *ptr, int len)` looping over USART1; keep example `fputc` as reference only | mitigated (rule recorded) | troubleshooting.md §4d |
| KI-06 | JTAG-capable pins (PB3, PB4, PA13–PA15) carry on-board pull networks; JTAG is enabled by default at reset | Using PB3/PB4 for SPI1 or PA15 as GPIO silently fails until JTAG is disabled | Disable JTAG via SYSCFG (not AFIO — this is F4, not F1) in BSP init; document any pin taken from the debug port | open | pin-resource-table.md |
| KI-07 | PC14/PC15 carry the 32.768 kHz RTC crystal | Never usable as GPIO; sourcing code that touches them breaks RTC | Treat as reserved | fixed (reserved by design) | pin-resource-table.md |
| KI-08 | Example BSP is Keil/MDK layout (USER/SYSTEM/HARDWARE) with HALLIB vendored per-project | Not CMake/CubeMX-buildable; cannot be used as a devloop/eval fixture as-is | Use as BSP reference only; port SYSTEM/usart.c (with KI-05 fix), delay.c, drivers into the CMake project | open | toolchain-profile.md |

## Errata Conclusions

One entry per errata item related to a change; conclusions must trace to the errata sheet for the current part number and silicon revision (see `project-facts.md`). A different family/revision or community experience is not a substitute.

| Errata item (doc + ID) | Silicon revision | Classification | Evidence / ST workaround | Follow-up |
| --- | --- | --- | --- | --- |
| none yet | `TBC` (revision not yet read from chip) | pending | silicon revision unknown — errata screening blocked | read revision via debugger, fetch STM32F407/417 errata sheet, screen against clock/DMA/FSMC usage |

If the revision ID or matching errata cannot be obtained: keep the item **pending**, mark affected hardware validation incomplete, and add it to Open Risks below.

## Decision Records

| Date | Decision | Context and options | Rationale | Reversible? (how) |
| --- | --- | --- | --- | --- |
| 2026-09-22 | Keep ALIENTEK software-I2C scheme for both GT9147 (PB0/PF11) and MPU6050 bus (PB8/PB9) instead of migrating to hardware I2C | (a) port BSP bit-bang drivers as-is; (b) rewrite on HAL I2C1 | Board routing fixes GT9147 on non-I2C touch nets — hardware I2C is impossible there; MPU6050 bus works either way but one consistent soft-I2C bus layer (bus/device/BSP split) keeps drivers portable and matches proven BSP | yes — swap bus interface implementation behind the driver contract |
| `<date>` | `TBC` | `TBC` | `TBC` | `TBC` |

## Hardware-State Migrations (option bytes / RDP / TrustZone / OTP)

Only with explicit user authorization and a backup of current values. One row per change; a development-board success is not production evidence.

| Date | What changed | Old value (backed up where) | New value | Recovery method | Validation done |
| --- | --- | --- | --- | --- | --- |
| — | none performed | — | — | — | — |

## Open Risks

| Risk | Likelihood / impact | Trigger to revisit | Owner |
| --- | --- | --- | --- |
| Silicon revision unrecorded → errata screening pending (KI/Errata table) | medium / medium | before any clock, DMA, FSMC, or low-power change ships | project owner |
| Jumper positions on the physical board unverified against this file | medium / low (wrong COM port or silent peripheral) | every new session using UART/USB/CAN | project owner |
| `TBC` | `TBC` | `TBC` | `TBC` |
