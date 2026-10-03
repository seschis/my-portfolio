---
title: 'GoldenMate BMS Reverse Engineering'
date: 2026-10-01T18:09:42-04:00
summary: 'Tearing down a GoldenMate battery management system: PCB analysis, firmware extraction over SWD, and Ghidra on an ARM Cortex-M0.'
tags: ['reverse-engineering', 'embedded', 'ghidra', 'hardware']
repo: 'seschis/goldenmate-re-work'
---

{{< github-card >}}

## TL;DR

A hardware reverse-engineering project on the GoldenMate BMS (Battery Management System). The repo documents the full path: annotated PCB analysis, firmware extraction via an ST-LINK SWD probe (with a custom OpenOCD build to support the Fudan Micro FM33LC026N Cortex-M0), decompilation and analysis in Ghidra, and signal tracing across the BMS ASIC, EEPROM, and RS485 interface.

## The problem

The GoldenMate BMS is a closed box: proprietary MCU, undocumented firmware, and an RS485 protocol I couldn't get speaking through the external connector. I wanted to know what's actually on the board, what the firmware does, and whether interoperability was achievable (the work is aimed at NUT integration). That meant a full hardware reverse-engineering pass: component identification, firmware extraction, decompilation, and signal tracing.

## Approach

The repo documents the full pipeline:

- **PCB analysis** — annotated board photos, component identification, and pin tracing. Key parts: Fudan Micro FM33LC026N (ARM Cortex-M0, ARMv6M, Thumb), SH367309U BMS ASIC, BL24C512A I2C EEPROM (PB3/PB4), CA-IS3721HS RS485 isolator, and 3PEAK TP8485E transceiver.
- **Firmware extraction** — ST-LINK V2J37S7 over SWD (100 kHz, `dapdirect_swd`). Stock OpenOCD v0.12.0 rejects the MCU with `Cortex-M PARTNO 0xc30 is unrecognized`, so I built a patched OpenOCD from source to support the FM33LC026N.
- **Memory map** — internal flash `0x0`–`0xf4e0`, LDT1 OTP at `0x1ffffc00` (populated: `cc5533aa 0050ffaf …`), IF0 OTP all `0xffffffff`, RAM MSP `0x20001238`, reset vector near `0x140`.
- **Ghidra** — decompilation and analysis of the boot sequence and interrupt handlers.
- **Signal tracing** — Mermaid and Excalidraw diagrams of the RS485 signal path across the BMS ASIC, EEPROM, and transceiver.

The repo is organized for reuse: `docs/` (research notes), `images/` (annotated PCB photos), `diagrams/` (signal paths), and `datasheets/` (including a Chinese-to-English translation of the MCU datasheet).

## Decisions & tradeoffs

- **SWD with a free ST-LINK instead of commercial JTAG tooling** — enough access for full firmware pulls, at the cost of maintaining a custom OpenOCD build for a niche MCU nobody else targets.
- **Documenting dead ends instead of dropping them** — the TP8485E on the RS485 path appears to serve a debug port rather than the external connector, and I couldn't connect through RS-485 signaling. It stays in the repo as an open investigation thread.
- **Licensing settled up front** — documentation and analysis under CC-BY-NC-4.0, tools under MIT, datasheets attributed to their manufacturers, with a NOTICE covering the interoperability purpose.

## Results

A complete component map of the board, a working SWD debug session on an unsupported Cortex-M0 (DPIDR `0x0bb11477`, CPUID `0x410cc300`), partial flash/OTP dumps, Ghidra analysis of the boot sequence and interrupt handlers, and a diagrammed RS485 signal path. Open threads are explicit: boot-mode pin configuration (what's mapped at `0x0000`), dumping the external EEPROM via a CH341A, logic-analyzer capture of RS485/UART4 during boot, and determining whether the internal flash is NOR or NAND so the OpenOCD read commands are correct.

## What I'd do differently

- I'd determine the flash type (NOR vs NAND) before the first dump, so the OpenOCD read commands are right from the start.
- I'd start with behavioral observation — logic-analyzer capture of RS485/UART4 during boot, and an EEPROM dump — because the external RS-485 connector didn't cooperate, and watching the board run would have saved speculation on the signal path.
- I'd document boot-mode pin configuration before extracting firmware, so the extracted image can be interpreted with confidence about what memory is mapped at `0x0000`.
