# hardware

Hardware for the Valence ecosystem: the **OSSM Flagship** controller board
(ESP32-P4 + ESP32-C6, runs [Nucleus](https://github.com/openvalence/Nucleus)),
and everything physical around it: harnesses, panels, enclosure parts, bench
fixtures, and the reference wiring for the drive.

Design tool: **KiCad** (native text formats, diffable). Vendor datasheets and
manuals live under `reference/` and are not tracked (see `.gitignore`); the
board documents what it needs from them.

## Layout

| Path | What |
|---|---|
| `flagship/` | The OSSM Flagship board: KiCad project, fabrication outputs, BOM |
| `harness/` | Cable and connector drawings (JST-XH pinouts, RS485, quadrature) |
| `enclosure/` | Mechanical: enclosure and mounting |
| `bench/` | Fixtures and jigs used to test boards |
| `reference/` | Datasheets, untracked |

## License

CERN-OHL-S v2 (see `LICENSE`). Firmware and protocol live in their own repos
under [openvalence](https://github.com/openvalence).
