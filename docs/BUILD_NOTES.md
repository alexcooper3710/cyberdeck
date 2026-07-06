# Cyberdeck Build Notes

FPGA + Raspberry Pi Compute Module cyberdeck. RISC-V soft-core via LiteX targeting Lattice ECP5.

---

## System Architecture

```
┌─────────────────────────────────────────────────────┐
│                  Custom KiCad Board                 │
│                                                     │
│  ┌──────────────┐   DDR bus   ┌──────────────────┐  │
│  │  Colorlight  │─────────── │  SO-DIMM DDR RAM  │  │
│  │  5A-75B ECP5 │             └──────────────────┘  │
│  │              │                                   │
│  │  (LiteX      │  ???  ┌──────────────────────┐   │
│  │   RISC-V     │───────│  Raspberry Pi CM5     │   │
│  │   SoC)       │       │                       │   │
│  └──────────────┘       │  Linux                │   │
│         │               │  PCIe → NVMe SSD      │   │
│    FT232H JTAG           │  PCIe → HDD           │   │
│    (programming)         └──────────────────────┘   │
└─────────────────────────────────────────────────────┘
```

### How it works

1. **FPGA ↔ SO-DIMM DDR** — ECP5 controls a laptop DDR RAM module via LiteDRAM, presenting it as a fast RAM-backed block store
2. **FPGA → CM5** — FPGA presents the RAM block to the CM5 as a hardware token / secure storage device (interface TBD — PCIe endpoint is a strong candidate given CM5's PCIe Gen 3 x1)
3. **CM5 (Linux)** — sees the FPGA device as a hardware security token; separate PCIe lanes for NVMe SSD + HDD
4. **FT232H** — used during development for JTAG programming the ECP5

### Interface Decision (TBD)

| Option | Pros | Cons |
|---|---|---|
| PCIe (ECP5 endpoint) | High bandwidth, appears as native Linux device, CM5 has Gen 3 x1 | Complex to implement in LiteX, needs PCIe PHY |
| USB via FT232H | Already have the chip, simple | Lower bandwidth, FT232H is a bridge not native |
| SPI/I2C | Very simple, good for token use case | Slow, limited for block device |

---

## Parts List / BOM

| Component | Part | Status |
|---|---|---|
| FPGA Board | Colorlight 5A-75B (Lattice ECP5 LFE5U-25F) | ✅ Have |
| USB-JTAG Bridge | Adafruit FT232H Breakout | ✅ Arrived 2026-05-24 |
| Compute Module | Raspberry Pi CM5 | _TODO_ |
| CM Carrier Board | Custom KiCad board (in design) | 🔧 Designing |
| SO-DIMM DDR RAM | Laptop DDR module (spec TBD) | _TODO_ |
| Display | _TODO_ | _TODO_ |
| Keyboard/Input | _TODO_ | _TODO_ |
| Power Supply | _TODO_ | _TODO_ |
| Enclosure | _TODO_ | _TODO_ |
| Misc | _TODO_ — add any breakout boards, connectors, etc. | |

---

## Software & Toolchain Setup

### Host machine: Ubuntu laptop

### 1. FPGA Synthesis & P&R

```bash
# Yosys (synthesis)
sudo apt install yosys

# nextpnr-ecp5 (place and route)
sudo apt install nextpnr-ecp5

# Project Trellis (ECP5 database)
sudo apt install libtrellis-dev prjtrellis
```

### 2. LiteX

```bash
# Clone and install LiteX + cores
git clone https://github.com/enjoy-digital/litex
cd litex
pip3 install migen
./litex_setup.py --init --install --user
./litex_setup.py --gcc=riscv  # install RISC-V GCC toolchain
```

### 3. Simulation

```bash
# Verilator (recommended for LiteX sim)
sudo apt install verilator

# OR iverilog
sudo apt install iverilog

# Run LiteX simulation
litex_sim --cpu-type=vexriscv
```

> ⚠️ **Known issue:** Simulation currently failing — see Build Status section below.

### 4. FPGA Programming via FT232H

```bash
# openFPGALoader
sudo apt install openFPGALoader

# Program the 5A-75B
openFPGALoader -c ft232 -b colorlight-5a-75b build/colorlight_5a_75b/gateware/colorlight_5a_75b.bit
```

---

## Wiring / Connections

### FT232H → Colorlight 5A-75B JTAG

The FT232H uses MPSSE mode for JTAG. Connect to the JTAG header on the 5A-75B:

| FT232H Pin | Signal | 5A-75B JTAG Header |
|---|---|---|
| AD0 | TCK | TCK |
| AD1 | TDI | TDI |
| AD2 | TDO | TDO |
| AD3 | TMS | TMS |
| GND | GND | GND |

> Note: 5A-75B runs at 3.3V logic. FT232H is 3.3V tolerant but verify your breakout's VCC configuration before connecting.

### Raspberry Pi CM5 → Custom Board

- **PCIe:** CM5 has PCIe Gen 3 x1 — one lane goes to NVMe SSD, one to HDD, potentially one to FPGA endpoint (TBD, depends on switch/mux)
- **FPGA ↔ CM5 interface:** TBD (see Architecture section — PCIe endpoint is preferred candidate)
- **Power rails:** CM5 requires 5V input — _TODO_ confirm regulator design
- **CM5 connector:** Uses standard high-density 100-pin Hirose DF40 connectors (x2)

### ECP5 ↔ SO-DIMM DDR

- LiteDRAM handles DDR controller in the FPGA fabric
- SO-DIMM slot on custom board — DDR3 or DDR4 (TBD based on ECP5 I/O bank voltage compatibility)
- _TODO_ confirm DDR standard and speed grade

---

## Build Status & Progress

### ✅ Done
- FT232H arrived (2026-05-24)
- LiteX installed on Ubuntu laptop
- Colorlight 5A-75B board acquired
- System architecture defined

### 🔧 In Progress
- **LiteX simulation debugging** — currently failing (error details TBD)
- **Custom KiCad board design** — FPGA + CM5 + SO-DIMM carrier board

### ⏳ Pending
- Decide FPGA ↔ CM5 interface (PCIe endpoint vs USB vs SPI)
- Order Raspberry Pi CM5
- Finalize DDR RAM spec for SO-DIMM slot
- Verify FT232H JTAG connection to 5A-75B
- First successful bitstream build and flash
- LiteDRAM bring-up on ECP5
- Hardware token firmware / gateware design
- RISC-V SoC bring-up over UART on CM5
- PCIe SSD + HDD integration on CM5
- Enclosure design / assembly

### Known Issues

| Issue | Status | Notes |
|---|---|---|
| LiteX simulation failing | 🔧 Active | Error TBD — add details here |
| _TODO_ | | |

---

*Last updated: 2026-05-24*
