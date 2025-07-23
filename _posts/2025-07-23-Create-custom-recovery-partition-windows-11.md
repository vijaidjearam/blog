---
layout: post
date: 2025-07-23 12:30:00
title: Create a Custom Recovery Partition on Windows 11
category: recovery 
tags: windows recovery 
---
# 📦 PowerShell Script: Create a Custom Recovery Partition on Windows 11

This script automates the process of capturing a custom system image and creating a dedicated recovery partition. The recovery partition can then be used with **"Reset this PC" > "Keep my files"** to restore the system with pre-installed software.

---

## ✅ Features

- Captures current system (`C:`) into a `.wim` image using DISM
- Creates a new recovery partition (30% of disk size)
- Copies the `.wim` into `Recovery\WindowsRE`
- Registers the custom image with `reagentc`
- Hides the recovery partition from normal users

---

## ⚠️ Requirements

- Must run as **Administrator**
- DISM must be available (built into Windows 10/11)
- Sufficient disk space to shrink `C:` and store `.wim`

---

## 💻 Usage Instructions

1. Save the script below as `Create-RecoveryPartition.ps1`
2. Run PowerShell **as Administrator**
3. Wait for all steps to complete — it may take several minutes
4. Verify recovery status using:

```powershell
reagentc /info
```

---

## 📝 PowerShell Script

```powershell
# === Config ===
$WimName      = "CustomRefresh"
$ImageName    = "Custom Windows Image"
$ImageIndex   = 1
$CapturePath  = "C:\"
$TempWim      = "C:\Recovery\$WimName.wim"
$RecFolder    = "Recovery\WindowsRE"
$VolumeLabel  = "Recovery"
$TempLetter   = "R"
$DiskNumber   = 0   # <-- Adjust if needed (0 is usually system disk)

# === Check Admin Privileges ===
if (-not ([Security.Principal.WindowsPrincipal] [Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole("Administrator")) {
    Write-Error "Run this script as Administrator."
    exit 1
}

# === Create folder if needed ===
if (!(Test-Path "C:\Recovery")) {
    New-Item -ItemType Directory -Path "C:\Recovery" | Out-Null
}

# === Capture current C: as WIM image ===
Write-Host "📦 Capturing system image to $TempWim ..."
$dismCmd = "dism /Capture-Image /ImageFile:`"$TempWim`" /CaptureDir:C:\ /Name:`"$ImageName`""
Start-Process -Wait -FilePath cmd.exe -ArgumentList "/c $dismCmd"

if (!(Test-Path $TempWim)) {
    Write-Error "DISM capture failed. Exiting."
    exit 1
}

# === Get Disk Size and Calculate Shrink Amount ===
Write-Host "📊 Calculating 30% shrink from disk 0..."
$vol = Get-Volume -DriveLetter C
$disk = Get-Disk -Number $DiskNumber
$shrinkSizeMB = [math]::Floor($disk.Size / 1024 / 1024 * 0.3)

# === Create DiskPart script ===
$diskpartScript = @"
select disk $DiskNumber
list volume
select volume $($vol.UniqueId)
shrink desired=$shrinkSizeMB
create partition primary
format fs=ntfs quick label=$VolumeLabel
assign letter=$TempLetter
set id=de94bba4-06d1-4d40-a16a-bfd50179d6ac
gpt attributes=0x8000000000000001
exit
"@

$scriptPath = "$env:TEMP\diskpart_script.txt"
$diskpartScript | Set-Content -Path $scriptPath -Encoding ASCII
Start-Process -Wait -FilePath diskpart -ArgumentList "/s `"$scriptPath`""
Remove-Item $scriptPath

# === Copy image to recovery partition ===
$targetFolder = "$($TempLetter):\$RecFolder"
Write-Host "📁 Copying image to $targetFolder ..."
New-Item -ItemType Directory -Path $targetFolder -Force | Out-Null
Copy-Item -Path $TempWim -Destination "$targetFolder\CustomRefresh.wim"

# === Register custom image ===
Write-Host "🛠️ Configuring recovery image with reagentc..."
Start-Process -Wait -FilePath cmd.exe -ArgumentList "/c reagentc /disable"
Start-Process -Wait -FilePath cmd.exe -ArgumentList "/c reagentc /SetOSImage /Path $TempLetter`:\$RecFolder /Index $ImageIndex"
Start-Process -Wait -FilePath cmd.exe -ArgumentList "/c reagentc /enable"

# === Show status ===
Write-Host "`n📋 Recovery configuration:"
Start-Process -Wait -FilePath cmd.exe -ArgumentList "/c reagentc /info"

# === Hide the recovery partition ===
Write-Host "🔒 Hiding recovery partition..."
$removeScript = @"
select volume $TempLetter
remove letter=$TempLetter
exit
"@
$removePath = "$env:TEMP\remove_letter.txt"
$removeScript | Set-Content -Path $removePath -Encoding ASCII
Start-Process -Wait -FilePath diskpart -ArgumentList "/s `"$removePath`""
Remove-Item $removePath

Write-Host "`n✅ Done! Recovery partition created and configured." -ForegroundColor Green
```

---

## 🔁 Result

Once the script is run, your system will be configured to use your captured recovery image when you choose:

> **Settings > System > Recovery > Reset this PC > Keep my files**

---

## 📌 Notes

- Only use on stable systems (tested and configured).
- Always keep a backup before modifying partitions.
- You can optionally place the `.wim` on a USB or external disk for reuse on other machines.

---


## 🔧 Bootable Recovery USB (Optional)

To create a **bootable USB** containing the recovery image and tools:

### 🛠️ Steps

1. **Insert a USB drive (8 GB or larger)**

2. **Format the USB as FAT32 or NTFS**

3. **Mount a Windows ISO** (same version as your current OS) or download the [Windows ADK](https://learn.microsoft.com/en-us/windows-hardware/get-started/adk-install) to access WinPE tools.

4. **Create WinPE boot environment** (if not using ISO):

```powershell
# From Deployment and Imaging Tools Environment (as Admin)
copype amd64 C:\WinPE_amd64
MakeWinPEMedia /UFD C:\WinPE_amd64 E:
```

5. **Copy your custom .wim file** to the USB:

```powershell
# Assuming CustomRefresh.wim exists
Copy-Item C:\Recovery\CustomRefresh.wim E:\sources\install.wim
```

6. **Make USB bootable** (if not already via MakeWinPEMedia). For ISO-based bootable USB, use `Rufus` to burn the Windows ISO and then replace the `install.wim`.

7. **Boot into USB and use DISM or Recovery Environment** to apply the image manually.

### ✅ Apply WIM manually (example):

```cmd
# In Windows PE command prompt:
dism /Apply-Image /ImageFile:E:\sources\install.wim /Index:1 /ApplyDir:C:\
```

---
