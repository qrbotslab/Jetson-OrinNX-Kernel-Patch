# Flashing Guide for Jetson Orin for QBITS Carrier Board

## Table of Contents
- [Downloading Resources](#downloading-resources)
- [Setting Up Resources](#setting-up-resources)
- [Applying Kernel Patches](#applying-kernel-patches)
- [Flashing](#flashing)
- [Note](#note)

## Downloading Resources
1. Go to [Jetson Linux Archive](https://developer.nvidia.com/embedded/jetson-linux-archive).
2. Choose version 35.4.1 or the relevant latest version.
3. Download the following files:
   - [jetson_linux_r35.4.1_aarch64.tbz2](https://developer.nvidia.com/downloads/embedded/l4t/r35_release_v4.1/release/jetson_linux_r35.4.1_aarch64.tbz2)
   - [tegra_linux_sample-root-filesystem_r35.4.1_aarch64.tbz2](https://developer.nvidia.com/downloads/embedded/l4t/r35_release_v4.1/release/tegra_linux_sample-root-filesystem_r35.4.1_aarch64.tbz2)

```bash
sudo mkdir sources_orin
cd sources_orin 
sudo mv ~/Downloads/jetson_linux_r35.4.1_aarch64.tbz2 ~/sources_orin/            
sudo mv ~/Downloads/tegra_linux_sample-root-filesystem_r35.4.1_aarch64.tbz2 ~/sources_orin/  
sudo tar -xjf jetson_linux_r35.4.1_aarch64.tbz2
cd Linux_for_Tegra/rootfs/
sudo tar -xjf ../../tegra_linux_sample-root-filesystem_r35.4.1_aarch64.tbz2
cd ../
sudo ./apply_binaries.sh
sudo ./tools/l4t_flash_prerequisites.sh

## Applying Kernel Patches

Before flashing, apply the following kernel patches:

- Copy *.dtsi files to `Linux_for_Tegra/bootloader/t186ref/BCT`
- Copy *.dtb files to `Linux_for_Tegra/kernel/dtb`
- Copy *.conf files to `Linux_for_Tegra/`

Edit `Linux_for_Tegra/bootloader/t186ref/BCT/tegra234-mb2-bct-misc-p3767-0000.dts`:

```diff
- cvb_eeprom_read_size = <0x100>
+ cvb_eeprom_read_size = <0x0>


## Flashing

Run the following command to flash:

```bash
sudo ./tools/kernel_flash/l4t_initrd_flash.sh --external-device nvme0n1p1 \
	 -c tools/kernel_flash/flash_l4t_external.xml \
	 -p "-c bootloader/t186ref/cfg/flash_t234_qspi.xml --no-systemimg" --network usb0 \
	 qbits-orin-nx nvme0n1p1

