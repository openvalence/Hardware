# OSSM Flagship -- features

A 58 x 58 mm controller for the OSSM that drives a YZ 60AIM40F integrated servo
(and has headroom for the 350 W 60AIM40) from a 36 V supply. It powers the
motor, commands it, protects everything from supply faults and regen, and talks
to the outside world through an ESP32-P4.

The rulings behind every number here live in [SPEC.md](SPEC.md); this page
summarizes them. Where the two disagree, SPEC.md wins.

## At a glance

- 36 V bus in, 10 A continuous to the motor
- Self-contained protection: nothing safety-critical depends on firmware
- Regen clamp on board, with a port for an external brake resistor
- Quadrature motion (A/B) plus RS485/Modbus to the drive
- ESP32-P4 with a stacked ESP32-C6 for WiFi and BLE
- Live bus voltage, current, power and energy telemetry
- Two buttons, one status LED, no reset button needed
- 6-layer board, single-sided assembly, 40 x 40 heatsink on the back

## Power input and protection

Everything between the supply and the rest of the board is self-acting.

- **15 A soldered fuse** as the last-resort dead-short backstop.
- **Input TVS** clamps hot-plug ringing and survives a reversed supply.
- **LTC4364 surge stopper** with back-to-back FETs:
  - rides through input spikes by holding the output at 51.5 V instead of
    cutting out
  - current limit about 25 A, folding back to about 12.5 A into a short
  - reverse polarity and reverse current blocking, which keeps regen from
    ever reaching the power supply
  - undervoltage 20 V / overvoltage 50 V lockout
  - about 40 ms soft start
- **Latches off on any fault** with a red fault LED powered straight from the
  input, so it lights even when the board is dark. Power-cycle to clear.
- **Bulk capacitance:** 4 x 100 uF 63 V hybrid polymer plus X7R ceramics,
  mirrored about the P4 module.

## Regen clamp

Back-driving the motor pumps energy into the bus. The clamp burns it off before
the supply's overvoltage protection trips, with no firmware involved.

- Comparator switches the dump FET on at 44.5 V and off at 43.2 V.
- Load: 3 x 24 ohm Vishay D2TO35M power resistors (8 ohm total), tabs into the
  heatsink. Sized for about 7 W continuous and 150 W peak.
- Freewheel diode across the resistors, TVS across the switch.
- **External brake resistor port** (XT30) with a cut-jumper: add a resistor in
  parallel with the on-board bank, or cut the jumper to use it instead.
- **Amber LED** lights whenever the clamp is dumping.
- **Clamp monitoring:** the P4 times how long the clamp is on (clamp power =
  duty x V^2 / 8 ohm) and reads a thermistor on the resistor bank, so firmware
  can report regen over Valence and back off motion before anything trips.

## Motor output

- **TPS48111 smart high-side switch** between the bus and the motor XT30:
  - pre-charges the drive's capacitors through 100 ohm, so no spark on enable
  - overcurrent trip at 15.2 A for 10 ms, short-circuit trip at 30 A within
    1.2 us, both latching (firmware re-arms)
  - undervoltage lockout about 23 V, over-temperature sensing beside the FETs
  - firmware enable (MOTOR_EN)
- **INA228 power monitor** on a true 4-terminal shunt: bus voltage, current,
  power and energy over I2C. Its alert line is wired straight into the motor
  switch, so a current alarm cuts the motor in hardware, and an unpowered
  monitor defaults the motor off.

## Drive interface

Two connectors on each side of the board, mirrored:

| Side | Power | Signals |
|---|---|---|
| Motor side | XT30, motor power out | 4-pin JST GH: QUAD_A, GND, QUAD_B, GND |
| Opposite side | XT30, external brake resistor | 6-pin JST GH: 5V, GND, 485A, 485B, WR (alarm), RDY |

- **Quadrature** comes from the P4's low-power core, buffered to 5 V
  (74AHCT125) into the drive's opto inputs.
- **RS485/Modbus** through a THVD1450 transceiver with ESD protection and an
  optional 120 ohm termination. The board supplies the drive's 485-side 5 V.
- **Drive status:** alarm (WR) and ready (RDY) inputs. Position comes over
  Modbus, so the encoder index is not wired.

## Rails

- 3 x TPS54560B bucks, 4 A each: 12 V (fan, clamp, accessories), 5 V (feeds
  the P4 module), 3.3 V (accessories).
- Logic runs from the P4 module's own 5 V and 3.3 V, so a USB-only bench
  session still powers the buffer and emits quadrature.

## Compute and user interface

- **M5Stack Stamp-P4 (ESP32-P4)** on one edge, USB-C facing out.
- **Stamp-AddOn C6** stacked on top for WiFi and BLE; its antenna sits above
  the board, so no keepout.
- **HOME button:** press to home, hold to reset.
- **PAIR button:** hold to pair; hold while powering on to start the WiFi setup
  access point.
- **WS2812B status LED**, level-shifted to 5 V for reliable colors.
- **No reset button.** Firmware handles reset from the HOME button, the chip's
  watchdogs catch a hang, and bare EN / BOOT / GND pads allow a tweezer reset
  or forced flashing mode for rescue. USB flashing needs no buttons.

## Sensing and cooling

- Thermistor input for an external temperature probe.
- Fan header, 12 V by default or 5 V by solder jumper, with tachometer input and
  PWM speed control.

## Board

- 58 x 58 mm, 6 layers, 1 oz copper throughout, filled and capped vias.
- All parts on the top side; through-hole parts only in the 9 mm edge ring.
- 40 x 40 mm heatsink on the back under the power parts.
- Connectors: 3 x XT30 (bus in, motor out, external brake resistor), JST GH
  for signals.
