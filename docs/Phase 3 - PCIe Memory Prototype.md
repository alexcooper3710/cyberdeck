---
tags: [roadmap, phase-3, pending, pcie]
created: 2026-07-01
status: pending
---

# Phase 3 — PCIe Memory Device Prototype

**Goal:** Prototype the cartridge memory subsystem using dev-board hardware to derisk the custom carrier design.

## Constraint

Colorlight and OrangeCrab both use LFE5U (no SerDes) — so *real* PCIe validation on those boards isn't possible. Options:

1. **Software-only prototype** — use LiteX simulator + LitePCIe testbench to validate DMA and register access logic without real PCIe
2. **Buy an ECPIX-5** (~$295) with LFE5UM5G-85F for real PCIe testing
3. **Skip to Phase 4** — do PCIe validation on the custom carrier itself

## Tasks (path 1 - simulation only)

- [ ] Design LiteX SoC with LitePCIe endpoint + DMA engine + LiteDRAM
- [ ] Run in LiteX simulator with PCIe testbench
- [ ] Develop `ramcartd` daemon against simulated PCIe device
- [ ] Test SPD reader logic in simulation

## Related

- [[Phase 4 - Custom Carrier PCB]]
- [[Software Stack]]
