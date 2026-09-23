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
| 2026-09-22 | **Sensing detailed**: INA228 at 0x40 on the Kelvin taps, ALERT shared with the motor-switch EN; I2C pull-ups fitted only if the stamp/add-on lacks them; thermistor 10k pull-up + 1k/100n into ADC1; fan supply by solder jumper (12 V or 5 V), tach 10k pull-up + 1k series, **PWM open-drain through a 2N7002** because a fan pulls its PWM input to 5 V and the P4 is not 5 V tolerant (firmware inverts) | sections 10, 12 |
| 2026-09-22 | **Input protection detailed** per LTC4364 datasheet Figure 1: 2.5 mOhm sense (20 A, 10 A folded back into a short), 22 nF HGATE (~40 ms ramp, ~0.4 A inrush, timer idle at start), 100 nF timer (~1 ms in a dead short), **output clamp 51.5 V (402k/10k), deliberately above the 44.7 V regen clamp so regen is never read as a surge by the latching -1**, UV 20 V / OV 50 V (374k/15k/10k), 15 V gate zeners (datasheet, >= 24 V), SMBJ45CA bidirectional input TVS, fault LED resistor 27k (FLT sinks <= 2 mA) | section 3 |
| 2026-09-22 | **Power FETs: Nexperia PSMN3R7-100BSE (100 V, 3.95 mOhm, D2PAK, enhanced linear-mode SOA) in all five power positions** (input Q1/Q2, motor switch Q401/Q402, regen clamp Q501): one BOM line. SOA ~30 A at 36 V for 1 ms (25 C) vs the 10 A folded-back short on Q1. Pre-charge Q403: Diodes DMN10H220L (100 V, SOT-23). Motor-switch bootstrap raised to 1 uF for 2 x 176 nC gate charge | resolves the FET open items |
| 2026-09-22 | **Regen resistors: 3 x Vishay D2TO35M 24 R = 8 R** (operator: 2-3 to spread the load). Multi-pulse rated, element isolated from the tab (2 kV), tabs tied to GND and stitched into the back sink. ~2.3 W each continuous, ~47 W each at the 141 W powered peak | resolves the TO-263 resistor and pulse-energy open items; Q501's own tab is the switch node, not ground |
| 2026-09-22 | **Fan supply jumper JP2 ships bridged to 12 V** (KiCad SolderJumper_3_Bridged12: a copper trace joins pads 1-2); 5 V fans: cut the 1-2 trace, bridge 2-3 with solder | operator; section 10 |
| 2026-09-22 | **Kelvin shunts: Vishay WSK2512 4-terminal** (Bourns CSS2H is 2-terminal) on the T2.21 mm footprint that WSK2512 uses at 1-4.9 mOhm: INA228 R2 1 mOhm (WSK25121L000FEA), motor switch R401 2 mOhm (WSK25122L000FEA), LTC4364 R1 2.5 mOhm (WSK25122L500FEA) now 4-terminal with SENSE and OUT on the taps (OUT is a sense-only pin) | operator ask; sections 3, 4, 12 |
| 2026-09-22 | **Bulk: 2 x 100 uF 63 V hybrid polymer (Panasonic EEH-ZA1J101P, 10 x 10.2 mm) mirrored about the stamp, + 2 x 2.2 uF 100 V X7R 1210 (Murata GRM32ER72A225KA35L)**; the same MLCC is every buck's input cap. 63 V covers the 51.5 V LTC4364 output clamp; ESR damps the ceramics as section 6 intends | section 6 |
| 2026-09-22 | Regen resistor ordering: Vishay D2TO35M, 24 R +-2 % (SAP pattern D2TO035M + 24R00 + G + packaging + E3; D2TO35M range is 10 R-10 k) | refines the regen resistor ruling |
| 2026-09-22 | **Regen clamp revised** (compared against the OSSM alt board's brake chopper): hysteresis 1.4 V, ON 44.5 V / OFF 43.2 V (R503 3.01M); UCC27517 low-side driver + 10 R gate (comparator-driven edges were ~1-2 us, watts in Q501); D503 SS310 (LCSC C15874) freewheel across the resistor bank so trace and J3 lead inductance clamps at the bus; D502 amber LED on the gate; CLAMP_MON goes to a P4 capture pin for on-time counting (power = duty x V^2 / 8 R, published over Valence); one NTC (TH501, SHUNT_TEMP) beside the hottest-placed D2TO35M | supersedes the regen-detailed row's thresholds, gate resistor and monitoring |
| 2026-09-23 | **Regen gate driver removed**: U3 (TLV1805 push-pull, 0.3 us into 4 nF per its datasheet, ~100 mA) drives Q501 directly through R505 10 R. Q_gd <= 77 nC gives ~0.7 us edges, ~0.16 W switching + ~0.1 W conduction at the worst-case ~2.8 kHz clamp cycle. The prior row's "1-2 us, watts in Q501" was wrong. Slow edges also keep L di/dt small (~1 V on board trace), so no snubber pads. D503 stays for the J3 flying leads | corrects the regen-clamp-revised row; the UCC27517 and C503 are out |
| 2026-09-23 | **Shunts moved to LCSC stock** (operator): INA228 R2 = Vishay WSK12161L000FEA 1 mOhm (LCSC C844476), true 4-terminal, LCSC footprint checked against Vishay's land pattern. LTC4364 R1 and motor switch R401 = Bourns CSS2H-2512K-2L00F 2 mOhm 5 W (LCSC C2093761, one BOM line), 2-terminal on `flagship:R_2512_Shunt_PseudoKelvin`: Bourns land, sense pads 2/3 at the inner pad edges, net-tied (DRC-clean). R1 at 2 mOhm moves the LTC limit to 25 A / 12.5 A folded back (was 20 / 10); Q1 SOA margin 2.4x (was 3x). 2.5 mOhm is not in the CSS2H range; 2.3 mOhm exists but LCSC stock is unconfirmed | supersedes the WSK2512 Kelvin row |

## Open (from the record's section 13, plus today)
- LPG0-15 to GPIO map before committing A/B pins (section 13).
- INA228 bus placement vs the C6's I2C (section 13).
- What limits the drive to 4.1-4.5 A when the 60 series runs to 7 A (section 13).
- Thermal pad rating and thickness under the sink; sink grounded or floating.
- TIM and heatsink part choice; JLC plated-wall thickness for the via arithmetic.
- Accessory power output ("a few amps" at 12 V, section 10) exceeds JST GH's ~1 A per pin: needs its own connector class.
- External regen-resistor connector (section 5): ~4 A pulses at the ~45 V clamp; not a GH job either. Third XT30 or a latching 2-pin power connector.
