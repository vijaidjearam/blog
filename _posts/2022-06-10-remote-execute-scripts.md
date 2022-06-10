---
layout: post
date: 2022-06-10 16:40:10
title: Remote Execute Scripts Via Powershell
category: powershell
tags: powershell
---

Here is the script that executes powershell code on remote Pc and gets the status of the install or uninstall.

```

Get-Job | Remove-Job -Force
$computers = gc -Path C:\temp\temp.txt
foreach ($computer in $computers)
{
if (Test-Connection $computer -Count 1 -Quiet)
{
write-host "Executing command on :"$computer -ForegroundColor Green
Invoke-Command -ComputerName $computer -ScriptBlock{

$kaspersky = Start-Process "msiexec.exe" -ArgumentList "/X {7EC66A9F-0A49-4DC0-A9E8-460333EA8013} /quiet /norestart" -Wait -PassThru; 
$kaspersky.ExitCode
#$kasperskyagent=Start-Process "msiexec.exe" -ArgumentList "/X {2924BEDA-E0D7-4DAF-A224-50D2E0B12F5B} /quiet /norestart" -Wait -PassThru;
#write-host $kasperskyagent.ExitCode
#write-host $LASTEXITCODE
} -AsJob
}
else
{
Write-Host "$computer - Is offline" -BackgroundColor Red
}

}
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
      if ($result -eq 0)
      {$installstatus= "ok"}
      else
      {$installstatus= "Nok"}

      $resultobject = New-Object psobject
      #$results+=@{ComputerName=$job.Location;Result=$result};
      $resultobject | Add-Member -MemberType NoteProperty -Name "ComputerName" -Value $job.Location
      $resultobject | Add-Member -MemberType NoteProperty -Name "Result" -Value $result
      $resultobject | Add-Member -MemberType NoteProperty -Name "Install-Staus" -Value $installstatus
      $results +=$resultobject
}
$results | Out-GridView

```
