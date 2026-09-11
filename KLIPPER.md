# BMCU on Klipper

As of September 2026, BMCU-on-Klipper has gone from "interesting reverse-engineering project" to multiple working implementations.

This page separates the main approaches because they solve slightly different problems.

## Option 1: BMCU-Klipper

**Project:** [jarczakpawel/BMCU-Klipper](https://github.com/jarczakpawel/BMCU-Klipper)

**Current release:** v1.0.2 (2026-09-09)

This is the project to look at first if you want BMCU to behave like a real multi-material source manager on a Klipper machine.

### Current scope

- Generic Klipper support (Voron, VzBot, custom machines)
- dedicated Snapmaker U1 integration
- load/unload
- refill handling
- prestaging
- tip forming
- source planning
- per-channel routing to endpoints/toolheads
- web panel
- firmware/update tooling
- OrcaSlicer-oriented workflow support

The upstream README describes Snapmaker U1 as fully completed and Generic Klipper as continuously developing.

### Connection

There are two common BMCU mainboard styles.

#### BMCU with built-in USB / CH340

Connect the BMCU to the Klipper host over USB.

#### TTL BMCU

Use an external CH340 USB-UART adapter:

```text
BMCU GND -> CH340 GND
BMCU TX  -> CH340 RX
BMCU RX  -> CH340 TX
```

For normal operation, do **not** connect the CH340 power line to BMCU.

### Power

BMCU still requires **24 V power**. The upstream documentation recommends a separate 24 V supply as the cleanest/safest option.

Do not assume USB powers the BMCU motors/controller just because the serial connection is USB.

### Host/UI

The project installs a host-side service and exposes a web panel on port `8291`.

See upstream documentation before installing:

- [Main README](https://github.com/jarczakpawel/BMCU-Klipper)
- [Connection guide](https://github.com/jarczakpawel/BMCU-Klipper/blob/main/docs/CONNECTION.md)
- [Generic Klipper setup](https://github.com/jarczakpawel/BMCU-Klipper/blob/main/printers/Generic/README.md)

## Option 2: klipper-bmcu-libre

**Project:** [kurtjcu/klipper-bmcu-libre](https://github.com/kurtjcu/klipper-bmcu-libre)

This is a different idea: modify the 370C firmware so the board exposes a normal serial interface that Linux/Klipper can use directly.

### Requirements

- BMCU **370C**
- Type-C mainboard with onboard **CH340**
- Hall-sensor V2 subboard
- USB-C cable to the Klipper host

The project specifically targets the Type-C/CH340 board. It does not need an external RS485 adapter or ESP32 bridge.

### What changes

Stock/community Bambu-oriented firmware speaks BambuBus, which uses unusual serial requirements (RS485, 1.25 Mbaud and 9-bit framing).

klipper-bmcu-libre replaces that communication layer with:

```text
USB-C -> CH340 -> 115200 8N1 UART -> CH32V203
```

The BMCU still handles the four feeder channels, Hall sensors and local motor control. Klipper talks to it through a custom extra.

### Best fit

This project was built around feeder-assist/toolchanger usage (StealthChanger / Tapchanger / klipper-toolchanger).

It provides channel run/stop/status/speed/direction and jam/runout monitoring, but it is **not mainly a full color/material planning system**.

## Earlier project: cuihuir/bmcu_klipper

**Project:** [cuihuir/bmcu_klipper](https://github.com/cuihuir/bmcu_klipper)

This work contains useful protocol analysis:

- BambuBus packet parsing
- CRC8 / CRC16 details
- A1 Mini captures
- 9-bit UART investigation
- initialization experiments

Its documented state is still prototype/reverse-engineering territory, including transmit-side 9-bit UART problems. It is worth reading if you want to understand BambuBus, but the newer projects above are much closer to "install this and use it."

## Which one should I use?

| Goal | Best place to start |
| --- | --- |
| Full multi-material behavior on a normal Klipper printer | **BMCU-Klipper** |
| Snapmaker U1 + BMCU | **BMCU-Klipper** |
| Voron/VzBot custom multicolor routing | **BMCU-Klipper** |
| Toolchanger secondary feeder / buffer assist | **klipper-bmcu-libre** |
| Learn/reverse-engineer BambuBus | **cuihuir/bmcu_klipper** |

## Important warning

These projects do **not** use identical firmware.

Do not flash firmware from one project and then follow the host/config instructions from another unless that project explicitly says they are compatible.

Also keep the BMCU's 24 V power wiring separate from the USB/UART signal wiring unless the specific hardware documentation says otherwise.
