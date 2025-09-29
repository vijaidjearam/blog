---
layout: post
date: 2025-09-26 08:57:41
title: Disable a service and set the recovery to take no action.
category: windows-services
tags: powershell services windows
---

Here’s the Powershell script:
```powershell
<#
.SYNOPSIS
    Disables a Windows service and resets its recovery options across multiple computers.

.DESCRIPTION
    This script connects to multiple computers specified in a text file, disables the specified service,
    resets its recovery options to prevent automatic restart, and generates a detailed status report.
    The script uses parallel processing for improved performance when dealing with multiple computers.

.PARAMETER ServiceName
    The name of the Windows service to disable. This is the service name (not display name).
    Example: "Spooler" for Print Spooler service.

.PARAMETER ComputerListFile
    Path to a text file containing computer names, one per line.
    The file should contain valid computer names or IP addresses accessible on the network.

.PARAMETER ThrottleLimit
    Maximum number of computers to process simultaneously. Default is 10.
    Higher values may improve speed but consume more resources.
    Valid range: 1-50

.PARAMETER OutputFile
    Path and filename for the CSV report. Default creates a timestamped file.
    Example: "ServiceStatusReport_20240115_143022.csv"

.PARAMETER ConnectionTimeout
    Timeout in seconds for testing computer connectivity. Default is 2 seconds.
    Increase this value for computers on slow networks.

.EXAMPLE

     .\service-disable-set-ecovery-off-clause-ver.ps1 -ServiceName SiemensTiaAdminV3 -ComputerListFile C:\temp\test-service.txt -ThrottleLimit 10 -OutputFile c:\temp\service-tiaadmin.txt -ConnectionTimeout 2

     Disables the SiemensTiaAdminV3 service and sets the recovery option to Take no Action

.EXAMPLE
    .\Disable-ServiceOnComputers.ps1 -ServiceName "Spooler" -ComputerListFile "C:\computers.txt"
    
    Disables the Print Spooler service on all computers listed in computers.txt using default settings.

.EXAMPLE
    .\Disable-ServiceOnComputers.ps1 -ServiceName "Spooler" -ComputerListFile "C:\computers.txt" -ThrottleLimit 20 -OutputFile "C:\Reports\SpoolerStatus.csv"
    
    Disables the Print Spooler service with custom throttle limit and output file location.

.EXAMPLE
    .\Disable-ServiceOnComputers.ps1 -ServiceName "WSearch" -ComputerListFile "C:\servers.txt" -ConnectionTimeout 5
    
    Disables Windows Search service with extended connection timeout for remote servers.

.NOTES
    Author: System Administrator
    Version: 2.0
    Requires: PowerShell 5.1 or higher, Administrative privileges on target computers
    
    The script requires:
    - Network connectivity to target computers
    - Administrative rights on remote computers
    - Windows Remote Management (WinRM) enabled on target computers
    - Appropriate firewall rules for remote management

.LINK
    https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/set-service

.OUTPUTS
    CSV file containing:
    - ComputerName: Name of the target computer
    - ServiceName: Name of the service
    - Status: Current service status (Running/Stopped/Error/Unreachable)
    - StartType: Service startup type (Automatic/Manual/Disabled)
    - RecoveryOptions: Service recovery actions in format "(action/delay); (action/delay); (action/delay)"
#>

#Requires -Version 5.1
[CmdletBinding()]
param(
    [Parameter(Mandatory = $true, Position = 0, HelpMessage = "Enter the service name (not display name)")]
    [ValidateNotNullOrEmpty()]
    [string]$ServiceName,

    [Parameter(Mandatory = $true, Position = 1, HelpMessage = "Path to text file containing computer names")]
    [ValidateScript({
        if (Test-Path $_ -PathType Leaf) { $true }
        else { throw "Computer list file '$_' not found." }
    })]
    [string]$ComputerListFile,

    [Parameter(HelpMessage = "Maximum number of concurrent connections (1-50)")]
    [ValidateRange(1, 50)]
    [int]$ThrottleLimit = 10,

    [Parameter(HelpMessage = "Path for the output CSV file")]
    [ValidateNotNullOrEmpty()]
    [string]$OutputFile = "ServiceStatusReport_$(Get-Date -Format 'yyyyMMdd_HHmmss').csv",

    [Parameter(HelpMessage = "Connection timeout in seconds")]
    [ValidateRange(1, 30)]
    [int]$ConnectionTimeout = 2
)

# ========================
# FUNCTIONS
# ========================

function Parse-RecoveryActions {
    <#
    .SYNOPSIS
        Parses sc.exe qfailure output to extract recovery actions in readable format
    #>
    param (
        [string[]]$ScOutput
    )
    
    try {
        # Find the line containing failure actions
        $actionsLine = $ScOutput | Where-Object { $_ -match "FAILURE_ACTIONS\s*:" }
        if (-not $actionsLine) {
            return "(none/0); (none/0); (none/0)"
        }
        
        # Extract the actions from subsequent lines
        $startIndex = [array]::IndexOf($ScOutput, $actionsLine)
        $actions = @()
        
        # Parse up to 3 recovery actions
        for ($i = 1; $i -le 3; $i++) {
            $line = $ScOutput[$startIndex + $i]
            if ($line -match "(\w+)\s*--\s*delay:\s*(\d+)\s*milliseconds") {
                $action = $matches[1].ToLower()
                $delay = [int]$matches[2]
                $actions += "($action/$delay)"
            }
            elseif ($line -match "(\w+)") {
                $action = $matches[1].ToLower()
                $actions += "($action/0)"
            }
        }
        
        # If we found actions, join them; otherwise return default
        if ($actions.Count -gt 0) {
            return $actions -join "; "
        }
        else {
            return "(none/0); (none/0); (none/0)"
        }
    }
    catch {
        return "(error parsing)"
    }
}

function Disable-ServiceWithRecovery {
    <#
    .SYNOPSIS
        Disables a service and resets its recovery options
    #>
    [CmdletBinding()]
    param (
        [Parameter(Mandatory)]
        [string]$ServiceName,
        
        [Parameter()]
        [string]$ComputerName = $env:COMPUTERNAME
    )

    try {
        # Check if service exists first
        $service = Get-Service -Name $ServiceName -ErrorAction Stop
        
        # Disable the service
        Set-Service -Name $ServiceName -StartupType Disabled -ErrorAction Stop
        
        # Reset recovery options
        $scResult = & sc.exe "\\$ComputerName" failure $ServiceName reset=0 actions=none/0/none/0/none/0 2>&1
        
        if ($LASTEXITCODE -ne 0) {
            throw "Failed to set recovery options: $scResult"
        }
        
        return @{
            Success = $true
            Message = "Service disabled and recovery options reset"
        }
    }
    catch {
        return @{
            Success = $false
            Message = $_.Exception.Message
        }
    }
}

function Get-ServiceDetails {
    <#
    .SYNOPSIS
        Retrieves detailed service information including recovery options
    #>
    [CmdletBinding()]
    param (
        [Parameter(Mandatory)]
        [string]$ServiceName,
        
        [Parameter()]
        [string]$ComputerName = $env:COMPUTERNAME
    )

    try {
        $service = Get-Service -Name $ServiceName -ErrorAction Stop
        $wmiService = Get-CimInstance -ClassName Win32_Service -Filter "Name='$ServiceName'" -ErrorAction Stop
        
        # Get recovery options
        $recoveryOutput = & sc.exe "\\$ComputerName" qfailure $ServiceName 2>&1
        $recoveryActions = if ($LASTEXITCODE -eq 0) {
            Parse-RecoveryActions -ScOutput $recoveryOutput
        } else {
            "(unable to retrieve)"
        }
        
        return [PSCustomObject]@{
            ComputerName    = $ComputerName
            ServiceName     = $ServiceName
            Status          = $service.Status
            StartType       = $wmiService.StartMode
            RecoveryOptions = $recoveryActions
            ErrorMessage    = $null
        }
    }
    catch {
        return [PSCustomObject]@{
            ComputerName    = $ComputerName
            ServiceName     = $ServiceName
            Status          = "Error"
            StartType       = "N/A"
            RecoveryOptions = "N/A"
            ErrorMessage    = $_.Exception.Message
        }
    }
}

# ========================
# MAIN SCRIPT
# ========================

# Initialize variables
$startTime = Get-Date
Write-Host "`n========================================" -ForegroundColor Cyan
Write-Host "Service Management Script" -ForegroundColor Cyan
Write-Host "========================================" -ForegroundColor Cyan
Write-Host "Service: $ServiceName" -ForegroundColor Yellow
Write-Host "Output: $OutputFile" -ForegroundColor Yellow
Write-Host "Throttle Limit: $ThrottleLimit" -ForegroundColor Yellow
Write-Host "========================================`n" -ForegroundColor Cyan

# Load and validate computer list
try {
    $computers = Get-Content -Path $ComputerListFile | 
        Where-Object { $_ -and $_.Trim() } | 
        ForEach-Object { $_.Trim() } |
        Select-Object -Unique
    
    if ($computers.Count -eq 0) {
        throw "No valid computer names found in '$ComputerListFile'"
    }
    
    Write-Host "Found $($computers.Count) unique computer(s) to process" -ForegroundColor Green
}
catch {
    Write-Error "Failed to load computer list: $_"
    exit 1
}

# Process computers in parallel batches
$results = [System.Collections.Concurrent.ConcurrentBag[PSCustomObject]]::new()
$processedCount = 0

# Create runspace pool for better performance
$runspacePool = [runspacefactory]::CreateRunspacePool(1, $ThrottleLimit)
$runspacePool.Open()

$jobs = foreach ($computer in $computers) {
    $powershell = [powershell]::Create().AddScript({
        param($Computer, $ServiceName, $ConnectionTimeout)
        
        # Test connectivity
        if (-not (Test-Connection -ComputerName $Computer -Count 1 -Quiet -TimeoutSeconds $ConnectionTimeout)) {
            return [PSCustomObject]@{
                ComputerName    = $Computer
                ServiceName     = $ServiceName
                Status          = "Unreachable"
                StartType       = "N/A"
                RecoveryOptions = "N/A"
                ErrorMessage    = "Computer is not reachable"
            }
        }
        
        # Execute remote command
        try {
            $result = Invoke-Command -ComputerName $Computer -ErrorAction Stop -ScriptBlock {
                param($ServiceName)
                
                # Define functions in remote session
                function Parse-RecoveryActions {
                    param ([string[]]$ScOutput)
                    
                    try {
                        $actionsLine = $ScOutput | Where-Object { $_ -match "FAILURE_ACTIONS\s*:" }
                        if (-not $actionsLine) {
                            return "(none/0); (none/0); (none/0)"
                        }
                        
                        $startIndex = [array]::IndexOf($ScOutput, $actionsLine)
                        $actions = @()
                        
                        for ($i = 1; $i -le 3; $i++) {
                            $line = $ScOutput[$startIndex + $i]
                            if ($line -match "(\w+)\s*--\s*delay:\s*(\d+)\s*milliseconds") {
                                $action = $matches[1].ToLower()
                                $delay = [int]$matches[2]
                                $actions += "($action/$delay)"
                            }
                            elseif ($line -match "(\w+)") {
                                $action = $matches[1].ToLower()
                                $actions += "($action/0)"
                            }
                        }
                        
                        if ($actions.Count -gt 0) {
                            return $actions -join "; "
                        }
                        else {
                            return "(none/0); (none/0); (none/0)"
                        }
                    }
                    catch {
                        return "(error parsing)"
                    }
                }
                
                function Disable-ServiceWithRecovery {
                    param ([string]$ServiceName)
                    try {
                        Set-Service -Name $ServiceName -StartupType Disabled -ErrorAction Stop
                        & sc.exe failure $ServiceName reset=0 actions=none/0/none/0/none/0 2>&1 | Out-Null
                        return @{ Success = $true; Message = "Success" }
                    }
                    catch {
                        return @{ Success = $false; Message = $_.Exception.Message }
                    }
                }
                
                function Get-ServiceDetails {
                    param ([string]$ServiceName)
                    try {
                        $service = Get-Service -Name $ServiceName -ErrorAction Stop
                        $wmiService = Get-CimInstance Win32_Service -Filter "Name='$ServiceName'" -ErrorAction Stop
                        $recoveryOutput = & sc.exe qfailure $ServiceName 2>&1
                        $recoveryActions = if ($LASTEXITCODE -eq 0) {
                            Parse-RecoveryActions -ScOutput $recoveryOutput
                        } else {
                            "(unable to retrieve)"
                        }
                        
                        return [PSCustomObject]@{
                            ComputerName    = $env:COMPUTERNAME
                            ServiceName     = $ServiceName
                            Status          = $service.Status
                            StartType       = $wmiService.StartMode
                            RecoveryOptions = $recoveryActions
                            ErrorMessage    = $null
                        }
                    }
                    catch {
                        return [PSCustomObject]@{
                            ComputerName    = $env:COMPUTERNAME
                            ServiceName     = $ServiceName
                            Status          = "Error"
                            StartType       = "N/A"
                            RecoveryOptions = "N/A"
                            ErrorMessage    = $_.Exception.Message
                        }
                    }
                }
                
                # Disable service and get status
                $disableResult = Disable-ServiceWithRecovery -ServiceName $ServiceName
                $statusResult = Get-ServiceDetails -ServiceName $ServiceName
                
                if (-not $disableResult.Success) {
                    $statusResult.ErrorMessage = $disableResult.Message
                }
                
                return $statusResult
            } -ArgumentList $ServiceName
            
            return $result
        }
        catch {
            return [PSCustomObject]@{
                ComputerName    = $Computer
                ServiceName     = $ServiceName
                Status          = "Error"
                StartType       = "N/A"
                RecoveryOptions = "N/A"
                ErrorMessage    = $_.Exception.Message
            }
        }
    }).AddArgument($computer).AddArgument($ServiceName).AddArgument($ConnectionTimeout)
    
    $powershell.RunspacePool = $runspacePool
    
    [PSCustomObject]@{
        PowerShell = $powershell
        Handle     = $powershell.BeginInvoke()
    }
}

# Wait for jobs and collect results
$totalComputers = $computers.Count
foreach ($job in $jobs) {
    $result = $job.PowerShell.EndInvoke($job.Handle)
    if ($result) {
        $results.Add($result)
    }
    $job.PowerShell.Dispose()
    
    $processedCount++
    $percentComplete = [math]::Round(($processedCount / $totalComputers) * 100, 2)
    Write-Progress -Activity "Processing Computers" -Status "$processedCount of $totalComputers complete" -PercentComplete $percentComplete
}

$runspacePool.Close()
$runspacePool.Dispose()

# Convert results to array and sort
$finalResults = $results.ToArray() | Sort-Object ComputerName

# Display summary
Write-Host "`nProcessing complete!" -ForegroundColor Green
$summary = $finalResults | Group-Object Status | Select-Object Name, Count
Write-Host "`nSummary:" -ForegroundColor Cyan
$summary | Format-Table -AutoSize

# Display detailed results
Write-Host "`nDetailed Results:" -ForegroundColor Cyan
$finalResults | Format-Table -AutoSize

# Export to CSV
try {
    $finalResults | Export-Csv -Path $OutputFile -NoTypeInformation -Force
    Write-Host "`n✅ Report saved to: $OutputFile" -ForegroundColor Green
    
    # Calculate execution time
    $executionTime = (Get-Date) - $startTime
    Write-Host "Total execution time: $($executionTime.ToString('mm\:ss'))" -ForegroundColor Yellow
}
catch {
    Write-Error "Failed to export results: $_"
}

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
