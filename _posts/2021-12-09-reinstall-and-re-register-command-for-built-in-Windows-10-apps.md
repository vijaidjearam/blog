---
layout: post
date: 2021-12-09 09:59:00
title: Reinstall and re-register command for built-in Windows 10 apps
category: Powershell
tags: powershell windowsapp
---
# Reinstall and re-register command for built-in Windows 10 apps
```
Get-AppxPackage -allusers | foreach {Add-AppxPackage -register "$($_.InstallLocation)appxmanifest.xml" -DisableDevelopmentMode}
```
