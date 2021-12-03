---
layout: post
date: 2021-12-03 09:16:00
title: BSOD-INACCESSIBLE_BOOT_DEVICE
category: BSOD 
tags: bsod windows boot
---
# Resolve INACCESSIBLE_BOOT_DEVICE Error
## Stage 1
* Boot Pc from windows CD -> repair option -> command prompt;
* for Safemode:
``` 
bcdedit /set {default} safeboot minimal
```
* for Safe Mode with Networking type in:
```
bcdedit /set {current} safeboot network
```
* to resest the pcto boot normally :
  * use msconfig -> Boot -> disable safeboot 
## Stage 2
* Boot the PC with windows iso and get into repair mode
* launch command prompt and search for the drive letter that has windows and programfiles use that drive letter in the below command
```
SFC /scannow /OFFBootdir=%drive%:\ /OFFWindir=%drive%:\Windows
Dism /Image:%drive%:\ /Cleanup-Image /RestoreHealth
chkdsk %drive%: /R /F
```
* you can also prepare a batch file with the following content below to simlpify the steps:
```
@echo off 
set /p drive=Input Drive letter:
SFC /scannow /OFFBootdir=%drive%:\ /OFFWindir=%drive%:\Windows
Dism /Image:%drive%:\ /Cleanup-Image /RestoreHealth
chkdsk %drive%: /R /F
pause
```
