---
tags: [roadmap, phase-2, pending]
created: 2026-07-01
status: pending
---

# Phase 2 — LiteX Fundamentals

**Goal:** Run `linux-on-litex-vexriscv` on the dev board and see Linux boot over UART.

## Prerequisites

- Phase 1 complete (bitstream flashing works)
- USB-UART adapter (or use FT232H in UART mode)

## Tasks

- [ ] Clone `linux-on-litex-vexriscv` repository
- [ ] Build for `colorlight_5a_75b --revision=8.0`
- [ ] Wire UART pins (find via silkscreen or docs)
- [ ] Flash bitstream, connect terminal at 115200 baud
- [ ] Watch Linux boot to shell

## Follow-up learning

- Build minimal custom LiteX SoC (just CPU + UART, no DRAM) to understand generator flow
- Explore LitePCIe example bitstreams
- Explore LiteDRAM configuration for DDR3/DDR4

## Related

- [[Phase 1 - FPGA Bring-Up]]
- [[Phase 3 - PCIe Memory Prototype]]
