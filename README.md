# BMCU resources / build notes

My collection of resources for my X1C BMCU build. BMCU information has ended up scattered across wikis, Reddit, GitHub forks and firmware projects, so this repo is meant to keep the useful stuff in one place.

> **Last refreshed: 2026-09-11**
>
> BMCU development has moved a lot since this repo was first assembled. The old 2025-era `0020` firmware is kept here for reference, but it is no longer the current community firmware.

## Core links

- [BMCU Wiki](https://wiki.yuekai.fr/)
- [r/BMCU](https://www.reddit.com/r/BMCU/)
- [r/OpenBambu](https://www.reddit.com/r/openbambu/)
- [Current BMCU 370C community firmware - BMCU-C-PJARCZAK](https://github.com/jarczakpawel/BMCU-C-PJARCZAK)
- [BMCU Flasher](https://github.com/jarczakpawel/BMCU-Flasher)
- [BMCU-Klipper](https://github.com/jarczakpawel/BMCU-Klipper)
- [Klipper notes in this repo](KLIPPER.md)
- [Firmware notes in this repo](Firmware/README.md)

## Hardware / build links

- [Parts kit I ordered](https://www.aliexpress.us/item/3256809034433237.html?spm=a2g0o.order_list.order_list_main.4.3b361802YS4V9c&gatewayAdapt=glo2usa) - 370C A Kit. I needed to order PCBs anyway, so this was the easiest route at the time.
- [Filamentalist](https://makerworld.com/en/models/1415066-filamentalist-fv3-rewinder-pc4-m6-mod#profileId-1469469) - filament rewinding setup for X1/P1-style use.

## Current firmware status

The original note in this repo said BMCU was waiting on "firmware 21." That is now very outdated.

The most active Bambu-compatible 370C firmware I found is [jarczakpawel/BMCU-C-PJARCZAK](https://github.com/jarczakpawel/BMCU-C-PJARCZAK). The current release is **V10.5** (2026-04-22).

V10.5 includes, among other changes:

- automatic unload when the buffer is lifted manually
- unique serial generation from the MCU UID
- full NVM cleanup during calibration
- system LED fixes
- P2S external-fan fix

Important V10.3+ behavior: first boot after flashing should be done with **all channels empty** so empty-channel detection can calibrate. Re-calibration can be triggered with all filament removed by holding a buffer for about five seconds.

For new flashing work, use [BMCU Flasher](https://github.com/jarczakpawel/BMCU-Flasher) rather than treating the WCHISPTool instructions in this repo as the primary method. The newer flasher supports Windows, Linux, macOS and Android and can download the appropriate firmware through its online workflow.

See [Firmware/README.md](Firmware/README.md) for the current firmware map and compatibility notes.

## X1 / X1C note

X1 compatibility is no longer as simple as the old "does not work past firmware 8" note.

There was a successful X1C fix/test in 2026 where newer BMCU firmware restored recognition and retained filament information on newer printer firmware. However, there is also an open report against **X1C 01.11.02.00 + BMCU 10.5** describing intermittent AMS communication loss.

So for X1/X1C I would currently call BMCU **working for some setups, but firmware-sensitive and not something I would label fully reliable on every current X1 firmware**.

Relevant upstream reports:

- [X1C compatibility / filament-save fix discussion](https://github.com/jarczakpawel/BMCU-C-PJARCZAK/issues/66)
- [Intermittent X1C communication report on 01.11.02.00](https://github.com/jarczakpawel/BMCU-C-PJARCZAK/issues/141)

## Klipper is now a real option

This changed a lot in 2026.

### BMCU-Klipper

[jarczakpawel/BMCU-Klipper](https://github.com/jarczakpawel/BMCU-Klipper) is an active full integration with:

- Generic Klipper support for Voron, VzBot and custom machines
- dedicated Snapmaker U1 support
- load/unload, refill, prestaging, tip forming and source planning
- per-channel routing to endpoints/toolheads
- a web panel
- firmware/update tooling
- USB or TTL/CH340 connection options depending on BMCU board revision

The current release found during this refresh is **v1.0.2 (2026-09-09)**.

### klipper-bmcu-libre

[kurtjcu/klipper-bmcu-libre](https://github.com/kurtjcu/klipper-bmcu-libre) takes a different approach for the **370C Type-C / CH340 board**: it replaces BambuBus with normal 115200 8N1 UART and talks directly over USB-C.

That project is especially interesting for feeder-assist / toolchanger setups such as StealthChanger or Tapchanger. It is **not primarily a full multicolor switching stack** like BMCU-Klipper.

### Earlier reverse-engineering work

[cuihuir/bmcu_klipper](https://github.com/cuihuir/bmcu_klipper) contains useful BambuBus reverse-engineering notes and tooling. It is valuable as technical reference, but it represents an earlier prototype path rather than the easiest current route.

See [KLIPPER.md](KLIPPER.md) for connection details and project differences.

## Safety

- Do not flash a BMCU while it is connected to the printer.
- Power the printer completely off before connecting or disconnecting BMCU hardware.
- BMCU hardware comes from many sellers/builders; verify board revision and wiring instead of assuming every unit is identical.
