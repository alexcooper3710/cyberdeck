# Cyberdeck — Full Project Outline

*Starting point: 2026-06-02 → working machine in 7–10 months*

## Where You Are Right Now

**Hardware on hand:** Colorlight 5A-75B V8.2 (LFE5U-25F ECP5 + W25Q32 SPI flash at U31), FT232H breakout, Ubuntu laptop. **Pending:** SOIC-8 test clip (~$10, on order). **Salvaged:** 3× DDR4 SO-DIMMs, M.2 NVMe SSD, laptop display + keyboard + speakers + battery.

**End goal:** Custom 140×100mm 6-layer carrier PCB hosting a Raspberry Pi CM5 8GB Lite + a Lattice ECP5-85F, with three DDR4 SO-DIMM sockets (1 live PCIe-attached RAM, 2 identity-cartridge bays). Linux runs on the CM5; the FPGA controls the live RAM cart and exposes it as `/dev/ramcart0`. Enclosed with salvaged laptop parts.

---

## Phase 0 — Toolchain + First Bitstream
**~1–3 days · ~$10 (SOIC clip)**

The smoke test for the entire toolchain. Gated by SOIC clip arrival.

1. Install `oss-cad-suite` on Ubuntu (Yosys + nextpnr-ecp5 + ecppack + openFPGALoader)
2. udev rule for FT232H (vendor 0403, product 6014)
3. Clip onto U31, wire FT232H per SPI table, confirm `--detect-flash` reports `winbond W25Q32`
4. Build a Verilog blinky, `nextpnr-ecp5 --25k --package CABGA256`, `ecppack`, `openFPGALoader --write-flash`
5. Watch the LED blink

**What this proves:** Everything from `your_design.v` → bits on the FPGA works end-to-end. The riskiest setup unknowns are gone.

---

## Phase 1 — Linux on the Colorlight via LiteX
**~1–2 weekends · $0**

The biggest software de-risk. If this works, the rest is "just hardware."

1. `git clone https://github.com/litex-hub/linux-on-litex-vexriscv`
2. `./make.py --board=colorlight_5a_75b --revision=8.0 --build --load`
3. Wire UART (likely J27 — the 4-pin header you found) to a USB-serial adapter
4. `screen /dev/ttyUSB0 115200`
5. Watch Linux boot. Try `ls`, `vi`, `ping`.

**What this proves:** The LiteX Python flow generates working SoCs on real hardware. The VexRiscv CPU works. The DRAM controller works. You can do this.

**Decision point at the end of Phase 1:** do the DDR3 starter PCB (Phase 3) or skip straight to the final carrier (Phase 4). Recommendation: do the starter. Success on the final board jumps from ~30% to ~70% for an extra ~$100 and ~6 weeks.

---

## Phase 2 — KiCad Carrier Schematic
**~3–6 weeks evenings · $0 · runs in parallel with Phase 1 and beyond**

Schematic capture takes weeks regardless of skill. Start as soon as Phase 0 is done; weekends stay for LiteX work.

1. Install KiCad 8+
2. Build symbol/footprint library: ECP5 LFE5UM-85F-6BG381C, CM5 board-to-board pair (DF40C-100DS), TE 2042205-1 SO-DIMM socket, ASM1184e, W25Q128JV, the power-tree parts from the BOM
3. Hierarchical schematic — sheets: Top, FPGA core, DDR4 banks (Live + 2× cart bays), CM5 carrier interface, PCIe switch + M.2, power tree, peripherals (LEDs/buttons/USB-C/JTAG)
4. Verify pinout against the FPGA pin budget (~150 user I/O used of 200)
5. Pass ERC

**What this proves:** You understand how the parts connect. The pin budget actually fits.

---

## Phase 3 — DDR3 Starter PCB *(intermediate stop, strongly recommended)*

The point: do BGA fanout, DDR routing, and the JLCPCB workflow **once** on a board where most failure modes don't apply. ECP5-25F + 1× DDR3 SO-DIMM + microSD + UART. 4-layer, ~80×60mm.

### Phase 3a — Starter Schematic
**~2–4 weeks · $0**

Much simpler than the carrier — no PCIe, no CM5, no cart bays, easier DDR3 instead of DDR4.

### Phase 3b — Starter PCB Layout
**~4–6 weeks · $50 fab + $80 assembly**

BGA fanout, DDR3 length-match (±10 mil within byte lanes is fine for DDR3), fly-by ADDR/CMD, JLCPCB 4-layer 5-pack.

### Phase 3c — Starter Bring-up
**~2–4 weeks · $20 cables/headers**

Power-on smoke test, flash bitstream via U31's SPI flash equivalent, build LiteX with this board target, boot Linux from microSD.

**What this proves:** You can design, route, fab, assemble, and bring up a custom FPGA board. The expensive unknowns are now known.

*Possible respin if needed: +4 weeks, +$130.*

---

## Phase 4 — Final Carrier PCB

The real thing.

### Phase 4a — Carrier PCB Layout
**~6–10 weeks evenings · $65 fab + $120 assembly**

- 6-layer, ~140×100mm
- DDR4 routing on J3 (Live socket): ±5 mil byte-lane match, 40/80Ω controlled impedance, fly-by ADDR/CMD
- PCIe Gen2 differential routing: ECP5 SerDes ↔ ASM1184e ↔ {CM5 PCIe, M.2 NVMe}
- CM5 board-to-board mating
- Multi-rail power tree (5V/3.3V/1.8V/1.35V/1.2V VDD/1.1V VCCINT/0.6V VTT)
- Cart bay SPD-only fanout (12 traces, trivial)
- Front-panel LED/button routing

### Phase 4b — Carrier Bring-up
**~4–6 weeks · $20 cables/headers**

1. **Smoke test** with bench supply + current limiter (don't let USB-C PD trigger until you've verified no shorts)
2. **FPGA bring-up** via U31-equivalent SPI flash — same workflow as the Colorlight
3. **CM5 power-on** independently, see if it boots Pi OS off microSD
4. **PCIe enumeration:** `lspci -nn | grep Lattice` from CM5
5. **LiteDRAM bring-up** on J3 with a slotted DDR4 stick
6. **M.2 NVMe enumeration** through the ASM1184e

*Possible respin: +4 weeks, +$185.*

**What this proves:** the hardware works. Everything from here is software.

---

## Phase 5 — Software Stack
**~4–6 weeks · $0**

1. LitePCIe kernel module on the CM5's Pi OS
2. `ramcartd` userspace daemon — polls SPD via FPGA-side I2C, identifies live stick by serial, exposes `/dev/ramcart0` block device, runs per-cartridge config from `/etc/ramcartd/cartridges.toml`
3. Wire up D1/D2/D3 RGB LED logic for cart status indication
4. Verify cart swap: physically move stick from J3 to J4, daemon reacts in <500ms
5. Per-cart configs (swap, mount, LUKS, whatever you want each stick to mean)

---

## Phase 6 — Enclosure & Final Assembly
**~2–4 weeks · $50–200**

- Mechanical design (3D printed or sheet metal — cheap if you have a printer)
- Mount the carrier PCB
- Integrate laptop display (via LCD controller board, ~$25)
- Integrate laptop keyboard (via USB controller adapter, ~$10)
- USB-C PD trigger + laptop battery
- PWM fan (controlled by FPGA pin)
- Front-panel cutouts for D1/D2/D3 + buttons + cart slot access
- Speakers via I2S DAC (CS4344 already in BOM)

---

## Total Time & Cost

| | Time | Cost |
|---|---|---|
| **With DDR3 starter (recommended)** | 7–10 months | $700–1,100 |
| **Without starter (aggressive)** | 5–7 months | $500–800, higher respin risk |

Tooling not already owned (soldering, JTAG cable, USB logic analyzer): add ~$200 one-time if needed.

## Decision Points

1. **End of Phase 1** — DDR3 starter or skip?
2. **End of Phase 3c** — starter respin or proceed to final?
3. **Start of Phase 4b** — smoke escape → respin; clean power → proceed
4. **End of Phase 4b** — PCIe + DDR4 working → software; not working → debug or respin

## Right Now (this week)

1. Wait for SOIC clip to arrive
2. Install `oss-cad-suite` on the Ubuntu laptop
3. Start KiCad library work for the carrier (you can do this entirely from the BOM in your build notes — doesn't need any hardware)
