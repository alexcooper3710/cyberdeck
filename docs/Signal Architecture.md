---
tags: [hardware, architecture, block-diagram]
created: 2026-07-01
---

# Signal Architecture (Block Level)

## Data flow

```
Raspberry Pi CM5 (Linux host)
     │
     │ PCIe Gen2 x1
     ▼
ASM1184e (PCIe Gen2 1→2 switch)
     │
     ├──── PCIe Gen2 x1 ────→ ECP5-85F FPGA
     │                          ├── LitePCIe Endpoint
     │                          ├── DMA Engine
     │                          ├── LiteDRAM (DDR4)
     │                          ├── Cartridge Manager
     │                          ├── SPD I2C Reader
     │                          └── Status LED Logic
     │                                │
     │                                ├── DDR4 PHY ──→ J2 (Live DDR4 SO-DIMM)
     │                                └── SPD I2C ──→ J2/J3/J4 (3 sockets)
     │
     └──── PCIe Gen2 x1 ────→ M.2 NVMe SSD (boot + storage)
```

## Signal counts crossing FPGA boundary

- **DDR4 PHY** → ~72 signals (DQ, DQS, ADDR, CMD, CK)
- **PCIe Gen2** → 4 high-speed diff + 4 control
- **SPD I2C** → 2 signals (broadcast to 3 sockets)
- **QSPI flash** → 6 signals (bitstream)
- **UART/JTAG/LEDs/PMOD** → ~30 signals

## Memory model

**Important:** cartridge RAM is NOT system RAM. See [[Memory Model]].

## Related

- [[Build Notes v2]]
- [[FPGA Pin Allocation]]
