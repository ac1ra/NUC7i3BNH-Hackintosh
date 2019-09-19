# NUC7i3BNH-Hackintosh
[GUIDE] Installing macOS Mojave (10.14.x) on Intel NUC7i3BNH using Clover UEFI
<h1>Overview</h1>
This guide take from @Rehabman's <link>https://www.tonymacx86.com/threads/guide-intel-nuc7-nuc8-using-clover-uefi-nuc7i7bxx-nuc8i7bxx-etc.261711/</link>

<h1>BIOS settings</h1>

BIOS version 0070. BIOS setup can be accessed by mashing the F2 key while booting up. It will get you to the main BIOS setup screens. To start, choose "Load Defaults" (choose from the menu or press F9 in the BIOS setup).

Then change:

Boot -> Boot Configuration, disable <h1>"Network Boot"</h1>
Power -> Secondary Power Settings, <h1>"Wake on LAN from S4/S5"</h1>, set to </h1>"Stay Off"</h1>
Boot -> Secure Boot, disable <h1>"Secure Boot"</h1>
Devices -> OnBoard Devices, disable <h1>"Bluetooth"</h1> (macOS is not compatible well with Intel Wi-Fi/Bluetooth)
Suggested:

Boot -> Boot Priority -> Legacy Boot Priority, enable <h1>"Legacy Boot"</h1>.

I create the procedure to be simple to install Mojave on the NUC7i3BNH.
1. Creating USB and installing using Clover UEFI works. Make USB flash with GPT parition for Clover UEFI. Terminal:
#(GPT, one parition)
diskutil list
# repartition /dev/disk1 GPT, one partition
# EFI will be created automatically
# second partition, "install_osx", HFS+J, remainder
diskutil partitionDisk /dev/disk1 1 GPT HFS+J "install_osx" R

<h1>The plist files in this guide require Clover v4658 or newer. For full functionality and best choice, use the latest RehabMan build.</h1>

Clover installer from RehabMan: https://github.com/RehabMan/Clover
Download the Clover installer on sourceforge, if you want to update to new version: http://sourceforge.net/projects/cloverefiboot/
 First task is to install to the USB "Clover EFI" parition. For Clover UEFI, run the Clover Installer package:
- check "Install for UEFI booting only", "Install Clover in the ESP" will automatically select
- the defaults for Drivers64UEFI are recommended

sudo diskutil mount disk1s1 *where EFI parition on the USB*

Remove CLOVER from EFI. Download CLOVER from here and copy to EFI.
USB bootloader ready.
2. Unfortuanely, my Intel NUC7i3BNH cannot read UEFI USB. I suspect what Intel includes new secures in new version bios (BIOS version 0070). Well, put F2 to BIOS and turn on Built-in EFI Shell in Boot. Put USB flash with Clover EFI and reboot. Turn F10 to get boot menu and choice Built-in EFI Shell.
# In EFI Shell input parition fs1: where USB with EFI.
cd fs1:\BOOT
ls *you should look file BOOTX64.efi*
BOOTX64.efi
EFI Clover boot should start from USB-flash. Install MacOS Mojave.
