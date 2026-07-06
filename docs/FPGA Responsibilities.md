---
tags: [architecture, fpga, litex]
created: 2026-07-01
---

# FPGA Responsibilities

The ECP5-85F is a peripheral, not a computer. Its jobs:

## 1. LitePCIe Endpoint

Exposes the FPGA as a PCIe device to the CM5 host. Provides BAR memory regions and DMA channels.

## 2. DMA Engine

High-throughput data movement between host and cartridge memory without CPU intervention on either side.

## 3. LiteDRAM Controller

DDR4 memory controller running on the ~800 MT/s Live socket. Handles refresh, ODT, calibration.

## 4. Cartridge Manager

Tracks socket state:

- Insert/remove events
- Which stick is in the Live socket
- Allocation of cartridge memory regions

## 5. SPD I2C Reader

Reads SPD EEPROMs on all 3 sockets to identify each cartridge by serial number.

## 6. Status LED Logic

Drives front-panel D1/D2/D3 RGB LEDs to show cart status (present, live, active).

## 7. Future security layer (optional)

- Encryption of cartridge data
- Secure erase
- Attestation / authentication
- Key storage

## What the FPGA does NOT do

- **No soft CPU** — no VexRiscv, no RISC-V, no Linux on FPGA
- **No video output** — CM5 handles that
- **No general-purpose I/O to userspace** — everything goes through the LitePCIe path

## Related

- [[Build Notes v2]]
- [[Signal Architecture]]
- [[Software Stack]]
