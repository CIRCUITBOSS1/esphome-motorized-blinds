# ESPHome Motorized Blinds

A DIY motorized blind / roller shade controller built on **ESPHome**, an
**ESP32-C6 Super Mini**, a **ULN2003** driver and a **28BYJ-48** stepper.
Appears in Home Assistant as a `cover` with open / close / position slider.

## Features
- Position-aware cover (0-100%) with state saved across reboots
- Manual calibration: Jog Up/Down, **Set Top Here**, **Set Bottom Here**
- Optional limit switches for one-button **Auto Calibrate**
- Coils power down when idle (no heat, no hum)
- Flash-friendly: position saved only when the motor stops

## Hardware
| Part | Notes |
|---|---|
| ESP32-C6 Super Mini | Needs ESPHome with ESP-IDF framework |
| ULN2003 driver board + 28BYJ-48 (5V) | Powered from 5V, **GND shared** with the ESP |
| 2x limit switches (optional) | Normally-open, one side to GND |

### Wiring
| ULN2003 | ESP32-C6 |
|---|---|
| IN1 | GPIO0 |
| IN2 | GPIO1 |
| IN3 | GPIO2 |
| IN4 | GPIO3 |
| + / VCC | 5V |
| - / GND | GND |
| Top limit (optional) | GPIO4 to GND |
| Bottom limit (optional) | GPIO5 to GND |

Pins are substitutions at the top of `esphome/blind.yaml`. Avoid GPIO8/9/15
(boot strapping pins) and GPIO12/13 (USB). Check the pinout printed on your
specific Super Mini clone.

## Quick start
```bash
pip install esphome
cd esphome
cp secrets.example.yaml secrets.yaml   # edit it
esphome run blind.yaml                 # or blind-auto.yaml with limit switches
```
First flash over USB-C: if the port doesn't appear, hold **BOOT**, tap
**RESET**, release BOOT. Later updates go over Wi-Fi (OTA).

## Calibration
**Manual** (`blind.yaml`)
1. Jog until the blind is fully open, press **1. Set Top Here**.
2. Jog down to the closed position, press **2. Set Bottom Here**.

**Automatic** (`blind-auto.yaml`) - press **Auto Calibrate**. The blind runs to
the top switch, then the bottom switch, and stores the travel.

## Troubleshooting
- **Moves backwards:** reverse the pin order (swap IN1<->IN4 and IN2<->IN3 in the substitutions).
- **Buzzes but doesn't turn:** lower `max_speed` (try `300 steps/s`) or check coil wiring order.
- **Position drifts:** steppers have no feedback; recalibrate occasionally or add the top limit switch.
- **No USB logs:** the config sets `hardware_uart: USB_SERIAL_JTAG` for the C6's native USB.

## Roadmap
- [ ] NEMA 17 + TMC2209 variant for heavier blinds
- [ ] Printable mounts / gear files
- [ ] Stall-based homing (TMC2209 StallGuard)
- [ ] Schedules and light-sensor automations

## License
MIT. Attribution: calibration flow inspired by a public ESP32-S3 + Blynk
roller blind project (CC BY-SA). No files from that project are included here.
