---
tags: [colorlight, jtag, ecp5, reference]
created: 2026-07-01
source: https://tekkix.com/articles/diy/2024/10/adaptation-of-the-colorlight-5a-75b-board-fo
---

# Colorlight 5A-75B V8.2 — JTAG Pinout

**Authoritative source:** Tekkix.com article by KoolASIC, adapting the 5A-75B for the "Digital Circuit Synthesis School." Author confirmed V8.2 board with LFE5U-25F-6BG256C.

## JTAG signal locations

| Signal | Silkscreen pad |
| ------ | -------------- |
| **TCK** | J27 |
| **TMS** | J31 |
| **TDI** | J32 |
| **TDO** | J30 |
| **VREF** (3V3) | J33 |
| **GND** | J34 |

Easiest to access these pads from the **back** of the board.

## FT232H (MPSSE mode) → JTAG wiring

| FT232H pin | JTAG signal | 5A-75B pad |
| ---------- | ----------- | ---------- |
| AD0 | TCK | J27 |
| AD1 | TDI | J32 |
| AD2 | TDO | J30 |
| AD3 | TMS | J31 |
| GND | GND | J34 |
| 3V3 (optional) | VREF | J33 |

**Power the Colorlight normally** via J17 or J18 (5V DC) — for JTAG the FPGA must be running. Do not back-feed through the FT232H.

## openFPGALoader command

```bash
sudo $(which openFPGALoader) -c ft232 --detect
```

Expected: an ECP5 IDCODE like `0x41111043` (LFE5U-25F).

## Also note

- **P5** — 25 MHz clock input to FPGA
- Board power via J17/J18 is 5V; J35/J36 are 3.3V (used only for LED-panel output buffer power, not general logic)

## Related

- [[FT232H Setup Guide]]
- [[2026-06-11 Colorlight V8.2 Debug Log]]
