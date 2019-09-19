# NUC7i3BNH-Hackintosh
[GUIDE] Installing macOS Mojave (10.14.x) on Intel NUC7i3BNH using Clover UEFI
### Overview
This guide take from @Rehabman's <link>https://www.tonymacx86.com/threads/guide-intel-nuc7-nuc8-using-clover-uefi-nuc7i7bxx-nuc8i7bxx-etc.261711/</link>

#### BIOS settings

BIOS version **0070**. BIOS setup can be accessed by mashing the F2 key while booting up. It will get you to the main BIOS setup screens. To start, choose "Load Defaults" (choose from the menu or press F9 in the BIOS setup).

Then change:
- Boot -> Boot Configuration, disable **"Network Boot**.
- Power -> Secondary Power Settings, **"Wake on LAN from S4/S5"**, set to **"Stay Off"**.
- Boot -> Secure Boot, disable **"Secure Boot"**.
- Devices -> OnBoard Devices, disable **"Bluetooth"** (macOS is not compatible well with Intel Wi-Fi/Bluetooth).
- Boot -> Boot Priority -> Legacy Boot Priority, enable **"Legacy Boot"**.

#### Creating USB

Creating USB and installing using Clover UEFI works on the NUC7i3BNH. Make USB flash with GPT parition for Clover UEFI. 

Terminal:

> diskutil list

> diskutil partitionDisk /dev/disk1 1 GPT HFS+J "install_osx" R
- EFI will be created automatically.
- Second partition, "install_osx", HFS+J, remainder.

**The plist files in this guide require Clover v4658 or newer. For full functionality and best choice, use the latest RehabMan build.**

Clover installer from RehabMan [recommend]: https://github.com/RehabMan/Clover

Download Clover installer on sourceforge, if you want to update to new version: http://sourceforge.net/projects/cloverefiboot/

First task is to install to the USB "Clover EFI" parition. For Clover UEFI, run the Clover Installer package:
- check "Install for UEFI booting only", "Install Clover in the ESP" will automatically select.
- the defaults for Drivers64UEFI are recommended

Terminal:

> sudo diskutil mount disk1s1

Remove CLOVER from EFI. Download CLOVER from here and copy to EFI.
USB bootloader ready.

#### Installation

Unfortuanely, my Intel NUC7i3BNH cannot read UEFI USB. I suspect what Intel includes new secures in new version bios (BIOS version **0070**). Well, I suggest to choise other method. Running load put **F2** to BIOS and turn on **Built-in EFI Shell** in Boot. Put USB flash with Clover EFI and reboot. Turn F10 to get boot menu and choice **Built-in EFI Shell**.
In EFI Shell input parition fs1: where USB with EFI:

> cd fs1:\BOOT.
> ls.
> BOOTX64.efi.

EFI Clover boot should start from USB-flash. Install MacOS Mojave.
