---
tags: [roadmap, phase-4, pending, kicad, pcb]
created: 2026-07-01
status: pending
---

# Phase 4 — Custom Carrier PCB

**Goal:** Design, fab, and bring up the real cyberdeck carrier board.

## Prerequisites

- Phase 1 + 2 complete
- KiCad schematic scaffolding ready (see [[KiCad Getting Started]])

## Sub-phases

### 4a — Schematic capture (~4–6 weeks)

- [ ] Download/create symbols for all major parts (ECP5-85F, CM5 connector, DDR4 socket, ASM1184e)
- [ ] Fill in each hierarchical sub-sheet:
  - [ ] `power_tree.kicad_sch`
  - [ ] `debug.kicad_sch`
  - [ ] `peripherals.kicad_sch`
  - [ ] `cm5_carrier.kicad_sch`
  - [ ] `pcie_switch.kicad_sch`
  - [ ] `ddr4_banks.kicad_sch`
  - [ ] `fpga_core.kicad_sch`
- [ ] Pass ERC clean

### 4b — PCB layout (~6–10 weeks)

- [ ] 6-layer stack-up defined
- [ ] Component placement per [[Bill of Materials]]
- [ ] DDR4 routing per [[DDR4 Routing Requirements]]
- [ ] PCIe routing per [[PCIe Routing Requirements]]
- [ ] Length matching validated
- [ ] DRC clean

### 4c — Fab + assembly (~2–3 weeks)

- [ ] Order 5-piece run from JLCPCB (6-layer ENIG)
- [ ] Turnkey SMT assembly for U2 (ECP5 BGA), U3 (ASM1184e QFN), etc.
- [ ] Hand-solder connectors (SO-DIMMs, HDMI, USB)

### 4d — Bring-up (~4–6 weeks)

- [ ] Power-on smoke test with current limiter
- [ ] FPGA bring-up via SPI flash (openFPGALoader)
- [ ] CM5 boot test (Raspberry Pi OS from microSD)
- [ ] PCIe enumeration test (`lspci` on CM5 sees the FPGA)
- [ ] LiteDRAM bring-up on J2 with a slotted DDR4 stick
- [ ] M.2 NVMe enumeration test

## Related

- [[KiCad Getting Started]]
- [[Bill of Materials]]
- [[DDR4 Routing Requirements]]
- [[PCIe Routing Requirements]]
- [[Power Tree]]
