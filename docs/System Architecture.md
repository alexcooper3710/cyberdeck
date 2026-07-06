---
tags: [architecture, overview]
created: 2026-07-01
---

# System Architecture

## The one-line summary

**CM5 hosts Linux. ECP5 FPGA is a PCIe-attached RAM cartridge controller.**

## Full component list

| Role | Component |
| ---- | --------- |
| Host CPU | Raspberry Pi CM5 (8GB Lite) |
| Primary Storage | M.2 NVMe SSD |
| System RAM | CM5 onboard LPDDR |
| FPGA | Lattice ECP5-85F (LFE5UM-85F variant) |
| Cartridge Memory | DDR4 SO-DIMM (3 sockets: 1 live + 2 cart bays) |
| Interconnect | PCIe Gen2 via ASM1184e switch |
| Bitstream storage | W25Q128 SPI flash |

## Key architectural decisions

- FPGA does NOT run Linux (no soft RISC-V CPU on the FPGA)
- FPGA is a *peripheral*, not a computer
- Cartridge RAM is DMA-accessed, not mapped as system RAM ([[Memory Model]])
- CM5 keeps its normal boot flow: microSD or NVMe → Raspberry Pi OS

## Related

- [[Build Notes v2]]
- [[Signal Architecture]]
- [[FPGA Responsibilities]]
