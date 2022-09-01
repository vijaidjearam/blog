---
layout: post
date: 2022-09-01 08:59:45
title: Powershell script to check if the software is installed on remote PC 
category: Powershell
tags: powershell
---

```
function check-software-install()
{
  Param
    (
        [Parameter(Mandatory = $true)] [Array] $computers,
        [Parameter(Mandatory = $true)] [string] $softwarename
    )

Get-Job | Remove-Job -Force
foreach ($computer in $computers)
{
if (Test-Connection $computer -Count 1 -Quiet)
{
write-host "Executing command on :"$computer -ForegroundColor Green
Invoke-Command -ComputerName $computer -ScriptBlock{

param ($computer,$softwarename)
Get-WmiObject -ComputerName $computer -Class Win32_Product | sort-object Name | Select-Object PSComputerName,Name,Version,InstallDate | Where-Object { $_.Name -like $softwarename}

} -AsJob -ArgumentList $computer,$softwarename
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

      $result = Receive-Job $job -Keep
      $resultobject = New-Object psobject
      $resultobject | Add-Member -MemberType NoteProperty -Name "ComputerName" -Value $job.Location
      $resultobject | Add-Member -MemberType NoteProperty -Name "Softwarename" -Value $result.Name
      $resultobject | Add-Member -MemberType NoteProperty -Name "Version" -Value $result.Version
      $resultobject | Add-Member -MemberType NoteProperty -Name "InstallDate" -Value $result.InstallDate
      $results +=$resultobject
}
$results | Out-GridView
}

# example of usage

# check-software-install -computers $computer -softwarename "Local Administrator Password Solution*"
```
