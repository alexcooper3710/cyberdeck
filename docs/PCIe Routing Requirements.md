---
tags: [hardware, pcie, pcb, routing]
created: 2026-07-01
---

# PCIe Gen2 Routing Requirements

The PCIe subsystem connects: **CM5 (host) → ASM1184e switch → {FPGA endpoint, M.2 NVMe endpoint}**.

## Electrical

| Parameter | Value |
| --------- | ----- |
| Differential impedance | 100 Ω ±10% |
| Single-ended impedance | 50 Ω |
| Data rate | 5.0 Gbps (Gen2) |

## Diff pair topology

Each PCIe link is 2 diff pairs (TX and RX) plus a shared REFCLK diff pair.

### Length matching

- **Intra-pair skew**: within each P/N pair, ±5 mil (e.g., TX+ ↔ TX-)
- **Inter-pair skew**: all pairs on the same link within ±25 mil of each other

### AC coupling

- **TX pairs need 100 nF inline caps** (per PCIe spec)
- Place caps near the transmit end
- Ensures DC bias independence between endpoints

### REFCLK

- 100 MHz differential, low-jitter oscillator (Y2 in BOM)
- Route on same layer as data pairs
- Distributed to all endpoints (or generated locally by ASM1184e — check datasheet)

## Via strategy

- Minimize layer changes on diff pairs
- **Max 2 vias per signal** (transmitter and receiver ends only, ideally)
- If layer change unavoidable, add ground stitching vias near diff pair vias

## KiCad routing checklist

1. Route REFCLK first
2. Route each TX diff pair, length-match within pair
3. Route each RX diff pair, length-match within pair
4. Verify inter-pair skew across the link
5. Add AC coupling caps on TX pairs
6. Ground stitch around all diff pair vias

## Related

- [[Bill of Materials]]
- [[FPGA Pin Allocation]]
