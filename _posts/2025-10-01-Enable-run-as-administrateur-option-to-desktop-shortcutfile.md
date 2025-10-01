---
layout: post
date: 2025-10-01 10:48:28
title: Enable Run as Administartor option to a shortcut in desktop
category: windows-shortcut
tags: powershell shortcut windows
---

Here is the powershell script to modify the Shortcut parameters:
```powershell
param(
    [Parameter(Mandatory=$true)]
    [string]$computerListPath,
    
    [Parameter(Mandatory=$true)]
    [string]$shortcutName,
    
    [Parameter(Mandatory=$false)]
    [string]$logPath
)

# Set default log path if not provided
if ([string]::IsNullOrWhiteSpace($logPath)) {
    $timestamp = Get-Date -Format 'yyyyMMdd_HHmmss'
    $logPath = "C:\temp\ShortcutModification_$timestamp.log"
    
    # Create temp directory if it doesn't exist
    if (!(Test-Path "C:\temp")) {
        New-Item -ItemType Directory -Path "C:\temp" -Force | Out-Null
    }
}

# Validate input file exists
if (!(Test-Path $computerListPath)) {
    Write-Host "ERROR: Computer list file not found: $computerListPath" -ForegroundColor Red
    exit 1
}

# Initialize log
"Shortcut Modification Log - $(Get-Date)" | Out-File $logPath
"Computer List: $computerListPath" | Out-File $logPath -Append
"Shortcut Name: $shortcutName" | Out-File $logPath -Append
"=" * 50 | Out-File $logPath -Append

# Read computer names
$computers = Get-Content $computerListPath

# Create results array
$results = @()

# Script block for remote execution
$scriptBlock = {
    param($shortcutName)
    
    $result = @{
        Computer = $env:COMPUTERNAME
        Status = "Unknown"
        Message = ""
    }
    
    $shortcutPath = "C:\Users\Default\Desktop\$shortcutName"
    
    try {
        if (Test-Path $shortcutPath) {
            $bytes = [System.IO.File]::ReadAllBytes($shortcutPath)
            
            # Check if already set
            if (($bytes[0x15] -band 0x20) -eq 0x20) {
                $result.Status = "AlreadySet"
                $result.Message = "Shortcut already configured to run as administrator"
            } else {
                # Set the flag
                $bytes[0x15] = $bytes[0x15] -bor 0x20
                [System.IO.File]::WriteAllBytes($shortcutPath, $bytes)
                $result.Status = "Success"
                $result.Message = "Successfully modified shortcut"
            }
        } else {
            $result.Status = "NotFound"
            $result.Message = "Shortcut not found at $shortcutPath"
        }
    } catch {
        $result.Status = "Error"
        $result.Message = $_.Exception.Message
    }
    
    return $result
}

# Process each computer
Write-Host "`nStarting shortcut modification process..." -ForegroundColor Cyan
Write-Host "Log file: $logPath" -ForegroundColor Gray

foreach ($computer in $computers) {
    if (![string]::IsNullOrWhiteSpace($computer)) {
        Write-Host "`nProcessing $computer..." -ForegroundColor Yellow
        
        $result = @{
            Computer = $computer
            Status = "Unknown"
            Message = ""
            Timestamp = Get-Date
        }
        
        try {
            if (Test-Connection -ComputerName $computer -Count 1 -Quiet) {
                $remoteResult = Invoke-Command -ComputerName $computer -ScriptBlock $scriptBlock -ArgumentList $shortcutName -ErrorAction Stop
                $result.Status = $remoteResult.Status
                $result.Message = $remoteResult.Message
            } else {
                $result.Status = "Unreachable"
                $result.Message = "Computer is not reachable"
            }
        } catch {
            $result.Status = "Error"
            $result.Message = $_.Exception.Message
        }
        
        # Display result
        switch ($result.Status) {
            "Success" { Write-Host "SUCCESS: $($result.Message)" -ForegroundColor Green }
            "AlreadySet" { Write-Host "INFO: $($result.Message)" -ForegroundColor Cyan }
            "NotFound" { Write-Host "WARNING: $($result.Message)" -ForegroundColor Yellow }
            default { Write-Host "ERROR: $($result.Message)" -ForegroundColor Red }
        }
        
        # Log result
        "$($result.Timestamp) - $($result.Computer) - $($result.Status) - $($result.Message)" | Out-File $logPath -Append
        
        $results += $result
    }
}

# Summary
Write-Host "`n" + "=" * 50 -ForegroundColor Cyan
Write-Host "SUMMARY:" -ForegroundColor Cyan
Write-Host "Total computers: $($results.Count)"
Write-Host "Successful: $(($results | Where-Object {$_.Status -eq 'Success'}).Count)" -ForegroundColor Green
Write-Host "Already configured: $(($results | Where-Object {$_.Status -eq 'AlreadySet'}).Count)" -ForegroundColor Cyan
Write-Host "Not found: $(($results | Where-Object {$_.Status -eq 'NotFound'}).Count)" -ForegroundColor Yellow
Write-Host "Errors: $(($results | Where-Object {$_.Status -eq 'Error' -or $_.Status -eq 'Unreachable'}).Count)" -ForegroundColor Red
Write-Host "`nLog saved to: $logPath" -ForegroundColor Gray

# Add summary to log
"`n" + "=" * 50 | Out-File $logPath -Append
"SUMMARY - $(Get-Date)" | Out-File $logPath -Append
"Total computers: $($results.Count)" | Out-File $logPath -Append
"Successful: $(($results | Where-Object {$_.Status -eq 'Success'}).Count)" | Out-File $logPath -Append
"Already configured: $(($results | Where-Object {$_.Status -eq 'AlreadySet'}).Count)" | Out-File $logPath -Append
"Not found: $(($results | Where-Object {$_.Status -eq 'NotFound'}).Count)" | Out-File $logPath -Append
"Errors: $(($results | Where-Object {$_.Status -eq 'Error' -or $_.Status -eq 'Unreachable'}).Count)" | Out-File $logPath -Append
```

## Usage Examples:

1. With all parameters:
```powershell
.\ModifyShortcut.ps1 -computerListPath "C:\Lists\computers.txt" -shortcutName "MyApp.lnk" -logPath "C:\Logs\shortcut_mod.log"
```
2.With default log path (will create log in C:\temp):
```powershell
.\ModifyShortcut.ps1 -computerListPath "C:\Lists\computers.txt" -shortcutName "MyApp.lnk"
```
3.Using positional parameters:
```powershell
.\ModifyShortcut.ps1 "C:\Lists\computers.txt" "MyApp.lnk"
```


Save the script as ModifyShortcut.ps1 and run it from an elevated PowerShell prompt.

## The script will:

- Accept the computer list file path and shortcut name as mandatory parameters
- Use C:\temp\ShortcutModification_[timestamp].log as the default log path if not specified
- Create the C:\temp directory if it doesn't exist
- Validate that the computer list file exists before proceeding
- Process all computers and provide detailed logging
