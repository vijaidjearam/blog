---
layout: post
date: 2024-04-18 08:53:47
title: Snippet for firewall rule in choco package
category: chocolatey
tags: chocolatey windows package
---
# Snippet for firewall rule in choco package

To add a firewall rule

```powershell
New-NetFirewallRule -Program “C:\simulia\estproducts\2024\win_b64\code\bin\elit_driverlm.exe” -Action Allow -Profile Domain,Private -DisplayName “SMAEliLicensing” -Description “SMAEliLicensing” -Direction Inbound -Protocol TCP
New-NetFirewallRule -Program “C:\simulia\estproducts\2024\win_b64\code\bin\elit_driverlm.exe” -Action Allow -Profile Domain,Private -DisplayName “SMAEliLicensing” -Description “SMAEliLicensing” -Direction Inbound -Protocol UDP
```
To Delete a firewall rule

```powershell
Remove-NetFirewallRule -DisplayName "SMAEliLicensing"
Remove-NetFirewallRule -DisplayName "SMACaeKMain"
```
