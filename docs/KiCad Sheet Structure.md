---
tags: [kicad, hardware, hierarchical]
created: 2026-07-01
---

# KiCad Hierarchical Sheet Structure

## Top sheet: `carrier.kicad_sch`

Contains only 7 sheet symbols linking to the sub-sheets below. No components at top level — it's pure glue.

## Sub-sheets

| Sheet | Contents | Nets crossing boundary |
| ----- | -------- | ---------------------- |
| `fpga_core.kicad_sch` | ECP5-85F + decoupling + JTAG + QSPI | ~80 nets out |
| `ddr4_banks.kicad_sch` | J2/J3/J4 SO-DIMM sockets + VTT termination | ~80 nets in |
| `cm5_carrier.kicad_sch` | CM5 B2B connectors + HDMI/USB/GbE/GPIO | ~120 nets in/out |
| `pcie_switch.kicad_sch` | ASM1184e + M.2 NVMe + REFCLK gen + AC caps | 12 nets |
| `power_tree.kicad_sch` | USB-C input + all regulators | Rails to all sheets |
| `peripherals.kicad_sch` | Front-panel LEDs + buttons + PMOD + audio DAC | ~30 nets to FPGA |
| `debug.kicad_sch` | CP2102N USB-UART + JTAG header + test points | Isolated |

## Key buses crossing sheet boundaries

- **DDR4 PHY:** `fpga_core` ↔ `ddr4_banks` — ~72 signals
- **PCIe SerDes:** `fpga_core` ↔ `pcie_switch` ↔ `cm5_carrier` — diff pairs + REFCLK + PERST
- **SPD I2C:** `fpga_core` ↔ `ddr4_banks` — 2 wires, broadcast
- **GPIO / status:** `fpga_core` ↔ `peripherals` — LEDs, buttons, PMOD
- **Power rails:** `power_tree` → all sheets — 3V3, 1V2, 1V1, 1V8, 2V5, VTT, GND
- **Debug:** `debug` ↔ `fpga_core` — UART RX/TX, JTAG dedicated pins

## Recommended fill order

1. `power_tree` (easiest, foundational)
2. `debug` (isolated, low risk)
3. `peripherals` (moderate)
4. `cm5_carrier` (many pins, mostly passthroughs)
5. `pcie_switch` (PCIe routing critical)
6. `ddr4_banks` (DDR4 signals need care)
7. `fpga_core` (final wiring, connects everything)

## Related

- [[KiCad Getting Started]]
- [[FPGA Pin Allocation]]
