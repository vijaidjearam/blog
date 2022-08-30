---
layout: post
date: 2022-08-30 08:43:11
title: Factory Reset Partition via Clonezilla
category: Recovery 
tags: recovery, clonezilla, factory-reset
---
## AUTOMATED UEFI-WINDOWS RESTORE USING CLONEZILLA

Credits : https://sites.google.com/rmprepusb.com/www/tutorials/142---windows-restore-uefi

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

To unhide use ATT VOL CLEAR HIDDEN
To clear readonly ATT VOL CLEAR READONLY
```
