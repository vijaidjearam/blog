---
layout: post
date: 2024-01-26 15:17:36
title: Powershell Script to Backup Hyperv VM
category: powershell 
tags: powershell synology WORM
---

# This powershell Script backups the VM to Synology WORM shared Folder

```powershell
$ErrorActionPreference = "Stop"
# Set your Hyper-V virtual machine name
$VMName = "test-vm"

# Check if a folder name exists in the local drive
if (Test-Path -Path D:\TempVmBackup){

# Set the local export folder path
$LocalExportFolder = "D:\TempVmBackup\$VMName"
}
else{
# create a localfolder to export the VM
New-Item -ItemType Directory -Path D:\TempVmBackup | Out-Null
# Set the local export folder path
$LocalExportFolder = "D:\TempVmBackup"
}
# Set the shared folder path
$SharedFolderPath = "\\xxx.xxx.xxx.xxx\WORM-VM-Backup"

# Set the backup destination folder with date and timestamp
$foldername = "$VMName" + "_Backup_" + (Get-Date -Format "ddMMyyyy_HHmmss")
$BackupFolder = Join-Path $SharedFolderPath -ChildPath $foldername

# Step 1: Shutdown the Hyper-V virtual machine
Write-Host "Shutting down $VMName..."
Stop-VM -Name $VMName -Force

# Step 2: Export the virtual machine to a local directory
Write-Host "Exporting virtual machine to local directory..."
Export-VM -Name $VMName -Path $LocalExportFolder 

# Step 3: Create the backup folder
Write-Host "Creating backup folder..."
New-Item -ItemType Directory -Path $BackupFolder | Out-Null

# Step 4: Copy the exported files to the backup folder
Write-Host "Copying virtual machine files to backup folder..."
Copy-Item -Path $LocalExportFolder -Destination $BackupFolder -Recurse -Force

# Step 5: Delete the local export
Write-Host "Deleting local export..."
Remove-Item -Path $LocalExportFolder -Recurse -Force

# Step 6: Start the Hyper-V virtual machine
Write-Host "Starting $VMName..."
Start-VM -Name $VMName

Write-Host "Backup completed successfully."
```
