---
layout: post
date: 2021-12-16 12:08:00
title: Powershell - Onliners
category: Powershell
tags: powershell oneliners
---
# Powershell Oneliners

## To get the scheduled-shutdown task parameters from each Pc
```
invoke-command -ComputerName $comp -ScriptBlock {Get-ScheduledTask -TaskName scheduled-shutdown} |Select-Object PSComputername,state, @{N="Starttime";E={$_.Triggers.StartBoundary}}
```

## To get the dell Bios Auto On Parameters
👿 Make sure DellBiosProvider powershell module is installed in the client. 

```
Invoke-Command -ComputerName $comp -ScriptBlock {import-module DellBIOSProvider;Select-Object @{N="AutoOn";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOn).CurrentValue}},@{N="AutoOnHr";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOnHr).CurrentValue}},@{N="AutoOnMn";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOnMn).CurrentValue}},@{N="AutoOnSun";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOnSun).CurrentValue}},@{N="AutoOnMon";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOnMon).CurrentValue}},@{N="AutoOnTue";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOnTue).CurrentValue}},@{N="AutoOnWed";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOnWed).CurrentValue}},@{N="AutoOnThur";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOnThur).CurrentValue}},@{N="AutoOnFri";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOnFri).CurrentValue}},@{N="AutoOnSat";E={(Get-ChildItem -Path DellSmbios:\PowerManagement\AutoOnSat).CurrentValue}} -InputObject '';}  | Select-Object PSComputerName,AutoOn,AutoOnHr,AutoOnMn,AutoOnSun,AutoOnMon,AutoOnTue,AutoOnWed,AutoOnThur,AutoOnFri,AutoOnSat | Ft -AutoSize
```
## To install Powershell Module on Remote Pc with out prompting confirmation to the user

```
Invoke-Command -ComputerName $comp -ScriptBlock{Install-Module DellBiosProvider -Force -Confirm:$false} 
```
