---
layout: post
date: 2022-11-30 12:18:39
title: Modify BIOS settings via dell command configure winPE ISO
category: BIOS
tags: bios dell winPE 
---

# Modify BIOS settings via dell command configure via winPE 

## Method 1 Via ISO
:information_source: The following script creates an iso by default, changing the makewinpemedia switch to udf and pointing to the drive lettre of usb creates a bootable usb.

:key: Pre-requisites:
* [Install the Windows ADK](https://go.microsoft.com/fwlink/?linkid=2196127)

* [Install the Windows PE add-on for the Windows ADK](https://go.microsoft.com/fwlink/?linkid=2196224)

*  Install Dell Command Configure



```Batch
@echo off
REM Check if Windows ADK is Installed
if exist "C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Deployment Tools" ( echo windows ADK is installed ) else (GOTO :WindowsADK)

REM check if dell command configure is installed
if exist "C:\Progra~2\Dell\Comman~2\X86_64" (echo Dell command configure Installed) else (GOTO :Dellcommandconfigurenotinstalled)

REM check if Dellbios folder exist on the machine
if exist "C:\dellbios" (GOTO :dellbiosfolderdetected) else (GOTO :main)
:dellbiosfolderdetected
echo dellbios folder detected so deleting and recreating the folder
rmdir /s /q c:\dellbios
GOTO :main

:main

REM Change Dir to Dep Tools
cd /d "C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Deployment Tools"

REM Set PE Tools ENV Variables
call DandISetEnv.bat

REM Un-mount any previous mounts to C:\dellbios\mount
imagex /unmount "C:\dellbios\mount"

REM Create PE Boot .WIM
call copype.cmd amd64 c:\dellbios

REM Mount Boot.WIM to Mount Folder
DISM /mount-Wim /WimFile:C:\dellbios\media\sources\boot.wim /index:1 /mountdir:C:\dellbios\mount

REM Add commonly used packages to mounted WIM
cd /d "C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Windows Preinstallation Environment\amd64\WinPE_OCs"
DISM /image=C:\dellbios\mount /Add-Package /PackagePath:"WinPE-FontSupport-JA-JP.cab"
DISM /image=C:\dellbios\mount /Add-Package /PackagePath:"winpe-fontsupport-zh-cn.cab"
DISM /image=C:\dellbios\mount /Add-Package /PackagePath:"winpe-wmi.cab"
DISM /image=C:\dellbios\mount /Add-Package /PackagePath:"winpe-scripting.cab"
DISM /image=C:\dellbios\mount /Add-Package /PackagePath:"winpe-wds-tools.cab"
DISM /image=C:\dellbios\mount /set-inputlocale:040c:0000040c

REM Copy Custom files to inside mounted WIM folder
xcopy C:\Progra~2\Dell\Comman~2\X86_64  c:\dellbios\mount\Command_Configure\X86_64 /S /E /i /Y

REM adding our batch script to run on the start up using startnet.cmd
set STARTNET=c:\dellbios\mount\windows\system32\STARTNET.CMD
echo echo off>> %STARTNET%
echo echo Starting WMI Services >> %STARTNET%
echo net start winmgmt >> %STARTNET%
echo echo ******************** >> %STARTNET%
echo X:\Command_Configure\X86_64\biosupdate.bat >> %STARTNET%
echo echo Successfully configured the BIOS >> %STARTNET%
echo echo *********************>> %STARTNET%

REM unmount the wim
DISM /Unmount-Wim /mountDir:C:\dellbios\mount /commit

REM creating the iso via Makewinpemedia 
call Makewinpemedia /iso C:\dellbios C:\dellbios\dellbios.iso
GOTO :end
:WindowsADK
@echo windows ADK is not installed
GOTO :end
:Dellcommandconfigurenotinstalled
echo Dell command configure not installed.
:end
pause
```

The above script creates a dellbios.iso in c:\dellbios

Booting the iso goes to the winPE environment:

-> STARTNET.cmd gets exectuted at startup 

-> The *biosupdate.bat* is injected to STARTNET.cmd and gets executed 

-> The *biosupdate.bat* content is as follows:

```batch
	@echo Find a drive that has bios.ini.
	@for %%a in (C D E F G H I J K L M N O P Q R S T U V W X Y Z) do @if exist %%a:\bios.ini set BIOSDRIVE=%%a
	@echo The Images folder is on drive: %BIOSDRIVE%
	@echo %BIOSDRIVE%:\bios.ini
	X:\Command_Configure\X86_64\cctk.exe -i %BIOSDRIVE%:\bios.ini --ValSetupPwd=test 
```

-> The *biosupdate.bat* file searches for *bios.ini* in the root of all the drives, when found executes 💡 *cctk.exe* and imports the *bios.ini* parametre settings

The *bios.ini* can be injected to the iso using [Anyburn](https://anyburn.com/download.php) please follow the instructions [here](https://anyburn.com/tutorials/edit-iso-file.htm)

## Method 2 : WinPE via FOG

ℹ️ Source: [link](https://ipxe.org/wimboot)

:key: Pre-requisites:
* [Install the Windows ADK](https://go.microsoft.com/fwlink/?linkid=2196127)

* [Install the Windows PE add-on for the Windows ADK](https://go.microsoft.com/fwlink/?linkid=2196224)

*  Install Dell Command Configure

In this method we will create a winpe environment in c:\dellbios

```Batch
@echo off
REM Check if Windows ADK is Installed
if exist "C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Deployment Tools" ( echo windows ADK is installed ) else (GOTO :WindowsADK)

REM check if dell command configure is installed
if exist "C:\Progra~2\Dell\Comman~2\X86_64" (echo Dell command configure Installed) else (GOTO :Dellcommandconfigurenotinstalled)

REM check if Dellbios folder exist on the machine
if exist "C:\dellbios" (GOTO :dellbiosfolderdetected) else (GOTO :main)
:dellbiosfolderdetected
echo dellbios folder detected so deleting the existing folder
REM Un-mount any previous mounts to C:\dellbios\mount
imagex /unmount "C:\dellbios\mount"
rmdir /s /q c:\dellbios
GOTO :main

:main

REM Change Dir to Dep Tools
cd /d "C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Deployment Tools"

REM Set PE Tools ENV Variables
call DandISetEnv.bat

REM Create PE Boot .WIM
call copype.cmd amd64 c:\dellbios

REM Mount Boot.WIM to Mount Folder
DISM /mount-Wim /WimFile:C:\dellbios\media\sources\boot.wim /index:1 /mountdir:C:\dellbios\mount

REM Add commonly used packages to mounted WIM
cd /d "C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Windows Preinstallation Environment\amd64\WinPE_OCs"
DISM /image=C:\dellbios\mount /Add-Package /PackagePath:"WinPE-FontSupport-JA-JP.cab"
DISM /image=C:\dellbios\mount /Add-Package /PackagePath:"winpe-fontsupport-zh-cn.cab"
DISM /image=C:\dellbios\mount /Add-Package /PackagePath:"winpe-wmi.cab"
DISM /image=C:\dellbios\mount /Add-Package /PackagePath:"winpe-scripting.cab"
DISM /image=C:\dellbios\mount /Add-Package /PackagePath:"winpe-wds-tools.cab"

REM Adding French keyboard layout to winPE environment
DISM /image=C:\dellbios\mount /set-inputlocale:040c:0000040c

REM Copy Custom files to inside mounted WIM folder
xcopy C:\Progra~2\Dell\Comman~2\X86_64  c:\dellbios\mount\Command_Configure\X86_64 /S /E /i /Y

REM unmount the wim
DISM /Unmount-Wim /mountDir:C:\dellbios\mount /commit

GOTO :end
:WindowsADK
@echo windows ADK is not installed
GOTO :end
:Dellcommandconfigurenotinstalled
echo Dell command configure not installed.
:end
pause
```
The above script creates the winpe folder structure in c:\dellbios

Make a directory *winpe* in fog in the location ```/var/www/html/isos```

Download the latest version of the kernel for wimboot from [here](https://github.com/ipxe/wimboot/releases) and place it in the root of the same directory.

Copy the contents of the dellbios folder to fog /var/www/html/isos/winpe

Here is the data structure: 

![image](https://user-images.githubusercontent.com/1507737/208092088-7a2a4368-e088-4512-b433-b2257d2488dc.png)

content of the install.bat

```Batch
Wpeutil InitializeNetwork
:Loop
ping -n 10 localhost
Wpeutil WaitForNetwork
echo error: %errorlevel% Network connection could not be established check the network switch port config ,retrying after few seconds
if %errorlevel% neq 0 goto :Loop
net use Z: \\10.57.0.4\batchs /user:user pass
X:\Command_Configure\X86_64\cctk.exe -i Z:\bios\bios.ini --ValSetupPwd=test
pause
```

content of winpeshl.ini

```Batch
[LaunchApps]
"install.bat"
```
Here are the files signed and needs to be in the respective places for the secure boot to work:

bootx64.efi -> /var/www/html/isos/winpe (root of the winpe directory)

wimboot.efi -> /var/www/html/isos/winpe (root of the winpe directory)

bzImage.efi -> /var/www/html/fog/service/ipxe

ipxe.efi -> /tftpboot

to sign the above file use the sbsign command in ubuntu

```bash

sbsign --key DB.key --cert DB.crt --output bootx64.efi bootx64-unsigned.efi 
```


To enroll the key in the BIOS:

Go to the secure boot settings in the BIOS and change the secure boot to **Audit mode**

In the fog go to fog configuration -> click on iPXE New Menu Entry and configure the settings as below:

![image](https://user-images.githubusercontent.com/1507737/210764287-4af8ca34-4896-4563-aedc-c94bc6c2d60f.png)

The parameter configuration should be as below:

```
chain http://${fog-ip}/fog/service/ipxe/Enrollkey.efi
echo Rebooting System in 5 Seconds
sleep 5
reboot
params
```
Now lets add a menu entry for configuring the BIOS 

In the fog go to fog configuration -> click on iPXE New Menu Entry and configure the settings as below:

![image](https://user-images.githubusercontent.com/1507737/210765679-4f48c9ed-ccfb-4828-bd58-e429d0c0ad98.png)

The parameter configuration should be as below:

😈 The wimboot.efi and bootx64.efi should be signed

```
set http-path http://${fog-ip}/isos/winpe
kernel ${http-path}/wimboot
initrd ${http-path}/bootx64.efi bootx64.efi
initrd ${http-path}/install.bat install.bat
initrd ${http-path}/winpeshl.ini winpeshl.ini
initrd ${http-path}/media/Boot/BCD BCD
initrd ${http-path}/media/Boot/boot.sdi boot.sdi
initrd ${http-path}/media/sources/boot.wim boot.wim
boot || goto MENU
```


In the client machine boot to pxe menu and select the Enroll key option in the fog menu.

The key will be automatically enrolled to the bios and the secure boot mode is set to **Deployed Mode**

Boot the machine via network IP V4 and select the winpe in the menu , that's it the winpe environment will configure the bios 😃

---


**References**

* [Creating Keys](https://www.rodsbooks.com/efi-bootloaders/controlling-sb.html#creatingkeys)
* Here are the dependencies required for creating keys

```
sudo apt-get update && apt-get upgrade

sudo apt install libfile-slurper-perl sudo apt-get install libssl-dev build-essential gnu-efi git make
```

Reference : https://www.geeksforgeeks.org/perl-slurp-module/

😈 install as root

```
perl -MCPAN -e shell

install File::Slurp
```

Ref: https://stackoverflow.com/questions/1039107/how-can-i-check-if-a-perl-module-is-installed-on-my-system-from-the-command-line 

Check if module is installed

```

alias modver="perl -e"eval qq{use \$ARGV[0];\\\$v=\\\$\${ARGV[0]}::VERSION;};\ print\$@?qq{No module found\n}:\$v?qq{Version \$v\n}:qq{Found.\n};"$1"mode

=> modver XML::Simple No module found
```

List all the installed Perl modules

```
perl -MFile::Find=find -MFile::Spec::Functions -Tlw -e 'find { wanted => sub { print canonpath $_ if /.pm\z/ }, no_chdir => 1 }, @INC'
```

Efitools install

```
git clone git://git.kernel.org/pub/scm/linux/kernel/git/jejb/efitools.git
```

https://community.spiceworks.com/topic/2343741-fog-self-signing-kernel-so-secure-boot-works?source=recommended

https://forums.fogproject.org/topic/15888/imaging-with-fog-and-secure-boot-poc/2

https://wiki.gentoo.org/wiki/User:Sakaki/Sakaki%27s_EFI_Install_Guide/Configuring_Secure_Boot_under_OpenRC







