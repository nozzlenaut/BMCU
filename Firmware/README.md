# Firmware overview

This folder contains older firmware and flashing material collected during the original BMCU 370C build.

## What is current?

### Bambu-printer firmware

The active community firmware I would start with now is:

**[jarczakpawel/BMCU-C-PJARCZAK](https://github.com/jarczakpawel/BMCU-C-PJARCZAK)**

Current release when this page was refreshed:

- **V10.5**
- released **2026-04-22**
- firmware package: [GitHub Releases](https://github.com/jarczakpawel/BMCU-C-PJARCZAK/releases)

V10.5 changes include automatic unload on manual buffer lift, MCU-UID-based serial numbers, full NVM cleanup during calibration, LED fixes and a P2S external-fan fix.

V10.3+ also introduced first-start empty-channel calibration and automatic Hall polarity calibration. **Boot with all channels empty after flashing.**

### Recommended flasher

Use **[BMCU Flasher](https://github.com/jarczakpawel/BMCU-Flasher)** for current firmware.

It supports Windows, Linux, macOS and Android, and includes an online firmware workflow. The bundled `WCHISPTool.zip` and old PDF instructions in this repo should be treated as legacy/reference material.

## Files in this repo

### `BMCU-370-C-V0.1-0020-beta-A1.bin`

This is a **historical 2025-era firmware image**. It is kept because it was part of this build/research trail, not because it is the current recommendation.

The `Obsolete/` directory contains even older 0013/0019 images.

### `source code/BMCU-370-C-V0.1-0020-beta-A1/`

Historical source snapshot matching the older firmware generation.

For current development, follow the active upstream projects instead of assuming this snapshot reflects current behavior.

## Current Bambu compatibility notes

BMCU compatibility can change when Bambu Lab printer firmware changes.

Known current nuance:

- A1/A1 Mini/P1S and newer printer generations are actively targeted by the current firmware.
- P2S support was added in V10.4 and further fixed in V10.5.
- X1/X1C support improved substantially in 2026, including a fix that restored recognition/filament metadata for testers.
- X1C is still not completely boring: an open report on printer firmware 01.11.02.00 + BMCU 10.5 describes intermittent communication failures.

For X1/X1C, check upstream issues before updating a known-working printer/BMCU combination.

## Klipper firmware paths

There are now two useful modern approaches.

### 1. BMCU-Klipper - full integration

**[jarczakpawel/BMCU-Klipper](https://github.com/jarczakpawel/BMCU-Klipper)**

Current release during this refresh: **v1.0.2 (2026-09-09)**.

This is the more complete route if the goal is actual multi-material behavior on Klipper:

- channel/source routing
- load/unload
- refill
- prestaging
- tip forming
- print source planning
- web UI
- Generic Klipper support
- dedicated Snapmaker U1 support

It supports BMCU boards with built-in USB/CH340 as well as TTL boards used with an external CH340. BMCU still requires proper **24 V power**.

### 2. klipper-bmcu-libre - simple USB-C UART layer

**[kurtjcu/klipper-bmcu-libre](https://github.com/kurtjcu/klipper-bmcu-libre)**

For a BMCU 370C with the Type-C mainboard and onboard CH340, this project replaces BambuBus with standard **115200 8N1 UART** over USB-C.

That avoids the awkward native BambuBus transport (RS485 / 1.25 Mbaud / 9-bit framing) on a Linux Klipper host.

This project is especially suited to secondary-feeder / toolchanger-assist use. It is not intended to be a complete multicolor planner in the same way as BMCU-Klipper.

Its firmware build references BMCU-C-PJARCZAK V10.5 and applies a small UART-focused patch layer.

## Earlier Klipper research

**[cuihuir/bmcu_klipper](https://github.com/cuihuir/bmcu_klipper)** contains useful BambuBus captures, CRC/protocol work and early Klipper integration experiments. It is best treated as a protocol/research reference now rather than the default install path.

## Safety

- Never flash while the BMCU is connected to the printer.
- Do not hot-plug the BMCU/printer connection.
- Verify the exact mainboard/subboard version before following wiring instructions.
- Different Klipper projects use different firmware/protocols; do not mix instructions between them.
