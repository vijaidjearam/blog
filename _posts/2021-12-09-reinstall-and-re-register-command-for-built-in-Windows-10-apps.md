---
layout: post
date: 2021-12-09 09:59:00
title: Reinstall and re-register command for built-in Windows 10 apps
category: Powershell
tags: powershell windowsapp
---
# Reinstall and re-register command for built-in Windows 10 apps
```
powershell -ExecutionPolicy Unrestricted Add-AppxPackage -DisableDevelopmentMode -Register $Env:SystemRoot\ImmersiveControlPanel\AppxManifest.xml

```
# If you receive an error

> <span style="color:red">Add-AppxPackage : Cannot find path 'C:\AppXManifest.xml' because it does not exist.
> At line:1 char:61
> + ...  | Foreach {Add-AppxPackage -DisableDevelopmentMode -Register "$($_.I ...
> +                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
>     + CategoryInfo          : ObjectNotFound: (C:\AppXManifest.xml:String) [Add-AppxPackage], ItemNotFoundException
>     + FullyQualifiedErrorId : PathNotFound,Microsoft.Windows.Appx.PackageManager.Commands.AddAppxPackageCommand</span>

