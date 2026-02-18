# ASUS X555LD / K555L Hackintosh - OpenCore Big Sur

![macOS Version](https://img.shields.io/badge/macOS-11.7.11-brightgreen)
![OpenCore Version](https://img.shields.io/badge/OpenCore-0.8.5-blue)
![Status](https://img.shields.io/badge/Status-Stable-success)

OpenCore EFI for ASUS X555LD / K555L laptops running **macOS Big Sur 11.7.11**. This configuration has been thoroughly tested and is stable for daily use.

## 📋 Table of Contents
- [Hardware Specifications](#-hardware-specifications)
- [macOS Version](#-macos-version)
- [What Works](#-what-works)
- [What Doesn't Work](#-what-doesnt-work)
- [BIOS Settings](#-bios-settings)
- [Installation Guide](#-installation-guide)
- [Post-Installation](#-post-installation)
- [Troubleshooting](#-troubleshooting)
- [Credits](#-credits)
- [License](#-license)

## 💻 Hardware Specifications
| Component | Details |
|-----------|---------|
| **Model** | ASUS X555LD / K555L |
| **CPU** | Intel Core i5-4210U (Haswell) @ 1.7GHz |
| **iGPU** | Intel HD Graphics 4400 |
| **RAM** | 8GB DDR3 1600MHz |
| **Storage** | 120GB SSD + HDD (optional) |
| **Audio** | Realtek ALC233 |
| **Ethernet** | Realtek RTL8111 |
| **Wi-Fi/BT** | Broadcom BCM43142 (❌ Unsupported - see notes) |
| **Trackpad** | Synaptics PS/2 |
| **Screen** | 15.6" HD 1366x768 |

## 🍏 macOS Version
- **Current:** macOS Big Sur 11.7.11
- **OpenCore:** 0.8.5
- **SMBIOS:** MacBookAir7,2

## ✅ What Works
| Component | Status | Notes |
|-----------|--------|-------|
| **Intel HD 4400 Graphics** | ✅ **Full QE/CI** | Metal supported, 1536MB VRAM |
| **Audio (Speakers)** | ✅ Working | Internal speakers |
| **Audio (Headphones)** | ✅ Working | *Requires post-install fix (see below)* |
| **Ethernet** | ✅ Working | RealtekRTL8111 kext |
| **Trackpad** | ✅ Working | VoodooPS2Controller, gestures supported |
| **Keyboard** | ✅ Working | All function keys |
| **USB Ports** | ✅ Working | USB 2.0 and 3.0 |
| **Battery Status** | ✅ Working | SMCBatteryManager |
| **Webcam** | ✅ Working | Built-in |
| **Sleep/Wake** | ✅ Working | |
| **iCloud/iMessage** | ✅ Working | *Requires valid SMBIOS* |
| **App Store** | ✅ Working | |
| **Boot Chime** | ✅ Working | PS2 startup sound |
| **Brightness Control** | ✅ Working | |
| **Bluetooth** | ⚠️ Partial | With compatible card only |

## ❌ What Doesn't Work
| Component | Status | Notes |
|-----------|--------|-------|
| **Wi-Fi (Stock)** | ❌ Not Working | Broadcom BCM43142 unsupported |
| **HDMI Audio** | ❌ Not Tested | May work with proper layout |
| **Card Reader** | ⚠️ Untested | May work with additional kexts |

## ⚙️ BIOS Settings
Configure your BIOS as follows before installation:

### **Disable:**
- Fast Boot
- Secure Boot
- CSM (Compatibility Support Module)
- Intel SGX

### **Enable:**
- AHCI Mode (for SATA)
- VT-x (Virtualization)
- VT-d (can be disabled if issues occur)
- Above 4G Decoding
- DVMT Pre-Allocated: **64MB or higher**

## 📥 Installation Guide

## 📂 Folder Structure Note
This repo contains the `BOOT` and `OC` folders. To use this EFI:

1. Create a folder named `EFI` on your desktop
2. Copy the `BOOT` and `OC` folders from this repo into it
3. Replace the `EFI` folder on your USB or EFI partition with this new folder


### **Step 1: Prepare the USB Installer**
1. Download macOS Big Sur from the App Store
2. Create bootable USB:
   ```bash
   sudo /Applications/Install\ macOS\ Big\ Sur.app/Contents/Resources/createinstallmedia --volume /Volumes/YourUSBName
Generate Your Own SMBIOS (CRITICAL!)

Do NOT use the placeholder values in this EFI!

   Download GenSMBIOS

   Run it and select Generate SMBIOS

   Choose model: MacBookAir7,2

   Copy the generated values into your config.plist under:
    text

   PlatformInfo → Generic

   MLB (Board Serial)

   ROM (MAC address)

   SystemUUID

   SerialNumber

Step 3: Install macOS

   Boot from the USB drive

   Select "Install macOS Big Sur" from OpenCore menu

   Format your drive as APFS in Disk Utility

   Install macOS

   System will restart 2-3 times

🔧 Post-Installation
Essential Fixes
1. Headphone Audio Fix

If headphones sound distorted or bad, run this command after each boot:
bash

alc-verb 0x19 SET_PIN_WIDGET_CONTROL 0x24

To automate this fix:

   Create a file called fix-audio.command on your Desktop:
    bash

   #!/bin/bash
   alc-verb 0x19 SET_PIN_WIDGET_CONTROL 0x24

   Make it executable:
   bash

   chmod +x ~/Desktop/fix-audio.command

   Add to Login Items:

   System Preferences → Users & Groups → Login Items

   Click + and select fix-audio.command

2. Boot-Args Used
text

-v alcid=13 -wegnoegpu

   -v : Verbose mode (remove after stable)

   alcid=13 : Audio layout ID for ALC233

   -wegnoegpu : Disables discrete GPU (broken/unsupported)

3. Install alc-verb Tool

If you don't have alc-verb installed:

Option A: Download from AppleALC Releases

   Go to AppleALC Releases

   Download the latest Debug version

   Extract and find alc-verb in the Utilities folder
   Copy to /usr/local/bin/:
   bash

   sudo cp ~/Downloads/alc-verb /usr/local/bin/
   sudo chmod +x /usr/local/bin/alc-verb

Option B: Build from Source
bash

git clone https://github.com/acidanthera/AppleALC.git
cd AppleALC/Utilities/alc-verb
make
sudo make install

Recommended Apps
HWMonitor - Check temperatures (your ASUS stays under 75°C!)
   
Hackintool - For troubleshooting
    
ProperTree - Edit config.plist
