---
tags: [hardware, sourcing, distributor]
created: 2026-07-01
---

# Component Sourcing Guide

## Primary distributors

- **Digi-Key** — most parts, fast US shipping
- **Mouser** — similar to Digi-Key, sometimes better stock on newer parts
- **LCSC** — best for Chinese-origin parts (ASM1184e, some LEDs, cheap passives)
- **JLCPCB parts library** — for turnkey assembly, use their "basic" catalog when possible to avoid extra setup fees

## KiCad symbols/footprints

- **SnapEDA** — [snapeda.com](https://www.snapeda.com/) — search by part number, download KiCad-ready
- **UltraLibrarian** — [ultralibrarian.com](https://www.ultralibrarian.com/) — similar to SnapEDA
- **Manufacturer sites** — Hirose, TE Connectivity, and Lattice publish their own KiCad libraries
- **Digi-Key symbol pages** — many DK product pages have symbol/footprint download links

## Verifying part availability before finalizing BOM

For any BGA-package IC (ECP5, ASM1184e), confirm:

1. **Multiple distributors carry it** — single-source is fragile
2. **JLCPCB assembly library has it** — otherwise you're placing parts yourself
3. **Not on last-time-buy** — Lattice occasionally EOLs specific speed grades

## Special notes

### ECP5-85F variants

- **LFE5U-85F** — no SerDes, can't do PCIe
- **LFE5UM-85F** — 4 SerDes @ 3.125 Gbps (PCIe Gen1)
- **LFE5UM5G-85F** — 4 SerDes @ 5 Gbps (PCIe Gen2 — **this is what the carrier needs**)

Same package (caBGA-381), same footprint — but only the -5G variants support Gen2.

### CM5 board-to-board connectors

The CM5 uses **Hirose DF40C-100DS-0.4V** (100-pin, 0.4mm pitch, 1.5mm stack height). You need TWO of them — the CM5 has connectors on both sides. Check the CM5 datasheet for the exact stack height and orientation.

### DDR4 SO-DIMM sockets (TE 2042205-1)

Only really available from TE Connectivity and their distributors. If the exact part is out of stock, TE 1734250-1 is the equivalent from the same series.

## Related

- [[Bill of Materials]]
