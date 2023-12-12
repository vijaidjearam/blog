---
layout: post
date: 2023-02-02 08:26:29
title: Insight Frequently used commands
category: Insight
tags: insight
---


# Insight Freequently used commands

```batch
cmd /k c:\uwf\uwf-disable.bat
cmd /k c:\uwf\uwf-enable.bat
cmd /k choco install automation-studio-7.1.0-sr1 -y
TASKKILL /F /IM Asprojet.exe /T
cmd /k c:\temp\RG_V9_Rev.ZA_ROBOGUIDE_[A08B-9410-J605]\roboguide-collect-license-info.exe
cmd /k c:\temp\roboguide-collect-license-info.exe
cmd /k del /S /Q c:\temp\*
cmd /k choco install fog-client-urca -y --params "'/server:fog-gmp.local.com'"
cmd /k rmdir c:\folder /s /q
```
Microsoft office change parameters:

To Uncheck the option "Use system separator"

```batch
REG LOAD HKU\Default C:\Users\Default\NTUSER.DAT
REG ADD "HKEY_CURRENT_USER\Software\Microsoft\Office\16.0\Excel\Options" /v "UseSystemSeparators" /t REG_DWORD /d 0 /f
REG UNLOAD HKU\Default
```

Insight command to deploy

```batch
cmd /k \\10.57.0.4\batchs\scripts\bat\office-uncheck-usesystemseparator.bat
```



## Program paths

Automation Studio
```
"C:\Program Files\Famic Technologies\Automation Studio E7.1\AsProjet.exe"
```

Dell Command Configure
```
c:\progra~2\dell\comman~2\x86_64\cctk.exe
```

MS Project 2013 Activation path
```
C:\PROGRA~2\MICROS~3\Office15\ospp.vbs
```

Roboguide 
```
"C:\Program Files (x86)\FANUC\ROBOGUIDE\bin\ROBOGUIDE.exe"
```

Roboguide License Manager
```
"C:\Program Files (x86)\FANUC\Shared\Utilities\FRLicenseManager.exe"
```

Catia
```
"C:\Program Files\Dassault Systemes\B31\win_b64\code\bin\CATSTART.exe"  -run "CNEXT.exe" -env CATIA.V5-6R2021.B31 -direnv "C:\ProgramData\DassaultSystemes\CATEnv" -nowindow 
```

F-secure GUI
```
"C:\Program Files (x86)\F-Secure\Client Security\fs_ui_32.exe"
```
Automation license Manager
```
"C:\Program Files\Siemens\Automation\Automation License Manager\almapp\almgui64x.exe"
```

## Wireshark
```batch
cmd /k choco install -y wireshark npcap
"c:\Program Files\Wireshark\wireshark.exe" -i pedagogie -k -Y "ip.addr == 192.168.2.1"
TASKKILL /F /IM dumpcap.exe /T
TASKKILL /F /IM wireshark.exe /T
```

