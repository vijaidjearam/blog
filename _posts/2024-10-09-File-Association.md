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
