---
tags: [software, linux, ramcartd, litepcie]
created: 2026-07-01
---

# Software Stack

## Layers (top to bottom)

```
Applications           userspace programs on Raspberry Pi OS
     │
ramcartd               daemon that manages RAM cartridges
     │
Linux kernel (CM5)     Raspberry Pi OS
     │
LitePCIe driver        kernel module, talks to FPGA over PCIe
     │
ASM1184e               PCIe switch (transparent)
     │
LitePCIe endpoint      inside FPGA bitstream
     │
DMA engine             inside FPGA
     │
LiteDRAM               DDR4 controller inside FPGA
     │
DDR4 SO-DIMM           physical RAM (Live socket J2)
```

## Key components

### LitePCIe (FPGA-side + host-side)

- Provides PCIe endpoint on FPGA
- Exposes memory-mapped BAR + DMA channels to host
- Companion kernel module: `litepcie` (Linux)
- Char device: `/dev/litepcie0`

### ramcartd (userspace daemon)

Custom daemon on the CM5:

- Polls SPD I2C on all 3 sockets via FPGA-side I2C
- Identifies cartridge by SPD serial number
- Exposes live cartridge as `/dev/ramcart0` (block device)
- Reads `/etc/ramcartd/cartridges.toml` for per-cart config
- Runs `on_insert` hook when cartridge changes

### cartridges.toml example

```toml
[carts."SN_0xABCD1234"]
label = "scratch"
on_insert = "swapon /dev/ramcart0"

[carts."SN_0xEFGH5678"]
label = "build-cache"
on_insert = "mount /dev/ramcart0 /mnt/build-cache"

[carts."SN_0x9ABCDEF0"]
label = "vault"
on_insert = "cryptsetup open /dev/ramcart0 vault"
```

## Related

- [[Build Notes v2]]
- [[Phase 5 - ramcartd Daemon]]
