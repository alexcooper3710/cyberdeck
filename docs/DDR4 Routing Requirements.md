---
tags: [hardware, ddr4, pcb, routing]
created: 2026-07-01
---

# DDR4 Routing Requirements — Live SO-DIMM (J2)

Only the **Live** DDR4 socket (J2) carries the full ~70-signal PHY. Cart bays (J3, J4) are SPD-I2C only and have trivial routing.

## Electrical

| Parameter | Value |
| --------- | ----- |
| Single-ended Z₀ | 40 Ω ±10% |
| Differential pair Z_diff | 80 Ω ±10% |
| Data rate | ~800 MT/s (DDR4-1600) |

## Signal groups

### Data (DQ) — length-matched within byte lane

Each byte lane (8 DQ + 1 DM + DQS diff pair) length-matches to that byte's own DQS reference to **±5 mil**. Byte lanes do NOT need to match each other — DDR4 does per-lane calibration.

- **Byte lane 0:** DQ[7:0] + DM0 + DQS0_p/n
- **Byte lane 1:** DQ[15:8] + DM1 + DQS1_p/n
- **Byte lane 2:** DQ[23:16] + DM2 + DQS2_p/n
- **Byte lane 3:** DQ[31:24] + DM3 + DQS3_p/n

### Address / Command — fly-by topology

ADDR[16:0], BA, BG, CS, ODT, CKE, RESET_n all use fly-by topology (signals visit each socket in physical order with a terminator at the far end). Length match to CK ±10 mil.

### Clock (CK_p / CK_n)

Longest trace in the DDR4 subsystem — sets the "T0" reference. Diff pair, ±2 mil within pair.

### Termination

- **VTT** (0.6V): TPS51200 IC, connected to ADDR/CMD line-end terminators
- **ODT**: on-die termination configured by SPD read at boot

## KiCad routing checklist

1. Route CK first — it's the reference
2. Route each byte lane's DQS pair
3. Route DQ within each byte lane, length-matching to that byte's DQS
4. Route ADDR/CMD in fly-by pattern
5. Add termination resistors + VTT rail
6. Run length-matching checker before finalizing

## Related

- [[Bill of Materials]]
- [[Power Tree]]
- [[FPGA Pin Allocation]]
