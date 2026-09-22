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
| DC input | 36 V bus via XT30; continuous current TBD | the 57AIM30's supply; the INA228 (5 mOhm shunt, 32.768 A full scale, ADCRANGE=0) monitors this bus |
| Motor supply path | measured (INA228 high-side on the 36 V bus) and pass-through to the drive; the brake chopper hangs on this bus | the biggest copper on the board |
| Rails | 5 V (? A), 3.3 V (? A) | buck from the input; 3.3 V for module, transceivers |
| Drive | 57AIM30 integrated closed-loop servo, encoder-follow (quadrature in), Modbus RTU over RS485 (19200 factory / 115200 reprogrammed; see the machine notes) | 2:1 reduction to a 25 mm capstan drum, 2048 steps/rev at the motor |
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
| Braking resistors | ON the board, top side, heat through filled+capped thermal via arrays to a back copper landing under the heatsink | ruled 2026-09-22: single-sided assembly; land the via array on the resistor's return terminal (DC-) so the back pour is quiet DC, not the switched node |
| Assembly | single-sided, everything on top | ruled 2026-09-22 |
| Vias | filled and capped throughout (via-in-pad allowed) | ruled 2026-09-22; a fab option, budget it in BOM.md |
| Brake chopper | ON THIS BOARD: a shunt regulator across the 36 V bus into the resistor bank (own design, cross-checked against a reference chopper schematic for the numbers) | the 57AIM30 is an integrated servo with no external-resistor terminals; its 0x15 overvoltage alarm is what the chopper prevents |
| Motor power connector | XT30 | 30 A class, brake pulses |

## Brake resistor sizing case (2026-09-22)
Measured on the bench rig: the drive's chopper does not fire on normal
deceleration (the bus capacitors absorb it); it fires only under an OVERHAULING
load -- the carriage pushed in the direction it is already moving -- and then for
at most half of each back-and-forth cycle. Size the bank for that:
- peak while pushed: P = F_push x v_max (e.g. 100 N x 0.5 m/s = 50 W);
- cycle average: half of the peak, only while someone is fighting the machine;
- normal use: ~0 W.
The chopper threshold sits just above the 36 V bus and, with the bank's resistance, sets the true peak (V_chop^2 / R). A 40 x 40 x 20 mm sink is ~3-4 C/W in still air (fine for the pulses, thermal mass
carries seconds of pushing) and ~1 C/W with the 40 mm fan the footprint accepts,
which covers sustained abuse. Policy hook: INA228 die temperature over a threshold
-> Flux amber and a lower speed ceiling, which reduces the pushed power at the
source (P = F x v).
Respin insurance: bring the brake net to two pads in the edge ring with the
on-board bank behind a removable link, so an external resistor can replace the
bank without a new board.

## Interfaces the firmware already expects
- SDIO slot 1 to the C6: CLK 43, CMD 44, D0 45, D1 46, D2 47, D3 48, slave reset 42 (bench pins; final pins are this board's call).
- Quadrature out on LP-core-capable GPIO (bench: LPG15 = A, LPG12 = B).
- The module's USB-serial-JTAG on the USB-C.

## Open questions
1. Continuous current on the 36 V input, and the chopper's threshold and the bank's resistance (peak = V_chop^2 / R).
2. Thermal pad rating and thickness under the sink for the bus voltage; heatsink grounded or floating.
3. Whether the P4's high-speed lanes (USB HS, MIPI) will ever be routed; that is what would justify six layers on signal grounds.
