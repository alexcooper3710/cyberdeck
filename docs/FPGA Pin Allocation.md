---
tags: [hardware, fpga, ecp5, pinout]
created: 2026-07-01
---

# FPGA Pin Allocation (ECP5-85F, ~200 user I/O)

Target chip: **LFE5UM-85F-6BG381C** (caBGA-381)

## Usage by function

| Function | Pin count | Notes |
| -------- | --------- | ----- |
| DDR4 PHY (J2 Live socket) | ~72 | DDR I/O bank, includes DQ/DQS/ADDR/CMD/CK |
| PCIe Gen2 x1 endpoint | 4 + 4 | 1 SerDes lane (of 4) + control signals |
| SPD I2C bus (shared J2/J3/J4) | 2 | SDA, SCL — broadcast |
| QSPI bitstream flash (W25Q128) | 6 | Dedicated config bank |
| LEDs + buttons + UART + PMOD | ~30 | Spread across banks B0/B1/B6 |
| **Total used** | **~112** | of ~200 available |
| **Spare** | **~88** + 3 SerDes lanes | Future accelerators |

## Bank assignment strategy

- **DDR I/O bank** — DDR4 PHY only, VCCIO = 1.2V
- **B0/B1 (top)** — CM5 passthrough signals, VCCIO = 3.3V
- **B6/B7 (right)** — General I/O + peripherals, VCCIO = 3.3V
- **B2/B3 (left)** — Config + JTAG + debug, VCCIO = 3.3V
- **Dedicated JTAG pins** — TCK/TMS/TDI/TDO on the ECP5 (not counted in user I/O)

## SerDes lane allocation

- **SD0** — PCIe Gen2 to ASM1184e
- **SD1** — reserved (future PCIe x2 or 10GbE)
- **SD2, SD3** — spare

## KiCad checklist

- Assign VCCIO for each bank based on function
- Route DDR4 PHY on lowest-inductance layer to DDR I/O bank
- Verify pin function against Lattice pinout CSV (LFE5UM-85F-6BG381C)

## Related

- [[Bill of Materials]]
- [[DDR4 Routing Requirements]]
- [[PCIe Routing Requirements]]
