# OSSM Flagship -- BOM cost (priced from the schematic, 2026-09-23)

Totals were re-derived after the FET and bulk-cap swap (SPEC 2026-09-23 rows): prices marked ~ are the
original pull minus the old FET line plus the new one. Purchasing split: JLCPCB places the cheap passives,
pricey chips are hand-soldered from DigiKey (SPEC).

Priced line by line from the KiCad BOM export (89 lines) on 2026-09-23: LCSC/JLCPCB first,
DigiKey where LCSC does not stock a part, shop.m5stack.com for the two M5Stack modules.
Point-in-time list prices; re-pull before ordering. Assembly is not included (hand-soldered).
The full priced table lives outside the repo (regenerable); the numbers that matter are here.

| Per board | Parts only | Parts + PCB |
|---|---|---|
| 5 boards | ~$75.69 | ~$94.09 (PCB $18.40/board) |
| 40 boards | ~$66.15 | ~$68.95 (PCB $2.80/board, the $112 JLCPCB quote) |

Totals include the regen bank (24 x AS122WJ0201T4E 200R 2512, LCSC C2770484, ~$0.08 each, ~$2/board) and the
fan driver and filter, the bus OVP trip, the connector protection and the external E-stop (~$1.30 together). The optional ACCESSORIES section adds about $2.50 in
passives, polyfuses and ESD plus the placeholder connectors (XH/GH/SH/2x6, ~$1.50) and is not in the totals above. Assembly fees
(JLC's per-extended-part loading fee matters at 5 boards) are not included.

## Cost drivers (per board, 40-board run)

| Part | Qty | $/board | Note |
|---|---|---|---|
| IPB042N10N3G power FET (was PSMN3R7-100BSE at $47.80/board) | 5 | $2.38 | LCSC C69300, ~21.6k stock; $3.08/board at 5 boards |
| M5Stack Stamp-P4 | 1 | $12.95 | official store |
| LTC4364IS-1#PBF | 1 | $9.57 | DigiKey (LCSC stocks only other variants) |
| M5Stack Stamp-AddOn C6 | 1 | $7.00 | official store |
| GRM32ER72A225KA35L 2.2 uF 100 V | 14 | $3.61 | |
| INA228 | 1 | $2.96 | DigiKey, 0-stock at pull |
| Nano2 453 15 A fuse | 1 | $2.60 | |
| TPS48111LQDGXRQ1 | 1 | $2.28 | |
| EEH-ZA1J820P 82 uF 63 V | 4 | $2.96 | DigiKey $0.74 @100; ~$4.68/board at 5 boards (20-pc break interpolated from $1.62 @1) |
| TPS54560BDDAR | 3 | $2.10 | |

## Open before ordering

- Thin stock at pull time: L2, L3, R1/R401 shunts, R412 (12-30 units).
