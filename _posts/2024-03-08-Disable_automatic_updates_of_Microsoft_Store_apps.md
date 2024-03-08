---
layout: post
date: 2024-03-08 11:04:55
title: Disable automatic updates of Microsoft Store apps
category: Microsoft-store-apps
tags: Microsoft-store-apps windows
---
Disable automatic updates of Microsoft Store apps

```Batch
reg add HKLM\SOFTWARE\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 2 /f 
```

PowerShell Script

```
$Name = “AutoDownload” 
$Value = 2 
$Path = “HKLM:\SOFTWARE\Policies\Microsoft\WindowsStore” 
If ((Test-Path $Path) -eq $false){ 
New-Item -Path $Path -ItemType Directory 
} 
If (-!(Get-ItemProperty -Path $Path -Name $name -ErrorAction SilentlyContinue)){ 
New-ItemProperty -Path $Path -Name $Name -PropertyType DWord -Value $Value 
} 
else{ 
Set-ItemProperty -Path $Path -Name $Name -Value $Value 
} 
```

Notes:
To re-enable the automatic updates of Microsoft Store apps, run the above scripts by assigning the value ‘4’ instead of ‘2’ for the Reg_Dword entry named AutoDownload.
