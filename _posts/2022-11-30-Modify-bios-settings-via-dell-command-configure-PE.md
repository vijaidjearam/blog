---
layout: post
date: 2022-11-30 12:18:39
title: Modify BIOS settings via dell command configure winPE ISO
category: BIOS
tags: bios dell winPE 
---

# Modify BIOS settings via dell command configure winPE ISO

:information_source: The following script creates an iso by default, changing the makewinpemedia switch to udf and pointing to the drive lettre of usb creates a bootoable usb

![image](https://user-images.githubusercontent.com/1507737/204784575-fbfee08a-d31c-45dd-87ac-779867ded3e3.png)



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

The above script creates a dellbios.iso in c:\dellbios

The *bios.ini* can be injected to the iso using [Anyburn](https://anyburn.com/download.php) please follow the instructions [here](https://anyburn.com/tutorials/edit-iso-file.htm)

