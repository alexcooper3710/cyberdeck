# From-Scratch Cyberdeck — CM5 + ECP5-85F Build Notes (Architecture Freeze v2.0)

## The project

A CM5-based cyberdeck that uses an ECP5 FPGA as a PCIe memory accelerator and cartridge controller.

Linux runs on the Raspberry Pi Compute Module 5 and stores data on an NVMe SSD. The FPGA manages removable DDR4 memory cartridges, identifies them through SPD EEPROMs, and exposes cartridge memory to Linux through a PCIe DMA interface.

This is **not** an FPGA-only Linux computer.

The CM5 is the host computer.

The FPGA is a high-performance peripheral that provides:

- Removable memory cartridges
- DMA-accessible memory pools
- Cartridge identification
- Future encryption and security features
- Experimental memory acceleration

---

# Architecture Freeze v2.0

```text
Host CPU:
  CM5 (8 GB Lite)

Primary Storage:
  NVMe SSD

System RAM:
  CM5 onboard LPDDR

FPGA:
  Lattice ECP5-85F

Cartridge Memory:
  DDR4 SO-DIMM cartridges

Interconnect:
  PCIe Gen2 via ASM1184e

Memory Model:
  FPGA-owned PCIe memory
  NOT system RAM

Software:
  Linux + ramcartd
```

---

# System Architecture

```text
CM5
├── Linux
├── NVMe SSD
├── Applications
└── PCIe Root Complex
        │
        ▼
ASM1184e PCIe Switch
        │
        ▼
ECP5-85F FPGA
├── LitePCIe Endpoint
├── DMA Engine
├── LiteDRAM Controller
├── Cartridge Manager
├── SPD Reader
└── Future Crypto Engine
        │
        ▼
DDR4 Cartridge Sockets
```

---

# Why ECP5-85F is the right FPGA

| Property | Value | Why it matters |
|---|---|---|
| Toolchain | Yosys + nextpnr + Project Trellis | Fully open source |
| Logic Capacity | ~84k LUT4 | Plenty for PCIe + DDR4 + DMA |
| SerDes | 4 × 3.125 Gbps | PCIe capable |
| Cost | ~$40 | Affordable |
| Package | BG381 | Manufacturable |
| Ecosystem | LiteX support | LitePCIe + LiteDRAM available |

Unlike the previous architecture, the FPGA no longer needs to run Linux or host a soft CPU.

Its job is memory management and acceleration.

---

# Bill of Materials

| Designator | Part | Function |
|---|---|---|
| U1 | Raspberry Pi CM5 (8 GB Lite) | Main computer |
| U2 | Lattice ECP5-85F | Cartridge controller |
| U3 | ASM1184e | PCIe switch |
| J1 | M.2 M-Key Socket | NVMe SSD |
| J2 | DDR4 SO-DIMM | Active memory cartridge |
| J3 | DDR4 SO-DIMM | Cartridge bay |
| J4 | DDR4 SO-DIMM | Cartridge bay |
| U4 | W25Q128JV | FPGA configuration flash |
| U5 | SPD EEPROMs | Cartridge identity |
| U6-U8 | Power regulators | System power |
| J5 | USB-C | Power input |
| J6 | HDMI | Display |
| J7 | USB | Keyboard/peripherals |

---

# Memory Model

This project does NOT attempt to make cartridge RAM become CM5 system RAM.

Instead:

```text
CM5 RAM
= onboard LPDDR
```

and

```text
DDR4 Cartridge RAM
= FPGA-managed PCIe memory
```

Linux accesses cartridge memory through:

- DMA transfers
- Memory-mapped PCIe regions
- Custom driver interfaces
- ramcartd

This is closer to GPU VRAM than traditional system RAM.

---

# Software Stack

```text
Applications
│
ramcartd
│
Linux (CM5)
│
PCIe Driver
│
ASM1184e
│
LitePCIe Endpoint
│
DMA Engine
│
LiteDRAM
│
DDR4 Cartridge
```

---

# FPGA Responsibilities

## LitePCIe

Provides PCIe endpoint functionality.

## LiteDRAM

Controls DDR4 cartridge memory.

## DMA Engine

Moves data efficiently between host and cartridge memory.

## Cartridge Manager

Tracks insertion, removal, status and allocation.

## SPD Reader

Reads cartridge identity and metadata.

## Future Security Layer

Potential support for:

- Encryption
- Secure erase
- Authentication
- Key storage

---

# Development Roadmap

## Phase 1 — FPGA Bring-Up

Goal:

Program and validate the Colorlight board.

Tasks:

- FT232H wiring
- openFPGALoader setup
- Detect SPI flash
- Load first bitstream

Status:

IN PROGRESS

---

## Phase 2 — LiteX Fundamentals

Goal:

Learn the toolchain used by the final design.

Tasks:

- Build LitePCIe examples
- Build LiteDRAM examples
- Understand DMA operation
- Validate PCIe endpoint generation

---

## Phase 3 — PCIe Memory Device Prototype

Goal:

Use the Colorlight board as a PCIe memory device.

Success Criteria:

- PCIe enumeration
- Host communication
- DMA transfers
- Memory mapping

Architecture:

```text
CM5
  │
PCIe
  │
Colorlight
  │
DDR Memory
```

---

## Phase 4 — Custom Carrier Board

Integrate:

- CM5
- ECP5-85F
- ASM1184e
- NVMe SSD
- DDR4 cartridge sockets

---

## Phase 5 — ramcartd

Implement:

- Cartridge discovery
- SPD identification
- Allocation management
- Userspace API

---

## Phase 6 — Cyberdeck Integration

Integrate:

- Display
- Keyboard
- Battery
- NVMe storage
- Cartridge system

---

# Final Deliverable

A portable cyberdeck that:

- Boots Linux on a CM5
- Uses NVMe storage
- Uses onboard LPDDR as system RAM
- Supports removable DDR4 memory cartridges
- Uses an ECP5 FPGA as a PCIe accelerator
- Exposes cartridge memory through DMA and mmap interfaces
- Identifies cartridges by SPD serial number
- Is managed through ramcartd
- Supports future security and encryption features

The FPGA is not the computer.

The CM5 is the computer.

The FPGA provides the cartridge-memory subsystem.
