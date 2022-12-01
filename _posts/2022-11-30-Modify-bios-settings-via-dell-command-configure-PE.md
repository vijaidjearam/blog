---
layout: post
date: 2022-11-30 12:18:39
title: Modify BIOS settings via dell command configure winPE ISO
category: BIOS
tags: bios dell winPE 
---

# Modify BIOS settings via dell command configure winPE ISO

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
if exist "C:\dellbios" (GOTO :dellbiosfolderdetected) else (GOTO :dellbiosfoldernotdetected)
:dellbiosfolderdetected
echo dellbios folder detected so deleting and recreating the folder
rmdir /s /q c:\dellbios
mkdir c:\dellbios
GOTO :main

:dellbiosfoldernotdetected
echo dellbios folder not detected so creating the folder
mkdir c:\dellbios

:main

REM Change Dir to Dep Tools
cd /d "C:\Program Files (x86)\Windows Kits\10\Assessment and Deployment Kit\Deployment Tools"

REM Set PE Tools ENV Variables
call DandISetEnv.bat

REM Un-mount any previous mounts to C:\WinPEx86\mount
imagex /unmount "C:\WinPEx86\mount"

REM Delete Working PE Build folder if exists
rd c:\WinPEx86 /s/q

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
## Scenario 1 : Create iso

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

## Scenario 2 : WinPE via FOG

The *biosupdate.bat* file is modifed, it searches the bios.ini config from a local network share

```batch
net use Z: \\10.57.0.4\batchs /user:user pass
X:\Command_Configure\X86_64\cctk.exe -i Z:\bios\bios.ini --ValSetupPwd=test 
```
Place the *biosupdate.bat* in *C:\Progra~2\Dell\Comman~2\X86_64*

Generate the dellbios.iso following the same method as described above.

ℹ️ Source: [link](https://ipxe.org/wimboot)

Mount the iso and copy the contents of the iso to fog */var/www/html/isos/winpe*

Download the latest version of the kernel for wimboot from [here](https://github.com/ipxe/wimboot/releases) and place it in the root of the same directory.

In the fog go to fog configuration -> click on iPXE New Menu Entry and configure the settings as below:

![image](https://user-images.githubusercontent.com/1507737/205088215-da033c83-83cc-4d66-adac-ef887968c296.png)

The parameter configuration should be as below:

```
set http-path http://${fog-ip}/isos/winpe
kernel ${http-path}/wimboot
initrd ${http-path}/Boot/BCD BCD
initrd ${http-path}/Boot/boot.sdi boot.sdi
initrd ${http-path}/sources/boot.wim boot.wim
boot || goto MENU
```

Boot the machine via network IP V4 and select the dellbios in the menu , that's it the winpe environment will configure the bios 😃







