---
layout: post
date: 2022-06-10 16:40:10
title: Powershell remote install/uninstall softwares
category: Powershell
tags: powershell
---

Powershell Function to uninstall msi remotely and get the output status

```
function uninstall-software()
{
  Param
    (
        [Parameter(Mandatory = $true)] [Array] $computers,
        [Parameter(Mandatory = $true)] [string] $uninstallstring,
        [string] $file,
        [Parameter(Mandatory = $true)] [ValidateSet("Msi","Exe")] $uninstalltype,
        [Parameter(Mandatory = $true)] [Array] $validexitcodes
    )
function Disable-ExecutionPolicy {($ctx = $executioncontext.gettype().getfield("_context","nonpublic,instance").getvalue( $executioncontext)).gettype().getfield("_authorizationManager","nonpublic,instance").setvalue($ctx, (new-object System.Management.Automation.AuthorizationManager "Microsoft.PowerShell"))}
Disable-ExecutionPolicy
import-module Invoke-CommandAs

Get-Job | Remove-Job -Force
foreach ($computer in $computers)
{
    if (Test-Connection $computer -Count 1 -Quiet)
    {
    write-host "Executing command on :"$computer -ForegroundColor Green
        if ($uninstalltype -eq "Msi")
        {
        Invoke-Command -ComputerName $computer -ScriptBlock{
        param($uninstallstring)

        $software = Start-Process "msiexec.exe" -ArgumentList "/X {$uninstallstring} /quiet /norestart" -Wait -PassThru; 
        $software.ExitCode

        } -AsJob -ArgumentList $uninstallstring
        }
        else
        {
        Invoke-CommandAs -ComputerName $computer -ScriptBlock{
                param(
                [string]$uninstallstring,
                [string]$file
                
                )

        write-host "uninstall string:" $uninstallstring
        Write-Host "File:"$file


        $software = Start-Process cmd -ArgumentList "/c $file $uninstallstring" -Wait -PassThru; 
        $software.ExitCode

        } -AsJob -ArgumentList ($uninstallstring,$file) -AsSystem

    }
    }
    else
    {
    Write-Host "$computer - Is offline" -BackgroundColor Red
    }

}
Write-Host "Command dispatched to all the Pcs online" -ForegroundColor Green
While (Get-Job -State "Running") {
    Get-Job
    Start-Sleep 1
    cls 
}
Get-Job
write-host "Jobs completed, getting output"


$results = @()

foreach($job in Get-Job){

      $result = Receive-Job $job
      if ($validexitcodes.Contains($result))
      {$installstatus= "ok"}
      else
      {$installstatus= "Nok"}

      $resultobject = New-Object psobject
      $resultobject | Add-Member -MemberType NoteProperty -Name "ComputerName" -Value $job.Location
      $resultobject | Add-Member -MemberType NoteProperty -Name "Result" -Value $result
      $resultobject | Add-Member -MemberType NoteProperty -Name "Install/uninstall-Status" -Value $installstatus
      $results +=$resultobject
}
$results | Out-GridView
}

# example of usage

# uninstall-software -computers $computers -uninstalltype "Msi" -uninstallstring "7EC66A9F-0A49-4DC0-A9E8-460333EA8013"
# uninstall-software -uninstalltype Exe -file \\batchs\antivirus\antivirus_Fsecure\FsUninstallationTool.exe -uninstallstring "--silent" -computers $computer -validexitcodes @(0,99)
# Kaspersky - 7EC66A9F-0A49-4DC0-A9E8-460333EA8013
# Kaspersky Agent - 2924BEDA-E0D7-4DAF-A224-50D2E0B12F5B

# 948B0156-E2D5-4507-A553-FCF05AA03496  - F-secure security premium
# A6A61BE1-7D2D-4B2A-8865-9CB38FF0E485 - F-Secure SWUP
# uninstall-software -computers 028F2-0D0003015 -uninstalltype "Msi" -uninstallstring "FFD8CF3D-3063-4D97-B007-26258E71D02F" -validexitcodes @(0,0)

```

function to remotely install chocolatey softwares and get the result status

```
function install-choco-software()
{
  Param
    (
        [Parameter(Mandatory = $true)] [Array] $computers,
        [Parameter(Mandatory = $true)] [string] $installstring,
        [Parameter(Mandatory = $true)] [Array] $validexitcodes
    )

Get-Job | Remove-Job -Force
foreach ($computer in $computers)
{
if (Test-Connection $computer -Count 1 -Quiet)
{
write-host "Executing command on :"$computer -ForegroundColor Green
Invoke-Command -ComputerName $computer -ScriptBlock{

param ($installstring)
$software = Start-Process "cmd" -ArgumentList "/c choco install -y $installstring" -Wait -PassThru; 
$software.ExitCode

} -AsJob -ArgumentList $installstring
}
else
{
Write-Host "$computer - Is offline" -BackgroundColor Red
}

}
Write-Host "Command dispatched to all the Pcs online" -ForegroundColor Green
While (Get-Job -State "Running") {
    Get-Job
    Start-Sleep 1
    cls
}
Get-Job
write-host "Jobs completed, getting output"


$results = @()

foreach($job in Get-Job){

      $result = Receive-Job $job
      if ($validexitcodes.Contains($result))
      {$installstatus= "ok"}
      else
      {$installstatus= "Nok"}

      $resultobject = New-Object psobject
      $resultobject | Add-Member -MemberType NoteProperty -Name "ComputerName" -Value $job.Location
      $resultobject | Add-Member -MemberType NoteProperty -Name "Result" -Value $result
      $resultobject | Add-Member -MemberType NoteProperty -Name "Install/uninstall-Status" -Value $installstatus
      $results +=$resultobject
}
$results | Out-GridView
}

# example of usage

# install-choco-software -computers $computer -installstring "f-secure" -validexitcodes @(0,3010)
```

