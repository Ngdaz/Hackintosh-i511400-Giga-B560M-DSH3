# Hackintosh-i511400-Giga-B560M-DSH3-OpenCore

This repo contains information for getting macOS 12 Montery for a PC.

The compatibility is very good for the most part, and it behaves as a real I MAC Pro. In general, the experience is pleasant, both in macOS and Win11 dual boot.

Currently running:

| Component     | Version |
| ------------- | ------- |
| macOS version | 12.6    |
| OpenCore      | 0.8.9   |
| BIOS version  | F21     |

## Hardware info

| Component | Model                       |
| --------- | --------------------------- |
| CPU       | Intel i5-11400              |
| Memory    | 16GB 2666Mhz                |
| Storage1  | WD SN750 250GB              |
| Storage2  | HDD WD 500GB                |
| Storage3  | SSD Sata BX500 240GB        |
| Display   | M27Q Gigabyte 2k 170Hz      |
| dGPU      | 6600xt XFX 8GB              |
| LAN       | Realtek's Gigabit Ethernet. |
| Bluetooth | USB Ugreen 30443            |

## Status

### Working

- [x] DP and HDMI from 6600xt
- [x] Ethernet
- [x] iCloud services - iMessage, FaceTime, iCloud Drive
- [x] Mac-like booting interface for multiboot
- [x] Sleep/wake
- [x] All USB port
- [x] Native CPU power management
- [x] DP and HDMI video and audio up to 1440p
- [x] FileVault
- [x] DRM content playback (Netflix, Apple TV+)
- [x] Audio jack
- [x] Bluetooth with usb ugreen and bluetooth kext
- [x] CPU, GPU, Fan sensor
- [x] Dual boot with Win 11
- [x] DP with 120hz
- [x] HDMI with 144hz

### Working, sort of

- [x] Sometime need to turn off and reopen monitor after wakeup from sleep
- [x] Screen go black but the PC still running when Win and Macos have the same refresh rate (I can't fix this but will merge pull request)

### Not working at the moment

- [ ] DP with 165hz (I have 2k 170hz Monitor)
- [ ] AirDrop (change to realmac bluetooth will solve this)

### Not Tested

- [ ] GPU acceleration
- [ ] CPU Power Management
- [ ] Temperatures and stability with 100% CPU
- [ ] BootCamp

## ACPI patches

| Patch                | Remark                    |
| -------------------- | ------------------------- |
| SSDT-PLUG            | x86 plugin injection fix  |
| SSDT-RTCAWAC         | Fix the system clocks     |
| SSDT-EC-USBX-DESKTOP | Fixing Embedded Controller|

## Pre-install: Creating the installation USB stick

First, read the [Dortania OC guide](https://dortania.github.io/OpenCore-Install-Guide/). The guide will take you through the creation of installation USB and drive formatting. Update your laptop to the latest BIOS firmware from your existing OS (the easiest way is to use fwupd on linux).

## Getting a valid Mac serial key

- [Fix iServices](https://dortania.github.io/OpenCore-Post-Install/universal/iservices.html#generate-a-new-serial) if you want to use iMessage, FaceTime, iCloud. You need a valid, unique Mac serial key (the config.plist in this repository does not have one as all Mac devices - including hackintosh - need a unique serial) to be able to use Apple's cloud services and authentication. If you don't, you won't be able to login with an Apple ID, thus no App Store either! To generate a serial and update directly the config.plist, you can use [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS). Use SMBIOS MacPro7,1.

## BIOS Configuration

### Tweaker:

- Extreme Memory Profile: **Profile 1**
- Advanced CPU Settings:
  - Hyper-Threading Technology: **Enabled**
  - Intel Turbo Boost Technology: **Enabled**
- Advanced Memory Setting:
  - Memory Boot Mode: **Enable Fast Boot**
  - Memory Enhancement Setting: Enhanced Performance

### Settings:

- Platform Power:

  - ErP: **Disabled**

  * Soft-Off by PWR-BTN: **Delay 4s**
  * Power Loading: **Enabled**
  * AC BACK: **Always On**

- IO Ports:
  - Initial Display Output: **PCIe 1 Slot**
  - Above 4G Decoding: Enabled
  - Re-Size BAR Support: **Disabled**(Enabled after finish install if you want) (if you are using RX 6000 Series graphic card)
  - Super IO Configuration → Serial Port: **Disabled** (Will cause the issue with Apple Watch unlock)
  - USB Configuration:
    - XHCI Hand-off → **Enabled**
    - Legacy USB Support → **Enabled**
    - USB Mass Storage Driver Support → **Enabled**
    - Port 60/64 Emulation → **Disabled**
  - Network Stack Configuration → Network Stack: **Disabled**
- Miscellaneous:
  - Intel Platform Trust Technology(PTT) → **Disabled**
  - Vt-d → **Disabled**

### Boot:

- Internal graphic **Disabled** (if you use 11th CPU)
- CFG Lock: **Disabled**
- Fast Boot: **Disable Link**
- Windows 10 Features: **Other OS**
- CSM Support: **Disabled**
- Secure Boot: **Disabled** (Secure Boot will be disabled by default, but good to check)

Now you can boot from your USB stick. If it fails to boot, try a different USB stick, double check your BIOS settings.

## Post install

- Generate your own SMBIOS
- Generate your own ACPIß
- [Make your own USB map kext](https://dortania.github.io/OpenCore-Post-Install/usb/)
- Generate your own CPU frequency vectors using [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend). The one included here is set to Balance power and CPU lowest frequency set to 500 MHz
- (Optional) [Rectangle](https://github.com/rxhanson/Rectangle) for window management similar to Windows (but better)
- (Optional) [LuLu](https://github.com/objective-see/LuLu) for network traffic control
- (Optional) To install dual boot win 11.
  - First method: Shrink the SSD and use this guide to install [Dual Windows](https://www.youtube.com/watch?v=ztxHRGdX0Sw)
  - Second method: Unplug the SSD have MacOs and add the second SSD to install Windows or if you dont want to unplug your MacOs SSD, use the same method above with the second SSD
- (Optional) Fix incorrect time change when dual boot (guide)(https://www.youtube.com/watch?v=KZ_1zw_HVR8)

## Issue

- Run this command in Recovery mode if you have monitor with high refresh rate (I have 2k and 170hz monitor). It will fix the screen go black when you change the refresh rate (only works with 120hz, 165hz still get black screen)
```sh
cd /Volumes/your startup disk name/Users/your username/Library/Preferences/ByHost
rm -f com.apple.windowserver.*
rm -f com.apple.preference.displays*
```

## Note

- For how to update OpenCore and kexts, read [this guide](https://dortania.github.io/OpenCore-Post-Install/universal/update.html#_5-boot). Personally, I use MountEFI to mount EFI partitions, ProperTree to edit plist files, and OCConfigCompare to compare my config files to the latest sample configs from OpenCore. I also always test drive my updated EFI with a USB stick before moving it to the EFI partition of the main SSD.

## CREDITS

- [Serouin repo](https://github.com/serouin/b660m-aorus-pro-hackintosh)
- [Dortania OC guide](https://dortania.github.io/OpenCore-Install-Guide/)
- [Dual boot Windows](https://www.youtube.com/watch?v=ztxHRGdX0Sw)
- Hackintosh Paradise (Discord channel) for EFI review and fake CPUid
