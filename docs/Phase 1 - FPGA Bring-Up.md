---
tags: [roadmap, phase-1, in-progress]
created: 2026-07-01
status: in-progress
---

# Phase 1 — FPGA Bring-Up

**Goal:** Program a bitstream to an ECP5 dev board and prove the toolchain works end-to-end.

## Status

**IN PROGRESS.** First Colorlight V8.2 attempt ended with U31 damaged. Next attempt after fresh board + multimeter arrive.

## Tasks

- [x] Install openFPGALoader
- [x] Set up udev rules for FT232H
- [x] Learn ECP5-25F pinout on 5A-75B V8.2
- [x] Identify JTAG pads (J27/J31/J32/J30/J33/J34)
- [ ] Order fresh Colorlight + multimeter
- [ ] Solder JTAG header wires (verified with continuity)
- [ ] Detect ECP5 IDCODE via `openFPGALoader -c ft232 --detect`
- [ ] Flash first blinky bitstream

## Success criteria

- `openFPGALoader -c ft232 --detect` returns an IDCODE
- A blinky bitstream flashes successfully
- Onboard LED blinks

## Related

- [[Colorlight V8.2 JTAG Pinout]]
- [[FT232H Setup Guide]]
- [[2026-06-11 Colorlight V8.2 Debug Log]]
- [[Project Outline]]
