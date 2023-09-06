---
layout: post
date: 2023-09-06 13:50:37
title: Chocolatey Uninstall MSI using uninstall string
category: chocolatey
tags: chocolatey powershell uninstall
---

# To uninstall a package which uses MSI filetype.

The defalt uninstall script provided by chocolatey loop over to find all the uninstall string corresponding to the software name.
Which is actually good, but not precise. So in order to uninstall the correct software we need to know the ID from the uninstall string

To get the uninstall string you can use the Get-UninstallRegistryKey built in chocolatey.

Use the following commands to load the chocolatey module

```
function Disable-ExecutionPolicy {($ctx = $executioncontext.gettype().getfield("_context","nonpublic,instance").getvalue( $executioncontext)).gettype().getfield("_authorizationManager","nonpublic,instance").setvalue($ctx, (new-object System.Management.Automation.AuthorizationManager "Microsoft.PowerShell"))}
Disable-ExecutionPolicy
Import-Module $env:ChocolateyInstall\helpers\chocolateyProfile.psm1
Import-Module $env:ChocolateyInstall\helpers\chocolateyInstaller.psm1
```
Here is an example of Get-UninstallRegistryKey
![image](https://github.com/vijaidjearam/blog/assets/1507737/07de61dc-30fb-45a8-a0b4-af342ca78068)

Here is an example script to uninstall java software using the ID got using the Get-UninstallRegistryKey

```
$ErrorActionPreference = 'Stop';
$packageArgs = @{
  packageName   = $env:ChocolateyPackageName
  softwareName  = 'stata*'
  fileType      = 'MSI'
  silentArgs    = "{71124AE4-039E-4CA4-87B4-2F64180371F0} /qn /norestart"
  validExitCodes= @(0, 3010, 1605, 1614, 1641)
}
Uninstall-ChocolateyPackage @packageArgs

```

Here is the template for uninstall script
```
$ErrorActionPreference = 'Stop';
$packageArgs = @{
  packageName   = $env:ChocolateyPackageName
  softwareName  = 'stata*'
  fileType      = 'MSI'
  silentArgs    = "{ID} /qn /norestart"
  validExitCodes= @(0, 3010, 1605, 1614, 1641)
}
Uninstall-ChocolateyPackage @packageArgs

```

