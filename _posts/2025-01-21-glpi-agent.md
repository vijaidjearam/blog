---
layout: post
date: 2025-01-21 09:03:26
title: Glpi-Agent
category: Glpi
tags: glpi inventory
---

Ref: [Doc](https://glpi-agent.readthedocs.io/en/1.11/)

## Windows

The glpi agent can be installed using the following command:

```batch
choco install -y glpi-agent-iut3
```

Chocolatey package script:  chocolateyinstall.ps1

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
c:\progra~1\glpi-agent\glpi-agent --force --logger=stderr
```

## Linux

Download the latest version of the pearl script : [glpi-agent-x.xx-linux-installer.pl](https://github.com/glpi-project/glpi-agent/releases) from github

Install glpi agent using the following command:

```bash
sudo perl glpi-agent-x.xx-linux-installer.pl --install --server=URL --runnow --verbose
```

The above command installs glpi agent and makes an inventory to the server.

To manually update inventory 

```bash
glpi-agent --force --logger=stderr
```

## MacOS

Get the latest .pkg package from the [releases page](https://github.com/glpi-project/glpi-agent/releases)

After installing it, you'll have to configure the agent to your needs by creating a dedicated .cfg file under the /Applications/GLPI-Agent/etc/conf.d folder.

You can for example create a local.cfg file and:

  - add the server = GLPI_URL line to point to your GLPI server,

A MacOSX installation video tutorial is available here: [GLPI Agent Demonstration - macOS Monterey - Apple M1](https://www.youtube.com/watch?v=zFYcURQNh9k)

