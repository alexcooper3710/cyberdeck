---
tags: [hardware, layout, pcb]
created: 2026-07-01
---

# Board Layout Visualization

Approximate physical placement on the 150×100 mm 6-layer carrier PCB (top view):

```
┌────────────────────────────────────────────────────────────────┐
│                                                                │
│              ┌──────────────────────────┐                      │
│              │  CM5 Board-to-Board      │                      │
│              │  Connectors (DF40C-100DS)│                      │
│              │  [J6]                    │                      │
│              │  [J7]                    │                      │
│              └──────────────────────────┘                      │
│                                                                │
│  ┌──────┐   ┌────────────┐  ┌────┐  ┌─────────────────────┐   │
│  │ M.2  │   │            │  │ASM │  │ J2 Live DDR4        │   │
│  │ NVMe │   │  ECP5-85F  │  │1184│  │ (full PHY wired)    │   │
│  │ [J1] │   │  [U2]      │  │[U3]│  ├─────────────────────┤   │
│  ├──────┤   │  BGA-381   │  └────┘  │ J3 Cart Bay 1       │   │
│  │[U4]  │   │            │          │ (SPD I2C only)      │   │
│  │flash │   └────────────┘          ├─────────────────────┤   │
│  └──────┘                           │ J4 Cart Bay 2       │   │
│                                     │ (SPD I2C only)      │   │
│                                     └─────────────────────┘   │
│                                                                │
│  ┌───────────────┐  ┌──────────────┐  ┌──────────────────┐    │
│  │ Power Tree    │  │ Front Panel  │  │ CM5 I/O          │    │
│  │ (regulators)  │  │ D1 D2 D3 SW1 │  │ HDMI x2, USB x2, │    │
│  │ USB-C in [J5] │  │ SW2 SW3      │  │ GbE, 40-pin GPIO │    │
│  └───────────────┘  └──────────────┘  └──────────────────┘    │
│                                                                │
└────────────────────────────────────────────────────────────────┘
                     ~150 mm × ~100 mm
```

## Placement rationale

- **CM5 top-center** — allows straight PCIe routing to ASM1184e directly below
- **FPGA left-center** — short DDR4 routes to J2, short PCIe routes to ASM1184e
- **3 SO-DIMM sockets right side** — accessible for cartridge swap, edge-mounted
- **M.2 NVMe far-left** — separates PCIe fanout, easy access to SSD
- **Power tree bottom-left** — keeps switching noise away from FPGA/DDR
- **Front panel bottom-center** — visible from cyberdeck front, buttons accessible

## Related

- [[KiCad Sheet Structure]]
- [[DDR4 Routing Requirements]]
