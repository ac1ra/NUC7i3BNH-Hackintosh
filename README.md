# NUC7i3BNH-Hackintosh
[GUIDE] Installing macOS Mojave (10.14.x)/ macOS Catalina(10.15.x) on Intel NUC7i3BNH using Clover UEFI. 

### Overview
This guide take from @Rehabman's <link>https://www.tonymacx86.com/threads/guide-intel-nuc7-nuc8-using-clover-uefi-nuc7i7bxx-nuc8i7bxx-etc.261711/</link>

My computer Intel NUC7i3BNH with MacOS 10.15.5. All devices work very well, but original Wi-Fi/BT adapter don't work.

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

**I recommend to create on the USB-flash with USB3.0, because the install will very long.** Creating USB and installing using Clover UEFI works on the NUC7i3BNH. Make USB flash with GPT parition for Clover UEFI.

Terminal:

> diskutil list

> diskutil partitionDisk /dev/disk1 1 GPT HFS+J "install_osx" R
- EFI will be created automatically.
- Second partition, "install_osx", HFS+J, remainder.

**The plist files in this guide require Clover v4658 or newer. For full functionality and best choice, use the latest RehabMan build.**

Clover installer from RehabMan [recommend]: https://github.com/RehabMan/Clover

Download Clover installer on sourceforge, if you want to update to new version: https://github.com/CloverHackyColor/CloverBootloader/releases

First task is to install to the USB Clover EFI parition. For Clover UEFI, run the Clover Installer package:
- check "Install for UEFI booting only", "Install Clover in the ESP" will automatically select.
- the defaults for Drivers64UEFI are recommended

Terminal:

> sudo diskutil mount disk1s1

Remove Drivers64UEFI from EFI\CLOVER. Download Drivers64UEFI from here and copy to EFI\CLOVER. Replace the operation with EFI\CLOVER\kext\other.

Download config_install_nuc7.plist: https://github.com/RehabMan/Intel-NUC-DSDT-Patch/raw/master/config_install_nuc7.plist
Rename config_install_nuc7.plist to config.plist in EFI/CLOVER. Old config.plist rename to config_default.

#### Createinstallmedia method

This is the same mechanism you would use to create a USB installer for a real Mac Mojave.

It is a single line, executed in Terminal:

> sudo "/Applications/Install macOS Mojave.app/Contents/Resources/createinstallmedia" --volume  /Volumes/install_osx --nointeraction

USB bootloader is ready.

#### Installation

Unfortuanely, my Intel NUC7i3BNH cannot read UEFI USB. I suspect what Intel includes new protects in new version bios (BIOS version **0070**). Well, I suggest to choise other method. Loading put **F2** to BIOS and turn on **Built-in EFI Shell** in Boot. Put USB flash with Clover Bootloader and reboot. Turn F10 to get boot menu and choice **Built-in EFI Shell**.
In EFI Shell input parition fs1: where USB with EFI:

> fs1:\EFI\BOOT\BOOTX64.efi

**or**

> fs1:\EFI\CLOVER\CLOVERX64.efi

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

> cd nuc.git

> download.sh

> ./install_downloads.sh

To finish the setup, we need a correctly patched ACPI.

> make

> make install_nuc7

Finally, 'make install_nuc7', mounts the EFI partition, and copies the built files where they can be loaded by Clover (to EFI/Clover/ACPI/patched).

Last moment, you should write in EFI/CLOVER/config.plist -> Boot:

> dart=0 -igfxnohdmi -cdfon lilucpu=9

Reboot system. MacOS Mojave ready.

#### Adding: Problem with hibernation ####

Everything required for CPU/IGPU power management is already installed with the steps above.
There is no longer any need to use the ssdtPRgen.sh script.

Be aware that hibernation (suspend to disk or S4 sleep) is not well supported on hackintosh.

You should disable it:
Code:
> sudo pmset -a hibernatemode 0
> sudo rm /var/vm/sleepimage
> sudo mkdir /var/vm/sleepimage

Always check your hibernatemode after updates and disable it. System updates tend to re-enable it, although the trick above (making sleepimage a directory) tends to help.

**UPD 23.03.2020:** MacOS Catalina (10.15.x) is working. Moving from macOS Mojave to macOS Catalina with an existing MultiBeast 11 for macOS Mojave installation. The following directions allow a user to manually remove kexts from /Library/Extensions and recache system on macOS Catalina.

1. Navigate to /Library/Extensions
2. If any 3rd party kexts exist, delete them:

**AHCI_3rdParty_eSATA.kext, AHCI_3rdParty_SATA.kext, AHCI_Intel_Generic_SATA.kext, AppleALC.kext, AppleIGB.kext, AppleIntelE1000e.kext, AtherosE2200Ethernet.kext, FakePCIID_XHCIMux.kext, FakePCIID.kext, GenericUSBXHCI.kext, IntelMausiEthernet.kext, Lilu.kext, NullCPUPowerManagement.kext, RealtekRTL8111.kext, USBInjectAll.kext, VoodooHDA.kext, VoodooTSCSync.kext, WhateverGreen.kext**
 
3.  For reference, the default macOS Catalina /Library/Extensions from a clean installation:
- ACS6x.kext
- ArcMSR.kext
- ATTOCelerityFC8.kext
- ATTOExpressSASHBA2.kext
- ATTOExpressSASRAID2.kext
- CalDigitHDProDrv.kext
- HighPointIOP.kext
- HighPointRR.kext
- PromiseSTEX.kext
- SoftRAID.kext

4. Open /Applications/Utilities/Terminal
5.  > sudo -s
6.  > mount -uw /
7. > touch /Library/Extensions /System/Library/Extensions
8. > kextcache -i /
9. Reboot​

**Enjoy it!**
