---
layout: post
date: 2026-01-16 14:26:00
title: Provisioning New HP Laptop after clonage
category: HP 
tags: hp bios provisioning powershell
---
# Provisioning New HP Laptop after clonage
 - This Script renames the Pc Name
 - Feeds the Asset Tag to Bios
 - Updates the BIOS


```powershell
#Requires -RunAsAdministrator
<#
.SYNOPSIS
    HP BIOS Update, PC Rename, and Asset Tag Configuration Script
.DESCRIPTION
    Prompts for PC name and Asset Tag, configures settings, and launches BIOS update silently
.NOTES
    Requires HP Client Management Script Library (HPCMSL)
    Run as Administrator
    Place the 'bin' folder from extracted Softpaq in the same location as this script
#>

# ========================== CONFIGURATION ==========================
$ScriptDirectory = $PSScriptRoot
$BiosExe = Join-Path -Path $ScriptDirectory -ChildPath "bin\HpFirmwareUpdRec.exe"

# ========================== MAIN SCRIPT ==========================

Clear-Host
Write-Host "==========================================" -ForegroundColor Cyan
Write-Host "   HP BIOS & System Configuration Tool   " -ForegroundColor Cyan
Write-Host "==========================================" -ForegroundColor Cyan
Write-Host ""

# Check if BIOS file exists
if (-not (Test-Path -Path $BiosExe)) {
    Write-Host "ERROR: HpFirmwareUpdRec.exe not found!" -ForegroundColor Red
    Write-Host "Expected path: $BiosExe" -ForegroundColor Yellow
    Write-Host ""
    Write-Host "Make sure the 'bin' folder is in: $ScriptDirectory" -ForegroundColor Yellow
    Read-Host "Press Enter to exit"
    exit 1
}

Write-Host "BIOS Update file found: $BiosExe" -ForegroundColor Green
Write-Host ""

# Check if HP CMSL module is available
if (-not (Get-Module -ListAvailable -Name "HPCMSL")) {
    Write-Host "WARNING: HP CMSL module not found." -ForegroundColor Yellow
    $InstallModule = Read-Host "Do you want to install it now? (Y/N)"
    
    if ($InstallModule -match '^[Yy]$') {
        Write-Host "Installing HP CMSL module..." -ForegroundColor Yellow
        Install-Module -Name HPCMSL -Force -AcceptLicense
        Import-Module HPCMSL
        Write-Host "HP CMSL module installed successfully." -ForegroundColor Green
    }
    else {
        Write-Host "Cannot proceed without HP CMSL module." -ForegroundColor Red
        Read-Host "Press Enter to exit"
        exit 1
    }
}

# Display current info
Write-Host "Current PC Name: $env:COMPUTERNAME" -ForegroundColor Gray
Write-Host ""

# ========================== USER INPUT ==========================

# Get new PC Name
do {
    $NewPCName = Read-Host "Enter the NEW PC Name"
    if ([string]::IsNullOrWhiteSpace($NewPCName)) {
        Write-Host "PC Name cannot be empty!" -ForegroundColor Red
    }
    elseif ($NewPCName.Length -gt 15) {
        Write-Host "PC Name cannot exceed 15 characters!" -ForegroundColor Red
        $NewPCName = $null
    }
} while ([string]::IsNullOrWhiteSpace($NewPCName))

# Get Asset Tracking Number
do {
    $AssetTag = Read-Host "Enter the Asset Tracking Number"
    if ([string]::IsNullOrWhiteSpace($AssetTag)) {
        Write-Host "Asset Tracking Number cannot be empty!" -ForegroundColor Red
    }
} while ([string]::IsNullOrWhiteSpace($AssetTag))

# ========================== CONFIRMATION ==========================

Write-Host ""
Write-Host "==========================================" -ForegroundColor Yellow
Write-Host "  New PC Name        : $NewPCName" -ForegroundColor White
Write-Host "  Asset Tracking No. : $AssetTag" -ForegroundColor White
Write-Host "==========================================" -ForegroundColor Yellow
Write-Host ""

$Confirm = Read-Host "Proceed with these settings? (Y/N)"
if ($Confirm -notmatch '^[Yy]$') {
    Write-Host "Operation cancelled." -ForegroundColor Yellow
    exit 0
}

Write-Host ""

# ========================== SET ASSET TRACKING NUMBER ==========================

Write-Host "[1/3] Setting Asset Tracking Number..." -ForegroundColor Yellow

try {
    Set-HPBIOSSettingValue -Name "Asset Tracking Number" -Value $AssetTag
    Write-Host "      SUCCESS: Asset Tracking Number set to '$AssetTag'" -ForegroundColor Green
}
catch {
    Write-Host "      ERROR: Failed to set Asset Tracking Number" -ForegroundColor Red
    Write-Host "      $($_.Exception.Message)" -ForegroundColor Red
}

# ========================== RENAME COMPUTER ==========================

Write-Host "[2/3] Renaming computer to '$NewPCName'..." -ForegroundColor Yellow

try {
    Rename-Computer -NewName $NewPCName -Force -ErrorAction Stop
    Write-Host "      SUCCESS: Computer will be renamed after restart" -ForegroundColor Green
}
catch {
    Write-Host "      ERROR: Failed to rename computer" -ForegroundColor Red
    Write-Host "      $($_.Exception.Message)" -ForegroundColor Red
}

# ========================== LAUNCH BIOS UPDATE (SILENT) ==========================

Write-Host "[3/3] Running BIOS Update (Silent Mode)..." -ForegroundColor Yellow
Write-Host "      Please wait, this may take a few minutes..." -ForegroundColor Gray
Write-Host "      DO NOT turn off the computer!" -ForegroundColor Red

try {
    # Silent install: -r (reboot) -b (bitlocker suspend) -s (silent)
    $Process = Start-Process -FilePath $BiosExe -ArgumentList "-r -b -s" -Wait -PassThru
    
    switch ($Process.ExitCode) {
        0 { Write-Host "      SUCCESS: BIOS Update completed" -ForegroundColor Green }
        256 { Write-Host "      INFO: BIOS is already up to date" -ForegroundColor Cyan }
        273 { Write-Host "      SUCCESS: BIOS Update staged for reboot" -ForegroundColor Green }
        282 { Write-Host "      INFO: BIOS downgrade blocked by policy" -ForegroundColor Yellow }
        3010 { Write-Host "      SUCCESS: BIOS Update completed (restart required)" -ForegroundColor Green }
        default { Write-Host "      Exit code: $($Process.ExitCode)" -ForegroundColor Yellow }
    }
}
catch {
    Write-Host "      ERROR: Failed to run BIOS update" -ForegroundColor Red
    Write-Host "      $($_.Exception.Message)" -ForegroundColor Red
}

# ========================== COMPLETION ==========================

Write-Host ""
Write-Host "==========================================" -ForegroundColor Cyan
Write-Host "            SCRIPT COMPLETED             " -ForegroundColor Cyan
Write-Host "==========================================" -ForegroundColor Cyan
Write-Host ""
Write-Host "RESTART REQUIRED to apply all changes!" -ForegroundColor Yellow
Write-Host ""

$Restart = Read-Host "Restart now? (Y/N)"
if ($Restart -match '^[Yy]$') {
    Restart-Computer -Force
}
```

## Folder Structure

Your folder should look like this:
Extract the contents bios.exe (in our case ex:  SP169859.exe) and rename it as bin and place it by the side of the powershell script.

```
📁 YourFolder
├── 📄 Setup.ps1 (this script)
└── 📁 bin
    ├── 📄 HpFirmwareUpdRec.exe
    └── 📄 (other files from Softpaq)
```

## Silent Install Switches Explained

| Switch | Description |
|--------|-------------|
| `-s` | Silent mode (no user prompts) |
| `-b` | Suspend BitLocker automatically |
| `-r` | Reboot automatically after update |

## Other Useful Switches

| Switch | Description |
|--------|-------------|
| `-f` | Force update even if same version |
| `-p` | Password (if BIOS is password protected) |

## If BIOS Has Password

```powershell
$Process = Start-Process -FilePath $BiosExe -ArgumentList "-r -b -s -p YourPassword" -Wait -PassThru
```


