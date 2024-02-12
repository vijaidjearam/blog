---
layout: post
date: 2024-01-26 15:17:36
title: Powershell Script to Backup Hyperv VM
category: Backup 
tags: backup synology WORM Hyperv windows
---

# This powershell Script backups the VM to Synology WORM shared Folder

```powershell
$ErrorActionPreference = "Stop"
# Set your Hyper-V virtual machine name
$VMName = "test-vm"

# Check if a folder name exists in the local drive
if (Test-Path -Path D:\TempVmBackup){
    # Set the local export folder path
    $LocalExportFolder = "D:\TempVmBackup"
}
else{
    # create a local folder to export the VM
    New-Item -ItemType Directory -Path D:\TempVmBackup | Out-Null
    # Set the local export folder path
    $LocalExportFolder = "D:\TempVmBackup"
}

# Set the shared folder path
$SharedFolderPath = "\\xxx.xxx.xxx.xxx\WORM-VM-Backup"

# Step 1: Shutdown the Hyper-V virtual machine
Write-Host "Shutting down $VMName..."
Stop-VM -Name $VMName -Force

# Step 2: Export the virtual machine to a local directory
Write-Host "Exporting virtual machine to local directory..."
Export-VM -Name $VMName -Path $LocalExportFolder

# Step 3: Start the Hyper-V virtual machine
Write-Host "Starting $VMName..."
Start-VM -Name $VMName

# Step 4: Compress the local export folder using 7-Zip
Write-Host "Compressing local export folder using 7-Zip..."
$ZipFileName = Join-Path $LocalExportFolder ($VMName + "-"+(Get-Date -Format "ddMMyyyy_HHmmss")+".7z")
& "C:\Program Files\7-Zip\7z.exe" a -t7z $ZipFileName $LocalExportFolder


# Step 5: Copy the exported 7z file to the backup folder
Write-Host "Copying virtual machine 7z file to shared folder..."
Copy-Item -Path $ZipFileName -Destination $SharedFolderPath -Force

# Step 6: Delete the local export and 7z file
Write-Host "Deleting local export and 7z file..."
Remove-Item -Path $LocalExportFolder -Recurse -Force

Write-Host "Backup completed successfully."
```
