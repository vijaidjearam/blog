---
layout: post
date: 2023-09-06 14:10:46
title: Chocolatey Install script snippets
category: chocolatey
tags: chocolatey install powershell
---

## When you need to check if a file exists in the network share before proceeding with the install use Get-Item command.
The Get-Item -path \\Network-share\file.txt checks if the file is in the path, if not it raises an error that stops the chocolatey install script because of the erroractionpreferrence set at the begining of the sript.

Here is an example script:

```
$ErrorActionPreference = 'Stop';
$licensefile = "\\Network-share\License.LIC"
$fileLocation = '\\Network-share\Setup.exe'
Get-Item -Path $licensefile

$packageArgs = @{
  packageName   = $env:ChocolateyPackageName
  fileType      = 'EXE'
  file          = $fileLocation
  softwareName  = 'example*'
  silentArgs    = '/s /v"/qn /norestart"'
  validExitCodes= @(0, 3010, 1641)
}

Install-ChocolateyInstallPackage @packageArgs
Copy-Item $licensefile -Destination 'C:\Program Files\example' -Force 
```
The above script checks if the license file is present in the network share using the Get-Item command. If the file doesnt exist then eventually the get-item raises an error, which eventually stops the script.
