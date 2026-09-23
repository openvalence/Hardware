# OSSM Flagship -- BOM cost (priced from the schematic, 2026-09-23)

Priced line by line from the KiCad BOM export (89 lines) on 2026-09-23: LCSC/JLCPCB first,
DigiKey where LCSC does not stock a part, shop.m5stack.com for the two M5Stack modules.
Point-in-time list prices; re-pull before ordering. Assembly is not included (hand-soldered).
The full priced table lives outside the repo (regenerable); the numbers that matter are here.

| Per board | Parts only | Parts + PCB |
|---|---|---|
| 5 boards | $115.88 | $134.28 (PCB $18.40/board) |
| 40 boards | $108.03 | $110.83 (PCB $2.80/board, the $112 JLCPCB quote) |

Both totals EXCLUDE R509-R511 (3x Vishay D2TO35M 24R), which no distributor listed under
that exact part number. At a rough $8/unit that adds about $24/board.

## Cost drivers (per board, 40-board run)

| Part | Qty | $/board | Note |
|---|---|---|---|
| PSMN3R7-100BSE(J) power FET | 5 | $47.80 | ~41 % of parts. LCSC lists $9.56-9.77 but is OUT OF STOCK; DigiKey lists $5.21 on backorder |
| M5Stack Stamp-P4 | 1 | $12.95 | official store |
| LTC4364IS-1#PBF | 1 | $9.57 | DigiKey (LCSC stocks only other variants) |
| M5Stack Stamp-AddOn C6 | 1 | $7.00 | official store |
| GRM32ER72A225KA35L 2.2 uF 100 V | 14 | $3.61 | |
| INA228 | 1 | $2.96 | DigiKey, 0-stock at pull |
| Nano2 453 15 A fuse | 1 | $2.60 | |
| TPS48111LQDGXRQ1 | 1 | $2.28 | |
| EEH-ZA1J101P 100 uF 63 V | 4 | $2.14 | see open item: exact part not found |
| TPS54560BDDAR | 3 | $2.10 | |

## Open before ordering

- **Bulk cap C1/C2/C5/C6:** EEH-ZA1J101P (100 uF 63 V, 10 x 10.2) was not found at any
  distributor; the price above is the 50 V part, which is NOT acceptable (the LTC4364
  output clamp is 51.5 V). Confirm the 63 V part exists or pick the real 63 V value/can.
- **Regen resistors R509-R511:** confirm the D2TO35M 24R ordering code and price.
- **Power FETs:** the one-BOM-line choice puts a linear-SOA part in all five positions,
  and it is the single biggest cost and a stock risk. Only the input hot-swap FET (Q1)
  needs linear-mode SOA; the ideal-diode, motor-switch and regen positions may take a
  cheaper standard 100 V FET. A design ruling, not a pricing fix.
- Thin stock at pull time: L2, L3, R1/R401 shunts, R412 (12-30 units).
