# Flashing Guide for Jetson Orin NX with QBITS Carrier Board

## Table of Contents
- [Downloading Resources](#downloading-resources)
- [System Installation](#system-installation)
- [Applying Kernel Patches](#applying-kernel-patches)
- [Flashing](#flashing)
- [Issues](#issues)

## Downloading Resources
1. Go to [Jetson Linux Archive](https://developer.nvidia.com/embedded/jetson-linux-archive).
2. Choose Jetson [Linux 35.4.1](https://developer.nvidia.com/embedded/jetson-linux-r3541) is part of [JetPack 5.1.2](https://developer.nvidia.com/embedded/jetpack-sdk-512) or the most recent release.
3. Download the following files:
   - [Driver Package (BSP)](https://developer.nvidia.com/downloads/embedded/l4t/r35_release_v4.1/release/jetson_linux_r35.4.1_aarch64.tbz2)
   - [Sample Root Filesystem](https://developer.nvidia.com/downloads/embedded/l4t/r35_release_v4.1/release/tegra_linux_sample-root-filesystem_r35.4.1_aarch64.tbz2)

## System Installation
1. Open the terminal on the Ubuntu computer or the host, and create a new file folder:
```bash
sudo mkdir sources_orin
cd sources_orin
```
2. Move the resource pack to the file folder and unzip it. (In practice, please try to use the tab key to autocomplete commands.)
```bash
sudo mv ~/Downloads/Jetson_Linux_R35.4.1_aarch64.tbz2 ~/sources_orin/            
sudo mv ~/Downloads/Tegra_Linux_Sample-Root-Filesystem_R35.4.1_aarch64.tbz2 ~/sources_orin/
```
3. Unzip the resource.
```bash
sudo tar -xjf Jetson_Linux_R35.4.1_aarch64.tbz2
cd Linux_for_Tegra/rootfs/
sudo tar -xjf ../../Tegra_Linux_Sample-Root-Filesystem_R35.4.1_aarch64.tbz2
cd ../
sudo ./apply_binaries.sh
sudo ./tools/l4t_flash_prerequisites.sh
```
## Applying Kernel Patches

Before flashing, apply the following kernel patches:

- Copy `*.dtsi` files to `Linux_for_Tegra/bootloader/t186ref/BCT`
- Copy `*.dtb` files to `Linux_for_Tegra/kernel/dtb`
- Copy `*.conf` files to `Linux_for_Tegra/`

Edit `Linux_for_Tegra/bootloader/t186ref/BCT/tegra234-mb2-bct-misc-p3767-0000.dts`:

```diff
- cvb_eeprom_read_size = <0x100>
+ cvb_eeprom_read_size = <0x0>
```

## Flashing

Run the following command to flash:

```bash
sudo ./tools/kernel_flash/l4t_initrd_flash.sh --external-device nvme0n1p1 \
	 -c tools/kernel_flash/flash_l4t_external.xml \
	 -p "-c bootloader/t186ref/cfg/flash_t234_qspi.xml --no-systemimg" --network usb0 \
	 qbits-orin-nx nvme0n1p1
```
## Install jtop - The Ultimate Tool for Monitoring NVIDIA Jetson Devices

```bash
sudo apt update
sudo apt install python3-pip
```
After pip installs:

```bash
sudo pip3 install -U jetson-stats
```
Once pip installs the jetson-stats package, you will need to logout/login or reboot the Jetson.
After rebooting, to run jtop open a Terminal and execute:

```bash
jtop
```
## Issues
If you received `RTNETLINK answers: Permission denied` while using `RCM-BOOT`:

To resolve the issue, add the following lines to `/etc/sysctl.conf`:
```
net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.default.disable_ipv6 = 0
net.ipv6.conf.lo.disable_ipv6 = 0
```
Followed by reboot or just:
```
sudo sysctl -p
```
