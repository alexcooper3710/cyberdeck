---
tags: [hardware, bom, sourcing]
created: 2026-07-01
---

# Bill of Materials

Full component list for the cyberdeck carrier PCB. Also see raw CSV: `06-KiCad-Project/cyberdeck-carrier/parts_list.csv`.

## Active components

| Designator | Part | Function | Package | Qty | Source |
| ---------- | ---- | -------- | ------- | --- | ------ |
| U1 | Raspberry Pi CM5 8GB Lite | Main host | SoM | 1 | Raspberry Pi |
| U2 | LFE5UM-85F-6BG381C | FPGA (ECP5-85F w/ SerDes) | caBGA-381 | 1 | Mouser/Digi-Key |
| U3 | ASM1184e | PCIe Gen2 1→2 switch | QFN-48 | 1 | LCSC |
| U4 | W25Q128JV | 128Mb SPI flash | SOIC-8 | 1 | Digi-Key |
| U5 | CP2102N-A02 | USB-UART debug | QFN-28 | 1 | Digi-Key |
| U6-U8 | TPS54331 ×3 | Buck regulators | SOIC-8 | 3 | Digi-Key |
| U9 | AP2112K-1.8 | 1.8V LDO | SOT23-5 | 1 | Digi-Key |
| U10 | TPS51200 | DDR4 VTT | SOIC-8 | 1 | Digi-Key |
| U11 | AP2112K-2.5 | 2.5V LDO | SOT23-5 | 1 | Digi-Key |
| U12 | CS4344 | I2S audio DAC (optional) | TSSOP-10 | 1 | Digi-Key |

## Connectors

| Designator | Part | Function | Qty |
| ---------- | ---- | -------- | --- |
| J1 | M.2 M-key 2280 | NVMe socket | 1 |
| J2 | TE 2042205-1 | DDR4 SO-DIMM (Live) | 1 |
| J3, J4 | TE 2042205-1 ×2 | DDR4 SO-DIMM (cart bays) | 2 |
| J5 | USB Type-C receptacle | Power input (PD) | 1 |
| J6, J7 | Hirose DF40C-100DS-0.4V | CM5 board-to-board (pair) | 2 |
| J8 | HDMI Type A | Display passthrough | 2 |
| J9 | USB-A | USB passthrough | 2 |
| J10 | RJ45 magjack HALO HFJ11-1G01E | GbE | 1 |
| J11 | 2×5 0.1" header | JTAG | 1 |
| J12 | 2×4 0.1" header | PMOD | 2 |
| J13 | 3.5mm audio jack | Audio out (optional) | 1 |

## Clocks

| Designator | Part | Function |
| ---------- | ---- | -------- |
| Y1 | SiTime SiT8208 50 MHz | FPGA system clock |
| Y2 | Crystal 100 MHz LVDS | PCIe REFCLK |

## Front panel

| Designator | Part | Function |
| ---------- | ---- | -------- |
| D1, D2, D3 | RGB common-anode LEDs ×3 | Live + cart status |
| SW1, SW2, SW3 | Tactile pushbuttons ×3 | Reset, boot, user |

## Passives (bulk)

| Description | Package | Qty |
| ----------- | ------- | --- |
| 100nF ceramic decoupling | 0402 | 100 |
| 10µF ceramic bulk | 0805 | 30 |
| 4.7k I2C pullup / general | 0402 | 20 |
| Ferrite bead | 0603 | 10 |

## Cost estimate

Carrier subtotal ~$215, CM5 module ~$80 → **~$295 total BOM**. Plus PCB fab (~$65 for 5-pack 6-layer JLCPCB) + assembly (~$120 turnkey) = **~$480 for a small run**.

## Related

- [[Component Sourcing]]
- [[Build Notes v2]]
