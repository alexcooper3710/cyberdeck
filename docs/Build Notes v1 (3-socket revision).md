# From-Scratch SBC — ECP5-85F Build Notes (final architecture)

**The project:** A Raspberry-Pi-equivalent single-board computer built around one FPGA chip and your salvaged laptop components. **No Raspberry Pi anywhere.** The ECP5-85F runs a soft RISC-V CPU, drives your DDR4 SO-DIMMs as real system RAM, connects to your SATA SSD/HDD, your laptop display, your laptop keyboard, and Gigabit Ethernet. Linux boots from microSD, runs in DDR4, and you wrote every layer of the system (or assembled it from open-source LiteX components, which is the same thing in this world).

---

## Why ECP5-85F is the right pick for this project

| Property | Value | Why it matters |
|---|---|---|
| Toolchain | Yosys + nextpnr + Project Trellis — 100% open source | You can read the source of every tool. No vendor binaries. |
| Logic capacity | 84,000 LUT4, 3.6 Mb BRAM | Big enough for RV32 CPU with caches + DDR + Ethernet + USB + SATA + HDMI + custom blocks, with headroom |
| Fmax (typical) | ~100 MHz for soft CPU with caches | Adequate for Linux to boot and be responsive (1996 Pentium-1 class) |
| SerDes lanes | 4 × 3.125 Gbps | Enough for SATA-3 + headroom |
| Package | 381-ball caBGA (17 × 17 mm) | Doable with hot-air rework; JLCPCB can assemble |
| Chip cost | ~$40 | Cheap |
| Hard IP | SerDes, PLLs, DDR I/O cells | Minimal — meaning you implement the rest |
| What's NOT hard IP | Memory controller, USB host, Ethernet MAC, SATA controller, video encoder, CPU | All of this is YOUR work via LiteX or hand-rolled HDL |

---

## Bill of Materials (ECP5-only SBC, no Pi)

| Designator | Part | Function | ~Price |
|---|---|---|---|
| **U1** | **Lattice LFE5UM-85F-6BG381C** | The FPGA (= the computer) | $40 |
| **J1** | TE 2042205-1 | LIVE DDR4 SO-DIMM socket | $4 |
| **J2** | TE 2042205-1 | Cart bay 1 (SPD-only) | $4 |
| **J3** | TE 2042205-1 | Cart bay 2 (SPD-only) | $4 |
| J4 | 7-pin SATA connector + 4-pin power | SATA SSD/HDD | $2 |
| J5 | microSD push-push socket | Boot media | $3 |
| J6 | HDMI Type A receptacle | Display | $2 |
| J7 | RJ45 magjack (HALO HFJ11-1G01E) | Ethernet | $3 |
| J8 | USB-A receptacle ×2 | Keyboard / peripherals | $4 |
| J9 | USB-C receptacle | Power input + debug UART | $1 |
| J10 | 2x5 0.1" header | JTAG | $1 |
| J11 | 2x4 0.1" header | PMOD expansion | $1 |
| J12 | 3.5mm audio jack | Audio out | $1 |
| U2 | KSZ9031RNX | Gigabit Ethernet PHY | $4 |
| U3 | USB3340 | USB 2.0 HS ULPI PHY | $3 |
| U4 | CS4344 (or PCM5102A) | Stereo I2S audio DAC | $3 |
| U5 | TPD12S016 | HDMI ESD protection / level shift | $2 |
| U6 | W25Q128JV | 16 MB QSPI flash (FPGA bitstream) | $2 |
| U7 | CP2102N | USB-UART for FPGA debug console | $3 |
| U8-U10 | TPS54331 ×3 | Buck regulators (5V, 1.2V VDD, 1.1V VCCINT) | $6 |
| U11 | TPS51200 | DDR4 VTT termination IC | $2 |
| U12-U14 | AP2112K ×3 | LDOs (3.3V, 1.8V, 2.5V VPP) | $1.50 |
| Y1 | SiTime SiT8208 50 MHz | FPGA system clock | $2 |
| Y2 | 25 MHz xtal | Ethernet PHY ref clock | $1 |
| D1-D3 | RGB common-anode LEDs | Front-panel cart status | $1 |
| SW1-SW3 | Tactile pushbuttons | Reset, boot mode, user button | $1 |
| Passives | ~250 (decoupling, biasing, filters) | Throughout | $15 |
| PCB | 6-layer, ~150×100 mm, ENIG, 5-pack JLCPCB | | $65 |
| **TOTAL BOM** | | | **~$180** |

**Compare to the Pi-CM design (~$245):** dropping the Pi and the PCIe switch saves ~$65. The ECP5-only build is the **cheapest** version of any architecture we've discussed.

Plus your salvaged parts (worth nothing in BOM but everything in spirit): 3× DDR4 SO-DIMM, 1× SATA SSD/HDD, 1× laptop display, 1× laptop keyboard, 1× speakers, fans, battery.

---

## ECP5 pin budget — does it fit?

| Function | Pins | Notes |
|---|---|---|
| DDR4 PHY (LIVE socket) | ~70 | DQ[31:0], DQS×4 diff, ADDR[16:0], BA, BG, CK diff, control |
| SPD I2C bus (shared, 3 sockets) | 2 | SCL, SDA |
| HDMI TMDS output | 8 | 3 data pairs + clock pair, on LVDS bank |
| RGMII Ethernet | 12 | TX[3:0], TXC, TX_CTL, RX[3:0], RXC, RX_CTL |
| MDIO (Ethernet management) | 2 | MDC, MDIO |
| USB ULPI | 12 | DATA[7:0], CLK, DIR, NXT, STP |
| I2S audio | 4 | BCLK, LRCLK, DOUT, MCLK |
| SDIO microSD | 6 | CMD, CLK, DAT[3:0] |
| **SATA-3 (uses 1 SerDes lane)** | 4 | TX±, RX± — SerDes pins, not regular I/O |
| QSPI flash | 6 | CS, CLK, IO[3:0] |
| Debug UART | 2 | TXD, RXD |
| JTAG | 4 | TCK, TMS, TDI, TDO (dedicated pins, not counted in 200) |
| RGB LEDs ×3 (common-anode) | 9 | 3 × R/G/B |
| Buttons (reset, boot, user) | 3 | |
| PMOD header | 8 | User expansion |
| Fan PWM control | 2 | 2 fan headers |
| **Total user I/O** | **~150** | of ~200 available |
| **SerDes lanes used** | **1 of 4** | The rest are free for future expansion (PCIe, 10GbE, etc.) |

**Verdict: fits comfortably.** ~50 pins and 3 SerDes lanes left for whatever else you want — PCIe slot for an external GPU, second SATA, anything.

---

## FPGA SerDes allocation (only 4 lanes)

| Lane | Use | Speed |
|---|---|---|
| **SD0** | SATA-3 to your SSD/HDD | 3.0 Gbps |
| SD1 | (Reserved — could host PCIe x1 endpoint later) | — |
| SD2 | (Reserved — future SATA #2, 10GbE, etc.) | — |
| SD3 | (Reserved) | — |

HDMI does **not** use SerDes — it uses LVDS pairs on a regular I/O bank, which can run at ~1.5 Gbps each (enough for 1080p TMDS). So the 4 SerDes lanes stay free for high-speed I/O upgrades down the road.

---

## The software stack — every layer is yours

```
                ┌──────────────────────────────────┐
APPLICATIONS    │ shells, vim, gcc, python,        │
                │ ssh, mpg123, nethack, mc, etc.   │
                ├──────────────────────────────────┤
USERSPACE       │ Buildroot rootfs you built       │
                │ from a list of Buildroot packages│
                ├──────────────────────────────────┤
KERNEL          │ Linux 6.x for riscv32, configured│
                │ with YOUR device tree            │
                │ (drivers: litepcie, ramcartd,    │
                │  liteeth, litesdcard, litesata)  │
                ├──────────────────────────────────┤
BOOTLOADER      │ U-Boot for RV32, custom board    │
                │ file describing your SoC         │
                ├──────────────────────────────────┤
BOOT ROM        │ ~200 lines of RV32 assembly you  │
                │ wrote — sets up clocks, copies   │
                │ U-Boot from QSPI to DRAM, jumps  │
                ├──────────────────────────────────┤
SOC (FPGA)      │ VexRiscv CPU + LiteDRAM +        │
                │ LiteEth + LiteUSB + LiteSDCard + │
                │ LiteSATA + HDMI encoder +        │
                │ cartridge logic + custom HDL     │
                │ (you wrote / configured via      │
                │  LiteX Python)                   │
                ├──────────────────────────────────┤
SILICON         │ ECP5-85F LUTs, BRAMs, SerDes,    │
                │ PLLs — the only layer you didn't │
                │ make.                            │
                └──────────────────────────────────┘
```

You touch every layer above the silicon. That's the project.

---

## Realistic timeline & cost (final)

| Phase | Time | Cost |
|---|---|---|
| Schematic in KiCad | 3–6 weeks | $0 |
| PCB layout (DDR4 dominates) | 6–10 weeks | $0 |
| First PCB fab (6-layer, JLCPCB) | 2 weeks | $65 |
| First assembly run | 1 week | $120 (JLCPCB) |
| Hardware bring-up | 3–6 weeks | $20 (cables, headers) |
| **Likely first respin** | + 4 weeks | $150 (fab + assembly) |
| LiteX SoC config + Linux port | 4–8 weeks | $0 |
| Custom drivers + userspace | 2–4 weeks | $0 |
| **TOTAL** | **5–8 months solo, evenings/weekends** | **~$425–550** |

Plus tooling if you don't have it: ~$200 for soldering setup, JTAG cable, USB logic analyzer.

**Total realistic project cost: $500–800.**

That's *less* than the Pi-CM hybrid design and substantially less than any Zynq path. The catch is performance — the soft CPU is genuinely slow. But for "I built every layer myself," it's the right chip at the right price.

---

## On-ramp before committing to a custom PCB (still strongly recommended)

You're considering a project that has a lot of "first times" stacked on top of each other:

- First custom PCB (probably)
- First multi-layer board with DDR routing
- First BGA assembly
- First FPGA SoC bring-up
- First Linux port to custom hardware
- First custom kernel drivers

That's a lot. **Don't do all of them at once.** Do them in stages:

### Step 1 (1–2 weekends): Learn LiteX on someone else's board

Buy a **Colorlight 5A-75B** (~$20 on Aliexpress) — it's an ECP5-25F module with DDR3 already on it. The LiteX community has ported Linux to it specifically as a teaching board. Follow the tutorial:

```bash
git clone https://github.com/litex-hub/linux-on-litex-vexriscv
cd linux-on-litex-vexriscv
./make.py --board=colorlight_5a_75b --build --load
```

In an afternoon you'll see a Linux prompt on a board you didn't design. **That tells you the software side works.** Now you know you can actually bring up the FPGA side of the project.

### Step 2 (2–4 weeks): Design a DDR3 starter board

Make a simple 4-layer board: ECP5-25F + one DDR3 SO-DIMM socket + microSD + UART. Get it manufactured for ~$50. Bring it up. Run Linux. **You've now done the hardware side once on easier rules.**

### Step 3 (4–6 months): Design THIS board

Now design the 6-layer DDR4 + full I/O ECP5-85F SBC. You've already debugged the LiteX flow, the DDR controller, the PCB process. You know what you're doing. Success probability goes from ~30% (cold attempt) to ~70%.

Total time for all three: roughly the same as one heroic attempt at this board cold. Total cost: maybe an extra $100. But the success rate is dramatically higher.

---

## What you have when this is done

A 150×100 mm, 6-layer, custom-designed single-board computer that:
- Boots Linux from a microSD card you put in
- Mounts your laptop SATA SSD as the root filesystem
- Uses your DDR4 SO-DIMMs as actual system RAM (one live, two hot-swap cart bays)
- Drives your laptop display via HDMI at 1080p
- Accepts input from your laptop keyboard via USB
- Plays sound through your laptop speakers via I2S audio
- Has gigabit Ethernet
- Runs on your laptop battery via a USB-C PD trigger
- Has a soft RISC-V CPU at ~100 MHz that **you put together**

Slow as a Pentium 1, but every transistor in the data path is one you chose. The Raspberry-Pi-equivalent you built from scratch.

This is the project. ECP5-85F is the right chip. Total ~$500–800 over 5–8 months. Salvaged parts get used. RAM is real RAM.
