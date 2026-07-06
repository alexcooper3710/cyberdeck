---
tags: [kicad, hardware, project]
created: 2026-07-01
---

# KiCad Project — Getting Started

The scaffolded KiCad 8 project lives at:
```
06-KiCad-Project/cyberdeck-carrier/
```

## What's in the scaffolding

- `carrier.kicad_pro` — project file (open this in KiCad 8)
- `carrier.kicad_sch` — top-level schematic with 7 hierarchical sheet symbols
- `carrier.kicad_pcb` — empty PCB with 6-layer stack + ~150×100 mm outline
- 7 sub-sheets: `fpga_core`, `ddr4_banks`, `cm5_carrier`, `pcie_switch`, `power_tree`, `peripherals`, `debug`
- `parts_list.csv` — BOM
- `libraries/` — for custom symbols and footprints
- `datasheets/` — drop PDFs here as you download them

## First-time open

1. Open `carrier.kicad_pro` in KiCad 8
2. Double-click any sheet symbol to enter that sub-sheet (should be empty)
3. Confirm schematic editor works, no errors

## Order of filling in sheets (easiest → hardest)

1. **`power_tree.kicad_sch`** — regulators only, straightforward datasheet reference
2. **`debug.kicad_sch`** — CP2102N + JTAG header
3. **`peripherals.kicad_sch`** — LEDs, buttons, PMOD
4. **`cm5_carrier.kicad_sch`** — mostly passthroughs
5. **`pcie_switch.kicad_sch`** — ASM1184e + M.2 socket, PCIe routing critical
6. **`ddr4_banks.kicad_sch`** — 3 sockets, careful signal grouping
7. **`fpga_core.kicad_sch`** — ECP5-85F, ties everything together

## Getting symbols/footprints

For big parts I couldn't scaffold accurately:

- **ECP5-85F (LFE5UM-85F-6BG381C)** — search SnapEDA or Lattice's own KiCad library
- **CM5 board-to-board (Hirose DF40C-100DS-0.4V)** — Hirose ships KiCad symbols; or SnapEDA
- **TE 2042205-1 SO-DIMM socket** — TE Connectivity KiCad library
- **ASM1184e** — SnapEDA usually has this
- **W25Q128** — often in KiCad's built-in libraries; else SnapEDA
- Passives, USB-C, RJ45, HDMI — KiCad built-ins usually work

## Related

- [[Bill of Materials]]
- [[Component Sourcing]]
- [[Phase 4 - Custom Carrier PCB]]
- [[DDR4 Routing Requirements]]
- [[PCIe Routing Requirements]]
- [[FPGA Pin Allocation]]
- [[Power Tree]]
