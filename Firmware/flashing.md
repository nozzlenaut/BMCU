# Flashing BMCU firmware

> **2026 update:** The easiest current path is [BMCU Flasher](https://github.com/jarczakpawel/BMCU-Flasher). The older WCHISPTool workflow is kept below only as a legacy/fallback method.

## Recommended method: BMCU Flasher

BMCU Flasher supports:

- Windows
- Linux
- macOS
- Android
- online firmware selection/download
- local `.bin` files

For current Bambu-compatible 370C firmware, start with:

- [BMCU-C-PJARCZAK](https://github.com/jarczakpawel/BMCU-C-PJARCZAK)
- [Latest releases](https://github.com/jarczakpawel/BMCU-C-PJARCZAK/releases)

The current release at the time this page was refreshed is **V10.5**.

### Before flashing

1. **Disconnect BMCU from the printer.**
2. Power the printer off completely before connecting/disconnecting BMCU hardware.
3. Confirm you actually have the expected board revision. BMCU 370C uses Hall-sensor subboards; sellers sometimes mix revisions.
4. If using V10.3 or newer, plan to boot after flashing with **all four filament channels empty** for calibration.

### Firmware selection notes

The upstream firmware package contains multiple builds. Read the included selection guides instead of picking a random binary.

General upstream guidance:

- choose the correct printer/load mode first
- `standard(A1)` is the normal A1-style option
- `soft_load(A1)` exists for A1/A1 Mini setups that grind/click with stronger loading
- `high_force_load(P1S)` is intended for P1S-style stronger loading
- choose the desired AUTOLOAD / RGB / AMS slot variant after that
- configure the printer as **AMS**, not AMS Lite

### First boot / calibration (V10.3+)

On first boot after flashing:

- remove filament from every channel
- allow the firmware to calibrate empty-channel detection

To re-calibrate later:

1. remove all filament
2. hold any one buffer in position for about **5 seconds**

## X1 / X1C warning

X1 compatibility has improved compared with the old 0019/0020-era firmware, but it is still sensitive to printer firmware.

Upstream issue #66 documents a 2026 X1C firmware change that restored recognition and filament-info retention for testers. A later open report (#141) describes intermittent AMS communication loss with X1C firmware 01.11.02.00 and BMCU 10.5.

Do not assume a BMCU/printer firmware combination is safe to update blindly on X1/X1C. Check the current upstream issues before changing a known-working setup.

- https://github.com/jarczakpawel/BMCU-C-PJARCZAK/issues/66
- https://github.com/jarczakpawel/BMCU-C-PJARCZAK/issues/141

## Legacy WCHISPTool method

This is retained for older boards/workflows or troubleshooting. For most people, use BMCU Flasher instead.

### Hardware

- CH340 or equivalent USB-to-UART adapter
- Dupont wires
- PC
- BMCU disconnected from the printer

Typical TTL wiring:

```text
BMCU R / RX  <- adapter TX
BMCU T / TX  -> adapter RX
BMCU GND     -> adapter GND
```

Only use the required power connection for the specific flashing method/board revision you are following. Do not casually mix printer power, USB power and adapter power.

### WCHISPTool settings used by the older guide

- Chip model: `CH32V203`
- Download type: `SerialPort`
- Baud: `1M` (115200 may help if communication is unreliable)
- Select the intended firmware `.bin`

The old process generally used the board's Boot/Reset buttons to enter the CH32 bootloader, remove protection if necessary, then download the binary.

Because the modern BMCU Flasher automates this workflow and supports more operating systems, it should now be considered the primary method.

## Klipper firmware is different

Do **not** flash normal Bambu-compatible firmware if your goal is one of the Klipper integrations without reading that project's instructions.

- [BMCU-Klipper](https://github.com/jarczakpawel/BMCU-Klipper) includes its own BMCU firmware and host integration.
- [klipper-bmcu-libre](https://github.com/kurtjcu/klipper-bmcu-libre) patches the 370C firmware to expose standard UART over USB-C for compatible Type-C boards.

See [../KLIPPER.md](../KLIPPER.md).
