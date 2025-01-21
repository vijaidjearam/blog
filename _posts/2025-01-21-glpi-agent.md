---
layout: post
date: 2025-01-21 09:03:26
title: Glpi-Agent
category: Glpi
tags: glpi inventory
---

The Glpi agent is installed with the following parameters using Chocolatey:
chocolateyinstall.ps1

```powershell
$ErrorActionPreference = 'Stop';
$version = '1.11'

$packageArgs = @{
  packageName   = $env:ChocolateyPackageName
  fileType      = 'msi'
  url64         = "https://github.com/glpi-project/glpi-agent/releases/download/$version/GLPI-Agent-$version-x64.msi"
  checksum64    = '9c28e6ea14c1f6aa20eb5beb3a04f3ad4104c763caa58ceb39c076bfc613cd40'
  checksumType64= 'sha256' 
  silentArgs    = '/quiet SERVER="URL" TASK_DAILY_MODIFIER=3 RUNNOW=1'
  validExitCodes= @(0, 3010, 1641)
}

Install-ChocolateyPackage @packageArgs
```

The glpi task frequency is set to exceute once every 3 days and with the runnow parameter, the inventory is executed as soon as the software package is installed.

To force the inventory in the local machine use the following commmand: 

```batch
c:\progra~1\glpi-agent\glpi-agent --forece --logger=stderr
```
