---
layout: post
date: 2022-06-10 16:40:10
title: Powershell remote install/uninstall softwares
category: powershell
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
        [Parameter(Mandatory = $true)] [Array] $validexitcodes
    )

Get-Job | Remove-Job -Force
foreach ($computer in $computers)
{
if (Test-Connection $computer -Count 1 -Quiet)
{
write-host "Executing command on :"$computer -ForegroundColor Green
Invoke-Command -ComputerName $computer -ScriptBlock{


$software = Start-Process "msiexec.exe" -ArgumentList "/X {$uninstallstring} /quiet /norestart" -Wait -PassThru; 
$software.ExitCode

} -AsJob
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

# uninstall-software -computers $computers -uninstallstring "7EC66A9F-0A49-4DC0-A9E8-460333EA8013"
# Kaspersky - 7EC66A9F-0A49-4DC0-A9E8-460333EA8013
# Kaspersky Agent - 2924BEDA-E0D7-4DAF-A224-50D2E0B12F5B

# 948B0156-E2D5-4507-A553-FCF05AA03496  - F-secure security premium
# A6A61BE1-7D2D-4B2A-8865-9CB38FF0E485 - F-Secure SWUP

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

