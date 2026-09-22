# OSSM Flagship -- board spec (working document)

Status: DRAFT 2026-09-22. Every line marked `[assumed]` came from the bench
firmware bring-up, not from a ruling; edit in place and drop the tag.

## What it is
The controller for a capstan-drum linear stroke machine: runs Nucleus (Valence
hub) on an ESP32-P4 with the ESP32-C6 as the WiFi 6 peripheral over SDIO, and
drives an external servo drive in encoder-follow mode with quadrature.

## Decisions (fill in)
| Decision | Value | Notes |
|---|---|---|
| P4 module | `[assumed]` P4 + C6 + 32 MB PSRAM + 16 MB flash on-module | bench proved this combination (SDIO 4-bit 40 MHz, PSRAM hex 200 MHz) |
| DC input | ? V, ? A continuous | decides the stack-up and the input protection |
| Motor supply path | pass-through / switched / measured? | if switched or measured, the biggest copper on the board |
| Rails | 5 V (? A), 3.3 V (? A) | buck from the input; 3.3 V for module, transceivers |
| Drive interface | quadrature A/B via 3.3 -> 5 V buffer; RS485 (Modbus) for config | isolated? (drive on its own supply) |
| Encoder input | ? | audit path; the drive reports over Modbus today |
| E-stop | hardware input? | protocol ESTOP is convenience over hardware (Valence H1) |
| Current sense | INA228 + shunt `[assumed]` | catalog gates the power channel on has_current_sensor |
| USB | USB-C, device (serial/JTAG) | rescue path only; deploy is OTA |
| LEDs | 1x addressable `[assumed]` | Flux grammar: color = system, effect = status |
| Connectors | JST-XH for the bench harnesses `[assumed]` | see ../harness |
| Expansion | ? | |
| Stack-up | 6-layer proposed for power | 4-layer 2 oz + two planes is the alternative until the current numbers say otherwise |
| Size | ? mm | drives the fab quote |

## Interfaces the firmware already expects
- SDIO slot 1 to the C6: CLK 43, CMD 44, D0 45, D1 46, D2 47, D3 48, slave reset 42 (bench pins; final pins are this board's call).
- Quadrature out on LP-core-capable GPIO (bench: LPG15 = A, LPG12 = B).
- The module's USB-serial-JTAG on the USB-C.

## Open questions
1. Peak and continuous current on the input, and whether it crosses the board.
2. Isolation on the drive side.
3. Whether the P4's high-speed lanes (USB HS, MIPI) will ever be routed; that is what would justify six layers on signal grounds.
