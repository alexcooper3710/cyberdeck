---
tags: [hardware, power, regulators]
created: 2026-07-01
---

# Power Tree

Input: **5V from USB-C PD trigger** (J5). Total system: ~12–18 W.

## Rail summary

| Rail | Regulator | Loads |
| ---- | --------- | ----- |
| **3.3V** | TPS54331 (U6) | CM5 main, ASM1184e, W25Q128, CP2102N, LEDs, I2C, PMOD, FPGA VCCIO banks |
| **1.2V** | TPS54331 (U7) | DDR4 VDD + FPGA DDR4 PHY bank |
| **1.1V** | TPS54331 (U8) | FPGA VCCINT (core only — clean ripple critical) |
| **1.8V** | AP2112K (U9) | FPGA VCCAUX + SerDes PLL |
| **2.5V** | AP2112K (U11) | DDR4 VPP |
| **0.6V** | TPS51200 (U10) | DDR4 VTT (address/cmd termination) |
| **~12V** (via CM5 PMIC) | 5V input | CM5 core rails (internal to CM5) |

## Sequencing

Manual sequencing (all rails come up on 5V-good) is acceptable for this design. If a cleaner sequence is needed later, add a sequencer IC (LTC2937 or similar).

## Per-rail decoupling

Each active IC needs local decoupling per its datasheet. Typical:

- 100 nF ceramic per VCC pin (0402)
- 10 µF bulk per rail branch (0805)
- Ferrite bead between switchers and sensitive analog rails
- Star grounding on VCCINT/VCCAUX for the FPGA

## Estimated peak currents

- 3.3V: ~1.5 A
- 1.2V: ~2 A (DDR4 under load)
- 1.1V: ~2 A (FPGA fully utilized)
- 1.8V: ~0.3 A
- 2.5V/VTT/VPP: <200 mA each

## Related

- [[Bill of Materials]]
- [[DDR4 Routing Requirements]]
