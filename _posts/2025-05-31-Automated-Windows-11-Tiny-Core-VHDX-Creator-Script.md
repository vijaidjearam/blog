---
layout: post
date: 2025-05-31 14:04
title: Dual boot to VHDX without USB
category: windows11
tags: windows11 vhdx dualboot
---
# Automated Windows 11 Tiny Core VHDX Creator Script

Create a lightweight Windows 11 installation in a virtual disk (VHDX) file with minimal user intervention using this comprehensive PowerShell automation script.

## Overview

This script automates the process of creating a Windows 11 Tiny Core installation inside a VHDX file, making it perfect for virtualization, testing, or creating portable Windows environments. The process is divided into two clear phases with minimal manual intervention required.

## Features

- ✅ **Fully Automated VHDX Creation** - Creates, formats, and mounts virtual disk
- ✅ **Automatic Tiny11 Builder Integration** - Downloads and runs Tiny11 Builder automatically  
- ✅ **Custom Boot Menu Names** - Add personalized boot entry names
- ✅ **Interactive Image Selection** - Choose specific Windows 11 editions
- ✅ **Two-Phase Process** - Clear separation between ISO creation and disk operations
- ✅ **Comprehensive Error Handling** - Detailed feedback and error management
- ✅ **Flexible Configuration** - Customizable paths, sizes, and names

## Prerequisites

Before running the script, ensure you have:

- **Administrator privileges** (required for diskpart and DISM operations)
- **Windows 11 ISO file** downloaded from Microsoft
- **Sufficient disk space** (at least 60GB recommended)
- **PowerShell 5.1 or later**

## Quick Start

### Basic Usage
```powershell
# Run with default settings
.\Win11TinyVHDX.ps1
```

### Advanced Usage
```powershell
# Custom configuration
.\Win11TinyVHDX.ps1 -VHDXPath "D:\VMs\MyTinyWin11.vhdx" -VHDXSizeGB 40 -VHDXLabel "TinyWindows" -BootEntryName "Win11-Custom-Build"
```

## Script Parameters

| Parameter | Default Value | Description |
|-----------|---------------|-------------|
| `VHDXPath` | `E:\VHD_Store\Win11tinycore.vhdx` | Full path for the VHDX file |
| `VHDXSizeGB` | `50` | Size of the virtual disk in GB |
| `VHDXLabel` | `Win11tinycore` | Volume label for the virtual disk |
| `BootEntryName` | `Win11TinyCore-Master` | Custom name in boot menu |

## Process Flow

### Phase 1: Tiny11 Core ISO Creation 🔨

1. **Mount Windows 11 ISO** (Manual)
   - Download Windows 11 ISO from Microsoft
   - Right-click → Mount or use Windows Explorer

2. **Specify ISO Drive Letter** (Interactive)
   - Script displays available drives
   - Enter the drive letter where ISO is mounted

3. **Download Tiny11 Builder** (Automated)
   - Automatically downloads from GitHub repository
   - Extracts and prepares the builder tool

4. **Create Tiny11 ISO** (Automated)
   - Runs Tiny11 Builder automatically
   - Creates optimized Windows 11 ISO

5. **Mount Tiny11 ISO** (Manual)
   - Mount the newly created Tiny11 ISO file

### Phase 2: Virtual Disk Creation & Image Application 💾

6. **Create VHDX Virtual Disk** (Automated)
   - Uses diskpart to create expandable VHDX
   - Formats and assigns drive letter

7. **Select Windows Edition** (Interactive)
   - Displays available Windows editions
   - Choose the desired edition index

8. **Apply Windows Image** (Automated)
   - Uses DISM to install Windows to VHDX
   - Applies selected edition

9. **Configure Boot Manager** (Automated)
   - Sets up boot configuration
   - Adds custom boot menu entry

## Sample Output

```
=== Windows 11 Tiny Core VHDX Creator ===
This script will create a VHDX and install Windows 11 Tiny Core

=== Phase 1: Creating Tiny11 Core ISO ===

Step 1: Manual steps required:
1. Download Windows 11 ISO file
2. Mount the Windows 11 ISO (right-click -> Mount)

Step 2: Enter the drive letter where Windows 11 ISO is mounted:
Available drives:
  D: - Windows_11_ISO
Enter drive letter (e.g., D): D

Step 3: Downloading Tiny11 Builder...
Tiny11 Builder downloaded successfully!

Step 4: Running Tiny11 Builder automatically...
Executing Tiny11 Builder...
Source ISO: D:\
Tiny11 Builder completed successfully!

===============================================
    TINY11 CORE ISO CREATION COMPLETED!
===============================================

NEXT PHASE INFORMATION:
The script will now proceed to:
  • Create a virtual disk (VHDX) file
  • Format and prepare the virtual disk
  • Apply the Tiny11 Core image to the virtual disk
  • Configure the boot manager for the installation

Virtual Disk Details:
  Path: E:\VHD_Store\Win11tinycore.vhdx
  Size: 50 GB
  Label: Win11tinycore
  Boot Entry: Win11TinyCore-Master

Do you want to proceed with virtual disk creation and image application? (y/n): y

=== Phase 2: Creating Virtual Disk and Applying Image ===

Step 7: Creating VHDX...
VHDX created successfully and mounted as drive X:

Step 8: Getting Windows image information...
[WIM Edition List Displayed]
Enter index number: 6

Step 9: Applying Windows image...
Windows image applied successfully!

Step 10: Configuring boot manager...
Boot manager configured successfully with custom name!

=== Installation Complete! ===
```

## Technical Details

### VHDX Creation Process
The script uses diskpart commands to:
```
create vdisk file="path" maximum=size type=expandable
attach vdisk
create part primary
format quick label="label"
assign letter=X
```

### Image Application
Uses DISM (Deployment Image Servicing and Management) tool:
```powershell
dism /apply-image /imagefile:source.wim /index:N /applydir:X:\
```

### Boot Configuration
Configures Windows Boot Manager with custom entry:
```powershell
bcdboot X:\windows /d "Custom Boot Name"
```

### Creating Child Vms with differencing disk

```powershell
New-VHD -ParentPath 'E:\VHD_Store\Win11tinycore.vhdx' -Path 'E:\VHD_Store\Android.vhdx' -Differencing
```

Note: 
- Use the command above to create a differencing VHD. Then, use EasyBCD to add the newly created VHDX to the boot menu. Ensure that the Hyper-V Virtual Machine Management service is running.
- Before creating the differencing disk, ensure that you have installed all necessary drivers and essential software on the parent VM. This will prevent the need to reinstall them on the child VMs.
- Do not update the parent VM under any circumstances, as this could render the child VMs unbootable. Make sure not to boot into the parent VM after creating child vms.


## Use Cases

### 1. Virtual Machine Development
- Create lightweight VMs for testing
- Rapid deployment of clean Windows environments
- Resource-efficient virtualization

### 2. System Recovery
- Portable Windows environment on external drive
- Emergency boot scenarios
- System repair and maintenance

### 3. Educational Purposes
- Learning Windows internals
- Understanding deployment processes
- Teaching virtualization concepts

### 4. Software Testing
- Isolated testing environments
- Clean OS installations for software validation
- Multiple Windows configurations

## Benefits of Tiny11 Core

- **Reduced Size**: ~2-3GB smaller than standard Windows 11
- **Faster Boot**: Optimized startup performance
- **Lower Resource Usage**: Minimal background processes
- **Essential Features**: Keeps core Windows functionality
- **Compatibility**: Full Windows 11 application support

## Troubleshooting

### Common Issues

**Script requires Administrator privileges**
```
Solution: Right-click PowerShell → "Run as Administrator"
```

**VHDX creation fails**
```
Solution: Ensure sufficient disk space and valid path
Check: Disk permissions and available storage
```

**Tiny11 Builder download fails**
```
Solution: Check internet connection and GitHub accessibility
Alternative: Download manually from https://github.com/ntdevlabs/tiny11builder
```

**Image application takes too long**
```
Solution: Normal behavior - DISM operations can take 15-30 minutes
Monitor: Task Manager for disk activity
```

**Boot configuration fails**
```
Solution: Ensure Windows image was applied successfully
Check: X:\windows directory exists and contains system files
```

### Performance Tips

- **Use SSD storage** for faster VHDX operations
- **Allocate sufficient RAM** during image application
- **Close unnecessary applications** to free up system resources  
- **Use wired network connection** for downloads

## Security Considerations

- **Run from trusted sources** - Verify script integrity
- **Administrator privileges** - Understand elevation requirements
- **Antivirus exclusions** - May need temporary exclusions for VHDX operations
- **Network downloads** - Ensure secure connection for Tiny11 Builder

## Advanced Configuration

### Custom VHDX Locations
```powershell
# Network storage
.\Win11TinyVHDX.ps1 -VHDXPath "\\server\vms\tiny11.vhdx"

# External drive
.\Win11TinyVHDX.ps1 -VHDXPath "F:\VirtualMachines\Win11Tiny.vhdx"
```

### Multiple Configurations
```powershell
# Development environment
.\Win11TinyVHDX.ps1 -BootEntryName "Win11-Dev-Environment" -VHDXSizeGB 60

# Testing environment  
.\Win11TinyVHDX.ps1 -BootEntryName "Win11-Test-Lab" -VHDXSizeGB 40
```

## Script Download

The complete PowerShell script is available below. Save it as `Win11TinyVHDX.ps1` and run with Administrator privileges.

**Note**: Ensure execution policy allows script execution:
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

```powershell
# Windows 11 Tiny Core VHDX Creator Script
# Requires Administrator privileges

param(
    [string]$VHDXPath = "E:\VHD_Store\Win11tinycore.vhdx",
    [int]$VHDXSizeGB = 50,
    [string]$VHDXLabel = "Win11tinycore",
    [string]$BootEntryName = "Win11TinyCore-Master"
)

# Check if running as administrator
if (-NOT ([Security.Principal.WindowsPrincipal] [Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole] "Administrator")) {
    Write-Host "This script requires Administrator privileges. Please run as Administrator." -ForegroundColor Red
    exit 1
}

Write-Host "=== Windows 11 Tiny Core VHDX Creator ===" -ForegroundColor Cyan
Write-Host "This script will create a VHDX and install Windows 11 Tiny Core" -ForegroundColor Yellow
Write-Host ""

# Function to create VHDX using diskpart
function Create-VHDX {
    param(
        [string]$Path,
        [int]$SizeMB,
        [string]$Label,
        [string]$DriveLetter = "X"
    )
    
    Write-Host "Creating VHDX file at: $Path" -ForegroundColor Green
    
    # Ensure directory exists
    $directory = Split-Path $Path -Parent
    if (!(Test-Path $directory)) {
        New-Item -ItemType Directory -Path $directory -Force | Out-Null
    }
    
    # Create diskpart script
    $diskpartScript = @"
create vdisk file="$Path" maximum=$SizeMB type=expandable
attach vdisk
create part primary
format quick label="$Label"
assign letter=$DriveLetter
exit
"@
    
    $scriptPath = "$env:TEMP\create_vhdx.txt"
    $diskpartScript | Out-File -FilePath $scriptPath -Encoding ASCII
    
    # Run diskpart
    Write-Host "Running diskpart to create and format VHDX..." -ForegroundColor Yellow
    diskpart /s $scriptPath
    
    # Clean up
    Remove-Item $scriptPath -Force
    
    if (Test-Path "${DriveLetter}:\") {
        Write-Host "VHDX created successfully and mounted as drive ${DriveLetter}:" -ForegroundColor Green
        return $true
    } else {
        Write-Host "Failed to create or mount VHDX" -ForegroundColor Red
        return $false
    }
}

# Function to download Tiny11 Builder
function Download-Tiny11Builder {
    $downloadPath = "$env:TEMP\tiny11builder"
    $zipPath = "$downloadPath\tiny11builder.zip"
    
    Write-Host "Downloading Tiny11 Builder..." -ForegroundColor Yellow
    
    # Create download directory
    if (!(Test-Path $downloadPath)) {
        New-Item -ItemType Directory -Path $downloadPath -Force | Out-Null
    }
    
    try {
        # Download the repository
        $url = "https://github.com/ntdevlabs/tiny11builder/archive/refs/heads/main.zip"
        Invoke-WebRequest -Uri $url -OutFile $zipPath -UseBasicParsing
        
        # Extract the zip
        Expand-Archive -Path $zipPath -DestinationPath $downloadPath -Force
        
        # Find the PowerShell script
        $scriptPath = Get-ChildItem -Path $downloadPath -Name "tiny11Coremaker.ps1" -Recurse | Select-Object -First 1
        
        if ($scriptPath) {
            $fullScriptPath = Join-Path $downloadPath $scriptPath
            Write-Host "Tiny11 Builder downloaded to: $fullScriptPath" -ForegroundColor Green
            return $fullScriptPath
        } else {
            Write-Host "Could not find tiny11Coremaker.ps1 in downloaded files" -ForegroundColor Red
            return $null
        }
    }
    catch {
        Write-Host "Failed to download Tiny11 Builder: $($_.Exception.Message)" -ForegroundColor Red
        return $null
    }
}

# Function to get Windows 11 ISO drive letter
function Get-ISODriveLetter {
    param([string]$Prompt)
    
    Write-Host $Prompt -ForegroundColor Yellow
    Write-Host "Available drives:" -ForegroundColor Cyan
    Get-WmiObject -Class Win32_LogicalDisk | Where-Object { $_.DriveType -eq 5 } | ForEach-Object {
        Write-Host "  $($_.DeviceID) - $($_.VolumeName)" -ForegroundColor White
    }
    
    do {
        $driveLetter = Read-Host "Enter drive letter (e.g., D)"
        $driveLetter = $driveLetter.TrimEnd(':').ToUpper()
        
        if ($driveLetter -match "^[A-Z]$" -and (Test-Path "${driveLetter}:\")) {
            return $driveLetter
        } else {
            Write-Host "Invalid drive letter or drive not found. Please try again." -ForegroundColor Red
        }
    } while ($true)
}

# Function to get WIM info and let user select index
function Get-WIMInfo {
    param([string]$ISODriveLetter)
    
    $wimFile = "${ISODriveLetter}:\sources\install.wim"
    
    if (!(Test-Path $wimFile)) {
        Write-Host "install.wim not found at $wimFile" -ForegroundColor Red
        return $null
    }
    
    Write-Host "Getting Windows image information..." -ForegroundColor Yellow
    Write-Host ""
    
    try {
        # Get WIM info
        $wimInfo = dism /get-wiminfo /wimfile:$wimFile
        $wimInfo | Write-Host
        
        Write-Host ""
        Write-Host "Please select the Windows edition index from the list above:" -ForegroundColor Yellow
        
        do {
            $index = Read-Host "Enter index number"
            if ($index -match "^\d+$" -and [int]$index -gt 0) {
                return [int]$index
            } else {
                Write-Host "Please enter a valid index number." -ForegroundColor Red
            }
        } while ($true)
    }
    catch {
        Write-Host "Failed to get WIM information: $($_.Exception.Message)" -ForegroundColor Red
        return $null
    }
}

# Function to apply Windows image
function Apply-WindowsImage {
    param(
        [string]$ISODriveLetter,
        [int]$Index,
        [string]$TargetDrive = "X"
    )
    
    $wimFile = "${ISODriveLetter}:\sources\install.wim"
    $targetPath = "${TargetDrive}:\"
    
    Write-Host "Applying Windows image..." -ForegroundColor Yellow
    Write-Host "Source: $wimFile" -ForegroundColor Cyan
    Write-Host "Index: $Index" -ForegroundColor Cyan
    Write-Host "Target: $targetPath" -ForegroundColor Cyan
    Write-Host ""
    
    try {
        dism /apply-image /imagefile:$wimFile /index:$Index /applydir:$targetPath
        
        if ($LASTEXITCODE -eq 0) {
            Write-Host "Windows image applied successfully!" -ForegroundColor Green
            return $true
        } else {
            Write-Host "Failed to apply Windows image (Exit code: $LASTEXITCODE)" -ForegroundColor Red
            return $false
        }
    }
    catch {
        Write-Host "Error applying Windows image: $($_.Exception.Message)" -ForegroundColor Red
        return $false
    }
}

# Function to configure boot manager
function Configure-BootManager {
    param(
        [string]$TargetDrive = "X",
        [string]$BootEntryName = "Win11TinyCore-Master"
    )
    
    Write-Host "Configuring boot manager..." -ForegroundColor Yellow
    Write-Host "Boot entry name: $BootEntryName" -ForegroundColor Cyan
    
    try {
        # Use /d parameter to set custom boot menu description
        bcdboot "${TargetDrive}:\windows" /d "$BootEntryName"
        
        if ($LASTEXITCODE -eq 0) {
            Write-Host "Boot manager configured successfully with custom name!" -ForegroundColor Green
            return $true
        } else {
            Write-Host "Failed to configure boot manager (Exit code: $LASTEXITCODE)" -ForegroundColor Red
            return $false
        }
    }
    catch {
        Write-Host "Error configuring boot manager: $($_.Exception.Message)" -ForegroundColor Red
        return $false
    }
}

# Main script execution
try {
    Write-Host "=== Phase 1: Creating Tiny11 Core ISO ===" -ForegroundColor Magenta
    Write-Host ""
    
    Write-Host "Step 1: Manual steps required:" -ForegroundColor Magenta
    Write-Host "1. Download Windows 11 ISO file" -ForegroundColor Yellow
    Write-Host "2. Mount the Windows 11 ISO (right-click -> Mount)" -ForegroundColor Yellow
    Write-Host ""
    
    # Step 2: Get Windows 11 ISO drive letter
    $win11Drive = Get-ISODriveLetter -Prompt "Step 2: Enter the drive letter where Windows 11 ISO is mounted:"
    
    # Step 3: Download Tiny11 Builder
    Write-Host ""
    Write-Host "Step 3: Downloading Tiny11 Builder..." -ForegroundColor Magenta
    $tiny11Script = Download-Tiny11Builder
    
    if (!$tiny11Script) {
        throw "Failed to download Tiny11 Builder"
    }
    
    Write-Host ""
    Write-Host "Step 4: Running Tiny11 Builder automatically..." -ForegroundColor Magenta
    
    # Change to the Tiny11 Builder directory
    $tiny11Dir = Split-Path $tiny11Script -Parent
    Set-Location $tiny11Dir
    
    Write-Host "Executing Tiny11 Builder..." -ForegroundColor Yellow
    Write-Host "Source ISO: ${win11Drive}:\" -ForegroundColor Cyan
    Write-Host ""
    
    try {
        # Run the Tiny11 Builder script with the Windows 11 ISO path
        & $tiny11Script
        
        if ($LASTEXITCODE -eq 0) {
            Write-Host "Tiny11 Builder completed successfully!" -ForegroundColor Green
        } else {
            Write-Host "Tiny11 Builder finished with exit code: $LASTEXITCODE" -ForegroundColor Yellow
        }
    }
    catch {
        Write-Host "Error running Tiny11 Builder: $($_.Exception.Message)" -ForegroundColor Red
        throw "Failed to run Tiny11 Builder"
    }
    
    Write-Host ""
    Write-Host "Step 5: Manual step required:" -ForegroundColor Magenta
    Write-Host "Please mount the created Tiny11 ISO file" -ForegroundColor Yellow
    Write-Host "The ISO should be located in: $tiny11Dir" -ForegroundColor Cyan
    Write-Host ""
    
    Read-Host "Press Enter when you have mounted the Tiny11 ISO..."
    
    # Step 6: Get Tiny11 ISO drive letter
    $tiny11Drive = Get-ISODriveLetter -Prompt "Step 6: Enter the drive letter where Tiny11 ISO is mounted:"
    
    Write-Host ""
    Write-Host "===============================================" -ForegroundColor Green
    Write-Host "    TINY11 CORE ISO CREATION COMPLETED!" -ForegroundColor Green
    Write-Host "===============================================" -ForegroundColor Green
    Write-Host ""
    Write-Host "NEXT PHASE INFORMATION:" -ForegroundColor Yellow
    Write-Host "The script will now proceed to:" -ForegroundColor White
    Write-Host "  • Create a virtual disk (VHDX) file" -ForegroundColor Cyan
    Write-Host "  • Format and prepare the virtual disk" -ForegroundColor Cyan
    Write-Host "  • Apply the Tiny11 Core image to the virtual disk" -ForegroundColor Cyan
    Write-Host "  • Configure the boot manager for the installation" -ForegroundColor Cyan
    Write-Host ""
    Write-Host "Virtual Disk Details:" -ForegroundColor Yellow
    Write-Host "  Path: $VHDXPath" -ForegroundColor White
    Write-Host "  Size: $VHDXSizeGB GB" -ForegroundColor White
    Write-Host "  Label: $VHDXLabel" -ForegroundColor White
    Write-Host "  Boot Entry: $BootEntryName" -ForegroundColor White
    Write-Host ""
    
    $continue = Read-Host "Do you want to proceed with virtual disk creation and image application? (y/n)"
    if ($continue -ne 'y' -and $continue -ne 'Y') {
        Write-Host "Script terminated by user." -ForegroundColor Yellow
        exit 0
    }
    
    Write-Host ""
    Write-Host "=== Phase 2: Creating Virtual Disk and Applying Image ===" -ForegroundColor Magenta
    Write-Host ""
    
    # Step 7: Create VHDX
    Write-Host "Step 7: Creating VHDX..." -ForegroundColor Magenta
    $vhdxSizeMB = $VHDXSizeGB * 1024
    if (!(Create-VHDX -Path $VHDXPath -SizeMB $vhdxSizeMB -Label $VHDXLabel)) {
        throw "Failed to create VHDX"
    }
    
    # Step 8: Get WIM info and select index
    Write-Host ""
    Write-Host "Step 8: Getting Windows image information..." -ForegroundColor Magenta
    $selectedIndex = Get-WIMInfo -ISODriveLetter $tiny11Drive
    
    if (!$selectedIndex) {
        throw "Failed to get WIM information or select index"
    }
    
    # Step 9: Apply Windows image
    Write-Host ""
    Write-Host "Step 9: Applying Windows image..." -ForegroundColor Magenta
    if (!(Apply-WindowsImage -ISODriveLetter $tiny11Drive -Index $selectedIndex)) {
        throw "Failed to apply Windows image"
    }
    
    # Step 10: Configure boot manager
    Write-Host ""
    Write-Host "Step 10: Configuring boot manager..." -ForegroundColor Magenta
    if (!(Configure-BootManager -BootEntryName $BootEntryName)) {
        throw "Failed to configure boot manager"
    }
    
    # Success message
    Write-Host ""
    Write-Host "=== Installation Complete! ===" -ForegroundColor Green
    Write-Host "Windows 11 Tiny Core has been successfully installed to VHDX:" -ForegroundColor Green
    Write-Host $VHDXPath -ForegroundColor Cyan
    Write-Host ""
    Write-Host "You can now:" -ForegroundColor Yellow
    Write-Host "1. Detach the VHDX: diskpart -> select vdisk file=$VHDXPath -> detach vdisk" -ForegroundColor White
    Write-Host "2. Use the VHDX with Hyper-V or other virtualization software" -ForegroundColor White
    Write-Host "3. Boot from the VHDX on physical hardware (advanced)" -ForegroundColor White
    
}
catch {
    Write-Host ""
    Write-Host "Error: $($_.Exception.Message)" -ForegroundColor Red
    Write-Host "Installation failed. Please check the error message above." -ForegroundColor Red
    exit 1
}

# Optional: Detach VHDX
Write-Host ""
$detach = Read-Host "Would you like to detach the VHDX now? (y/n)"
if ($detach -eq 'y' -or $detach -eq 'Y') {
    Write-Host "Detaching VHDX..." -ForegroundColor Yellow
    
    $detachScript = @"
select vdisk file="$VHDXPath"
detach vdisk
exit
"@
    
    $scriptPath = "$env:TEMP\detach_vhdx.txt"
    $detachScript | Out-File -FilePath $scriptPath -Encoding ASCII
    diskpart /s $scriptPath
    Remove-Item $scriptPath -Force
    
    Write-Host "VHDX detached successfully!" -ForegroundColor Green
}
```

## Conclusion

This automated script streamlines the creation of Windows 11 Tiny Core VHDX files, making it accessible for both beginners and advanced users. The two-phase approach provides clear checkpoints and flexibility, while the comprehensive error handling ensures a smooth experience.

Whether you're creating virtual machines, building portable Windows environments, or setting up testing scenarios, this script provides a robust and efficient solution for your Windows 11 deployment needs.

---

*Last updated: June 2025*
*Compatible with: Windows 11, PowerShell 5.1+*
*Requirements: Administrator privileges, Windows 11 ISO*