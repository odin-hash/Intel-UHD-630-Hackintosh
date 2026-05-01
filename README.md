# Hackintosh Intel UHD 630 EFI

This repository contains the OpenCore EFI folder and a comprehensive post-installation guide for a Hackintosh build utilizing Intel UHD 630 Graphics.

> [!WARNING]
> **Important:** The `config.plist` in this repository does **NOT** contain SMBIOS data (Serial Number, UUID, MAC Address). You **must** generate your own using GenSMBIOS before booting, otherwise your Apple ID may get blacklisted. See the section below on how to do this.

## 💻 Hardware Specifications

| Component | Model/Details |
| :--- | :--- |
| **CPU** | Intel Core iX (Update with your specific model) |
| **GPU** | Intel UHD Graphics 630 |
| **RAM** | XX GB DDR4 |
| **Storage** | Update with your SSD model |
| **Wi-Fi/BT** | Intel Wireless/Bluetooth |

## ⚙️ BIOS Settings

Before installing macOS, ensure your BIOS settings are configured correctly:

**Disable:**
- Fast Boot
- Secure Boot
- Serial/COM Port
- Parallel Port
- VT-d (can be enabled if `DisableIoMapper` is set to YES in config.plist)
- CSM (Compatibility Support Module)
- Intel SGX

**Enable:**
- VT-x
- Above 4G Decoding
- Hyper-Threading
- Execute Disable Bit
- EHCI/XHCI Hand-off
- OS type: Windows 8.1/10 UEFI Mode

---

## 🚀 Post-Installation Complications & Fixes

During the setup of this machine, several common Hackintosh issues were encountered and successfully resolved. Here is how you can fix them if you face the same issues:

### 1. Wi-Fi and iPhone Hotspot Connectivity
**Issue:** While normal Wi-Fi worked, connecting to an iPhone Personal Hotspot failed.
**Solution:**
We utilized the `itlwm.kext` along with the **HeliPort** application instead of `AirportItlwm.kext`.
1. Ensure `itlwm.kext` is in your `EFI/OC/Kexts` folder and enabled in `config.plist`.
2. Download the [HeliPort app](https://github.com/OpenIntelWireless/HeliPort/releases).
3. If macOS blocks HeliPort with an "unverified developer" warning, open Terminal and run:
   ```bash
   sudo xattr -rd com.apple.quarantine /Applications/HeliPort.app
   ```
4. Launch HeliPort, and you will now be able to connect to your iPhone Hotspot reliably!

### 2. 120Hz Display Refresh Rate & Brightness Control
**Issue:** The display was stuck at 60Hz and brightness controls were missing.
**Solution:**
This requires precise configuration of the Intel UHD 630 `DeviceProperties` in `config.plist`.
1. We used Hackintool to identify the correct `AAPL,ig-platform-id` and `device-id` for the UHD 630.
2. Injected the properties under `PciRoot(0x0)/Pci(0x2,0x0)`.
3. Brightness control was restored using `WhateverGreen.kext` and `SSDT-PNLF.aml`.
4. *Tip:* Ensure you are using a DisplayPort connection (or a high-bandwidth Type-C adapter) if your built-in screen or external monitor supports 120Hz, as HDMI 1.4 often caps at 4K@30Hz or 1080p@60Hz.

### 3. Slow Wake from Sleep
**Issue:** The laptop would take a very long time (10-20 seconds) to wake up after opening the lid.
**Solution:**
This is often related to USB mapping or hibernation settings.
1. Disabled macOS hibernation by running these terminal commands:
   ```bash
   sudo pmset -a hibernatemode 0
   sudo rm /var/vm/sleepimage
   sudo mkdir /var/vm/sleepimage
   sudo pmset -a standby 0
   sudo pmset -a autopoweroff 0
   ```
2. Ensured a proper custom USB Map (`USBMap.kext` or `USBPorts.kext`) was created, setting the Bluetooth USB internal connection to `Internal (255)`.

### 4. Cleaning up the Boot Menu
**Issue:** The OpenCore boot menu showed duplicate entries like "EFI" and "mac" after installation.
**Solution:**
1. In `config.plist` -> `Misc` -> `Boot`, set `HideAuxiliary` to `True`.
2. This hides recovery partitions and tools. You can press the `Spacebar` at the boot menu to reveal them when needed.

---

## 🔑 How to Generate Your Own SMBIOS

1. Download [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS).
2. Run `GenSMBIOS.command`.
3. Select option `1` to download MacSerial.
4. Select option `2` to select your `config.plist` path.
5. Select option `3` to generate SMBIOS. Enter the appropriate model for your hardware (e.g., `MacBookPro15,2` or `Macmini8,1`).
6. Press Enter. The script will automatically update your `config.plist` with the new Serial Number, Board Serial, and SmUUID.

## ⌨️ Shortcuts Cheatsheet
We have included a comprehensive [macOS Keyboard Shortcuts Cheatsheet](macOS_Keyboard_Shortcuts.md) in this repository to help you get familiar with the macOS environment!

## 🤝 Credits
* [Dortania](https://dortania.github.io/OpenCore-Install-Guide/) for the incredible OpenCore Install Guide.
* [Acidanthera](https://github.com/acidanthera) for OpenCore and essential kexts.
* [OpenIntelWireless](https://github.com/OpenIntelWireless) for Intel Wi-Fi and Bluetooth drivers.
