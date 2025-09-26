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
    param ([string]$ServiceName)

    try {
        Set-Service -Name $ServiceName -StartupType Disabled -ErrorAction Stop
        sc.exe failure $ServiceName reset=0 actions=none/0/none/0/none/0 | Out-Null
        return $true
    }
    catch {
        return $false
    }
}

function Get-ServiceRecoveryStatus {
    param ([string]$ServiceName)

    $service  = Get-Service -Name $ServiceName -ErrorAction SilentlyContinue
    $recovery = sc.exe qfailure $ServiceName

    [PSCustomObject]@{
        ComputerName    = $env:COMPUTERNAME
        ServiceName     = $ServiceName
        Status          = $service.Status
        StartType       = (Get-CimInstance Win32_Service -Filter "Name='$ServiceName'").StartMode
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

$Computers  = Get-Content $ComputerListFile | Where-Object { $_.Trim() }
$Total      = $Computers.Count
$Processed  = 0
$AllResults = @()

if ($Total -eq 0) {
    Write-Error "❌ No computer names found in '$ComputerListFile'."
    exit
}

# Split into batches
$Batches = [System.Collections.Generic.List[object]]::new()
for ($i=0; $i -lt $Total; $i += $Throttle) {
    $Batches.Add($Computers[$i..([math]::Min($i+$Throttle-1, $Total-1))])
}

foreach ($Batch in $Batches) {
    $jobs = @()

    foreach ($Computer in $Batch) {
        if (Test-Connection -ComputerName $Computer -Count 1 -Quiet) {
            $jobs += Invoke-Command -ComputerName $Computer -AsJob -ScriptBlock {
                param($ServiceName)

                function Disable-ServiceAndSetRecovery {
                    param([string]$ServiceName)
                    try {
                        Set-Service -Name $ServiceName -StartupType Disabled -ErrorAction Stop
                        sc.exe failure $ServiceName reset=0 actions=none/0/none/0/none/0 | Out-Null
                        return $true
                    }
                    catch { return $false }
                }

                function Get-ServiceRecoveryStatus {
                    param([string]$ServiceName)
                    $service  = Get-Service -Name $ServiceName -ErrorAction SilentlyContinue
                    $recovery = sc.exe qfailure $ServiceName
                    [PSCustomObject]@{
                        ComputerName    = $env:COMPUTERNAME
                        ServiceName     = $ServiceName
                        Status          = $service.Status
                        StartType       = (Get-CimInstance Win32_Service -Filter "Name='$ServiceName'").StartMode
                        RecoveryOptions = ($recovery | Select-String "FAILURE_ACTIONS" -Context 0,3).Context.PostContext -join "; "
                    }
                }

                if (Disable-ServiceAndSetRecovery -ServiceName $ServiceName) {
                    Get-ServiceRecoveryStatus -ServiceName $ServiceName
                } else {
                    [PSCustomObject]@{
                        ComputerName    = $env:COMPUTERNAME
                        ServiceName     = $ServiceName
                        Status          = "Failed"
                        StartType       = "N/A"
                        RecoveryOptions = "N/A"
                    }
                }
            } -ArgumentList $ServiceName
        }
        else {
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
        $AllResults += Receive-Job -Job $jobs
        Remove-Job -Job $jobs
    }

    $Processed += $Batch.Count
    $Percent = [math]::Round(($Processed / $Total) * 100,2)
    Write-Progress -Activity "Processing Computers" -Status "$Processed of $Total complete" -PercentComplete $Percent
}

# Show results
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

⚡ Parallelized Script (PowerShell 7+)

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
    param ([string]$ServiceName)

    try {
        Set-Service -Name $ServiceName -StartupType Disabled -ErrorAction Stop
        sc.exe failure $ServiceName reset=0 actions=none/0/none/0/none/0 | Out-Null
        return $true
    }
    catch {
        return $false
    }
}

function Get-ServiceRecoveryStatus {
    param ([string]$ServiceName)

    $service  = Get-Service -Name $ServiceName -ErrorAction SilentlyContinue
    $recovery = sc.exe qfailure $ServiceName

    [PSCustomObject]@{
        ComputerName    = $env:COMPUTERNAME
        ServiceName     = $ServiceName
        Status          = $service.Status
        StartType       = (Get-CimInstance Win32_Service -Filter "Name='$ServiceName'").StartMode
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

$Computers = Get-Content $ComputerListFile | Where-Object { $_.Trim() }
if (-not $Computers) {
    Write-Error "❌ No computer names found in '$ComputerListFile'."
    exit
}

$Total = $Computers.Count
$Processed = 0
$Sync = [System.Collections.Concurrent.ConcurrentBag[object]]::new()

$Computers | ForEach-Object -Parallel {
    param($ServiceName, $Total, $Sync)

    $Computer = $_
    if (-not (Test-Connection -ComputerName $Computer -Count 1 -Quiet)) {
        $Sync.Add([PSCustomObject]@{
            ComputerName    = $Computer
            ServiceName     = $ServiceName
            Status          = "Unreachable"
            StartType       = "N/A"
            RecoveryOptions = "N/A"
        })
        return
    }

    try {
        function Disable-ServiceAndSetRecovery {
            param([string]$ServiceName)
            try {
                Set-Service -Name $ServiceName -StartupType Disabled -ErrorAction Stop
                sc.exe failure $ServiceName reset=0 actions=none/0/none/0/none/0 | Out-Null
                return $true
            }
            catch { return $false }
        }

        function Get-ServiceRecoveryStatus {
            param([string]$ServiceName)
            $service  = Get-Service -Name $ServiceName -ErrorAction SilentlyContinue
            $recovery = sc.exe qfailure $ServiceName
            [PSCustomObject]@{
                ComputerName    = $env:COMPUTERNAME
                ServiceName     = $ServiceName
                Status          = $service.Status
                StartType       = (Get-CimInstance Win32_Service -Filter "Name='$ServiceName'").StartMode
                RecoveryOptions = ($recovery | Select-String "FAILURE_ACTIONS" -Context 0,3).Context.PostContext -join "; "
            }
        }

        if (Disable-ServiceAndSetRecovery -ServiceName $ServiceName) {
            $result = Get-ServiceRecoveryStatus -ServiceName $ServiceName
        } else {
            $result = [PSCustomObject]@{
                ComputerName    = $Computer
                ServiceName     = $ServiceName
                Status          = "Failed"
                StartType       = "N/A"
                RecoveryOptions = "N/A"
            }
        }

        $Sync.Add($result)
    }
    catch {
        $Sync.Add([PSCustomObject]@{
            ComputerName    = $Computer
            ServiceName     = $ServiceName
            Status          = "Error: $_"
            StartType       = "N/A"
            RecoveryOptions = "N/A"
        })
    }

    # Update progress (approximate since parallel)
    [System.Threading.Interlocked]::Increment([ref]$using:Processed) | Out-Null
    $Percent = [math]::Round(($using:Processed / $Total) * 100, 2)
    Write-Progress -Activity "Processing Computers" -Status "$using:Processed of $Total complete" -PercentComplete $Percent

} -ArgumentList $ServiceName, $Total, $Sync -ThrottleLimit $Throttle

# ========================
# OUTPUT
# ========================

$Results = $Sync.ToArray() | Sort-Object ComputerName
$Results | Format-Table -AutoSize
$Results | Export-Csv $OutFile -NoTypeInformation
Write-Host "✅ Report saved to $OutFile"
```
## 🚀 Key Advantages

1. No manual job management — ForEach-Object -Parallel handles parallelism.

2. Throttle control built-in via -ThrottleLimit.

3. Thread-safe collection (ConcurrentBag) to aggregate results safely from parallel threads.

4. Cleaner progress tracking with Interlocked.Increment.

5. Better error reporting (explicit "Failed" vs "Unreachable" vs "Error: ...")
