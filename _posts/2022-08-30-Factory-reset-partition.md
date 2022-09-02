---
layout: post
date: 2022-08-30 08:43:11
title: Factory Reset Partition via Clonezilla
category: Recovery 
tags: recovery, clonezilla, factory-reset
---
## AUTOMATED UEFI-WINDOWS RESTORE USING CLONEZILLA

Credits : [RMPrepUSB](https://sites.google.com/rmprepusb.com/www/tutorials/142---windows-restore-uefi)

# OUTLINE

We can backup or restore Windows with just the press of a key!

The backup is kept on the same system, no external drive is required.

We will add two new GPT partitions to the Windows internal hard disk:

300MB FAT32 - Clonezilla

(large) NTFS - Volume to store backup image(s)

We will then use EasyUEFI or BootIce to set the UEFI Firmware Non-Volatile RAM (NVRAM) BIOS setting to boot to the grub2 Clonezilla menu before booting to Windows.

As well as booting via the grub2 menu, you will still be able to boot directly to Windows using your BIOS firmware boot selection menu.

Note: The current scripts search disk 0 1 and 2 only for the backup folder (marked by backup.tag) and assume C: is the Windows drive to be backed up which is device sda under CloneZilla.

Note that Secure Boot must be disabled in the system's BIOS options.

I have not tested this on a BitLocker-encrypted Windows volume. I believe CloneZilla uses dd on such volumes and so would require a backup volume that is at least as large as the Windows volume. The auto-restore function will not work if grub2 cannot access the C: drive, so you will need to use an alternative method if drive C: is encrypted (see notes at bottom).

Note that filenames and folder names are case-sensitive under grub2 - follow the Tutorial carefully!

You can modify the boot menu to password-protect some menu entries and you can change the grub2 menu heading too.

For non-English users, you can edit the grub.cfg file menu entries (but do not change the line  set lang=eng).

A restore of an 18GB freshly-installed x64 UEFI Windows 10 system (SSD) takes about 5 minutes.

# SETUP

You must disable Secure Boot in the BIOS settings before you begin.

1. First use the Windows Disk Management to shrink the current partition and create two new GPT partitions (Windows key+R - diskmgmt.msc):

300MB FAT32 - Name=Clonezilla

(large)  NTFS -  Name=Backup - Volume to store backup image(s)

The actual volume names are not important and can be changed to your own language.

The large Backup volume needs to be big enough to store a compressed image of your Windows partition.

e.g. To store all files from a minimal Windows 18GB volume (excluding pagefile) we need approx 10GB of space.

As a rough rule, to store just 1 backup image, the Backup partition should be 2/3 of the total Windows files excluding the \pagefile.sys file  (e.g. Windows = 300GB of files, Backup volume size = 180GB)

If you want to store several different backups, you will obviously need a lot more space for the backup volume.

Tip: Create another NTFS partition called 'Files' and use this volume to store any files that you do not need to be backed up with the Windows OS. 

For instance, any files which you have downloaded from the Internet or which also exist elsewhere (on another device or in the 'Cloud') do not need to be backed up with the Windows OS image.

2. Create a file called backup.tag in the root of the Backup volume and create a new Folder called images (lower case).

backup.tag

\images

NOTE: make sure it is not called backup.tag.txt by setting Explorer to show File Extensions!

3. Download the latest stable CloneZilla .zip file.

![image](https://user-images.githubusercontent.com/1507737/187368936-15495d35-b559-47ed-9b4d-8d9639c1cee9.png)

Extract the \live and \EFI folders directly to the root of the Clonezilla volume

\EFI

\live



4. Download and extract the contents of the .zip file at the bottom of this page to the (Clonezilla volume)  \EFI\boot folder.

grub.cfg is overwritten with the grub.cfg file in the download.

I have also included a rmprep.png wallpaper file which should also be placed in the same \EFI\boot folder.

Copy the three folders (locales, x86_64-efi and i386-efi) folder to the \EFI\boot folder:

New files/folders added should now be:

(files)

\EFI\boot\grub.cfg

\EFI\boot\rmprep.png

(folders)

\EFI\boot\locales\

\EFI\boot\x86_64-efi\

\EFI\boot\i386-efi\

The three _Autoxxxxxxxx.cmd files can be copied or moved to any convenient folder (e.g. Windows Desktop or the Backup or CloneZilla volumes)

They are only required if you want to run an unattended backup or unattended restore from Windows.

5. We need to modify the BIOS\Firmware Non-Volatile RAM so that it boots to grub2 first instead of the Windows Boot manager file....

Either:

5.1 Download and run BootIce... (no need to install)

![image](https://user-images.githubusercontent.com/1507737/187368915-1370940e-725c-4645-ae77-0ab14af2066c.png)

  BootIce - UEFI - Add - (Clonezilla)\EFI\boot\bootx64.efi or bootia32.efi - (rename menu title to Clonezilla) - Save current boot entry

  Ensure Clonezilla entry is at top of list using Up button.
  
  
  # MAKE A BACKUP
  ![image](https://user-images.githubusercontent.com/1507737/187368886-f25c6348-57fb-429d-ade5-65a174200ee8.png)
  
  (the menu heading will be changed if you add the locales\en.mo file)

Check that the device names (sda4 and sda5) are correct for your system.

The Clonezilla menu options are:

I have changed the hot keys for French keyboard layout

Original :

[W]  Boot to Windows

[A] Auto-Restore Windows from backup file

[R] Restore Backup Image

[Z] Auto-Backup Windows

[N] Create New Backup Image

Modified :
[Z]  Boot to Windows

[Q] Auto-Restore Windows from backup file

[R] Restore Backup Image

[W] Auto-Backup Windows

[N] Create New Backup Image

Most people will want just one backup image so they can restore it in case of emergency, so just press Z.

This will automatically make a backup called IMG to the \images\IMG folder on the Backup volume.

The user can restore it simply by booting to the Clonezilla menu and pressing A.

The R and N options will create/restore more backups.

The A option is fully automated with no user prompts. If you prefer more control, use the R menu entry (user will be prompted to choose a backup file).

You can edit the \EFI\grub\grub.cfg menu and delete any menu entries you do not want.

# PASSWORD ACCESS

To prevent unauthorised access (or accidental access if the user presses 'c' or 'e') set a password (in grub.cfg file) by uncommenting the lines and editing:

#prevent user from editing or reaching console by setting superuser (use set superuser= to prevent anyone from reaching console)

insmod password

set superusers="easy2boot root"

password easy2boot easy2boot

password root root

password doris passwd

With the above settings, to get access to the grub2 console (superusers only), press C and then enter a username of root and a password of root.

I have added --unrestricted to all the menu entries to allow any menu entry to be used without needing a password.

Change this to --users "" for any entry you want to allow superuser access only or --users "doris" if a password has been set for non-superuser doris. (e.g.add line password doris passwd to the password section)

# Tips

The Clonezilla and Backup Partition are hidden and made readonly via the following command.

```
Diskpart

list vol

sel vol  X  (where X is desired volume)

ATT VOL SET HIDDEN
ATT VOL SET READONLY

sel vol  Y  (where Y is desired volume)

ATT VOL SET HIDDEN
ATT VOL SET READONLY

```
To unhide use ATT VOL CLEAR HIDDEN
To clear readonly ATT VOL CLEAR READONLY

Here is the grub.cfg file customized

```
set pref=/EFI/boot
set default="0"
set timeout="5"

insmod gettext
set locale_dir=$prefix/locale
set lang=en
export locale

# Load graphics (only corresponding ones will be found)
# (U)EFI
insmod efi_gop
insmod efi_uga
# legacy BIOS
insmod vbe

if loadfont $pref/unicode.pf2; then
  set gfxmode=auto
  insmod gfxterm
  terminal_output gfxterm
fi

# set to true if you don't want to see timeout counter
set hidden_timeout_quiet=false

insmod png
if background_image $pref/rmprep.png; then
  set color_normal=black/black
  set color_highlight=white/black
else
  set color_normal=cyan/blue
  set color_highlight=white/blue
fi

# beep-beep on speaker when menu loads
insmod play
#play 960 440 1 0 4 440 1


# ##### Find volumes by looking on hd0 hd1 and hd2 ####

set BAKDRV=
set WDRV=
for i in 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15; do
   if [ -e (hd2,gpt$i)/backup.tag ]; then set BAKDRV=$i; fi
   if [ -e (hd1,gpt$i)/backup.tag ]; then set BAKDRV=$i; fi
   if [ -e (hd0,gpt$i)/backup.tag ]; then set BAKDRV=$i; fi
   if [ -e (hd0,gpt$i)/Windows/explorer.exe ]; then set WDRV=$i; fi
   if [ -e (hd1,gpt$i)/Windows/explorer.exe ]; then set WDRV=$i; fi
   if [ -e (hd2,gpt$i)/Windows/explorer.exe ]; then set WDRV=$i; fi
   if [ ! @$BAKDRV@ == @@ and ! @$WDRV@ == @@ ]; then break; fi
done
if [ @$BAKDRV@ = @@ ]; then set BAKDRV="***ERROR: BACKUP FOLDER NOT FOUND***"; fi
if [ @$WDRV@ = @@ ];   then set WDRV="***ERROR: WINDOWS VOLUME NOT FOUND***"; fi

#prevent user from editing or reaching console
insmod password
set superusers="easy2boot root"
password easy2boot easy2boot
password root root


# folder with clonezilla files
set CLZDIR=/live
# folder to keep backup images in
set BAKDIR=/images

#AUTO options
   if [ -e (hd0,gpt$WDRV)/autobackup.tag and ! -d (hd0,gpt$BAKDRV)$BAKDIR/IMG ]; then set default="5" ; set timeout="10"; fi
   if [ -e (hd1,gpt$WDRV)/autobackup.tag and ! -d (hd1,gpt$BAKDRV)$BAKDIR/IMG ]; then set default="5" ; set timeout="10"; fi
   if [ -e (hd2,gpt$WDRV)/autobackup.tag and ! -d (hd2,gpt$BAKDRV)$BAKDIR/IMG ]; then set default="5" ; set timeout="10"; fi
   if [ -e (hd0,gpt$WDRV)/autorestore.tag and -d (hd0,gpt$BAKDRV)$BAKDIR/IMG ]; then set default="2" ; set timeout="10"; fi
   if [ -e (hd1,gpt$WDRV)/autorestore.tag and -d (hd1,gpt$BAKDRV)$BAKDIR/IMG ]; then set default="2" ; set timeout="10"; fi
   if [ -e (hd2,gpt$WDRV)/autorestore.tag and -d (hd2,gpt$BAKDRV)$BAKDIR/IMG ]; then set default="2" ; set timeout="10"; fi



# --- CLONEZILLA INFO ---

#http://clonezilla.org/fine-print-live-doc.php?path=clonezilla-live/doc/99_Misc/00_live-boot-parameters.doc
#http://clonezilla.org/clonezilla-live/doc/02_Restore_disk_image/advanced/09-advanced-param.php
#http://drbl.org/faq/fine-print.php?path=./2_System/88_mbr_related_options.faq
#https://www.gnu.org/software/grub/manual/grub/grub.html

# hint: cat /proc/cmdline - check clonezilla parameters
# hint: user:user pass:live

# Preseed codes (ocs = Opensource Clone System)
# video=uvesafb:mode_option=1024x768-32
# vga=788  - use video mode 788 (800-600) or  791=1024x768, 785=640x480,  vga=normal (no frame buffer)
# nosplash - does not show splash screen
# quiet    - reduce amount of boot messages
keyboard-layouts=fr   -set french kbd  or use NONE (=US) or uk
# locales=en_US.UTF-8   - choices are de_DE.UTF-8 en_US.UTF-8 es_ES.UTF-8 fr_FR.UTF-8 it_IT.UTF-8 ja_JP.UTF-8 pt_BR.UTF-8 ru_RU.UTF-8 zh_CN.UTF-8 zh_TW.UTF-8 
#                       - use locale -a in shell to display available locales
# noprompt   - does not ask to eject CD
# vga        - vieo mode  791=1024x768  788=800x600

# ocs_live_run ocs-sr parameters:
# --batch  - Automate run
# -c       - Asks user before completing action - Are you sure you want to continue ? (y/n) 
# -e1 auto - Automatically adjust filesystem geometry for a NTFS boot partition if exists
# -t       - Client does not restore the MBR (Master Boot Record)
# -g auto  - Reinstall grub in client disk MBR (only if grub config exists)
# -e1 auto - Automatically adjust filesystem geometry for a NTFS boot partition if exists
# -e2      - sfdisk uses CHS of hard drive from EDD (for non-grub boot loader)
# -u       - Asks the user for the image name (could be set in config too).
# restoredisk or savedisk - Which mode to run, store, restore, partition or hard-drive
# ask_user - requests name from user.
# nvme0n1px     – Which hard-drive should be written or read.
# -q2      – Use “partclone”. 
# -z1p     – Use gzip-compression (with multicore)
# -i 2048  – Split filesize in megabyte (Split every 2GB a new file for the backup - use if FAT32 backup ptn.)
# -sc      - Suppress verify check after backup
# -p poweroff - power off after successfully running the script. or reboot or choose
# -rm-win-swap-hib Removes the page and hibernation files in Win if exists

# set the CLONEZILLA BASIC PARAMETERS
set BOPT="boot=live union=overlay username=user config components noswap quiet nolocales edd=on nomodeset nodmraid noeject"
set RUN="ocs_live_run=\"ocs-live-general\" keyboard-layouts=NONE locales=en_US.UTF-8"
set RUN2="live-media-path=$CLZDIR bootfrom=/dev/nvme0n1p$BAKDRV toram=filesystem.squashfs ocs_live_batch=\"yes\""
set RUN3="vga=791 ip= net.ifnames=0 i915.blacklist=yes radeonhd.blacklist=yes nouveau.blacklist=yes vmwgfx.enable_fbdev=1"
set PRERUN="ocs_prerun=\"mount /dev/nvme0n1p$BAKDRV /mnt\" ocs_prerun1=\"mount --bind /mnt$BAKDIR /home/partimag/\""

# set root to clonezilla volume wherever it is
search --set -f $CLZDIR/vmlinuz


# #### MENU STARTS HERE ####

menuentry "[Z] Boot to Windows" --unrestricted --hotkey=w --class windows {
search --no-floppy --set -f /efi/Microsoft/Boot/bootmgfw.efi
chainloader /efi/Microsoft/Boot/bootmgfw.efi
}

menuentry "     " {
set root=$root
}

menuentry "[Q] Auto-Restore Windows from backup file nvme0n1p$BAKDRV$BAKDIR/IMG" --unrestricted --hotkey=a --class gnu-linux --class gnu --class os {
set RUN1="ocs_live_run=\"ocs-sr --batch -e1 auto -e2 -j2 -k -p reboot restoreparts IMG nvme0n1p$WDRV\""
linux $CLZDIR/vmlinuz $BOPT $RUN $RUN2 $PRERUN $RUN1 $RUN3
initrd $CLZDIR/initrd.img
}

menuentry "[R] Restore Backup Image to nvme0n1p$WDRV    (from nvme0n1p$BAKDRV$BAKDIR folder)" --unrestricted --hotkey=r --class gnu-linux --class gnu --class os {
set RUN1="ocs_live_run=\"ocs-sr --batch -g auto -e1 auto -e2 -j2 -k -p reboot restoreparts ask_user nvme0n1p$WDRV\""
linux $CLZDIR/vmlinuz $BOPT $RUN $RUN2 $PRERUN $RUN1 $RUN3
initrd $CLZDIR/initrd.img
}

menuentry "     " {
set root=$root
}

menuentry "[W] Auto-Backup  Windows to   backup file nvme0n1p$BAKDRV$BAKDIR/IMG" --unrestricted --hotkey=z --class gnu-linux --class gnu --class os {
set RUN1="ocs_live_run=\"ocs-sr -q2 -sc -rm-win-swap-hib -c --batch -j2 -z2p -i 2000 -p reboot saveparts IMG nvme0n1p$WDRV\""
linux $CLZDIR/vmlinuz $BOPT $RUN $RUN2 $PRERUN $RUN1 $RUN3
initrd $CLZDIR/initrd.img
}

menuentry "[N] Create New Backup Image of nvme0n1p$WDRV (in nvme0n1p$BAKDRV$BAKDIR folder)" --unrestricted --hotkey=n --class gnu-linux --class gnu --class os {
set RUN1="ocs_live_run=\"ocs-sr -q2 --batch -j2 -z2p -i 2000 -sc -c -rm-win-swap-hib -p reboot saveparts ask_user nvme0n1p$WDRV\""
linux $CLZDIR/vmlinuz $BOPT $RUN $RUN2 $PRERUN $RUN1 $RUN3
initrd $CLZDIR/initrd.img
}


menuentry "     " {
set root=$root
}
menuentry "    ###### CLONEZILLA MENU #######   " {
set root=$root
}
menuentry "     " {
set root=$root
}

# HERE ARE STANDARD CLONEZILLA MENU ENTRIES

menuentry "Clonezilla live (Default settings, VGA 800x600)" --unrestricted {
  search --set -f /live/vmlinuz
  linux /live/vmlinuz boot=live union=overlay username=user config components quiet noswap edd=on nomodeset locales= keyboard-layouts= ocs_live_run="ocs-live-general" ocs_live_extra_param="" ocs_live_batch="no" vga=788 ip= net.ifnames=0  nosplash i915.blacklist=yes radeonhd.blacklist=yes nouveau.blacklist=yes vmwgfx.enable_fbdev=1
  initrd /live/initrd.img
}
menuentry "Clonezilla live (Default settings, VGA 1024x768)" --unrestricted {
  search --set -f /live/vmlinuz
  linux /live/vmlinuz boot=live union=overlay username=user config components quiet noswap edd=on nomodeset locales= keyboard-layouts= ocs_live_run="ocs-live-general" ocs_live_extra_param="" ocs_live_batch="no" vga=791 ip= net.ifnames=0  nosplash i915.blacklist=yes radeonhd.blacklist=yes nouveau.blacklist=yes vmwgfx.enable_fbdev=1
  initrd /live/initrd.img
}

menuentry "Clonezilla live (Default settings, VGA 640x480)" --unrestricted {
  search --set -f /live/vmlinuz
  linux /live/vmlinuz boot=live union=overlay username=user config components quiet noswap edd=on nomodeset locales= keyboard-layouts= ocs_live_run="ocs-live-general" ocs_live_extra_param="" ocs_live_batch="no" vga=785 ip= net.ifnames=0  nosplash i915.blacklist=yes radeonhd.blacklist=yes nouveau.blacklist=yes vmwgfx.enable_fbdev=1
  initrd /live/initrd.img
}

menuentry "Clonezilla live (Default settings, KMS)" --unrestricted {
  search --set -f /live/vmlinuz
  linux /live/vmlinuz boot=live union=overlay username=user config components quiet noswap edd=on locales= keyboard-layouts= ocs_live_run="ocs-live-general" ocs_live_extra_param="" ocs_live_batch="no" vga=791 ip= net.ifnames=0  nosplash
  initrd /live/initrd.img
}

menuentry "Clonezilla live (To RAM, boot media can be removed later)" --unrestricted {
  search --set -f /live/vmlinuz
  linux /live/vmlinuz boot=live union=overlay username=user config components quiet noswap edd=on nomodeset locales= keyboard-layouts= ocs_live_run="ocs-live-general" ocs_live_extra_param="" ocs_live_batch="no" vga=788 toram=live,syslinux ip= net.ifnames=0  nosplash i915.blacklist=yes radeonhd.blacklist=yes nouveau.blacklist=yes vmwgfx.enable_fbdev=1
  initrd /live/initrd.img
}

menuentry "Clonezilla live Safe graphic settings (vga=normal)" --unrestricted {
  search --set -f /live/vmlinuz
  linux /live/vmlinuz boot=live union=overlay username=user config components quiet noswap edd=on nomodeset locales= keyboard-layouts= ocs_live_run="ocs-live-general" ocs_live_extra_param="" ocs_live_batch="no" ip= net.ifnames=0 nomodeset vga=normal nosplash
  initrd /live/initrd.img
}

menuentry "Clonezilla live (Failsafe mode)" --unrestricted {
  search --set -f /live/vmlinuz
  linux /live/vmlinuz boot=live union=overlay username=user config components quiet noswap edd=on nomodeset locales= keyboard-layouts= ocs_live_run="ocs-live-general" ocs_live_extra_param="" ocs_live_batch="no" acpi=off irqpoll noapic noapm nodma nomce nolapic nosmp ip= net.ifnames=0 nomodeset vga=normal nosplash
  initrd /live/initrd.img
}

menuentry --hotkey=s "Clonezilla live (speech synthesis)" --unrestricted {
  search --set -f /live/vmlinuz
  linux /live/vmlinuz boot=live union=overlay username=user config components quiet noswap edd=on nomodeset locales= keyboard-layouts= ocs_live_run="ocs-live-general" ocs_live_extra_param="" ocs_live_batch="no" vga=788 ip= net.ifnames=0  nosplash i915.blacklist=yes radeonhd.blacklist=yes nouveau.blacklist=yes vmwgfx.enable_fbdev=1 speakup.synth=soft ---
  initrd /live/initrd.img
}
```
# Bios config file .ini

```
[cctk]
Absolute=Enabled
AdminSetupLockout=Disabled
AdvBatteryChargeCfg=Disabled
Advsm=TEMPERATURE_1:NA
Advsm=TEMPERATURE_2:NA
Advsm=TEMPERATURE_3:NA
Advsm=TEMPERATURE_4:NA
Advsm=CD_1:0
AllowBiosDowngrade=Enabled
Asset=21899
AutoOn=Disabled
AutoOnHr=0
AutoOnMn=0
AutoOSRecoveryThreshold=OFF
BIOSConnect=Disabled
BiosLogClear=Keep
BiosRcvrFrmHdd=Enabled
BiosVer=1.11.1
BlockSleep=Disabled
BluetoothDevice=Enabled
BootOrder=activebootlist,uefi
BootOrder=uefitype,+hdd.2,+hdd.1
BrightnessAc=10
BrightnessBattery=0
Camera=Enabled
CapsuleFirmwareUpdate=Enabled
CpuCore=CoresAll
CStatesCtrl=Enabled
DockWarningsEnMsg=Enabled
EmbNic1=EnabledPxe
EmbSataRaid=Ahci
EnergyStarLogo=Disabled
ExtPostTime=0s
FanSpeed=Auto
Fastboot=Minimal
FnLock=Enabled
FnLockMode=EnableSecondary
ForcePxeOnNextBoot=Disabled
FOTA=Enabled
FullScreenLogo=Disabled
HdFreeFallProtect=Enabled
IntegratedAudio=Enabled
InternalSpeaker=Enabled
IntlPlatformTrust=Disabled
KbdBacklightTimeoutAc=10s
KbdBacklightTimeoutBatt=10s
KeyboardIllumination=Dim
Keypad=EnabledByFnKey
LidSwitch=Enabled
LogicProc=Enabled
M2PcieSsd0=Enabled
MacAddrPassThru=SystemUnique
MasterPasswordLockout=Disabled
MediaCard=Enabled
MicMuteLed=Enabled
Microphone=Enabled
NumLock=Enabled
OromKeyboardAccess=Enabled
PasswordBypass=Disabled
PasswordConfiguration=PwdMinLen:4
PasswordConfiguration=PwdLowerCaseRqd:Disabled
PasswordConfiguration=PwdUpperCaseRqd:Disabled
PasswordConfiguration=PwdDigitRqd:Disabled
PasswordConfiguration=PwdSpecialCharRqd:Disabled
PasswordLock=Enabled
PeakShiftBatteryThreshold=15
PeakShiftCfg=Disabled
PowerLogClear=Keep
PowerWarn=Enabled
PrimaryBattChargeCfg=Adaptive
PropOwnTag=
Sata0=Enabled
Sata2=Enabled
SdCard=Enabled
SdCardBoot=Disabled
SdCardReadOnly=Disabled
SecureBoot=Disabled
SecureBootMode=AuditMode
ServiceOsClear=Disabled
SfuEnabled=Yes
SHA256=Enabled
SmartErrors=Disabled
SmmSecurityMitigation=Enabled
SoftGuardEn=SoftControlled
SpeedShift=Enabled
Speedstep=Enabled
StrongPassword=Disabled
SupportAssistOSRecovery=Disabled
SvcTag=1WHLM53
SysId=9a0
SysName=Latitude 5410
TelemetryAccessLvl=Full
ThermalLogClear=Keep
ThermalManagement=Optimized
ThunderboltBoot=Disabled
ThunderboltPreboot=Disabled
Touchscreen=Enabled
TpmActivation=Enabled
TpmPpiClearOverride=Disabled
TpmPpiDpo=Disabled
TpmPpiPo=Enabled
TpmSecurity=Enabled
TrustExecution=Disabled
TurboMode=Enabled
TypeCPower=15W
UefiBootPathSecurity=Always
UefiNwStack=Disabled
UnobtrusiveMode=Disabled
UsbEmu=Enabled
UsbPortsExternal=Enabled
UsbPowerShare=Disabled
Virtualization=Enabled
VtForDirectIo=Enabled
WakeOnAc=Disabled
WakeOnDock=Enabled
WakeOnLan=LanOnly
WarningsAndErr=PromptWrnErr
WirelessLan=Enabled
WirelessWwan=Enabled
WlanAutoSense=Disabled
WwanAutoSense=Disabled

```
# For Backing up the Disk
:warning: Clonezilla live cd was not able to detect the nvme disk in the dell, so use the following mode to backup

To backup the disk boot the machine using  Linum mint live cd

Install clonezilla using the following command
```
sudo apt install clonezilla
```
Launch clonezilla with the following command
```
sudo clonezilla
```
Make the backup in the usual way.

After backup is completed boot to the bios menu, you will find two partition to boot
The Partition 2 is windows boot
The partition 5 is clonezilla boot
Please rename the boot menu using Bootice in windows environment. Restart and confirm the bootmenu changes.

⚠️ The backup partition is set to readonly if you want to create a new backup of the windows partition please change the attribute of the partition to readonly via the following command

```
ATT VOL CLEAR READONLY
ATT VOL CLEAR HIDDEN
```


# Clone the disk using dd

Boot from linuxmint or ubuntu and use dd to create a backup.
Tried clonezilla , Macrium reflect , Lazersoft .. nothing works gives an error.
so boot into live environment and use the following command to backup

```
sudo dd if=/dev/nvme0n1 of=/media/mint/Vijai/test/test.img bs=1K conv=noerror,sync status=progress
```

using Gzip compression

```
sudo dd if=/dev/nvme0n1 bs=1K conv=noerror,sync status=progress | gzip > /media/mint/Vijai/test/test.gz
```

To restore

```
sudo dd if=/media/mint/Vijai/test/test.img of=/dev/nvme0n1  bs=1K conv=noerror,sync status=progress

```
using Gzip compression

```
sudo gzip -c /media/mint/Vijai/test/test.gz | dd of=/dev/nvme0n1  bs=1K conv=noerror,sync status=progress

```


