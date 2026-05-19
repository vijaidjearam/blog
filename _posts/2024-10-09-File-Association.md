---
layout: post
date: 2024-10-09 11:36:50
title: File Association
category: windows
tags: windows_settings 
---

# Steps to Automate Setting File Associations via Group Policy Using Command Line

Run the following command to export the current file associations to an XML file:

```cmd
Dism /Online /Export-DefaultAppAssociations:C:\file_associations.xml
```

Note : There is a command via DISM to import the file, but it breaks windows profile  Please do not use this  "Dism /Online /Import-DefaultAppAssociations:C:\file_associations.xml"

## Option 1: Apply the XML via PowerShell Script

```powershell
# Path to the XML file
$xmlPath = "C:\path\to\file_associations.xml"

# Set the Group Policy for default app associations
Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System" -Name "DefaultAssociationsConfiguration" -Value $xmlPath

```
## OPtion 2: Via Psexec

```cmd
psexec \\%%i -s cmd /c "reg add HKLM\SOFTWARE\Policies\Microsoft\Windows\System /v DefaultAssociationsConfiguration /t REG_SZ /d C:\file_associations.xml /f"
```


# File Association Management Documentation

## Overview
This documentation covers managing default file associations in a Windows 11 environment using the `Set-DefaultFileAssociations.ps1` PowerShell script with local XML storage.

---

## Table of Contents
1. [Initial Setup](#initial-setup)
2. [Setting New File Associations](#setting-new-file-associations)
3. [Modifying Existing Associations](#modifying-existing-associations)
4. [Removing/Resetting File Associations](#removing-resetting-file-associations)
5. [Troubleshooting](#troubleshooting)
6. [Reference Commands](#reference-commands)

---

## Initial Setup

### Prerequisites
- Windows 11 Pro/Enterprise
- Administrative access
- PowerShell 5.1 or later
- Reference computer with desired file associations configured

### Step 1: Configure Reference Computer

1. **Sign in** to a reference computer as a standard user
2. **Install applications** that you want to use for file associations (e.g., Adobe Reader, Chrome, VLC)
3. **Set default applications** for each file type:
   - Right-click a file (e.g., `.pdf`)
   - Select **Open with** → **Choose another app**
   - Select desired application
   - Check **"Always use this app to open .pdf files"**
   - Click **OK**
4. **Repeat** for all file types and protocols you want to configure

### Step 2: Export File Associations

Run the PowerShell script on the reference computer:

```powershell
# Run PowerShell as Administrator
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force

# Navigate to script location
cd C:\Scripts

# Run the script
.\Set-DefaultFileAssociations.ps1
```

**Script Actions:**
- Exports current user's file associations to `C:\DefaultAssoc.xml`
- Configures registry policy to apply associations
- Updates Group Policy

### Step 3: Verify Export

Check the generated XML file:

```powershell
# View the exported associations
Get-Content C:\DefaultAssoc.xml

# Count number of associations
(Select-Xml -Path C:\DefaultAssoc.xml -XPath "//Association").Count
```

**Example Output:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<DefaultAssociations>
  <Association Identifier=".pdf" ProgId="Acrobat.Document.DC" ApplicationName="Adobe Acrobat" />
  <Association Identifier=".html" ProgId="ChromeHTML" ApplicationName="Google Chrome" />
  <Association Identifier=".txt" ProgId="Applications\notepad.exe" ApplicationName="Notepad" />
</DefaultAssociations>
```

### Step 4: Configure Group Policy (Optional)

**For Domain Environment:**

1. Open **Group Policy Management Console (GPMC)**
2. Create or edit a GPO (e.g., "Default File Associations")
3. Navigate to:
   ```
   Computer Configuration
   → Administrative Templates
   → Windows Components
   → File Explorer
   ```
4. Double-click **"Set a default associations configuration file"**
5. Select **Enabled**
6. Enter the path: `C:\DefaultAssoc.xml`
7. Click **OK**
8. Link the GPO to appropriate OUs
9. Run on a client: `gpupdate /force`

**For Standalone/Workgroup Computers:**

The registry method is automatically configured by the script (no additional steps needed).

### Step 5: Deploy to Other Computers

Copy the XML file to each computer:

```powershell
# Copy to remote computers
$Computers = @("PC01", "PC02", "PC03")

foreach ($Computer in $Computers) {
    # Copy XML file
    Copy-Item "C:\DefaultAssoc.xml" "\\$Computer\C$\DefaultAssoc.xml" -Force
    
    # Configure registry on remote computer
    Invoke-Command -ComputerName $Computer -ScriptBlock {
        $regPath = "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System"
        
        if (!(Test-Path $regPath)) {
            New-Item -Path $regPath -Force | Out-Null
        }
        
        New-ItemProperty -Path $regPath `
            -Name "DefaultAssociationsConfiguration" `
            -Value "C:\DefaultAssoc.xml" `
            -PropertyType String -Force | Out-Null
            
        New-ItemProperty -Path $regPath `
            -Name "NoDefaultBrowserReset" `
            -Value 1 `
            -PropertyType DWord -Force | Out-Null
            
        # Update Group Policy
        gpupdate /force
    }
    
    Write-Host "Configured: $Computer" -ForegroundColor Green
}
```

---

## Setting New File Associations

### Scenario: Adding a New Application

**Example: Setting VLC as default for video files**

#### Step 1: Configure on Reference Computer

```powershell
# Method 1: Manual Configuration
# 1. Right-click an .mp4 file
# 2. Open with → VLC Media Player
# 3. Check "Always use this app"

# Method 2: Using PowerShell (if you know the ProgId)
$ProgId = "VLC.mp4"
cmd /c "assoc .mp4=$ProgId"
```

#### Step 2: Re-export Associations

```powershell
# Run as Administrator
# Backup existing XML first
Copy-Item C:\DefaultAssoc.xml "C:\DefaultAssoc_backup_$(Get-Date -Format 'yyyyMMdd_HHmmss').xml" -Force

# Export new associations
dism /Online /Export-DefaultAppAssociations:"C:\DefaultAssoc.xml"

# Verify the new association is included
Select-String -Path C:\DefaultAssoc.xml -Pattern "\.mp4"
```

#### Step 3: Deploy to Other Computers

```powershell
# Copy updated XML to all computers
$Computers = Get-Content "C:\Scripts\ComputerList.txt"

foreach ($Computer in $Computers) {
    Copy-Item "C:\DefaultAssoc.xml" "\\$Computer\C$\DefaultAssoc.xml" -Force
    
    # Force GP update remotely
    Invoke-Command -ComputerName $Computer -ScriptBlock {
        gpupdate /force
    }
    
    Write-Host "Updated: $Computer" -ForegroundColor Green
}
```

### Finding ProgId for Applications

```powershell
# Method 1: Check current association
Get-ItemProperty "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\FileExts\.pdf\UserChoice" | Select-Object ProgId

# Method 2: List all registered ProgIds
Get-ChildItem "HKLM:\SOFTWARE\Classes" | Where-Object {$_.PSChildName -notlike ".*"} | Select-Object PSChildName

# Method 3: Check specific file type
cmd /c assoc .pdf
cmd /c ftype AcroExch.Document.DC

# Method 4: Search for application
Get-ChildItem "HKLM:\SOFTWARE\Classes" | Where-Object {$_.PSChildName -like "*Adobe*"}
```

**Common ProgIds:**

| Application | File Type | ProgId |
|------------|-----------|--------|
| Adobe Acrobat DC | .pdf | `Acrobat.Document.DC` |
| Foxit Reader | .pdf | `FoxitReader.Document` |
| Google Chrome | .html, .htm | `ChromeHTML` |
| Microsoft Edge | .html, .htm | `MSEdgeHTM` |
| Firefox | .html, .htm | `FirefoxHTML` |
| VLC | .mp4 | `VLC.mp4` |
| Notepad++ | .txt | `Notepad++_file` |
| 7-Zip | .zip | `7-Zip.zip` |

---

## Modifying Existing Associations

### Scenario: Changing from Adobe Reader to Foxit Reader

#### Method 1: Complete Re-export (Recommended)

```powershell
# Step 1: Install Foxit Reader on reference computer

# Step 2: Set Foxit as default for PDF files
# Right-click a .pdf file → Open with → Foxit Reader → Always use this app

# Step 3: Verify the change locally
Get-ItemProperty "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\FileExts\.pdf\UserChoice" | Select-Object ProgId

# Expected output: FoxitReader.Document

# Step 4: Backup current XML
$BackupPath = "C:\DefaultAssoc_Backup_$(Get-Date -Format 'yyyyMMdd_HHmmss').xml"
Copy-Item C:\DefaultAssoc.xml $BackupPath

Write-Host "Backup created: $BackupPath" -ForegroundColor Green

# Step 5: Export new associations
dism /Online /Export-DefaultAppAssociations:"C:\DefaultAssoc.xml"

# Step 6: Verify the change
Select-String -Path C:\DefaultAssoc.xml -Pattern "\.pdf"

# Step 7: Deploy to other computers
$Computers = @("PC01", "PC02", "PC03")

foreach ($Computer in $Computers) {
    # Backup remote XML
    Copy-Item "\\$Computer\C$\DefaultAssoc.xml" "\\$Computer\C$\DefaultAssoc_backup.xml" -Force -ErrorAction SilentlyContinue
    
    # Copy new XML
    Copy-Item "C:\DefaultAssoc.xml" "\\$Computer\C$\DefaultAssoc.xml" -Force
    
    # Force GP update
    Invoke-Command -ComputerName $Computer -ScriptBlock {
        gpupdate /force
    }
    
    Write-Host "Updated: $Computer" -ForegroundColor Green
}
```

#### Method 2: Manual XML Edit (Quick Change)

```powershell
# Step 1: Find Foxit's ProgId
# Right-click PDF → Properties → Opens with → Change → Select Foxit
Get-ItemProperty "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\FileExts\.pdf\UserChoice" | Select-Object ProgId

# Step 2: Backup current XML
Copy-Item C:\DefaultAssoc.xml "C:\DefaultAssoc_backup_$(Get-Date -Format 'yyyyMMdd_HHmmss').xml" -Force

# Step 3: Edit XML directly
$XMLPath = "C:\DefaultAssoc.xml"
$xml = [xml](Get-Content $XMLPath)

# Find and update the PDF association
$pdfAssociation = $xml.DefaultAssociations.Association | Where-Object {$_.Identifier -eq ".pdf"}

if ($pdfAssociation) {
    $oldProgId = $pdfAssociation.ProgId
    $pdfAssociation.ProgId = "FoxitReader.Document"
    $pdfAssociation.ApplicationName = "Foxit Reader"
    
    Write-Host "Changed .pdf from $oldProgId to FoxitReader.Document" -ForegroundColor Green
} else {
    Write-Warning "PDF association not found in XML"
}

# Save the file
$xml.Save($XMLPath)

# Verify the change
Get-Content $XMLPath | Select-String "\.pdf"

# Step 4: Deploy to other computers
$Computers = Get-Content "C:\Scripts\ComputerList.txt"

foreach ($Computer in $Computers) {
    Copy-Item $XMLPath "\\$Computer\C$\DefaultAssoc.xml" -Force
    
    Invoke-Command -ComputerName $Computer -ScriptBlock {
        gpupdate /force
    }
    
    Write-Host "Updated: $Computer" -ForegroundColor Green
}
```

#### Method 3: PowerShell Script for Specific Change

Save as `Update-PDFAssociation.ps1`:

```powershell
#Requires -RunAsAdministrator

param(
    [Parameter(Mandatory=$true)]
    [ValidateSet("Adobe", "Foxit", "Edge", "Chrome")]
    [string]$PDFReader,
    
    [string]$XMLPath = "C:\DefaultAssoc.xml",
    
    [switch]$DeployToComputers,
    
    [string[]]$Computers = @()
)

# Define ProgIds
$ProgIds = @{
    "Adobe" = @{
        ProgId = "Acrobat.Document.DC"
        AppName = "Adobe Acrobat"
    }
    "Foxit" = @{
        ProgId = "FoxitReader.Document"
        AppName = "Foxit Reader"
    }
    "Edge" = @{
        ProgId = "MSEdgePDF"
        AppName = "Microsoft Edge"
    }
    "Chrome" = @{
        ProgId = "ChromeHTML"
        AppName = "Google Chrome"
    }
}

Write-Host "=== Update PDF Association ===" -ForegroundColor Cyan
Write-Host "Target application: $PDFReader" -ForegroundColor Yellow
Write-Host ""

# Backup current XML
$BackupPath = "$XMLPath.backup_$(Get-Date -Format 'yyyyMMdd_HHmmss')"
Copy-Item $XMLPath $BackupPath -Force
Write-Host "[✓] Backup created: $BackupPath" -ForegroundColor Green

# Load XML
$xml = [xml](Get-Content $XMLPath)

# Update PDF association
$pdfAssoc = $xml.DefaultAssociations.Association | Where-Object {$_.Identifier -eq ".pdf"}

if ($pdfAssoc) {
    $oldProgId = $pdfAssoc.ProgId
    $pdfAssoc.ProgId = $ProgIds[$PDFReader].ProgId
    $pdfAssoc.ApplicationName = $ProgIds[$PDFReader].AppName
    Write-Host "[✓] Updated .pdf association from $oldProgId to $PDFReader" -ForegroundColor Green
} else {
    # Create new association if doesn't exist
    $newAssoc = $xml.CreateElement("Association")
    $newAssoc.SetAttribute("Identifier", ".pdf")
    $newAssoc.SetAttribute("ProgId", $ProgIds[$PDFReader].ProgId)
    $newAssoc.SetAttribute("ApplicationName", $ProgIds[$PDFReader].AppName)
    $xml.DefaultAssociations.AppendChild($newAssoc) | Out-Null
    Write-Host "[✓] Created new .pdf association for $PDFReader" -ForegroundColor Green
}

# Save local XML
$xml.Save($XMLPath)
Write-Host "[✓] Local XML updated: $XMLPath" -ForegroundColor Green

# Display current association
Write-Host ""
Write-Host "Current PDF association:" -ForegroundColor Yellow
$xml.DefaultAssociations.Association | Where-Object {$_.Identifier -eq ".pdf"} | Format-Table -AutoSize

# Deploy to other computers
if ($DeployToComputers -and $Computers.Count -gt 0) {
    Write-Host ""
    Write-Host "Deploying to remote computers..." -ForegroundColor Cyan
    
    foreach ($Computer in $Computers) {
        try {
            # Test connectivity
            if (Test-Connection -ComputerName $Computer -Count 1 -Quiet) {
                # Copy XML
                Copy-Item $XMLPath "\\$Computer\C$\DefaultAssoc.xml" -Force -ErrorAction Stop
                
                # Force GP update
                Invoke-Command -ComputerName $Computer -ScriptBlock {
                    gpupdate /force | Out-Null
                } -ErrorAction Stop
                
                Write-Host "[✓] $Computer - Updated successfully" -ForegroundColor Green
            } else {
                Write-Host "[✗] $Computer - Not reachable" -ForegroundColor Red
            }
        } catch {
            Write-Host "[✗] $Computer - Error: $_" -ForegroundColor Red
        }
    }
}

Write-Host ""
Write-Host "=== Update Complete ===" -ForegroundColor Green
Write-Host ""

if (!$DeployToComputers) {
    Write-Host "To apply changes locally, run:" -ForegroundColor Cyan
    Write-Host "  gpupdate /force" -ForegroundColor White
    Write-Host ""
    Write-Host "To deploy to other computers, run:" -ForegroundColor Cyan
    Write-Host "  .\Update-PDFAssociation.ps1 -PDFReader $PDFReader -DeployToComputers -Computers 'PC01','PC02','PC03'" -ForegroundColor White
}
```

**Usage Examples:**

```powershell
# Change to Foxit Reader (local only)
.\Update-PDFAssociation.ps1 -PDFReader Foxit

# Change to Adobe and deploy to specific computers
.\Update-PDFAssociation.ps1 -PDFReader Adobe -DeployToComputers -Computers "PC01","PC02","PC03"

# Change to Edge and deploy to computers from file
$ComputerList = Get-Content "C:\Scripts\Computers.txt"
.\Update-PDFAssociation.ps1 -PDFReader Edge -DeployToComputers -Computers $ComputerList
```

---

## Removing/Resetting File Associations

### Remove All Custom Associations (Reset to Windows Defaults)

```powershell
# Method 1: Disable Group Policy (if using GPO)
# 1. Open Group Policy Management
# 2. Edit the File Associations GPO
# 3. Set "Set a default associations configuration file" to "Not Configured"
# 4. Run: gpupdate /force

# Method 2: Via PowerShell (Local/Registry method)
Remove-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System" `
    -Name "DefaultAssociationsConfiguration" -Force -ErrorAction SilentlyContinue

Remove-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System" `
    -Name "NoDefaultBrowserReset" -Force -ErrorAction SilentlyContinue

# Force GP update
gpupdate /force

Write-Host "File association policy removed. Users will use Windows defaults." -ForegroundColor Green
```

### Remove Specific File Type Association

```powershell
# Backup current XML
Copy-Item C:\DefaultAssoc.xml "C:\DefaultAssoc_backup_$(Get-Date -Format 'yyyyMMdd_HHmmss').xml" -Force

# Edit the XML to remove specific association
$XMLPath = "C:\DefaultAssoc.xml"
$xml = [xml](Get-Content $XMLPath)

# Remove .pdf association
$xml.DefaultAssociations.Association = $xml.DefaultAssociations.Association | 
    Where-Object {$_.Identifier -ne ".pdf"}

Write-Host "Removed .pdf association from XML" -ForegroundColor Green

# Save
$xml.Save($XMLPath)

# Display remaining associations
Write-Host ""
Write-Host "Remaining associations:" -ForegroundColor Yellow
$xml.DefaultAssociations.Association | Format-Table Identifier, ProgId, ApplicationName -AutoSize

# Apply changes
gpupdate /force
```

### Reset User File Associations (Individual User)

```powershell
# Run as the affected user (NOT as Administrator)

# Method 1: Reset specific file type
Remove-Item "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\FileExts\.pdf" -Recurse -Force

# Method 2: Reset all user choice hashes
Remove-Item "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\FileExts\*\UserChoice" -Recurse -Force

# Method 3: Use Windows Settings
# Settings → Apps → Default apps → Reset

# Method 4: PowerShell command to open Settings
Start-Process ms-settings:defaultapps
```

### Complete Reset Script

Save as `Reset-FileAssociations.ps1`:

```powershell
#Requires -RunAsAdministrator

param(
    [switch]$RemovePolicy,
    [switch]$DeleteXML,
    [switch]$ResetAllUsers,
    [switch]$DeployToComputers,
    [string[]]$Computers = @()
)

Write-Host "=== File Association Reset Tool ===" -ForegroundColor Cyan
Write-Host ""

# Backup current configuration
$BackupDate = Get-Date -Format 'yyyyMMdd_HHmmss'
$BackupDir = "C:\FileAssociation_Backup_$BackupDate"
New-Item -Path $BackupDir -ItemType Directory -Force | Out-Null

# Backup XML
if (Test-Path "C:\DefaultAssoc.xml") {
    Copy-Item "C:\DefaultAssoc.xml" "$BackupDir\DefaultAssoc.xml" -Force
    Write-Host "[✓] Backup: Local XML saved to $BackupDir" -ForegroundColor Green
}

# Backup registry policy
$RegPath = "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System"
if (Test-Path $RegPath) {
    reg export "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" "$BackupDir\SystemPolicies.reg" /y | Out-Null
    Write-Host "[✓] Backup: Registry policies saved" -ForegroundColor Green
}

# Remove Policy setting
if ($RemovePolicy) {
    Write-Host "[Action] Removing registry policy..." -ForegroundColor Yellow
    
    Remove-ItemProperty -Path $RegPath -Name "DefaultAssociationsConfiguration" -Force -ErrorAction SilentlyContinue
    Remove-ItemProperty -Path $RegPath -Name "NoDefaultBrowserReset" -Force -ErrorAction SilentlyContinue
    
    Write-Host "[✓] Done: Registry policy removed" -ForegroundColor Green
}

# Delete XML files
if ($DeleteXML) {
    Write-Host "[Action] Deleting XML file..." -ForegroundColor Yellow
    
    if (Test-Path "C:\DefaultAssoc.xml") {
        Remove-Item "C:\DefaultAssoc.xml" -Force
        Write-Host "[✓] Done: Deleted C:\DefaultAssoc.xml" -ForegroundColor Green
    }
}

# Reset all user profiles
if ($ResetAllUsers) {
    Write-Host "[Action] Resetting all user profiles..." -ForegroundColor Yellow
    
    # Get all user profiles
    $UserProfiles = Get-ChildItem "C:\Users" -Directory | Where-Object {
        $_.Name -notin @('Public', 'Default', 'Default User')
    }
    
    foreach ($Profile in $UserProfiles) {
        $UserRegPath = Join-Path $Profile.FullName "NTUSER.DAT"
        
        if (Test-Path $UserRegPath) {
            $TempHive = "HKU\TempUser_$($Profile.Name)"
            
            # Load user hive
            reg load $TempHive $UserRegPath 2>$null
            
            if ($LASTEXITCODE -eq 0) {
                # Remove file associations
                reg delete "$TempHive\Software\Microsoft\Windows\CurrentVersion\Explorer\FileExts" /f 2>$null
                
                # Unload hive
                [gc]::Collect()
                Start-Sleep -Seconds 1
                reg unload $TempHive 2>$null
                
                Write-Host "  [✓] Reset: $($Profile.Name)" -ForegroundColor Gray
            }
        }
    }
    
    Write-Host "[✓] Done: All user profiles reset" -ForegroundColor Green
}

# Update Group Policy locally
Write-Host "[Action] Updating local Group Policy..." -ForegroundColor Yellow
gpupdate /force | Out-Null
Write-Host "[✓] Done: Group Policy updated" -ForegroundColor Green

# Deploy to remote computers
if ($DeployToComputers -and $Computers.Count -gt 0) {
    Write-Host ""
    Write-Host "[Action] Resetting remote computers..." -ForegroundColor Cyan
    
    foreach ($Computer in $Computers) {
        try {
            if (Test-Connection -ComputerName $Computer -Count 1 -Quiet) {
                Invoke-Command -ComputerName $Computer -ScriptBlock {
                    param($RemovePolicy, $DeleteXML)
                    
                    # Remove registry policy
                    if ($RemovePolicy) {
                        Remove-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System" `
                            -Name "DefaultAssociationsConfiguration" -Force -ErrorAction SilentlyContinue
                        Remove-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System" `
                            -Name "NoDefaultBrowserReset" -Force -ErrorAction SilentlyContinue
                    }
                    
                    # Delete XML
                    if ($DeleteXML) {
                        Remove-Item "C:\DefaultAssoc.xml" -Force -ErrorAction SilentlyContinue
                    }
                    
                    # Update GP
                    gpupdate /force | Out-Null
                    
                } -ArgumentList $RemovePolicy, $DeleteXML -ErrorAction Stop
                
                Write-Host "[✓] $Computer - Reset successfully" -ForegroundColor Green
            } else {
                Write-Host "[✗] $Computer - Not reachable" -ForegroundColor Red
            }
        } catch {
            Write-Host "[✗] $Computer - Error: $_" -ForegroundColor Red
        }
    }
}

Write-Host ""
Write-Host "=== Reset Complete ===" -ForegroundColor Green
Write-Host "Backup location: $BackupDir" -ForegroundColor Cyan
Write-Host ""
Write-Host "To restore from backup:" -ForegroundColor Yellow
Write-Host "  1. Copy XML: Copy-Item '$BackupDir\DefaultAssoc.xml' C:\ -Force" -ForegroundColor White
Write-Host "  2. Import registry: reg import '$BackupDir\SystemPolicies.reg'" -ForegroundColor White
Write-Host "  3. Update GP: gpupdate /force" -ForegroundColor White
Write-Host ""
```

**Usage Examples:**

```powershell
# Remove policy only (keep XML for reference)
.\Reset-FileAssociations.ps1 -RemovePolicy

# Remove everything (policy + XML)
.\Reset-FileAssociations.ps1 -RemovePolicy -DeleteXML

# Complete reset including all user profiles
.\Reset-FileAssociations.ps1 -RemovePolicy -DeleteXML -ResetAllUsers

# Reset on remote computers
.\Reset-FileAssociations.ps1 -RemovePolicy -DeleteXML -DeployToComputers -Computers "PC01","PC02","PC03"

# Reset on computers from file
$ComputerList = Get-Content "C:\Scripts\Computers.txt"
.\Reset-FileAssociations.ps1 -RemovePolicy -DeleteXML -DeployToComputers -Computers $ComputerList
```

### Emergency Rollback

```powershell
# Restore from backup
$BackupFile = "C:\FileAssociation_Backup_20240115_143022\DefaultAssoc.xml"

# Copy backup to active location
Copy-Item $BackupFile "C:\DefaultAssoc.xml" -Force

# Re-apply registry policy
$regPath = "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System"
New-ItemProperty -Path $regPath `
    -Name "DefaultAssociationsConfiguration" `
    -Value "C:\DefaultAssoc.xml" `
    -PropertyType String -Force

# Force GP update
gpupdate /force

Write-Host "Rollback complete. File associations restored from backup." -ForegroundColor Green
```

---

## Troubleshooting

### Issue: Associations Not Applying to New Users

**Diagnosis:**

```powershell
# Check if policy is configured
Get-ItemProperty "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System" -Name "DefaultAssociationsConfiguration" -ErrorAction SilentlyContinue

# Check if XML exists and is readable
Test-Path "C:\DefaultAssoc.xml"
Get-Content "C:\DefaultAssoc.xml"

# Check GP results on client
gpresult /H C:\GPReport.html
Start-Process C:\GPReport.html
```

**Solutions:**

```powershell
# 1. Verify XML path in registry matches actual file location
$regValue = Get-ItemProperty "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System" -Name "DefaultAssociationsConfiguration"
Write-Host "Registry points to: $($regValue.DefaultAssociationsConfiguration)" -ForegroundColor Yellow

if (Test-Path $regValue.DefaultAssociationsConfiguration) {
    Write-Host "XML file exists" -ForegroundColor Green
} else {
    Write-Host "XML file NOT found!" -ForegroundColor Red
}

# 2. Check NTFS permissions
icacls "C:\DefaultAssoc.xml"

# 3. Grant read permissions if needed
icacls "C:\DefaultAssoc.xml" /grant "Users:(R)"
icacls "C:\DefaultAssoc.xml" /grant "SYSTEM:(R)"

# 4. Force GP refresh
gpupdate /force

# 5. Restart explorer for current user
Stop-Process -Name explorer -Force
```

### Issue: Associations Work But Get Reset

**Diagnosis:**

```powershell
# Check Event Viewer for reset events
Get-WinEvent -LogName "Microsoft-Windows-Shell-Core/Operational" -MaxEvents 50 | 
    Where-Object {$_.Message -like "*association*" -or $_.Message -like "*default*"} |
    Format-Table TimeCreated, Message -Wrap
```

**Solution:**

```powershell
# Disable browser reset protection
$regPath = "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System"

if (!(Test-Path $regPath)) {
    New-Item -Path $regPath -Force | Out-Null
}

New-ItemProperty -Path $regPath `
    -Name "NoDefaultBrowserReset" `
    -Value 1 `
    -PropertyType DWord -Force

Write-Host "Browser reset protection disabled" -ForegroundColor Green

# Force GP update
gpupdate /force
```

### Issue: Specific Application Associations Don't Work

**Diagnosis:**

```powershell
# Check if ProgId exists in registry
$ProgId = "FoxitReader.Document"
$exists = Test-Path "HKLM:\SOFTWARE\Classes\$ProgId"

if ($exists) {
    Write-Host "ProgId '$ProgId' exists" -ForegroundColor Green
    
    # Get details
    Get-Item "HKLM:\SOFTWARE\Classes\$ProgId"
} else {
    Write-Host "ProgId '$ProgId' NOT found!" -ForegroundColor Red
    Write-Host "Application may not be installed or registered correctly" -ForegroundColor Yellow
}

# List all ProgIds for an application
Write-Host "`nSearching for Foxit ProgIds..." -ForegroundColor Cyan
Get-ChildItem "HKLM:\SOFTWARE\Classes" | 
    Where-Object {$_.PSChildName -like "*Foxit*"} |
    Select-Object PSChildName
```

**Solution:**

```powershell
# Find correct ProgId
# 1. Manually set the association on reference computer
# 2. Check the registry for the ProgId

$fileType = ".pdf"
$userChoice = Get-ItemProperty "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\FileExts\$fileType\UserChoice" -ErrorAction SilentlyContinue

if ($userChoice) {
    Write-Host "Current ProgId for $fileType : $($userChoice.ProgId)" -ForegroundColor Green
    
    # 3. Update XML with correct ProgId
    $xml = [xml](Get-Content C:\DefaultAssoc.xml)
    $assoc = $xml.DefaultAssociations.Association | Where-Object {$_.Identifier -eq $fileType}
    
    if ($assoc) {
        $assoc.ProgId = $userChoice.ProgId
        Write-Host "Updated XML with ProgId: $($userChoice.ProgId)" -ForegroundColor Green
    }
    
    $xml.Save("C:\DefaultAssoc.xml")
} else {
    Write-Host "No association set for $fileType" -ForegroundColor Yellow
}
```

### Issue: XML File is Corrupted

**Diagnosis:**

```powershell
# Validate XML
try {
    [xml]$xml = Get-Content C:\DefaultAssoc.xml
    Write-Host "XML is valid" -ForegroundColor Green
    Write-Host "Associations found: $($xml.DefaultAssociations.Association.Count)" -ForegroundColor Cyan
} catch {
    Write-Host "XML is corrupted!" -ForegroundColor Red
    Write-Host "Error: $_" -ForegroundColor Yellow
}
```

**Solution:**

```powershell
# Option 1: Re-export from a working computer
dism /Online /Export-DefaultAppAssociations:"C:\DefaultAssoc_New.xml"
Copy-Item C:\DefaultAssoc_New.xml C:\DefaultAssoc.xml -Force

# Option 2: Restore from backup
$backups = Get-ChildItem "C:\DefaultAssoc*.backup*" | Sort-Object LastWriteTime -Descending
if ($backups) {
    Write-Host "Available backups:" -ForegroundColor Cyan
    $backups | Format-Table Name, LastWriteTime -AutoSize
    
    # Restore most recent
    Copy-Item $backups[0].FullName "C:\DefaultAssoc.xml" -Force
    Write-Host "Restored from: $($backups[0].Name)" -ForegroundColor Green
}

# Option 3: Create minimal valid XML manually
$minimalXML = @'
<?xml version="1.0" encoding="UTF-8"?>
<DefaultAssociations>
  <Association Identifier=".pdf" ProgId="MSEdgePDF" ApplicationName="Microsoft Edge" />
  <Association Identifier=".html" ProgId="MSEdgeHTM" ApplicationName="Microsoft Edge" />
  <Association Identifier=".txt" ProgId="txtfile" ApplicationName="Notepad" />
</DefaultAssociations>
'@

Set-Content -Path "C:\DefaultAssoc.xml" -Value $minimalXML -Force
Write-Host "Created minimal XML file" -ForegroundColor Green
```

### Issue: Cannot Delete or Modify XML File

**Diagnosis:**

```powershell
# Check file locks
$file = "C:\DefaultAssoc.xml"

# Check who has the file open
$processes = Get-Process | Where-Object {
    try {
        $_.Modules | Where-Object {$_.FileName -eq $file}
    } catch {}
}

if ($processes) {
    Write-Host "File is locked by:" -ForegroundColor Yellow
    $processes | Format-Table ProcessName, Id -AutoSize
} else {
    Write-Host "File is not locked by any process" -ForegroundColor Green
}

# Check permissions
$acl = Get-Acl $file
$acl.Access | Format-Table IdentityReference, FileSystemRights, AccessControlType -AutoSize
```

**Solution:**

```powershell
# Take ownership and grant permissions
takeown /F "C:\DefaultAssoc.xml" /A

# Grant full control to Administrators
icacls "C:\DefaultAssoc.xml" /grant "Administrators:(F)"

# Now try to modify
$xml = [xml](Get-Content C:\DefaultAssoc.xml)
# Make your changes
$xml.Save("C:\DefaultAssoc.xml")
```

### Verification Script

Save as `Test-FileAssociations.ps1`:

```powershell
param(
    [string]$XMLPath = "C:\DefaultAssoc.xml"
)

Write-Host "=== File Association Verification ===" -ForegroundColor Cyan
Write-Host ""

# 1. Check Registry Policy
Write-Host "[1] Checking Group Policy Registry..." -ForegroundColor Yellow
$GPReg = Get-ItemProperty "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System" -Name "DefaultAssociationsConfiguration" -ErrorAction SilentlyContinue

if ($GPReg) {
    Write-Host "  [✓] Policy Configured" -ForegroundColor Green
    Write-Host "      Path: $($GPReg.DefaultAssociationsConfiguration)" -ForegroundColor Gray
    
    if ($GPReg.DefaultAssociationsConfiguration -eq $XMLPath) {
        Write-Host "      Matches expected path" -ForegroundColor Green
    } else {
        Write-Host "      WARNING: Path mismatch!" -ForegroundColor Yellow
        Write-Host "      Expected: $XMLPath" -ForegroundColor Yellow
    }
} else {
    Write-Host "  [✗] Policy NOT Configured" -ForegroundColor Red
}

# Check reset protection
$resetProtection = Get-ItemProperty "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System" -Name "NoDefaultBrowserReset" -ErrorAction SilentlyContinue
if ($resetProtection -and $resetProtection.NoDefaultBrowserReset -eq 1) {
    Write-Host "  [✓] Browser reset protection enabled" -ForegroundColor Green
} else {
    Write-Host "  [!] Browser reset protection not enabled" -ForegroundColor Yellow
}

# 2. Check XML File
Write-Host ""
Write-Host "[2] Checking XML File..." -ForegroundColor Yellow

if (Test-Path $XMLPath) {
    Write-Host "  [✓] File Exists: $XMLPath" -ForegroundColor Green
    
    # Check file size
    $fileInfo = Get-Item $XMLPath
    Write-Host "      Size: $($fileInfo.Length) bytes" -ForegroundColor Gray
    Write-Host "      Modified: $($fileInfo.LastWriteTime)" -ForegroundColor Gray
    
    # Validate XML
    try {
        [xml]$xml = Get-Content $XMLPath
        $count = $xml.DefaultAssociations.Association.Count
        Write-Host "  [✓] XML Valid: $count associations found" -ForegroundColor Green
    } catch {
        Write-Host "  [✗] XML Corrupted: $_" -ForegroundColor Red
    }
} else {
    Write-Host "  [✗] File NOT Found: $XMLPath" -ForegroundColor Red
}

# 3. Check Permissions
Write-Host ""
Write-Host "[3] Checking File Permissions..." -ForegroundColor Yellow

if (Test-Path $XMLPath) {
    $acl = Get-Acl $XMLPath
    
    # Check for Users read access
    $usersAccess = $acl.Access | Where-Object {
        ($_.IdentityReference -like "*Users*" -or $_.IdentityReference -like "*Everyone*") -and 
        $_.FileSystemRights -match "Read" -and
        $_.AccessControlType -eq "Allow"
    }
    
    if ($usersAccess) {
        Write-Host "  [✓] Users have Read permission" -ForegroundColor Green
    } else {
        Write-Host "  [!] Users may not have Read permission" -ForegroundColor Yellow
    }
    
    # Display all permissions
    Write-Host "      Current permissions:" -ForegroundColor Gray
    $acl.Access | Select-Object IdentityReference, FileSystemRights, AccessControlType | Format-Table -AutoSize
}

# 4. List Configured Associations
Write-Host ""
Write-Host "[4] Configured Associations:" -ForegroundColor Yellow

if (Test-Path $XMLPath) {
    try {
        [xml]$xml = Get-Content $XMLPath
        
        if ($xml.DefaultAssociations.Association.Count -gt 0) {
            $xml.DefaultAssociations.Association | 
                Sort-Object Identifier |
                Format-Table @{Label="File Type";Expression={$_.Identifier}}, 
                            @{Label="ProgId";Expression={$_.ProgId}}, 
                            @{Label="Application";Expression={$_.ApplicationName}} -AutoSize
        } else {
            Write-Host "  No associations found in XML" -ForegroundColor Yellow
        }
    } catch {
        Write-Host "  Cannot read associations from XML" -ForegroundColor Red
    }
}

# 5. Test Current User Associations
Write-Host ""
Write-Host "[5] Testing Current User's Associations..." -ForegroundColor Yellow

$testExtensions = @('.pdf', '.html', '.htm', '.txt', '.jpg', '.png')

foreach ($ext in $testExtensions) {
    $userChoice = Get-ItemProperty "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\FileExts\$ext\UserChoice" -ErrorAction SilentlyContinue
    
    if ($userChoice) {
        Write-Host "  $ext → $($userChoice.ProgId)" -ForegroundColor Gray
    } else {
        Write-Host "  $ext → [Not Set]" -ForegroundColor DarkGray
    }
}

# 6. Verify ProgIds Exist
Write-Host ""
Write-Host "[6] Verifying ProgIds are Registered..." -ForegroundColor Yellow

if (Test-Path $XMLPath) {
    [xml]$xml = Get-Content $XMLPath
    
    foreach ($assoc in $xml.DefaultAssociations.Association) {
        $progIdPath = "HKLM:\SOFTWARE\Classes\$($assoc.ProgId)"
        
        if (Test-Path $progIdPath) {
            Write-Host "  [✓] $($assoc.ProgId)" -ForegroundColor Green
        } else {
            Write-Host "  [✗] $($assoc.ProgId) - NOT FOUND (Application may not be installed)" -ForegroundColor Red
        }
    }
}

# 7. Check Recent Events
Write-Host ""
Write-Host "[7] Checking Recent Events..." -ForegroundColor Yellow

try {
    $events = Get-WinEvent -LogName "Microsoft-Windows-Shell-Core/Operational" -MaxEvents 10 -ErrorAction SilentlyContinue | 
        Where-Object {$_.Message -like "*association*" -or $_.Message -like "*default app*"}
    
    if ($events) {
        Write-Host "  Recent file association events found:" -ForegroundColor Yellow
        $events | Format-Table TimeCreated, Id, Message -Wrap -AutoSize
    } else {
        Write-Host "  No recent events found" -ForegroundColor Gray
    }
} catch {
    Write-Host "  Could not read event log" -ForegroundColor DarkGray
}

# Summary
Write-Host ""
Write-Host "=== Verification Complete ===" -ForegroundColor Cyan
Write-Host ""

# Provide recommendations
$issues = @()

if (!$GPReg) { $issues += "Registry policy not configured" }
if (!(Test-Path $XMLPath)) { $issues += "XML file missing" }

if ($issues.Count -gt 0) {
    Write-Host "Issues Found:" -ForegroundColor Red
    $issues | ForEach-Object { Write-Host "  • $_" -ForegroundColor Yellow }
    Write-Host ""
    Write-Host "Run the deployment script to fix these issues:" -ForegroundColor Cyan
    Write-Host "  .\Set-DefaultFileAssociations.ps1" -ForegroundColor White
} else {
    Write-Host "Configuration appears correct!" -ForegroundColor Green
    Write-Host "If associations still don't apply, try:" -ForegroundColor Cyan
    Write-Host "  • Run: gpupdate /force" -ForegroundColor White
    Write-Host "  • Sign out and sign back in" -ForegroundColor White
    Write-Host "  • Check if applications are installed" -ForegroundColor White
}

Write-Host ""
```

**Usage:**

```powershell
# Test default location
.\Test-FileAssociations.ps1

# Test custom location
.\Test-FileAssociations.ps1 -XMLPath "C:\CustomPath\FileAssoc.xml"

# Save results to file
.\Test-FileAssociations.ps1 | Out-File C:\FileAssoc_Report.txt

# Run on remote computer
Invoke-Command -ComputerName PC01 -FilePath .\Test-FileAssociations.ps1
```

---

## Reference Commands

### Quick Reference Table

| Task | Command |
|------|---------|
| Export associations | `dism /Online /Export-DefaultAppAssociations:"C:\DefaultAssoc.xml"` |
| View current PDF association | `Get-ItemProperty "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\FileExts\.pdf\UserChoice"` |
| List all ProgIds | `Get-ChildItem "HKLM:\SOFTWARE\Classes" \| Select-Object PSChildName` |
| Validate XML | `[xml]$xml = Get-Content C:\DefaultAssoc.xml` |
| Force GP update (local) | `gpupdate /force` |
| Force GP update (remote) | `Invoke-Command -ComputerName "PC01" -ScriptBlock {gpupdate /force}` |
| Check GP result | `gpresult /H C:\GPReport.html` |
| Reset user associations | `Remove-Item "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\FileExts\*\UserChoice" -Recurse -Force` |
| Copy XML to remote PC | `Copy-Item C:\DefaultAssoc.xml \\PC01\C$\DefaultAssoc.xml -Force` |
| Check file permissions | `icacls C:\DefaultAssoc.xml` |
| Grant read permissions | `icacls C:\DefaultAssoc.xml /grant "Users:(R)"` |

### Complete Workflow Script

Save as `Manage-FileAssociations.ps1`:

```powershell
#Requires -RunAsAdministrator

param(
    [Parameter(Mandatory=$true)]
    [ValidateSet("Export", "Deploy", "Update", "Verify", "Reset", "Backup")]
    [string]$Action,
    
    [string]$XMLPath = "C:\DefaultAssoc.xml",
    [string]$FileType = "",
    [string]$Application = "",
    [string[]]$Computers = @()
)

function Export-Associations {
    Write-Host "=== Exporting File Associations ===" -ForegroundColor Cyan
    Write-Host ""
    
    # Backup existing XML if it exists
    if (Test-Path $XMLPath) {
        $backup = "$XMLPath.backup_$(Get-Date -Format 'yyyyMMdd_HHmmss')"
        Copy-Item $XMLPath $backup -Force
        Write-Host "[✓] Backed up existing XML to: $backup" -ForegroundColor Green
    }
    
    # Export
    Write-Host "Exporting current user's file associations..." -ForegroundColor Yellow
    dism /Online /Export-DefaultAppAssociations:"$XMLPath"
    
    if ($LASTEXITCODE -eq 0) {
        Write-Host "[✓] Exported to: $XMLPath" -ForegroundColor Green
        
        # Display associations
        [xml]$xml = Get-Content $XMLPath
        $count = $xml.DefaultAssociations.Association.Count
        Write-Host "[✓] Found $count associations" -ForegroundColor Green
        Write-Host ""
        Write-Host "Associations:" -ForegroundColor Cyan
        $xml.DefaultAssociations.Association | 
            Format-Table @{Label="Type";Expression={$_.Identifier}}, 
                        @{Label="ProgId";Expression={$_.ProgId}}, 
                        @{Label="Application";Expression={$_.ApplicationName}} -AutoSize
    } else {
        Write-Error "Export failed. Make sure you have set file associations for this user."
        return
    }
}

function Deploy-Associations {
    Write-Host "=== Deploying File Associations ===" -ForegroundColor Cyan
    Write-Host ""
    
    # Verify XML exists
    if (!(Test-Path $XMLPath)) {
        Write-Error "XML file not found: $XMLPath"
        Write-Host "Run with -Action Export first" -ForegroundColor Yellow
        return
    }
    
    # Validate XML
    try {
        [xml]$xml = Get-Content $XMLPath
        $count = $xml.DefaultAssociations.Association.Count
        Write-Host "[✓] XML valid with $count associations" -ForegroundColor Green
    } catch {
        Write-Error "XML file is corrupted: $_"
        return
    }
    
    # Configure local registry
    Write-Host "Configuring local registry policy..." -ForegroundColor Yellow
    $regPath = "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System"
    
    if (!(Test-Path $regPath)) {
        New-Item -Path $regPath -Force | Out-Null
    }
    
    New-ItemProperty -Path $regPath `
        -Name "DefaultAssociationsConfiguration" `
        -Value $XMLPath `
        -PropertyType String -Force | Out-Null
    
    New-ItemProperty -Path $regPath `
        -Name "NoDefaultBrowserReset" `
        -Value 1 `
        -PropertyType DWord -Force | Out-Null
    
    Write-Host "[✓] Registry configured" -ForegroundColor Green
    
    # Set file permissions
    Write-Host "Setting file permissions..." -ForegroundColor Yellow
    icacls $XMLPath /grant "Users:(R)" /grant "SYSTEM:(R)" | Out-Null
    Write-Host "[✓] Permissions set" -ForegroundColor Green
    
    # Update Group Policy
    Write-Host "Updating Group Policy..." -ForegroundColor Yellow
    gpupdate /force | Out-Null
    Write-Host "[✓] Group Policy updated" -ForegroundColor Green
    
    Write-Host ""
    Write-Host "=== Local Deployment Complete ===" -ForegroundColor Green
    
    # Deploy to remote computers if specified
    if ($Computers.Count -gt 0) {
        Write-Host ""
        Write-Host "Deploying to remote computers..." -ForegroundColor Cyan
        
        foreach ($Computer in $Computers) {
            try {
                # Test connectivity
                if (!(Test-Connection -ComputerName $Computer -Count 1 -Quiet)) {
                    Write-Host "[✗] $Computer - Not reachable" -ForegroundColor Red
                    continue
                }
                
                # Copy XML file
                $destPath = "\\$Computer\C$\DefaultAssoc.xml"
                Copy-Item $XMLPath $destPath -Force -ErrorAction Stop
                
                # Configure registry remotely
                Invoke-Command -ComputerName $Computer -ScriptBlock {
                    param($XMLPath)
                    
                    $regPath = "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System"
                    
                    if (!(Test-Path $regPath)) {
                        New-Item -Path $regPath -Force | Out-Null
                    }
                    
                    New-ItemProperty -Path $regPath `
                        -Name "DefaultAssociationsConfiguration" `
                        -Value $XMLPath `
                        -PropertyType String -Force | Out-Null
                    
                    New-ItemProperty -Path $regPath `
                        -Name "NoDefaultBrowserReset" `
                        -Value 1 `
                        -PropertyType DWord -Force | Out-Null
                    
                    # Set permissions
                    icacls $XMLPath /grant "Users:(R)" /grant "SYSTEM:(R)" | Out-Null
                    
                    # Update GP
                    gpupdate /force | Out-Null
                    
                } -ArgumentList $XMLPath -ErrorAction Stop
                
                Write-Host "[✓] $Computer - Deployed successfully" -ForegroundColor Green
                
            } catch {
                Write-Host "[✗] $Computer - Error: $_" -ForegroundColor Red
            }
        }
    }
    
    Write-Host ""
    Write-Host "Next steps:" -ForegroundColor Cyan
    Write-Host "  • Create a new test user account" -ForegroundColor White
    Write-Host "  • Sign in and verify file associations" -ForegroundColor White
    Write-Host "  • Existing users: Sign out and back in" -ForegroundColor White
}

function Update-Association {
    if ([string]::IsNullOrEmpty($FileType) -or [string]::IsNullOrEmpty($Application)) {
        Write-Error "Please specify -FileType and -Application"
        Write-Host "Example: -FileType '.pdf' -Application 'FoxitReader.Document'" -ForegroundColor Yellow
        return
    }
    
    Write-Host "=== Updating File Association ===" -ForegroundColor Cyan
    Write-Host "File Type: $FileType" -ForegroundColor Yellow
    Write-Host "Application ProgId: $Application" -ForegroundColor Yellow
    Write-Host ""
    
    # Verify XML exists
    if (!(Test-Path $XMLPath)) {
        Write-Error "XML file not found: $XMLPath"
        return
    }
    
    # Backup
    $backup = "$XMLPath.backup_$(Get-Date -Format 'yyyyMMdd_HHmmss')"
    Copy-Item $XMLPath $backup -Force
    Write-Host "[✓] Backup created: $backup" -ForegroundColor Green
    
    # Load and update XML
    [xml]$xml = Get-Content $XMLPath
    
    $assoc = $xml.DefaultAssociations.Association | Where-Object {$_.Identifier -eq $FileType}
    
    if ($assoc) {
        $oldProgId = $assoc.ProgId
        $assoc.ProgId = $Application
        $assoc.ApplicationName = $Application
        Write-Host "[✓] Updated $FileType from '$oldProgId' to '$Application'" -ForegroundColor Green
    } else {
        # Create new association
        $newAssoc = $xml.CreateElement("Association")
        $newAssoc.SetAttribute("Identifier", $FileType)
        $newAssoc.SetAttribute("ProgId", $Application)
        $newAssoc.SetAttribute("ApplicationName", $Application)
        $xml.DefaultAssociations.AppendChild($newAssoc) | Out-Null
        Write-Host "[✓] Created new association for $FileType" -ForegroundColor Green
    }
    
    # Save
    $xml.Save($XMLPath)
    Write-Host "[✓] XML updated" -ForegroundColor Green
    
    # Display updated association
    Write-Host ""
    Write-Host "Updated association:" -ForegroundColor Cyan
    $xml.DefaultAssociations.Association | 
        Where-Object {$_.Identifier -eq $FileType} |
        Format-Table Identifier, ProgId, ApplicationName -AutoSize
    
    # Deploy to remote computers if specified
    if ($Computers.Count -gt 0) {
        Write-Host ""
        Write-Host "Deploying updated XML to remote computers..." -ForegroundColor Cyan
        
        foreach ($Computer in $Computers) {
            try {
                if (Test-Connection -ComputerName $Computer -Count 1 -Quiet) {
                    # Backup remote XML
                    $remoteXML = "\\$Computer\C$\DefaultAssoc.xml"
                    if (Test-Path $remoteXML) {
                        Copy-Item $remoteXML "$remoteXML.backup" -Force -ErrorAction SilentlyContinue
                    }
                    
                    # Copy updated XML
                    Copy-Item $XMLPath $remoteXML -Force
                    
                    # Force GP update
                    Invoke-Command -ComputerName $Computer -ScriptBlock {
                        gpupdate /force | Out-Null
                    }
                    
                    Write-Host "[✓] $Computer - Updated" -ForegroundColor Green
                } else {
                    Write-Host "[✗] $Computer - Not reachable" -ForegroundColor Red
                }
            } catch {
                Write-Host "[✗] $Computer - Error: $_" -ForegroundColor Red
            }
        }
    }
    
    Write-Host ""
    Write-Host "Update complete. Run 'gpupdate /force' to apply changes." -ForegroundColor Yellow
}

function Test-Associations {
    Write-Host "=== File Association Verification ===" -ForegroundColor Cyan
    Write-Host ""
    
    # 1. Check Registry
    Write-Host "[1] Registry Policy:" -ForegroundColor Yellow
    $gp = Get-ItemProperty "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System" -Name "DefaultAssociationsConfiguration" -ErrorAction SilentlyContinue
    
    if ($gp) {
        Write-Host "  [✓] Configured: $($gp.DefaultAssociationsConfiguration)" -ForegroundColor Green
        
        if ($gp.DefaultAssociationsConfiguration -ne $XMLPath) {
            Write-Host "  [!] WARNING: Path doesn't match expected location!" -ForegroundColor Yellow
            Write-Host "      Registry: $($gp.DefaultAssociationsConfiguration)" -ForegroundColor Gray
            Write-Host "      Expected: $XMLPath" -ForegroundColor Gray
        }
    } else {
        Write-Host "  [✗] Not configured" -ForegroundColor Red
    }
    
    # Check reset protection
    $reset = Get-ItemProperty "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System" -Name "NoDefaultBrowserReset" -ErrorAction SilentlyContinue
    if ($reset -and $reset.NoDefaultBrowserReset -eq 1) {
        Write-Host "  [✓] Reset protection enabled" -ForegroundColor Green
    } else {
        Write-Host "  [!] Reset protection not enabled" -ForegroundColor Yellow
    }
    
    # 2. Check XML
    Write-Host ""
    Write-Host "[2] XML File:" -ForegroundColor Yellow
    
    if (Test-Path $XMLPath) {
        $fileInfo = Get-Item $XMLPath
        Write-Host "  [✓] Exists: $XMLPath" -ForegroundColor Green
        Write-Host "      Size: $($fileInfo.Length) bytes" -ForegroundColor Gray
        Write-Host "      Modified: $($fileInfo.LastWriteTime)" -ForegroundColor Gray
        
        try {
            [xml]$xml = Get-Content $XMLPath
            Write-Host "  [✓] Valid XML with $($xml.DefaultAssociations.Association.Count) associations" -ForegroundColor Green
        } catch {
            Write-Host "  [✗] XML is corrupted: $_" -ForegroundColor Red
        }
    } else {
        Write-Host "  [✗] File not found: $XMLPath" -ForegroundColor Red
    }
    
    # 3. Check Permissions
    Write-Host ""
    Write-Host "[3] File Permissions:" -ForegroundColor Yellow
    
    if (Test-Path $XMLPath) {
        $acl = Get-Acl $XMLPath
        $usersRead = $acl.Access | Where-Object {
            ($_.IdentityReference -like "*Users*" -or $_.IdentityReference -like "*Everyone*") -and 
            $_.FileSystemRights -match "Read"
        }
        
        if ($usersRead) {
            Write-Host "  [✓] Users can read the file" -ForegroundColor Green
        } else {
            Write-Host "  [!] Users may not be able to read the file" -ForegroundColor Yellow
        }
    }
    
    # 4. List Associations
    if (Test-Path $XMLPath) {
        Write-Host ""
        Write-Host "[4] Configured Associations:" -ForegroundColor Yellow
        
        try {
            [xml]$xml = Get-Content $XMLPath
            $xml.DefaultAssociations.Association | 
                Sort-Object Identifier |
                Format-Table @{Label="File Type";Expression={$_.Identifier};Width=15}, 
                            @{Label="ProgId";Expression={$_.ProgId};Width=30}, 
                            @{Label="Application";Expression={$_.ApplicationName};Width=25} -AutoSize
        } catch {
            Write-Host "  Cannot read associations" -ForegroundColor Red
        }
    }
    
    # 5. Verify ProgIds
    Write-Host ""
    Write-Host "[5] Verifying Application Registrations:" -ForegroundColor Yellow
    
    if (Test-Path $XMLPath) {
        [xml]$xml = Get-Content $XMLPath
        
        foreach ($assoc in $xml.DefaultAssociations.Association) {
            $progIdExists = Test-Path "HKLM:\SOFTWARE\Classes\$($assoc.ProgId)"
            
            if ($progIdExists) {
                Write-Host "  [✓] $($assoc.ProgId.PadRight(30)) - Registered" -ForegroundColor Green
            } else {
                Write-Host "  [✗] $($assoc.ProgId.PadRight(30)) - NOT FOUND" -ForegroundColor Red
                Write-Host "      Application may not be installed" -ForegroundColor Gray
            }
        }
    }
    
    # Summary
    Write-Host ""
    Write-Host "=== Verification Complete ===" -ForegroundColor Cyan
}

function Reset-Associations {
    Write-Host "=== Resetting File Associations ===" -ForegroundColor Cyan
    Write-Host ""
    
    $confirm = Read-Host "This will remove all file association policies. Continue? (Y/N)"
    if ($confirm -ne 'Y' -and $confirm -ne 'y') {
        Write-Host "Cancelled" -ForegroundColor Yellow
        return
    }
    
    # Backup
    $backupDir = "C:\FileAssoc_Backup_$(Get-Date -Format 'yyyyMMdd_HHmmss')"
    New-Item -Path $backupDir -ItemType Directory -Force | Out-Null
    
    if (Test-Path $XMLPath) {
        Copy-Item $XMLPath "$backupDir\DefaultAssoc.xml" -Force
        Write-Host "[✓] Backed up XML to: $backupDir" -ForegroundColor Green
    }
    
    # Export registry
    reg export "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" "$backupDir\SystemPolicies.reg" /y | Out-Null
    Write-Host "[✓] Backed up registry to: $backupDir" -ForegroundColor Green
    
    # Remove registry settings
    Write-Host "Removing registry policies..." -ForegroundColor Yellow
    Remove-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System" -Name "DefaultAssociationsConfiguration" -Force -ErrorAction SilentlyContinue
    Remove-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System" -Name "NoDefaultBrowserReset" -Force -ErrorAction SilentlyContinue
    Write-Host "[✓] Registry policies removed" -ForegroundColor Green
    
    # Optionally delete XML
    $deleteXML = Read-Host "Delete XML file? (Y/N)"
    if ($deleteXML -eq 'Y' -or $deleteXML -eq 'y') {
        Remove-Item $XMLPath -Force -ErrorAction SilentlyContinue
        Write-Host "[✓] XML file deleted" -ForegroundColor Green
    }
    
    # Update GP
    Write-Host "Updating Group Policy..." -ForegroundColor Yellow
    gpupdate /force | Out-Null
    Write-Host "[✓] Group Policy updated" -ForegroundColor Green
    
    # Reset on remote computers if specified
    if ($Computers.Count -gt 0) {
        Write-Host ""
        Write-Host "Resetting remote computers..." -ForegroundColor Cyan
        
        foreach ($Computer in $Computers) {
            try {
                if (Test-Connection -ComputerName $Computer -Count 1 -Quiet) {
                    Invoke-Command -ComputerName $Computer -ScriptBlock {
                        Remove-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System" -Name "DefaultAssociationsConfiguration" -Force -ErrorAction SilentlyContinue
                        Remove-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System" -Name "NoDefaultBrowserReset" -Force -ErrorAction SilentlyContinue
                        Remove-Item "C:\DefaultAssoc.xml" -Force -ErrorAction SilentlyContinue
                        gpupdate /force | Out-Null
                    }
                    
                    Write-Host "[✓] $Computer - Reset" -ForegroundColor Green
                } else {
                    Write-Host "[✗] $Computer - Not reachable" -ForegroundColor Red
                }
            } catch {
                Write-Host "[✗] $Computer - Error: $_" -ForegroundColor Red
            }
        }
    }
    
    Write-Host ""
    Write-Host "=== Reset Complete ===" -ForegroundColor Green
    Write-Host "Backup location: $backupDir" -ForegroundColor Cyan
    Write-Host ""
    Write-Host "To restore:" -ForegroundColor Yellow
    Write-Host "  reg import '$backupDir\SystemPolicies.reg'" -ForegroundColor White
    Write-Host "  Copy-Item '$backupDir\DefaultAssoc.xml' C:\ -Force" -ForegroundColor White
    Write-Host "  gpupdate /force" -ForegroundColor White
}

function Backup-Associations {
    Write-Host "=== Creating Backup ===" -ForegroundColor Cyan
    Write-Host ""
    
    $backupDir = "C:\FileAssoc_Backup_$(Get-Date -Format 'yyyyMMdd_HHmmss')"
    New-Item -Path $backupDir -ItemType Directory -Force | Out-Null
    
    # Backup XML
    if (Test-Path $XMLPath) {
        Copy-Item $XMLPath "$backupDir\DefaultAssoc.xml" -Force
        Write-Host "[✓] XML backed up" -ForegroundColor Green
    } else {
        Write-Host "[!] XML file not found: $XMLPath" -ForegroundColor Yellow
    }
    
    # Backup registry
    reg export "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" "$backupDir\SystemPolicies.reg" /y | Out-Null
    Write-Host "[✓] Registry backed up" -ForegroundColor Green
    
    # Create restore script
    $restoreScript = @"
# File Association Restore Script
# Created: $(Get-Date)

Write-Host "Restoring file associations..." -ForegroundColor Cyan

# Restore XML
Copy-Item "$(Split-Path $backupDir)\$(Split-Path $backupDir -Leaf)\DefaultAssoc.xml" "C:\DefaultAssoc.xml" -Force
Write-Host "XML restored" -ForegroundColor Green

# Restore registry
reg import "$(Split-Path $backupDir)\$(Split-Path $backupDir -Leaf)\SystemPolicies.reg"
Write-Host "Registry restored" -ForegroundColor Green

# Update GP
gpupdate /force
Write-Host "Group Policy updated" -ForegroundColor Green

Write-Host ""
Write-Host "Restore complete!" -ForegroundColor Green
"@
    
    Set-Content "$backupDir\Restore.ps1" -Value $restoreScript -Force
    Write-Host "[✓] Restore script created" -ForegroundColor Green
    
    Write-Host ""
    Write-Host "Backup location: $backupDir" -ForegroundColor Cyan
    Write-Host "To restore, run: $backupDir\Restore.ps1" -ForegroundColor Yellow
}

# Execute requested action
switch ($Action) {
    "Export" { Export-Associations }
    "Deploy" { Deploy-Associations }
    "Update" { Update-Association }
    "Verify" { Test-Associations }
    "Reset" { Reset-Associations }
    "Backup" { Backup-Associations }
}
```

**Usage Examples:**

```powershell
# 1. Export associations from current user
.\Manage-FileAssociations.ps1 -Action Export

# 2. Deploy to local computer
.\Manage-FileAssociations.ps1 -Action Deploy

# 3. Deploy to local and remote computers
.\Manage-FileAssociations.ps1 -Action Deploy -Computers "PC01","PC02","PC03"

# 4. Update specific file type
.\Manage-FileAssociations.ps1 -Action Update -FileType ".pdf" -Application "FoxitReader.Document"

# 5. Update and deploy to remote computers
.\Manage-FileAssociations.ps1 -Action Update -FileType ".pdf" -Application "Acrobat.Document.DC" -Computers "PC01","PC02"

# 6. Verify configuration
.\Manage-FileAssociations.ps1 -Action Verify

# 7. Create backup
.\Manage-FileAssociations.ps1 -Action Backup

# 8. Reset everything
.\Manage-FileAssociations.ps1 -Action Reset

# 9. Reset on remote computers
.\Manage-FileAssociations.ps1 -Action Reset -Computers "PC01","PC02"

# 10. Use custom XML path
.\Manage-FileAssociations.ps1 -Action Verify -XMLPath "C:\CustomPath\MyAssoc.xml"
```

### Bulk Deployment Script

Save as `Deploy-ToMultipleComputers.ps1`:

```powershell
#Requires -RunAsAdministrator

param(
    [Parameter(Mandatory=$true)]
    [string]$ComputerListPath,
    
    [string]$XMLPath = "C:\DefaultAssoc.xml"
)

# Verify XML exists
if (!(Test-Path $XMLPath)) {
    Write-Error "XML file not found: $XMLPath"
    Exit 1
}

# Read computer list
if (!(Test-Path $ComputerListPath)) {
    Write-Error "Computer list not found: $ComputerListPath"
    Exit 1
}

$computers = Get-Content $ComputerListPath

Write-Host "=== Bulk Deployment ===" -ForegroundColor Cyan
Write-Host "XML Source: $XMLPath" -ForegroundColor Yellow
Write-Host "Computers: $($computers.Count)" -ForegroundColor Yellow
Write-Host ""

$results = @()

foreach ($computer in $computers) {
    $result = [PSCustomObject]@{
        Computer = $computer
        Status = "Unknown"
        Message = ""
    }
    
    Write-Host "Processing: $computer..." -ForegroundColor Cyan
    
    try {
        # Test connectivity
        if (!(Test-Connection -ComputerName $computer -Count 1 -Quiet)) {
            $result.Status = "Offline"
            $result.Message = "Computer not reachable"
            Write-Host "  [✗] Offline" -ForegroundColor Red
            $results += $result
            continue
        }
        
        # Copy XML
        $destPath = "\\$computer\C$\DefaultAssoc.xml"
        Copy-Item $XMLPath $destPath -Force -ErrorAction Stop
        Write-Host "  [✓] XML copied" -ForegroundColor Green
        
        # Configure registry
        Invoke-Command -ComputerName $computer -ScriptBlock {
            param($XMLPath)
            
            $regPath = "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System"
            
            if (!(Test-Path $regPath)) {
                New-Item -Path $regPath -Force | Out-Null
            }
            
            New-ItemProperty -Path $regPath `
                -Name "DefaultAssociationsConfiguration" `
                -Value $XMLPath `
                -PropertyType String -Force | Out-Null
            
            New-ItemProperty -Path $regPath `
                -Name "NoDefaultBrowserReset" `
                -Value 1 `
                -PropertyType DWord -Force | Out-Null
            
            icacls $XMLPath /grant "Users:(R)" | Out-Null
            
            gpupdate /force | Out-Null
            
        } -ArgumentList $XMLPath -ErrorAction Stop
        
        Write-Host "  [✓] Configuration applied" -ForegroundColor Green
        
        $result.Status = "Success"
        $result.Message = "Deployed successfully"
        
    } catch {
        $result.Status = "Failed"
        $result.Message = $_.Exception.Message
        Write-Host "  [✗] Error: $($_.Exception.Message)" -ForegroundColor Red
    }
    
    $results += $result
}

# Display summary
Write-Host ""
Write-Host "=== Deployment Summary ===" -ForegroundColor Cyan
Write-Host ""

$results | Format-Table Computer, Status, Message -AutoSize

$successCount = ($results | Where-Object {$_.Status -eq "Success"}).Count
$failedCount = ($results | Where-Object {$_.Status -eq "Failed"}).Count
$offlineCount = ($results | Where-Object {$_.Status -eq "Offline"}).Count

Write-Host ""
Write-Host "Total Computers: $($results.Count)" -ForegroundColor Cyan
Write-Host "Success: $successCount" -ForegroundColor Green
Write-Host "Failed: $failedCount" -ForegroundColor Red
Write-Host "Offline: $offlineCount" -ForegroundColor Yellow

# Export results
$reportPath = "C:\FileAssoc_Deployment_$(Get-Date -Format 'yyyyMMdd_HHmmss').csv"
$results | Export-Csv $reportPath -NoTypeInformation
Write-Host ""
Write-Host "Report saved to: $reportPath" -ForegroundColor Cyan
```

**Usage:**

```powershell
# Create computer list file
@"
PC001
PC002
PC003
LAPTOP-01
DESKTOP-05
"@ | Out-File C:\Computers.txt

# Run deployment
.\Deploy-ToMultipleComputers.ps1 -ComputerListPath C:\Computers.txt

# With custom XML path
.\Deploy-ToMultipleComputers.ps1 -ComputerListPath C:\Computers.txt -XMLPath C:\CustomAssoc.xml
```

---

## Best Practices

1. **Always backup before making changes**
   - Create backups of XML files with timestamps
   - Export registry before modifications
   - Keep a change log

2. **Test changes on a reference computer first**
   - Create test user accounts
   - Verify associations apply correctly
   - Check for conflicts with existing software

3. **Use version control for XML files**
   ```powershell
   # Example versioning
   Copy-Item C:\DefaultAssoc.xml "C:\FileAssoc_Archive\DefaultAssoc_v1.0_$(Get-Date -Format 'yyyyMMdd').xml"
   ```

4. **Document your ProgIds**
   ```powershell
   # Create a reference document
   $doc = @"
   File Association Reference
   Generated: $(Get-Date)
   
   Application Versions:
   - Adobe Acrobat DC: 2023.001.20093
   - Google Chrome: 120.0.6099.129
   - VLC Media Player: 3.0.18
   
   ProgIds:
   .pdf  → Acrobat.Document.DC
   .html → ChromeHTML
   .mp4  → VLC.mp4
   "@
   
   Set-Content "C:\FileAssoc_Documentation.txt" -Value $doc
   ```

5. **Monitor and maintain**
   ```powershell
   # Schedule regular verification
   $trigger = New-ScheduledTaskTrigger -Daily -At 9am
   $action = New-ScheduledTaskAction -Execute "PowerShell.exe" -Argument "-File C:\Scripts\Manage-FileAssociations.ps1 -Action Verify"
   Register-ScheduledTask -TaskName "Verify File Associations" -Trigger $trigger -Action $action -RunLevel Highest
   ```

6. **Keep XML file in a consistent location**
   - Always use `C:\DefaultAssoc.xml`
   - Don't change paths after deployment
   - Ensures consistency across all systems

7. **Set proper permissions**
   ```powershell
   # Ensure XML is readable but not writable by users
   icacls C:\DefaultAssoc.xml /inheritance:r
   icacls C:\DefaultAssoc.xml /grant "SYSTEM:(F)"
   icacls C:\DefaultAssoc.xml /grant "Administrators:(F)"
   icacls C:\DefaultAssoc.xml /grant "Users:(R)"
   ```

---

## Support and Maintenance

### Regular Maintenance Tasks

```powershell
# Weekly: Verify configuration
.\Manage-FileAssociations.ps1 -Action Verify

# After application updates: Re-export and compare
$oldXML = [xml](Get-Content C:\DefaultAssoc.xml)
dism /Online /Export-DefaultAppAssociations:"C:\DefaultAssoc_New.xml"
$newXML = [xml](Get-Content C:\DefaultAssoc_New.xml)

# Compare
Compare-Object $oldXML.DefaultAssociations.Association.ProgId $newXML.DefaultAssociations.Association.ProgId

# Monthly: Create backup
.\Manage-FileAssociations.ps1 -Action Backup

# Quarterly: Clean up old backups
Get-ChildItem "C:\FileAssoc_Backup_*" | 
    Where-Object {$_.LastWriteTime -lt (Get-Date).AddMonths(-3)} | 
    Remove-Item -Recurse -Force
```

### Monitoring Script

Save as `Monitor-FileAssociations.ps1`:

```powershell
#Requires -RunAsAdministrator

param(
    [string[]]$Computers = @(),
    [switch]$EmailReport,
    [string]$EmailTo = "",
    [string]$SMTPServer = ""
)

$results = @()

if ($Computers.Count -eq 0) {
    $Computers = @($env:COMPUTERNAME)
}

foreach ($computer in $Computers) {
    $result = [PSCustomObject]@{
        Computer = $computer
        XMLExists = $false
        XMLValid = $false
        PolicyConfigured = $false
        AssociationCount = 0
        LastModified = $null
        Issues = @()
    }
    
    try {
        if ($computer -eq $env:COMPUTERNAME) {
            # Local check
            $xmlPath = "C:\DefaultAssoc.xml"
            
            # Check XML
            if (Test-Path $xmlPath) {
                $result.XMLExists = $true
                $fileInfo = Get-Item $xmlPath
                $result.LastModified = $fileInfo.LastWriteTime
                
                try {
                    [xml]$xml = Get-Content $xmlPath
                    $result.XMLValid = $true
                    $result.AssociationCount = $xml.DefaultAssociations.Association.Count
                } catch {
                    $result.Issues += "XML is corrupted"
                }
            } else {
                $result.Issues += "XML file missing"
            }
            
            # Check policy
            $policy = Get-ItemProperty "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System" -Name "DefaultAssociationsConfiguration" -ErrorAction SilentlyContinue
            if ($policy) {
                $result.PolicyConfigured = $true
                
                if ($policy.DefaultAssociationsConfiguration -ne $xmlPath) {
                    $result.Issues += "Policy path mismatch"
                }
            } else {
                $result.Issues += "Policy not configured"
            }
            
        } else {
            # Remote check
            $info = Invoke-Command -ComputerName $computer -ScriptBlock {
                $xmlPath = "C:\DefaultAssoc.xml"
                $data = @{
                    XMLExists = (Test-Path $xmlPath)
                    XMLValid = $false
                    PolicyConfigured = $false
                    AssociationCount = 0
                    LastModified = $null
                    Issues = @()
                }
                
                if ($data.XMLExists) {
                    $fileInfo = Get-Item $xmlPath
                    $data.LastModified = $fileInfo.LastWriteTime
                    
                    try {
                        [xml]$xml = Get-Content $xmlPath
                        $data.XMLValid = $true
                        $data.AssociationCount = $xml.DefaultAssociations.Association.Count
                    } catch {
                        $data.Issues += "XML corrupted"
                    }
                } else {
                    $data.Issues += "XML missing"
                }
                
                $policy = Get-ItemProperty "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System" -Name "DefaultAssociationsConfiguration" -ErrorAction SilentlyContinue
                if ($policy) {
                    $data.PolicyConfigured = $true
                } else {
                    $data.Issues += "Policy not configured"
                }
                
                return $data
            }
            
            $result.XMLExists = $info.XMLExists
            $result.XMLValid = $info.XMLValid
            $result.PolicyConfigured = $info.PolicyConfigured
            $result.AssociationCount = $info.AssociationCount
            $result.LastModified = $info.LastModified
            $result.Issues = $info.Issues
        }
        
    } catch {
        $result.Issues += "Error: $_"
    }
    
    $results += $result
}

# Display results
Write-Host "=== File Association Monitoring Report ===" -ForegroundColor Cyan
Write-Host "Generated: $(Get-Date)" -ForegroundColor Gray
Write-Host ""

$results | Format-Table Computer, XMLExists, XMLValid, PolicyConfigured, AssociationCount, @{Label="Issues";Expression={$_.Issues -join "; "}} -AutoSize

# Count issues
$problemComputers = $results | Where-Object {$_.Issues.Count -gt 0}

if ($problemComputers) {
    Write-Host ""
    Write-Host "Computers with issues: $($problemComputers.Count)" -ForegroundColor Yellow
    
    $problemComputers | ForEach-Object {
        Write-Host "  $($_.Computer):" -ForegroundColor Red
        $_.Issues | ForEach-Object {
            Write-Host "    - $_" -ForegroundColor Yellow
        }
    }
} else {
    Write-Host ""
    Write-Host "All computers configured correctly!" -ForegroundColor Green
}

# Save report
$reportPath = "C:\FileAssoc_MonitoringReport_$(Get-Date -Format 'yyyyMMdd_HHmmss').csv"
$results | Export-Csv $reportPath -NoTypeInformation
Write-Host ""
Write-Host "Report saved: $reportPath" -ForegroundColor Cyan

# Email report if requested
if ($EmailReport -and $EmailTo -and $SMTPServer) {
    $emailBody = $results | ConvertTo-Html -Head "<style>table {border-collapse: collapse;} th, td {border: 1px solid black; padding: 5px;}</style>" | Out-String
    
    Send-MailMessage -To $EmailTo `
        -From "fileassoc-monitor@yourdomain.com" `
        -Subject "File Association Monitoring Report - $(Get-Date -Format 'yyyy-MM-dd')" `
        -Body $emailBody `
        -BodyAsHtml `
        -SmtpServer $SMTPServer `
        -Attachments $reportPath
    
    Write-Host "Email sent to: $EmailTo" -ForegroundColor Green
}
```

**Usage:**

```powershell
# Monitor local computer
.\Monitor-FileAssociations.ps1

# Monitor multiple computers
.\Monitor-FileAssociations.ps1 -Computers "PC01","PC02","PC03"

# Monitor and email report
.\Monitor-FileAssociations.ps1 -Computers (Get-Content C:\Computers.txt) -EmailReport -EmailTo "admin@company.com" -SMTPServer "smtp.company.com"
```

---

## Troubleshooting Quick Reference

| Problem | Quick Fix |
|---------|-----------|
| XML missing | `dism /Online /Export-DefaultAppAssociations:"C:\DefaultAssoc.xml"` |
| Policy not set | `New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System" -Name "DefaultAssociationsConfiguration" -Value "C:\DefaultAssoc.xml" -Force` |
| Associations reset | `New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System" -Name "NoDefaultBrowserReset" -Value 1 -PropertyType DWord -Force` |
| ProgId not found | Check if application is installed and registered |
| XML corrupted | Restore from backup or re-export |
| Permissions issue | `icacls C:\DefaultAssoc.xml /grant "Users:(R)"` |

---

**Document Version:** 2.0  
**Last Updated:** May 2026 
**Tested On:** Windows 11 Eductaion
**Storage Location:** `C:\DefaultAssoc.xml` (local)

---

