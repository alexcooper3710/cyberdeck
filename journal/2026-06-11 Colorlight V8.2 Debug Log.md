---
tags: [journal, colorlight, bring-up, lessons]
created: 2026-07-01
date: 2026-06-11
---

# 2026-06-11 — Colorlight V8.2 Debug Log

Long day of trying to bring up the 5A-75B V8.2 with an FT232H breakout. Ended with the U31 SPI flash almost certainly damaged from heat cycling. Documenting lessons so the next attempt is clean.

## What I tried

1. **SPI flash direct programming via SOIC clip on U31**
   - Started with a 16-pin clip that doesn't fit SOIC-8 → garbage readings
   - Got a proper SOIC-8 clip → still inconsistent readings
   - Soldered wires directly to U31 pins → progressively degraded from 0x05 → 0xFB → 0xFF
   - Conclusion: **U31 damaged from heat cycling**

2. **JTAG via community-guessed pads**
   - First tried J27/J31/J23/J30 (J23 doesn't exist — my error)
   - Fixed to J27/J31/J32/J30 per V8.2 authoritative source
   - Got past FTDI-open, sees the chain being clocked, but "0 devices"
   - Cause unclear without a multimeter — could be cold joint, FPGA damage, or VCCINT rail not up

## Lessons for the next Colorlight

### Do

- ✅ Have a **multimeter** before starting — non-optional
- ✅ Use JTAG (J27/J31/J32/J30/J33/J34), never touch U31
- ✅ Power the board normally via J18 screw terminals with 5V — no back-feed
- ✅ Verify every solder joint with continuity before applying power
- ✅ Use 30 AWG magnet wire or ribbon-cable wires for JTAG connections
- ✅ Use flux paste — makes soldering dramatically easier

### Don't

- ❌ Solder to U31 directly — SPI flash path is a trap
- ❌ Use a 16-pin clip on an 8-pin SOIC
- ❌ Assume community pinouts apply to your specific board rev
- ❌ Try to debug via wire wiggling / resoldering without measuring
- ❌ Skip the multimeter to save $10

## Time cost

Approximately 12 hours of active debugging. Would have been ~30 minutes with a multimeter and the authoritative pinout in hand.

## Money cost

$10 FT232H breakout + ~$8 SOIC clip = $18 in tooling that's still useful for the project.
The dead Colorlight is a $20 lesson.

## What transferred

- ✅ Toolchain (openFPGALoader, udev, sudo workarounds)
- ✅ JTAG concepts and wiring conventions
- ✅ Soldering practice (especially SMD-adjacent work)
- ✅ Understanding of ECP5 config flow
- ✅ [[FT232H Setup Guide]] — permanent reference
- ✅ [[Colorlight V8.2 JTAG Pinout]] — authoritative

## Next attempt plan

1. Order fresh Colorlight 5A-75B ($20) + $10 multimeter
2. Solder JTAG header only (never U31)
3. Verify continuity on all 6 joints before applying power
4. Load blinky bitstream → confirm toolchain
5. Move to Phase 2 (LiteX + Linux boot)

## Related

- [[Colorlight V8.2 JTAG Pinout]]
- [[FT232H Setup Guide]]
- [[Phase 1 - FPGA Bring-Up]]
