---
tags: [architecture, memory, ramcartd]
created: 2026-07-01
---
[[Software Architecture]][[Software Stack]]
# Memory Model

**Cartridge RAM is not system RAM.**

## The two memory pools

```
CM5 system RAM = onboard LPDDR (8 GB)
                 ↑ Linux runs here, standard system RAM
                 
DDR4 cartridge = FPGA-managed PCIe memory
                 ↑ closer to GPU VRAM than DRAM
                 ↑ accessed via DMA + memory-mapped PCIe regions
```

## Why not treat cartridge as system RAM?

- Latency: PCIe → FPGA → DDR4 is much slower than direct CM5 → LPDDR
- Coherency: no cache-coherent path to cartridge memory
- Hot-swap: system RAM must never disappear; cartridges can be pulled

## How Linux sees the cartridge

- `/dev/ramcart0` — block device
- `mmap()` — memory-mapped region backed by DMA
- Explicit DMA transfers for high-throughput workloads

## Use cases

- Swap space (`swapon /dev/ramcart0`) — high priority
- Scratch filesystem
- Build cache (persistent across boots if cart stays inserted)
- Encrypted vaults (LUKS on `/dev/ramcart0`)

## Related

- [[Build Notes v2]]
- [[Software Stack]]
- [[Signal Architecture]]
