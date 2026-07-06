# cyberdeck

A from-scratch cyberdeck built around a Raspberry Pi CM5 and a Lattice ECP5-85F FPGA, with removable DDR4 SO-DIMM memory cartridges.

Linux runs on the CM5. The FPGA is a PCIe-attached memory accelerator: it manages the cartridge sockets, identifies cartridges through their SPD EEPROMs, and exposes cartridge memory to Linux over PCIe DMA. Think game cartridges, but they're sticks of laptop RAM.

## Architecture (v2.0)

```
CM5 (8 GB Lite)
├── Linux + applications
├── NVMe SSD (primary storage)
└── PCIe Root Complex
        │
ASM1184e PCIe switch
        │
ECP5-85F FPGA
├── LitePCIe endpoint
├── DMA engine
├── LiteDRAM controller
├── Cartridge manager + SPD reader
└── (future) crypto engine
        │
3× DDR4 SO-DIMM cartridge sockets
```

Key design decision: cartridge RAM is **not** system RAM. The CM5 keeps its onboard LPDDR; cartridges are FPGA-owned PCIe memory pools managed by a Linux userspace daemon (`ramcartd`).

## Why the ECP5-85F

- Fully open-source toolchain (Yosys + nextpnr + Project Trellis)
- ~84k LUTs — enough for PCIe endpoint + DDR4 controller + DMA
- 4× 3.125 Gbps SerDes, PCIe capable
- ~$40, BG381 package, solid LiteX support (LitePCIe + LiteDRAM)

## Roadmap

| Phase | Goal | Status |
|---|---|---|
| 1 | FPGA bring-up — Colorlight 5A-75B dev board + LiteX toolchain | in progress |
| 2 | LiteX fundamentals — SoC generation on real hardware | |
| 3 | PCIe memory prototype on dev board | |
| 4 | Custom carrier PCB (KiCad, in `hardware/`) | scaffolded |
| 5 | `ramcartd` Linux daemon | |
| 6 | Integration — enclosure, display, battery | |

## Repo contents

- `docs/` — architecture notes, memory model, DDR4/PCIe routing requirements, power tree, pin allocation, BOM
- `hardware/cyberdeck-carrier/` — hierarchical KiCad project (FPGA core, CM5 carrier, DDR4 banks, PCIe switch, power tree, peripherals, debug)
- `journal/` — bring-up logs (JTAG hunting on a Colorlight V8.2 was a whole day of my life I'm not getting back)

## Toolchain

```bash
# synthesis / place-and-route
sudo apt install yosys nextpnr-ecp5 prjtrellis

# LiteX + RISC-V toolchain
git clone https://github.com/enjoy-digital/litex && cd litex
./litex_setup.py --init --install --user
./litex_setup.py --gcc=riscv

# programming via FT232H
openFPGALoader -c ft232 -b colorlight-5a-75b build/.../gateware.bit
```

## Status

Phase 1. Current dev board had a rough JTAG debugging session (see journal); a fresh 5A-75B is on the shopping list. KiCad carrier project is scaffolded and waiting on ECP5-85F + CM5 connector symbols.

This is a hobby hardware project in active development — expect TBDs.
