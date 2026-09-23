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
| 5 boards | ~$70.56 | ~$88.96 (PCB $18.40/board) |
| 40 boards | ~$62.61 | ~$65.41 (PCB $2.80/board, the $112 JLCPCB quote) |

Both totals EXCLUDE R509-R511 (3x Vishay D2TO35M 24R), which no distributor listed under
that exact part number. At a rough $8/unit that adds about $24/board.

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
| EEH-ZA1J820P 82 uF 63 V | 4 | ~$2.14 | placeholder from the 100 uF line; re-price at DigiKey |
| TPS54560BDDAR | 3 | $2.10 | |

## Open before ordering

- **Regen resistors R509-R511:** confirm the D2TO35M 24R ordering code and price.
- Thin stock at pull time: L2, L3, R1/R401 shunts, R412 (12-30 units).
