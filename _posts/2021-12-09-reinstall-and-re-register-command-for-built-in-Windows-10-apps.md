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
# If you receive an <span style="color:red">error</span>

```diff
- Add-AppxPackage : Cannot find path 'C:\AppXManifest.xml' because it does not exist.
- At line:1 char:61
- + ...  | Foreach {Add-AppxPackage -DisableDevelopmentMode -Register "$($_.I ...
- +                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
-     + CategoryInfo          : ObjectNotFound: (C:\AppXManifest.xml:String) [Add-AppxPackage], ItemNotFoundException
-     + FullyQualifiedErrorId : PathNotFound,Microsoft.Windows.Appx.PackageManager.Commands.AddAppxPackageCommand
```

The above errors if the Microsoft Store package folder is missing (or incomplete) from the C:\Program Files\WindowsApps folder. You’ll need to download the Microsoft Store appx bundle/package from Microsoft and install it in those cases. The instructions are given in below.

# Download the Microsoft Store installer (Appx package)

You can download the Microsoft Store app and its dependencies in the form of .Appx and .AppxBundle package or installers from Microsoft’s servers. Follow these steps to do so:

1. Visit the following website:

> [https://store.rg-adguard.net/](https://store.rg-adguard.net/)

The above third-party site can generate download links (to app installers) for the chosen app. These are direct download links pointing at the official Microsoft servers.

2. On the above page, paste the following link in the URL text box. The following is the Microsoft Store app’s official link.

> [https://www.microsoft.com/en-us/p/microsoft-store/9wzdncrfjbmp](https://www.microsoft.com/en-us/p/microsoft-store/9wzdncrfjbmp)

3. Select Retail (or the appropriate branch accordingly), and click the generate button.
microsoft store reinstall app bundle

4. As the Microsoft Store app depends on .NET Framework, .NET Runtime, and VC Libs, download the latest packages of each item listed. Be sure to download the correct ones matching the bitness (x86 vs. x64) of your Windows 10.
5. Now, you would have downloaded these four Appx packages — the version numbers will vary according to the build/version of the Microsoft Store app.
```
Microsoft.NET.Native.Framework.2.2_2.2.27912.0_x64__8wekyb3d8bbwe.Appx

Microsoft.NET.Native.Runtime.2.2_2.2.28604.0_x64__8wekyb3d8bbwe.Appx

Microsoft.VCLibs.140.00_14.0.29231.0_x64__8wekyb3d8bbwe.Appx

Microsoft.WindowsStore_12010.1001.313.0_neutral___8wekyb3d8bbwe.AppxBundle
```

6. Run each .appx installers first, as they’re the dependencies of Microsoft Store. Alternatively, you can use PowerShell to install each package. The PowerShell command-line syntax is below:
```
Add-AppxPackage -Path "C:\Path\filename.Appx"
```
- If you get the error Deployment failed with HRESULT: 0x80073D02, skip the package. It’s most likely because the package or dependency is already installed and currently in use by some other app.

- Also, you can run the following command to check if an app package is already installed or not:
```
get-appxpackage | sort-object -Property PackageFullName | select packagefullname | out-gridview
```

If the package (of the same version) is already installed, you don’t have to install it again.

list installed packages
