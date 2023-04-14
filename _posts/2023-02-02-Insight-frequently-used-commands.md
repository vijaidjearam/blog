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

MS Project 2013
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
