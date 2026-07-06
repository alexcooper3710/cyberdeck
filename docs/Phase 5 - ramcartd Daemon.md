---
tags: [roadmap, phase-5, pending, software]
created: 2026-07-01
status: pending
---

# Phase 5 — ramcartd Userspace Daemon

**Goal:** Build the Linux userspace daemon that manages cartridge insertion, identification, and per-cartridge configuration.

## Tasks

- [ ] LitePCIe kernel module loaded on CM5's Pi OS
- [ ] Implement `ramcartd` in Rust or C:
  - [ ] SPD I2C polling via FPGA-side I2C
  - [ ] Cartridge identification by SPD serial
  - [ ] `/dev/ramcart0` block device exposure
  - [ ] Per-cartridge config from `/etc/ramcartd/cartridges.toml`
  - [ ] `on_insert` hook execution
- [ ] Wire up front-panel D1/D2/D3 LED logic
- [ ] Test cartridge swap detection (<500ms latency)

## Related

- [[Software Stack]]
- [[Phase 4 - Custom Carrier PCB]]
