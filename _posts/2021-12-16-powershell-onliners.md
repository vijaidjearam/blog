---
layout: post
date: 2021-12-16 12:08:00
title: Powershell - Onliners
category: Powershell
tags: powershell oneliners
---


### To get scheduled-shutdown task parameters from Remote Pc

```powershell
invoke-command -ComputerName $comp -ScriptBlock {Get-ScheduledTask -TaskName scheduled-shutdown} |Select-Object PSComputername,state, @{N="Starttime";E={$_.Triggers.StartBoundary}}
```
### To set scheduled-shutdown time to midnight on a Remote Pc

```powershell
invoke-command -ComputerName $comp -ScriptBlock {Set-ScheduledTask -TaskName "scheduled-shutdown" -Trigger (New-ScheduledTaskTrigger -At 00:00 -Once)}
```

### To install Powershell Modules on Remote Pc with out prompting confirmation to the user (ex: DellBiosprovider)

```powershell
Invoke-Command -ComputerName $comp -ScriptBlock{Install-Module DellBiosProvider -Force -SkipPublisherCheck -Confirm:$false} 
```

### To install Powershell Modules on Remote Pc If Nuget is not installed (ex: DellBiosprovider)

```powershell
Invoke-Command -ComputerName $comp -ScriptBlock{[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12; Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.208 -Force; Install-Module DellBiosProvider -Force -SkipPublisherCheck -Confirm:$false} 
```

### To get Dell Bios Auto On Parameters

👿 Make sure DellBiosProvider powershell module is installed in the client. 

```powershell
Invoke-Command -ComputerName $comp -ScriptBlock {import-module DellBIOSProvider;Select-Object @{N="AutoOn";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOn).CurrentValue}},@{N="AutoOnHr";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOnHr).CurrentValue}},@{N="AutoOnMn";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOnMn).CurrentValue}},@{N="AutoOnSun";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOnSun).CurrentValue}},@{N="AutoOnMon";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOnMon).CurrentValue}},@{N="AutoOnTue";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOnTue).CurrentValue}},@{N="AutoOnWed";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOnWed).CurrentValue}},@{N="AutoOnThur";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOnThur).CurrentValue}},@{N="AutoOnFri";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOnFri).CurrentValue}},@{N="AutoOnSat";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOnSat).CurrentValue}} -InputObject '';}  | Select-Object PSComputerName,AutoOn,AutoOnHr,AutoOnMn,AutoOnSun,AutoOnMon,AutoOnTue,AutoOnWed,AutoOnThur,AutoOnFri,AutoOnSat | Ft -AutoSize
```

### To get Dell Bios Ac-Power Recovery Status

👿 Make sure DellBiosProvider powershell module is installed in the client. 

```powershell
Invoke-Command -ComputerName $comp -ScriptBlock {import-module DellBIOSProvider;Select-Object @{N="AcPwrRcvry";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AcPwrRcvry).CurrentValue}} -InputObject '';}  | Select-Object PSComputerName,AcPwrRcvry | Ft -AutoSize
```

### To Disable Autoon in Bios

👿 Make sure DellBiosProvider powershell module is installed in the client. 

```powershell
Invoke-Command -ComputerName $comp -scriptblock {import-module DellBIOSProvider; si -Path DellSmbios:\PowerManagement\AutoOn "Disabled" -Password ""}
```

### To set Autoon to weekdays in Bios

👿 Make sure DellBiosProvider powershell module is installed in the client. 

```powershell
Invoke-Command -ComputerName $comp -scriptblock {import-module DellBIOSProvider; si -Path DellSmbios:\PowerManagement\AutoOn "Weekdays" -Password ""}
```

### To set Autoon only on Saturday at 08H00 in Bios

```powershell
Invoke-Command -ComputerName $comp -scriptblock {import-module DellBIOSProvider; si -Path DellSmbios:\PowerManagement\AutoOn "SelectDays" -Password ""}
Invoke-Command -ComputerName $comp -scriptblock {import-module DellBIOSProvider; si -Path DellSmbios:\PowerManagement\AutoOnSat "Enabled" -Password ""}
Invoke-Command -ComputerName $comp -scriptblock {import-module DellBIOSProvider; si -Path DellSmbios:\PowerManagement\AutoOnHr "08" -Password ""}
Invoke-Command -ComputerName $comp -scriptblock {import-module DellBIOSProvider; si -Path DellSmbios:\PowerManagement\AutoOnMn "00" -Password ""}
```
