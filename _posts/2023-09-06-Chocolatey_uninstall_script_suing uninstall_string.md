---
layout: post
date: 2023-09-06 13:50:37
title: Chocolatey Uninstall MSI using uninstall string
category: chocolatey
tags: chocolatey powershell
---

To uninstall a package which uses MSI filetype.

The defalt uninstall script provided by chocolatey loop over to find all the uninstall string corresponding to the software name.
Which is actually good, but not precise. So inorder to uninstall the correct software we need to get the ID from the uninstall string

To get the uninstall string you can use the Get-UninstallRegistryKey built in chocolatey.

Use the following commands to load the chocolatey module

```
function Disable-ExecutionPolicy {($ctx = $executioncontext.gettype().getfield("_context","nonpublic,instance").getvalue( $executioncontext)).gettype().getfield("_authorizationManager","nonpublic,instance").setvalue($ctx, (new-object System.Management.Automation.AuthorizationManager "Microsoft.PowerShell"))}
Disable-ExecutionPolicy
Import-Module $env:ChocolateyInstall\helpers\chocolateyProfile.psm1
Import-Module $env:ChocolateyInstall\helpers\chocolateyInstaller.psm1
```
Here is an example of Get-UninstallRegistryKey


Here is an example script to uninstall stata software

```
$ErrorActionPreference = 'Stop';
$packageArgs = @{
  packageName   = $env:ChocolateyPackageName
  softwareName  = 'stata*'
  fileType      = 'MSI'
  silentArgs    = "{10194505-E31E-4C7A-A76A-4D421FAAD4D0} /qn /norestart"
  validExitCodes= @(0, 3010, 1605, 1614, 1641)
}
Uninstall-ChocolateyPackage @packageArgs

```
Here in the above script need to find the correspnding ID for the software need sto be uninstalled.

The ID can be found from the uninstall string.

