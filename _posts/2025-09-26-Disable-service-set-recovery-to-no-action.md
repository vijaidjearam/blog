---
layout: post
date: 2025-09-26 08:57:41
title: Disable a service and set the recovery to take no action.
category: windows-services
tags: powershell services windows
---

Here’s the Powershell script:
```powershell
param(
    [Parameter(Mandatory = $true)]
    [string]$ServiceName,

    [Parameter(Mandatory = $true)]
    [string]$ComputerListFile,

    [int]$Throttle = 10,

    [string]$OutFile = "ServiceStatusReport.csv"
)

# ========================
# FUNCTIONS
# ========================

function Disable-ServiceAndSetRecovery {
    param (
        [string]$ServiceName
    )
    try {
        Set-Service -Name $ServiceName -StartupType Disabled -ErrorAction Stop
        sc.exe failure $ServiceName reset= 0 actions= none/0/none/0/none/0 | Out-Null
        return $true
    }
    catch {
        return $false
    }
}

function Get-ServiceRecoveryStatus {
    param (
        [string]$ServiceName
    )

    $service  = Get-Service -Name $ServiceName -ErrorAction SilentlyContinue
    $recovery = sc.exe qfailure $ServiceName

    [PSCustomObject]@{
        ComputerName    = $env:COMPUTERNAME
        ServiceName     = $ServiceName
        Status          = $service.Status
        StartType       = (Get-WmiObject -Class Win32_Service -Filter "Name='$ServiceName'").StartMode
        RecoveryOptions = ($recovery | Select-String "FAILURE_ACTIONS" -Context 0,3).Context.PostContext -join "; "
    }
}

# ========================
# MAIN SCRIPT
# ========================

if (-not (Test-Path $ComputerListFile)) {
    Write-Error "❌ Computer list file '$ComputerListFile' not found."
    exit
}

$Computers  = Get-Content $ComputerListFile | Where-Object { $_ -and $_.Trim() -ne "" }
$Total      = $Computers.Count
$Processed  = 0
$AllResults = @()

if ($Total -eq 0) {
    Write-Error "❌ No computer names found in '$ComputerListFile'."
    exit
}

foreach ($Batch in ($Computers | ForEach-Object -Begin { $i=0 } -Process { 
    [PSCustomObject]@{ Index = [math]::Floor($i++ / $Throttle); Computer = $_ } 
} | Group-Object Index)) {
    
    $jobs = @()

    foreach ($Computer in $Batch.Group.Computer) {
        # Test connectivity with one ping
        if (Test-Connection -ComputerName $Computer -Count 1 -Quiet) {
            $jobs += Invoke-Command -ComputerName $Computer -AsJob -ScriptBlock {
                param($ServiceName)
                Disable-ServiceAndSetRecovery -ServiceName $ServiceName | Out-Null
                Get-ServiceRecoveryStatus -ServiceName $ServiceName
            } -ArgumentList $ServiceName
        }
        else {
            # Add "Unreachable" entry for skipped computer
            $AllResults += [PSCustomObject]@{
                ComputerName    = $Computer
                ServiceName     = $ServiceName
                Status          = "Unreachable"
                StartType       = "N/A"
                RecoveryOptions = "N/A"
            }
        }
    }

    if ($jobs.Count -gt 0) {
        Wait-Job -Job $jobs | Out-Null
        $results = Receive-Job -Job $jobs
        $AllResults += $results
        Remove-Job -Job $jobs
    }

    # Update progress bar
    $Processed += $Batch.Group.Count
    $Percent = [math]::Round(($Processed / $Total) * 100,2)
    Write-Progress -Activity "Processing Computers" -Status "$Processed of $Total complete" -PercentComplete $Percent
}

# Show results in table
$AllResults | Format-Table -AutoSize

# Export results
$AllResults | Export-Csv $OutFile -NoTypeInformation
Write-Host "✅ Report saved to $OutFile"
```
## Usage Examples

```powershell
# Run against computers in C:\Temp\computers.txt
.\ServiceControl.ps1 -ServiceName "Spooler" -ComputerListFile "C:\Temp\computers.txt"

# Run with custom throttle and output file
.\ServiceControl.ps1 -ServiceName "wuauserv" -ComputerListFile "C:\Temp\computers.txt" -Throttle 20 -OutFile "C:\Reports\UpdateServiceReport.csv"

```

- If a PC fails ping, it’s skipped — script continues smoothly.

- The report still contains that PC with "Unreachable" in the Status column.

✅ Example output if PC2 is offline:

```css
ComputerName ServiceName Status       StartType RecoveryOptions
------------ ----------- ------       --------- ---------------
PC1          Spooler     Stopped      Disabled  (none/0); (none/0); (none/0)
PC2          Spooler     Unreachable  N/A       N/A
PC3          Spooler     Stopped      Disabled  (none/0); (none/0); (none/0)

```
