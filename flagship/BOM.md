# OSSM Flagship -- BOM cost (rough, 2026-09-22)

Assumptions: the `[assumed]` rows in SPEC.md; qty 5; JLCPCB-class fab and
assembly; small-quantity distributor pricing. A quote replaces this table the
day the schematic exists.

| Line | Qty 5, per board |
|---|---|
| P4 + C6 module (PSRAM + flash on-module) | $12-18 |
| Bucks: input -> 5 V (3 A class) and 5 -> 3.3 V, with passives | $6-9 |
| RS485 transceiver, 5 V buffers, ESD, TVS, fuse, reverse-polarity | $5-8 |
| INA228 + shunt | $3 |
| USB-C, JST-XH/PH headers, power terminal, buttons, LED | $6-10 |
| Passives, misc | $3 |
| **Parts** | **$35-50** |
| PCB, 6-layer, ~100 x 100 mm, 1 oz | $12-20 |
| PCB alternative, 4-layer, 2 oz outer | $8-12 |
| Assembly, two-sided, extended-part fees included | $30-60 |
| **All-in, qty 5** | **$80-130** |
| All-in, qty 50 | $55-80 |

Optional: isolation on the drive side (RS485 + quadrature through digital
isolators): +$8-12.

Method: rough category sums from bench-era parts, not a parts list. First real
BOM is generated from KiCad with the JLCPCB plugin and replaces this file.
