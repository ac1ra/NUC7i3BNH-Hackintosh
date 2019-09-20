# NUC7i3BNH-Hackintosh
[GUIDE] Installing macOS Mojave (10.14.x) on Intel NUC7i3BNH using Clover UEFI
### Overview
This guide take from @Rehabman's <link>https://www.tonymacx86.com/threads/guide-intel-nuc7-nuc8-using-clover-uefi-nuc7i7bxx-nuc8i7bxx-etc.261711/</link>

My computer Intel NUC7i3BNH with MacOS 10.14.6. All devices work very well, but original Wi-Fi/BT adapter don't work.

![frst img](/img/PC.jpg)

#### Performance
![frst img](/img/all.png)

#### Specs
- **CPU:** Intel Core i3 7100U, 2.4 ГГц (dual-core)
- **RAM:** 2x4 Gb DDR4 Kingston 2133 Mhz
- **SSD:** 120 Gb M'2 2280, WD Green WDS120G2G0B
- **GPU:** Intel HD Graphics 620
- **Ports:** SDXC/USB-3/USB Type-C(TB3)/LAN/3'5 Jack

#### Don't work
- Original Wi-Fi/BT

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

First task is to install to the USB Clover EFI parition. For Clover UEFI, run the Clover Installer package:
- check "Install for UEFI booting only", "Install Clover in the ESP" will automatically select.
- the defaults for Drivers64UEFI are recommended

Terminal:

> sudo diskutil mount disk1s1

Remove CLOVER from EFI. Download CLOVER from here and copy to EFI.
USB bootloader ready.

#### Installation

Unfortuanely, my Intel NUC7i3BNH cannot read UEFI USB. I suspect what Intel includes new protects in new version bios (BIOS version **0070**). Well, I suggest to choise other method. Loading put **F2** to BIOS and turn on **Built-in EFI Shell** in Boot. Put USB flash with Clover Bootloader and reboot. Turn F10 to get boot menu and choice **Built-in EFI Shell**.
In EFI Shell input parition fs1: where USB with EFI:

> cd fs1:\BOOT

> ls

> BOOTX64.efi

EFI Clover boot should start from USB-flash. Install MacOS Mojave.

#### Post Installation

After installation mount local EFI disk. Terminal:

> sudo diskutil mount disk0s1

Rename CLOVER to CLOVER_bck for backup folder. Copy CLOVER from here to EFI partition.

**P.S.:** If you want to update files into EFI, installation of the tools and patching is easy provided the scripts and tools at the repository: https://github.com/RehabMan/Intel-NUC-DSDT-Patch

To start, the developer tools must be installed. Run Terminal, and type:

> xcode-select --install

> mkdir ~/Projects

> cd ~/Projects

> git clone https://github.com/RehabMan/Intel-NUC-DSDT-Patch nuc.git

> download.sh

> ./install_downloads.sh

To finish the setup, we need a correctly patched ACPI.

> make

> make install_nuc7

Finally, 'make install_nuc7', mounts the EFI partition, and copies the built files where they can be loaded by Clover (to EFI/Clover/ACPI/patched).

Last moment, you should write in EFI/CLOVER/config.plist -> Boot:

> dart=0 -igfxnohdmi -cdfon lilucpu=9

Reboot system. MacOS Mojave ready.
