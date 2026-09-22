# OSSM Flagship -- stackup and the power/thermal arithmetic

Every number here is ARITHMETIC dated 2026-09-22, not a measurement. The
measured inputs come from `design-considerations.md` (regen 6.8 W mean /
141 W peak powered, 13.6 W mean / 189 W peak unpowered; 10 A path). The fab
numbers come from JLCPCB's published multilayer capabilities the same day.
The KiCad project (`ossm-flagship.kicad_pcb`, Board Setup) carries the same
values; this file is why they are what they are.

## 1. Fab constraints entered in Board Setup (JLCPCB multilayer, 1 oz, as quoted 2026-09-22)

| Constraint | Value | Source |
|---|---|---|
| Min trace / space, 1 oz | 0.1 / 0.1 mm | JLC: 3.5/3.5 mil at 1 oz; 0.1 leaves margin |
| Via drill / diameter | 0.3 / 0.6 mm, one size | operator ruling: 0.3 mm vias throughout; 0.15 annular |
| Min annular width | 0.15 mm | JLC absolute min at 1 oz |
| Hole to hole | 0.2 mm | JLC |
| Copper to board edge | 0.3 mm | JLC min 0.2 |
| Via treatment | epoxy filled + capped, tented both sides | free at 6 layers; via-in-pad legal for 0.15 to 0.55 mm diameter |
| Finish | OSP (quoted); ENIG the alternative | OSP ages on the shelf and with handling; ENIG if boards wait for the group buy |
| Thickness, material, colors | 1.6 mm, TG155, white mask, black silk | |

Net classes: `Default` 0.2 mm track; `PWR` 5.0 mm track, 0.3 clearance; `RS485`
0.25 mm pair; every class 0.6/0.3 vias. Custom rules in
`ossm-flagship.kicad_dru` enforce the SPEC.md geometry rulings (through-hole
only in the ring, PWR never below 4.0 mm, 0.3 mm drill floor).

## 2. Stackup entered in the board

Dielectrics are JLC's published `JLC06161H-7628` (the 1 oz / 0.5 oz calculator
stackup: 7628 prepreg 0.2104, core 0.40, 7628 0.2028, core 0.40, 7628 0.2104).
Copper is 1 oz on every layer as quoted. **Re-enter the dielectric rows from
the order page if the TG155 stackup differs.** Nothing on this board is impedance controlled, so the
dielectric numbers only affect the 3D view and the thickness sum.

| Layer | Copper | Job |
|---|---|---|
| F.Cu | 1 oz | every component, the whole 10 A path, the two shunt resistor pads and their via fields |
| In1.Cu | 1 oz | GND |
| In2.Cu | 1 oz | 36 V bus pour (return for the FET switching loops sits on In1) |
| In3.Cu | 1 oz | 5 V / 3.3 V / signal escape |
| In4.Cu | 1 oz | GND |
| B.Cu | 1 oz | bare thermal pour under the 40 x 40 sink, nothing else (rule area `back_face`) |

## 3. Current: the copper is not the problem

IPC-2221, 20 degC rise, 10 A. External I = 0.048 dT^0.44 A^0.725, internal
k = 0.024 (A in mil^2).

| Conductor | Width for 10 A at +20 degC | at +10 degC |
|---|---|---|
| 2 oz external | 2.3 mm | 3.5 mm |
| 1 oz external | 4.6 mm | 7.1 mm |
| 2 oz internal | 6.0 mm | |
| 1 oz internal | 12.1 mm | |
| 0.5 oz internal | 24.2 mm | |

The whole 10 A path (XT30, eFuse, motor switch, INA228 shunt, motor terminal)
fits on F.Cu at 1 oz as a 5 mm pour (4.6 mm is +20 degC, 7.1 mm is +10). Its
resistance is noise: a 5 mm x 40 mm 1 oz path is 8 squares x 0.49 mOhm =
3.9 mOhm, 0.39 W at 10 A. The PWR class defaults to 5.0 mm for this reason.
**Keep the 10 A path on F.Cu and never change layers with it.** Then the via
question below is only about heat.

If it must transition: one 0.3 mm drill via with 20 um plating (JLC plated
wall ASSUMED 20 um; confirm on the capabilities page) is 0.019 mm^2 of copper
and carries ~1.0 A at +20 degC by the internal formula; 0.4 mm drill ~1.3 A.
16 vias of 0.3 mm per 10 A transition.

## 4. Heat: where it actually goes

Sources, worst case (supply unplugged, machine driven): shunt resistors
13.6 W mean total (6.8 W each), motor-switch FET and eFuse ~0.5 W each at 10 A
(5 mOhm class), bucks ~1 W, P4 module ~1 W. About 17 W, 14 of it in two TO-263
tabs. Powered normal case: 6.8 W total in the resistors, ~10 W board.

Path per resistor: tab -> F.Cu pad -> via field -> B.Cu pour -> TIM -> sink.

**Via field.** One 0.3 mm / 20 um via through 1.6 mm: R = L / (k A) =
1.6e-3 / (390 x 1.9e-8) = ~220 K/W. Fill barely matters: epoxy (k ~0.5) adds
nothing; copper-paste fill (k ~20) takes it to ~180 K/W. The count is what
matters:

| Pitch under a ~9 x 7 mm tab | Vias | Field R |
|---|---|---|
| 1.0 mm | 48 | 4.5 K/W |
| 0.8 mm | 80 | 2.7 K/W |

0.8 mm pitch with 0.3 mm drill leaves 0.5 mm hole-to-hole, legal against the
0.2 floor. **Rule: 0.8 mm pitch, the whole tab, both resistors.**

**Board spreading (the 6-layer argument).** Lateral resistance of one copper
sheet from board center to edge, 29 mm over a 58 mm width: R = 0.5 / (k t).
2 oz: 18 K/W. 1 oz: 37 K/W. 0.5 oz: 73 K/W. Stacks in parallel:

| Stack | Lateral R |
|---|---|
| 6 layers, all 2 oz | 3.0 K/W |
| 6 layers, 2 oz outer + 1 oz inner | 4.6 K/W |
| **6 layers, all 1 oz (as quoted)** | **6.2 K/W** |
| 6 layers, 2 oz outer + 0.5 oz inner (JLC default) | 6.0 K/W |
| 4 layers, all 2 oz | 4.5 K/W |
| 4 layers, 2 oz outer + 1 oz inner | 6.6 K/W |

**Six layers of 1 oz spread a little worse than four of 2 oz.** Layer count is a
routing decision, not a thermal one; copper weight is the thermal knob, and
even that is second order next to the via field and the sink, which is why the
1 oz ruling costs nothing that matters in the totals below.

**TIM.** The heat leaves B.Cu through two ~60 mm^2 spots, not the whole
40 x 40. A 0.5 mm gap pad at 3 W/mK is 2.8 K/W per spot; 0.1 mm of grease or a
thin pad is ~0.5 K/W. Use the thin one.

**Sink.** A 40 x 40 x ~10 mm extrusion is ~5 K/W natural convection, ~1.2 K/W
with a 40 mm fan (typical catalog values; the chosen part's curve replaces
these).

**Totals, resistor tab above ambient:**

| Case | Sink rise (13.6 or 6.8 W shared) | Per-resistor (2.7 via + 0.5 TIM) | Tab rise |
|---|---|---|---|
| unpowered, natural | 68 K | 22 K | **~90 K** |
| unpowered, fan | 16 K | 22 K | ~38 K |
| powered, natural | 34 K | 11 K | ~45 K |
| powered, fan | 8 K | 11 K | ~19 K |

PWR263S-35 derates from 35 W at 25 degC case to zero at 155 degC: at a
115 degC tab (25 + 90) it is good for ~11 W, against 6.8 W demanded. Passes on
paper with no fan, in the abuse case, with margin that a hot enclosure eats.
The fan is the answer to that, and the INA228 die temperature is the
witness, as ruled. Without the sink the 58 x 58 board alone is ~25 K/W to air:
the sink is mandatory in every case, not only the abuse one.

## 5. What this does NOT settle

- **Pulse energy.** 189 W peak for a half-stroke is energy the thick-film
  element absorbs before any of the above applies. E = 189 W x t_half. Needs
  the half-stroke time from the bench log against the PWR263S-35 pulse curve;
  a wirewound TO-263 may be the right part instead. Open on SPEC.md.
- Plated wall thickness assumed 20 um.
- Sink and TIM are catalog-typical, not a chosen part.
- No measurement. The first board with the INA228 logging is the stamp.
