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
| DC input | ? V, ? A continuous, via XT30 | decides the stack-up and the input protection |
| Motor supply path | pass-through / switched / measured? | if switched or measured, the biggest copper on the board |
| Rails | 5 V (? A), 3.3 V (? A) | buck from the input; 3.3 V for module, transceivers |
| Drive interface | quadrature A/B via 74AHCT125 (5 V out); RS485 via a 3.3 V transceiver (THVD1450/MAX3485 class) driven by the P4 UART in RS485 half-duplex mode | NOT isolated on this board: the drive's Modbus side is already opto-isolated and is 5 V-powered from this board's buck; run is a few cm (ruled 2026-09-22) |
| Encoder input | ? | audit path; the drive reports over Modbus today |
| E-stop | hardware input? | protocol ESTOP is convenience over hardware (Valence H1) |
| Current sense | INA228 + shunt `[assumed]` | catalog gates the power channel on has_current_sensor |
| USB | USB-C, device (serial/JTAG) | rescue path only; deploy is OTA |
| LEDs | 1x addressable `[assumed]` | Flux grammar: color = system, effect = status |
| Connectors | JST-XH for the bench harnesses `[assumed]` | see ../harness |
| Expansion | ? | |
| Stack-up | 6-layer proposed for power | 4-layer 2 oz + two planes is the alternative until the current numbers say otherwise |
| Size | 58 x 58 mm | ruled 2026-09-22 |
| Heatsink | 40 x 40 mm on the BACK, centered | standard fan/heatsink footprint; screws in the 9 mm ring |
| Through-hole keep-out | none in the central 40 x 40; through-hole only in the 9 mm edge ring (XT30, connectors that must be through-hole) | ruled 2026-09-22 |
| Braking resistors | ON the board, cooled by the back heatsink | decide: resistors on the bottom under the sink (thermal pad on the bodies) vs on top with filled+capped thermal vias |
| Brake chopper | on the drive (external-resistor terminals) or on this board? | decides whether the board is two fat traces (4-layer 2 oz) or a power stage (6-layer) |
| Motor power connector | XT30 | 30 A class, brake pulses |

## Interfaces the firmware already expects
- SDIO slot 1 to the C6: CLK 43, CMD 44, D0 45, D1 46, D2 47, D3 48, slave reset 42 (bench pins; final pins are this board's call).
- Quadrature out on LP-core-capable GPIO (bench: LPG15 = A, LPG12 = B).
- The module's USB-serial-JTAG on the USB-C.

## Open questions
1. Peak and continuous current on the input, and the brake pulse current.
2. Resistors under the sink (bottom) vs thermal vias (top, filled+capped): decides a fab option.
3. Insulation under the heatsink: thermal pad rating for the bus voltage; solder mask is not insulation.
4. Whether the P4's high-speed lanes (USB HS, MIPI) will ever be routed; that is what would justify six layers on signal grounds.
