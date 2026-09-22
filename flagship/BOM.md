# OSSM Flagship -- BOM cost (rough, 2026-09-22)

Assumptions: the record in design-considerations.md and the rulings in SPEC.md; qty 5; JLCPCB-class fab and
assembly; small-quantity distributor pricing. A quote replaces this table the
day the schematic exists.

| Line | Qty 5, per board |
|---|---|
| P4 + C6 module (PSRAM + flash on-module) | $12-18 |
| Input section: reverse-blocking + eFuse/hot-swap at 10 A, TVS, bulk polymer 63 V | $8-14 |
| Motor switch (series FET, gate drive, snubber) | $4-8 |
| Bucks: 36 V -> 5 V (3 A class) and 5 -> 3.3 V, with passives | $6-9 |
| RS485 transceiver, 5 V buffers, ESD, TVS, fuse, reverse-polarity | $5-8 |
| INA228 + shunt | $3 |
| XT30, signal connector(s), USB-C, buttons, LED | $6-10 |
| Passives, misc | $3 |
| **Parts** | **$50-70** |
| PCB, 58 x 58 mm, filled+capped vias (via-in-pad), 2 oz outer: 4-layer | $10-16 |
| same, 6-layer | $16-24 |
| Regen shunt: clamp (comparator/ref, gate drive, FET, snubber) + two TO-263 power resistors + thermal pad | $10-18 |
| Assembly, two-sided, extended-part fees included | $30-60 |
| **All-in, qty 5** | **$95-150** |
| All-in, qty 50 | $70-95 |

Optional: isolation on the drive side (RS485 + quadrature through digital
isolators): +$8-12.

Method: rough category sums from bench-era parts, not a parts list. First real
BOM is generated from KiCad with the JLCPCB plugin and replaces this file.
