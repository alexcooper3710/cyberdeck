---
tags: [ft232h, openfpgaloader, setup, ubuntu, linux]
created: 2026-07-01
---

# FT232H Setup Guide (Ubuntu Laptop)

## 1. Install openFPGALoader

Fastest: apt package for early experimentation.

```bash
sudo apt update
sudo apt install openfpgaloader
openFPGALoader --version
```

For the full toolchain (Yosys + nextpnr-ecp5 + ecppack), download **oss-cad-suite** from [YosysHQ releases](https://github.com/YosysHQ/oss-cad-suite-build/releases/latest) — grab `oss-cad-suite-linux-x64-*.tgz`, extract, and:

```bash
cd ~
tar -xzf ~/Downloads/oss-cad-suite-linux-x64-*.tgz
echo "source $HOME/oss-cad-suite/environment" >> ~/.bashrc
source ~/.bashrc
```

## 2. udev rule (avoid needing sudo)

```bash
echo 'ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6014", MODE="0666", GROUP="plugdev", TAG+="uaccess"' | sudo tee /etc/udev/rules.d/60-ftdi.rules
sudo usermod -aG plugdev $USER
sudo udevadm control --reload-rules
sudo udevadm trigger
```

Then **unplug FT232H, wait 5 seconds, plug back in**, and log out + log back in so plugdev takes effect.

## 3. Confirm device visibility

```bash
lsusb | grep 0403
# Expect: Bus XXX Device XXX: ID 0403:6014 Future Technology Devices International, Ltd FT232H
```

If the kernel `ftdi_sio` module claims it as `/dev/ttyUSB0`, unbind:

```bash
ls /dev/ttyUSB*                                 # if it exists, ftdi_sio grabbed the device
ls /sys/bus/usb/drivers/ftdi_sio/               # note the interface path (like "3-2:1.0")
echo -n "3-2:1.0" | sudo tee /sys/bus/usb/drivers/ftdi_sio/unbind
```

## 4. sudo PATH fallback

sudo's stripped PATH sometimes can't find openFPGALoader. Workaround:

```bash
sudo $(which openFPGALoader) -c ft232 --detect
```

## Gotchas caught the hard way

- The command name is **case-sensitive**: `openFPGALoader` (not `openfpgaloader`)
- If you get `libusb error -4 (no such device)`: unplug/replug + retry with sudo
- If you get `read id failed`: TDI/TDO probably swapped
- If you get `TDO stuck at 0`: AD2 wire is grounded or on wrong pad
- If you get `TDO stuck at 0xFF`: TDO line floating (no connection)
- If you get `found 0 devices`: wiring plausible but no JTAG signal — check pad assignments

## Related

- [[Colorlight V8.2 JTAG Pinout]]
- [[2026-06-11 Colorlight V8.2 Debug Log]]
