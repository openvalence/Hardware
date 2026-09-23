# OSSM Flagship -- decisions ledger

The reasoning, the measurements, and the option-by-option design record live in
`design-considerations.md` (moved from the Nucleus repo 2026-09-22; its numbers
came from the 2026-09-19/20 bench and their status home is the dev board). This
file is only the ledger of RULINGS, dated, and where a ruling supersedes that
document's "likely" option it says so. Do not restate the record here.

## Fixed facts (from the record)
- Target motor: **YZ 60AIM40F**, 200 W, 7 A. The board is rated to also drive
  the 350 W **60AIM40** at 9.5-10 A: power path **10 A continuous** (section 2).
- Supply: **Mean Well GST360A36**, 36 V; its OVP trips at 37.8-48.6 V and
  latches, which is the real bus ceiling (section 1).
- Regen, measured: powered 6.8 W mean / 141 W peak (4.8 % duty); unpowered
  back-drive 13.6 W mean / 189 W peak; hard throw back-EMF 58.6 V (section 1).
- Shunt clamp threshold ~45 V, below the OVP, which requires the input section
  to block reverse current (section 5). Sizing ~7 W continuous / ~150 W peak
  once the motor switch removes the unpowered case.
- Motor ports: 6-way power+motion (+V, GND, PU+/-, DIR+/- carrying A/B in
  quadrature mode) and a 10-way comms/status block (section 11).

## Rulings
| Date | Ruling | Relation to the record |
|---|---|---|
| 2026-09-22 | Board 58 x 58 mm | new |
| 2026-09-22 | Heatsink **40 x 40** on the BACK, centered; screws in the edge ring | supersedes section 9's "likely" 50 x 50 on a twist-on cap; the record itself notes 40 x 40 as the NEMA 23 common size |
| 2026-09-22 | Through-hole ONLY in the 9 mm edge ring (XT30 and connectors that cannot be SMD); the central 40 x 40 stays free for the sink | consistent with section 11 (XT30 is the one through-hole part that survives the all-SMD rule) |
| 2026-09-22 | **Single-sided assembly, everything on top**; shunt resistors on top over filled+capped thermal via fields; filled+capped vias throughout (via-in-pad allowed) | supersedes section 9's "factory reflows the top, operator hand-solders the back" |
| 2026-09-22 | Regen shunt on-board (two TO-263 over the via field) with the external-resistor connector and the parallel/replacement solder jumper | as section 5's "likely" |
| 2026-09-22 | RS485 via a 3.3 V transceiver (THVD1450/MAX3485 class) from the P4 UART in RS485 half-duplex mode; NOT isolated (the drive's comms side is opto-isolated and 5 V-fed from this board); quadrature via 74AHCT125 | new detail under section 11 |
| 2026-09-22 | XT30 for the 36 V bus; signals in a separate shell | as section 11 |
| 2026-09-22 | Stack-up: 6-layer, mostly pours, valued as heat spreading as much as routing; 4-layer 2 oz remains the alternative until the fab quote and the thermal numbers argue | superseded 2026-09-22 (below) |
| 2026-09-22 | **Fab spec, quoted at JLCPCB:** 6-layer, **1 oz every layer**, TG155, 1.6 mm, white mask / black silk, epoxy filled and capped vias (free at 6 layers), **0.3 mm via drill** throughout, OSP finish with the fab vacuum-packing in groups so unopened boards keep, or ENIG for ~$20 more on the order; finish not yet chosen. 40 pcs quoted at $112 (5 pcs was ~$20 less) | supersedes the 6-layer "leaning" row and the 2 oz outer proposal in `stackup.md`; arithmetic there re-run for 1 oz |
| 2026-09-22 | First batch is hand-soldered by the operator; group-buy boards are assembled from the same 40; **parts purchase on hold**; assembly cost decided when gerbers exist | new |
| 2026-09-22 | **Power connectors: two XT30, right-angle (XT30PW-M), bus IN and bus OUT to the motor**, in the edge ring | as section 11 (XT30 for power); fixes the count at two |
| 2026-09-22 | **Stamp-P4 centered on one edge, USB-C facing out of that edge** ("down" in board view) | new; section 8 names the stamp but not its position |
| 2026-09-22 | **Bulk capacitance symmetric about the stamp's centerline**: SMD aluminum electrolytic (polymer) plus MLCC, mirrored left/right | section 7 detail; electrolytics are SMD cans so the through-hole ruling holds |
| 2026-09-22 | **Signal connectors: JST GH (1.25 mm, latching), right-angle SMD (SMxxB-GHS-TB)**: drive motion pairs, drive comms/RS485, fan, thermistor | section 11's "separate signal connector", now named |
| 2026-09-22 | Stamp-AddOn C6 is a daughterboard stacked on top of the stamp; its antenna sits above the stamp, so the radio sets no board-edge or copper-keepout constraint. Keep tall metal (electrolytic cans) from standing above the add-on's height beside it | resolves the antenna open item |
| 2026-09-22 | Harness: pre-crimped GH leads on the board end, operator crimps the far end | new |
| 2026-09-22 | **Motion is emitted by the P4 (LP-core quadrature); no RP2350 on the board** | supersedes section 8's "plus an RP2350 for motion"; operator dropped it after the P4 LP-core emitter proved out on the bench |
| 2026-09-22 | **Passives: common, slightly larger sizes.** 0805 default, 1206 for power, bulk and high-voltage positions, nothing below 0603. Operator picks connectors; the schematic carries generic connector placeholders until then | new |
| 2026-09-22 | **Input protection latches off on a fault (LTC4364-1), with a red fault LED powered from the raw input and lit by FLT.** A latched fault kills every rail including the MCU's, so the indicator cannot depend on the board | new; section 3 detail |
| 2026-09-22 | **Candidate ICs accepted for detailing:** LTC4364, TPS48111, TLV1805 + TLV431, 3x TPS54560B, INA228, THVD1450, 74AHCT125 | new |
| 2026-09-22 | **Logic power comes from the stamp's own rails**: +5V_SYS = stamp SYS_5V (pad 39, its OR of VIN and USB-C) for the 74AHCT125, the drive's 485_5V and the status LED; +3V3_SYS = stamp SOC_3.3V (pad 28) for the INA228, the RS485 transceiver and every pull-up. A USB-only bench session therefore emits quadrature. The 3.3 V buck becomes accessory-only (+3V3_ACC); the 5 V buck still feeds the stamp's VIN | new; section 10 detail. Also removes pull-ups to a rail that could be dead while the P4 is alive |
| 2026-09-22 | **Rails detailed** per the TPS54560B datasheet design procedure (values on the Rails sheet): 400 kHz, 4 A per rail, VIN 20-48 V, UVLO start 20 V / stop 17 V | section 10 |
| 2026-09-22 | **Schematic drawing rule: wire it, don't label it.** Discrete parts are wired to the IC pins they serve and each section reads as one drawn circuit. Labels only where a net leaves the sheet (power symbols for rails, global/hierarchical labels for signals). A sheet of label-to-label connections is not done | new; applies to every sheet |
| 2026-09-22 | **Input fuse: Nano2 453 15 A very-fast (65 V DC, 150 A interrupting), SOLDERED, first in the path after BUS IN.** Supersedes the same-day OMNI-BLOK socket: the GST360A36 (10 A, hiccup-mode protection) cuts out on a short long before this fuse would blow, so it only ever acts with a stiffer source (bigger supply, battery), and a part that almost never blows gains nothing from a socket that sat at its own 10 A rating. About 3 mm tall. **No polyfuse**: the LTC4364 current limit plus the TPS48111 trip are the resettable protection | new; section 3 |
| 2026-09-22 | **Motor switch detailed** per TPS48111 datasheet Figure 9-14 (values on the sheet): 2 mOhm sense, over-current 15.2 A / 10 ms, short-circuit 30 A / 1.2 us, **latch-off** on over-current (firmware re-arms by toggling MOTOR_EN; veto-able: auto-retry is one resistor removed), pre-charge 100 R via Q403 and Q402's body diode, UVLO ~23 V, MMBT3904 over-temperature beside the FETs, SMBJ48CA on the output. INA_ALERT_N now pulls down a bus-powered EN divider, so an unpowered INA defaults the switch off | section 4 |
| 2026-09-22 | **Regen shunt detailed**: TLV1805 + TLV431 self-acting clamp, ON 44.7 V / OFF 41.7 V (348k / 10k / 1.40M), 1 nF divider filter, 47 R gate drive, the 10k + 3.3k CLAMP_MON divider doubles as the gate pull-down (clamp off whenever +12V is down), SHDN tied low (always armed), SMBJ48A across the switch | section 5 |
| 2026-09-22 | **Connectors are placed by the operator during layout.** Schematic sheets end every off-board signal in a named net (global label); existing generic connector placeholders are disposable | new |
| 2026-09-22 | **Drive IO detailed**: THVD1450 (DE and /RE tied, 120 R termination DNP), SM712 RS485 ESD at the cable end, KiCad 74AHCT125 per-gate symbol (gates 1-2 drive DRV_PU+ / DRV_DIR+, gates 3-4 disabled), status inputs 10k pull-up to +3V3_SYS + 1k series into the P4 | sections 11-12 |

## Open (from the record's section 13, plus today)
- eFuse coverage at 10 A / 36 V (section 13).
- LPG0-15 to GPIO map before committing A/B pins (section 13).
- INA228 bus placement vs the C6's I2C (section 13).
- What limits the drive to 4.1-4.5 A when the 60 series runs to 7 A (section 13).
- Thermal pad rating and thickness under the sink; sink grounded or floating.
- Regen shunt pulse energy: 189 W peak x half-stroke time against the PWR263S-35 pulse curve, or a wirewound TO-263 (stackup.md section 5).
- TIM and heatsink part choice; JLC plated-wall thickness for the via arithmetic.
- Accessory power output ("a few amps" at 12 V, section 10) exceeds JST GH's ~1 A per pin: needs its own connector class.
- External regen-resistor connector (section 5): ~4 A pulses at the ~45 V clamp; not a GH job either. Third XT30 or a latching 2-pin power connector.
- Motor-switch FETs: Q401/Q402 100 V, D2PAK class, low Rds(on), Qg within the 470 nF bootstrap budget; Q403 100 V SOT-23 >= 1 A pulse. Generic Q_NMOS symbols until picked (their D/G/S pin letters need the real part's numbered pads).
- Regen clamp FET Q501 (100 V, >= 12 A with an external resistor in parallel) and the TO-263 resistor part (pulse-energy item above).
