---
layout: post
date: 2021-11-05 11:29:00
title: Upgrade Zalman VE-300 firmware to an iodd 2531 firmware
category: materiel
tags: uwf powershell
---
# The following script helps to make changes on a PC with UWF Active.
```
Remove-Job * -Force
$filter   = Read-Host 'Enter the Filter for PC::'
$comp = Get-ADComputer -SearchBase " " -Filter 'Name -like $filter'|Select-Object -ExpandProperty Name | Out-GridView -PassThru


$ScriptBlock = {
param($computer)

Invoke-Command -ComputerName $computer -ScriptBlock{
#Before Restart
uwfmgr volume unprotect c:
uwfmgr filter disable
}
restart-computer -Wait -Force -ComputerName $computer



Invoke-Command -ComputerName $computer -ScriptBlock{
#After restart
#Set-ScheduledTask -TaskName scheduled-shutdown -User $env:COMPUTERNAME\user
install-module DellBIOSProvider -Force
#$env:Path += ";C:\Program Files (x86)\Dell\Command Configure\X86_64\"
uwfmgr volume protect c:
uwfmgr filter enable
Restart-Computer -Force
}


}


foreach($computer in $comp){

Start-Job $ScriptBlock -ArgumentList $computer -Name $computer

}

While (Get-Job -State "Running") {
    cls
    Get-Job
    Start-Sleep 1 
}
cls
Get-Job
write-host "Jobs completed, getting output"

foreach($job in Get-Job){
    Receive-Job -Name $job.Name | out-file c:\temp\jobs.log -append    
}

Remove-Job * -Force
notepad c:\temp\jobs.log
```
