[[FPGA Pin Allocation]]# Cyberdeck Carrier — KiCad Project Scaffolding[[01 - Computer Science],[[[03 - Computer Engineering]]]
}]

This is **scaffolding only** — the project structure opens in KiCad 8 and gives you a hierarchical
skeleton to fill in, but does NOT contain working schematics. You'll add components and wiring yourself.

## What's here

```
cyberdeck-carrier/
├── carrier.kicad_pro      KiCad 8 project file
├── carrier.kicad_sch      Top-level schematic (links to 7 sub-sheets)
├── carrier.kicad_pcb      Empty PCB with board outline (~150x100mm)
├── fpga_core.kicad_sch    Sub-sheet: ECP5-85F + decoupling + JTAG + QSPI
├── ddr4_banks.kicad_sch   Sub-sheet: 3x SO-DIMM sockets + termination
├── cm5_carrier.kicad_sch  Sub-sheet: CM5 board-to-board + I/O passthroughs
├── pcie_switch.kicad_sch  Sub-sheet: ASM1184e + M.2 NVMe
├── power_tree.kicad_sch   Sub-sheet: USB-C input + all regulators
├── peripherals.kicad_sch  Sub-sheet: LEDs + buttons + PMOD + audio
├── debug.kicad_sch        Sub-sheet: CP2102N UART + JTAG + test points
├── parts_list.csv         BOM with Digi-Key/Mouser sourcing
├── libraries/             Empty folder for your custom symbols + footprints
└── datasheets/            Empty folder — drop part datasheets here
```

## How to use

1. **Open `carrier.kicad_pro` in KiCad 8.** It should load without errors and show 7 sheet symbols.
2. **Source library components** — for each major part:
   - Search snapeda.com and ultralibrarian.com for "LFE5UM-85F" — Lattice ECP5 parts are often available
   - For Hirose DF40C-100DS, check Hirose's official symbols
   - For TE 2042205-1, check TE Connectivity downloads
   - For everything else, KiCad's built-in libraries cover most of it
3. **Add components to each sub-sheet** following the visualizations from the planning session.
4. **Wire signals** between sub-sheets using hierarchical labels — the visual diagrams listed which signals cross each boundary.
5. **Run ERC** as you go — the project's ERC settings are already configured.
6. **Move to PCB layout** only after schematic is ERC-clean.

## Recommended evening order

1. Open in KiCad, confirm it loads
2. Search snapeda + download main IC symbols (1-2 hours)
3. Start with `power_tree.kicad_sch` — easiest, lowest pin count
4. Then `debug.kicad_sch` and `peripherals.kicad_sch` — moderate
5. Then `cm5_carrier.kicad_sch` — many connections but mostly passthroughs
6. Then `pcie_switch.kicad_sch` — PCIe routing critical
7. Then `ddr4_banks.kicad_sch` — DDR4 PHY connections
8. Finally `fpga_core.kicad_sch` — connect everything to the FPGA banks

## Stack-up note (for PCB)

The carrier.kicad_pcb is set up for a **6-layer** stack:
- Top (signal/components)
- In1 (GND plane)
- In2 (signal/power)
- In3 (signal/power)
- In4 (GND plane)
- Bottom (signal/components)

Configure proper trace impedances for DDR4 (40/80 ohm) and PCIe (50/100 ohm)
after you have actual board layout going.

## Caveats

- The .kicad_sch files use the KiCad 8 format (version 20231120). If you open in KiCad 7 or older it may not load.
- The .kicad_pcb file is minimal — only the board outline is set, no layers populated.
- The .kicad_pro has default DRC/ERC settings that you should review.
- Don't trust this scaffolding to be production-ready. Verify everything in KiCad.

Generated as a starting point — the real engineering is yours to do.
