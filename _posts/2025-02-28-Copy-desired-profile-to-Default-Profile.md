---
layout: post
date: 2025-02-28 15:51:12
title: Copy a desired profile to default profile
category: windows11
tags: defaultprofile windows11
---


How to Use the Script:

1️⃣ Log in using a temporary account (NOT the profile you want to copy).

2️⃣ Run PowerShell as Administrator.

3️⃣ Enter the source profile name when prompted.

4️⃣ The script will:

 - Back up the C:\Users\Default folder to c:\users\Default-timestamp.
 - Delete all existing contents from C:\Users\Default.
 - Copy only the necessary files and folders, \appdata\local\microsoft\ and \appdata\roaming\microsoft excluding OneDrive & WindowsApps.
 - Copy NTUSER.DAT to the root of the default profile

5️⃣ Success message appears when done.

```powershell
# Prompt user for source profile
$sourceProfile = Read-Host "Enter the source profile name (e.g., User1)"

# Get system paths
$sourceProfilePath = "$env:SystemDrive\Users\$sourceProfile"
$currentUser = $env:USERNAME
$defaultProfile = "$env:SystemDrive\Users\Default"
$timestamp = Get-Date -Format "yyyyMMdd_HHmmss"
$backupPath = "$env:SystemDrive\Users\Backup_Default_$timestamp"

# Check if the source profile is valid
if (!(Test-Path $sourceProfilePath)) {
    Write-Host "Error: Profile '$sourceProfile' not found! Exiting..." -ForegroundColor Red
    exit
}

# Check if the source profile is the currently logged-in user
if ($sourceProfile -eq $currentUser) {
    Write-Host "Error: You cannot copy from the currently logged-in user '$currentUser'!" -ForegroundColor Red
    Write-Host "Please log in with a temporary or different user account before running this script." -ForegroundColor Yellow
    exit
}

# Ensure the backup directory exists
if (!(Test-Path $backupPath)) {
    New-Item -Path $backupPath -ItemType Directory -Force
}

# Backup the Default Profile
Write-Host "Backing up Default Profile to $backupPath..." -ForegroundColor Yellow
Copy-Item -Path $defaultProfile -Destination $backupPath -Recurse -Force

# Remove all contents from the Default Profile folder after backup
Write-Host "Clearing contents of Default Profile folder..." -ForegroundColor Red
Get-ChildItem -Path $defaultProfile -Force | Remove-Item -Recurse -Force -ErrorAction SilentlyContinue

# Define source paths
$sourcePaths = @(
    "$sourceProfilePath\AppData\Local\Microsoft",
    "$sourceProfilePath\AppData\Roaming\Microsoft"
)

# Define exclusion list (Exact folder match using -match)
$excludedPaths = @(
    "\\OneDrive",  # Matches any path containing \OneDrive
    "\\WindowsApps"  # Matches any path containing \WindowsApps
)

# Copy files/folders while properly excluding specified directories
foreach ($source in $sourcePaths) {
    if (Test-Path $source) {
        $destination = $defaultProfile + $source.Substring($sourceProfilePath.Length)

        # Ensure the destination folder exists
        if (!(Test-Path $destination)) {
            New-Item -Path $destination -ItemType Directory -Force
        }

        Write-Host "Copying $source to $destination..." -ForegroundColor Green

        # Perform the copy while excluding OneDrive & WindowsApps
        Get-ChildItem -Path $source -Recurse -Force | ForEach-Object {
            $fullPath = $_.FullName

            # Check if the path contains any excluded folders
            if ($excludedPaths | Where-Object { $fullPath -match $_ }) {
                Write-Host "Skipping excluded path: $fullPath" -ForegroundColor Cyan
                return
            }

            # Determine target destination
            $relativePath = $fullPath.Substring($sourceProfilePath.Length).TrimStart("\")
            $target = Join-Path $defaultProfile $relativePath

            if ($_.PSIsContainer) {
                # Ensure directories exist
                if (!(Test-Path $target)) {
                    New-Item -Path $target -ItemType Directory -Force
                }
            } else {
                # Copy files
                Copy-Item -Path $fullPath -Destination $target -Force
            }
        }
    } else {
        Write-Host "Skipping: $source not found." -ForegroundColor DarkGray
    }
}

# Copy NTUSER.DAT as a file (not as a folder)
$ntUserSource = "$sourceProfilePath\NTUSER.DAT"
$ntUserDestination = "$defaultProfile\NTUSER.DAT"

if (Test-Path $ntUserSource) {
    Write-Host "Copying NTUSER.DAT..." -ForegroundColor Green
    Copy-Item -Path $ntUserSource -Destination $ntUserDestination -Force
} else {
    Write-Host "Skipping: NTUSER.DAT not found in source profile." -ForegroundColor DarkGray
}

Write-Host "Operation completed successfully!" -ForegroundColor Magenta

```
