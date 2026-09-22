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

## Open (from the record's section 13, plus today)
- eFuse coverage at 10 A / 36 V (section 13).
- LPG0-15 to GPIO map before committing A/B pins (section 13).
- INA228 bus placement vs the C6's I2C (section 13).
- What limits the drive to 4.1-4.5 A when the 60 series runs to 7 A (section 13).
- Thermal pad rating and thickness under the sink; sink grounded or floating.
- Regen shunt pulse energy: 189 W peak x half-stroke time against the PWR263S-35 pulse curve, or a wirewound TO-263 (stackup.md section 5).
- TIM and heatsink part choice; JLC plated-wall thickness for the via arithmetic.
