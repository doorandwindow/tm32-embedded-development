# Hardware Profile

Board-level facts: clock tree, debug access, external devices, and known board quirks. All values must come from the schematic, reference manual, or measurement — not from family assumptions. Unconfirmed values stay `TBC`.

## Clock Tree (configured)

| Item | Value | Source (schematic / code / measurement) |
| --- | --- | --- |
| HSE frequency | `<TBC>` | `<TBC>` |
| PLL config (M/N/P) | `<TBC>` | `<TBC>` |
| SYSCLK | `<TBC>` | `<TBC>` |
| AHB / APB1 / APB2 | `<TBC>` | `<TBC>` |
| Flash wait states | `<TBC>` | `<TBC>` |
| Peripheral kernel-clock notes (USB, ADC, RTC/LSE, ...) | `<TBC>` | `<TBC>` |

## Debug and Programming Access

| Item | Value |
| --- | --- |
| Debug interface | `<TBC>` (SWD/JTAG, pin header type) |
| Programmer / debugger | `<TBC>` (ST-LINK V2/V3, ...) |
| Serial console | `<TBC>` (UART, baud, connector) |

## Serial Capture Channel (HIL evidence path)

Drives `scripts/devloop.py` / `serial_capture.py` defaults. COM numbers drift across replugs — always record VID:PID as the primary locator.

| Item | Value |
| --- | --- |
| USB-UART bridge | `<TBC>` (CH340 / CH9102 / FT232 / CP210x / ST-LINK VCP) |
| VID:PID | `<TBC>` (e.g. `1A86:7523` for CH340) |
| Current COM number | `<TBC>` (informational only, re-enumerate to confirm) |
| Baud rate | `<TBC>` (e.g. 115200) |
| DTR/RTS convention | `<TBC>` (default: not asserted; note explicitly if the board resets on DTR) |
| Expected boot banner | `<TBC>` (first line(s) printed at boot — the "alive" baseline for capture checks) |

## External Devices

| Device | Bus / Interface | Key pins | Address / CS | Supply | Notes |
| --- | --- | --- | --- | --- | --- |
| `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` | `<TBC>` |

## Board Quirks and Constraints

- `<TBC>` (level shifters, shared pins with BOOT config, pull-up conventions, RF layout constraints, ...)

## Hardware Profile Change Log

| Date | Item changed | Reason | Confirmed by |
| --- | --- | --- | --- |
| `<date>` | `<TBC>` | `<TBC>` | `<TBC>` |
