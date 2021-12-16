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
