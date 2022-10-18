---
layout: post
date: 2022-10-17 15:08:24
title: HP BIOS Settings Management
category: BIOS
tags: bios hp
---
HP BIOS Settings Management
[source](https://www.configjon.com/hp-bios-settings-management/)

JULY 29, 2019 BY JON ANDERSON
HP BIOS Settings Management
This post was updated on September 18th, 2020.

This post is one of 3 posts in my series on managing BIOS settings using PowerShell. I’ve also written about Dell and Lenovo. In this post I’ll be talking about using PowerShell to manage HP BIOS settings.

The script can be downloaded from my GitHub: https://github.com/ConfigJon/Firmware-Management/tree/master/HP

HP, WMI, and PowerShell
HP provides a WMI interface that can be used for querying and modifying BIOS settings on their hardware models. This means that we can use PowerShell to directly view and edit BIOS settings without the need for a vendor specific program. This script uses 3 of the HP provided WMI classes.

The first WMI class is HP_BIOSEnumeration. It is located in the root\HP\InstrumentedBIOS namespace. This class is used to return a list of the commonly configurable BIOS settings on a device. This list would include things like power settings, TPM settings, and Secure Boot settings to name a few examples.

```
#Connect to the HP_BIOSEnumeration WMI class
$SettingList = Get-WmiObject -Namespace root\HP\InstrumentedBIOS -Class HP_BIOSEnumeration
 
#Return a list of all configurable settings
$SettingList | Select-Object Name,Value
 
#Return the current and available values for a specific setting
($SettingList | Where-Object Name -eq "Deep Sleep").Value
```

#Connect to the HP_BIOSEnumeration WMI class
$SettingList = Get-WmiObject -Namespace root\HP\InstrumentedBIOS -Class HP_BIOSEnumeration
 
#Return a list of all configurable settings
$SettingList | Select-Object Name,Value
 
#Return the current and available values for a specific setting
($SettingList | Where-Object Name -eq "Deep Sleep").Value
The second WMI class is HP_BIOSSetting. It is located in the root\HP\InstrumentedBIOS namespace. This class is used to return a list of all BIOS settings on a device. This class contains all of the same things as the HP_BIOSEnumeration class, but it also contains extra information like the serial number, UUID, and MAC address. The reason this class is used, is for querying the BIOS password status. The HP_BIOSEnumeration class does not contain the password settings.

```
#Connect to the HP_BIOSSetting WMI class
$HPBiosSettings = Get-WmiObject -Namespace root\HP\InstrumentedBIOS -Class HP_BIOSSetting
 
#Return a list of all settings
$HPBiosSettings | Select-Object Name,Value
 
#Check the status of the setup password
($HPBiosSetting | Where-Object Name -eq "Setup Password").IsSet
```

#Connect to the HP_BIOSSetting WMI class
$HPBiosSettings = Get-WmiObject -Namespace root\HP\InstrumentedBIOS -Class HP_BIOSSetting
 
#Return a list of all settings
$HPBiosSettings | Select-Object Name,Value
 
#Check the status of the setup password
($HPBiosSetting | Where-Object Name -eq "Setup Password").IsSet
The third WMI class is HP_BIOSSettingInterface. It is located in the root\HP\InstrumentedBIOS namespace. This class is used to set or change BIOS settings.

```
#Connect to the HP_BIOSSettingInterface WMI class
$Interface = Get-WmiObject -Namespace root\HP\InstrumentedBIOS -Class HP_BIOSSettingInterface
 
#Set a specific value for a specific setting when a BIOS password is not set
$Interface.SetBIOSSetting("Deep Sleep","On")
 
#Set a specific value for a specific setting when a BIOS password is set
$Interface.SetBIOSSetting("Deep Sleep","On","<utf-16/>" + "Password")
```

#Connect to the HP_BIOSSettingInterface WMI class
$Interface = Get-WmiObject -Namespace root\HP\InstrumentedBIOS -Class HP_BIOSSettingInterface
 
#Set a specific value for a specific setting when a BIOS password is not set
$Interface.SetBIOSSetting("Deep Sleep","On")
 
#Set a specific value for a specific setting when a BIOS password is set
$Interface.SetBIOSSetting("Deep Sleep","On","<utf-16/>" + "Password")
The HP_BIOSSettingInterface WMI class contains a method called SetBIOSSetting. This method allows for changing HP BIOS settings.

For reference, these are the possible return codes for the SetBIOSSetting method:

0 – Success
1 – Not Supported
2 – Unspecified Error
3 – Timeout
4 – Failed (Usually caused by a typo in the setting value)
5 – Invalid Parameter
6 – Access Denied (Usually caused by an incorrect BIOS password)
For more detailed information on the HP WMI interface, refer to the official documentation: http://h20331.www2.hp.com/Hpsub/downloads/cmi_whitepaper.pdf

Manage-HPBiosSettings.ps1
This script takes the basic commands and adds logic to allow for a more automated settings management process. The script has four parameters.

GetSettings – Use this parameter to instruct the script to generate a list of all current BIOS settings. The settings will be displayed to the screen by default.
SetSettings – Use this parameter to instruct the script to set specific BIOS settings. Settings can be specified either in the body of the script or from a CSV file.
CsvPath – Use this parameter to specify the location of a CSV file. If used with the GetSettings switch, this acts as the location where a list of current BIOS settings will be saved. If used with the SetSettings switch, this acts as the location where the script will read BIOS settings to be set from. Using this switch with the SetSettings switch will also cause the script to ignore any settings specified in the body of the script.
SetupPassword – Used to specify the BIOS password
When using the script to set settings, the list of settings can either be specified in the script itself or in a CSV file. To specify settings in the script, look for the $Settings array near the top of the script. The settings should be in the format of “Setting Name,Setting Value”
```
#List of settings to be configured =================================
#===================================================================
$Settings = (
    "Deep S3,Off",
    "Deep Sleep,Off",
    "S4/S5 Max Power Savings,Disable",
    "S5 Maximum Power Savings,Disable",
    "Fingerprint Device,Disable",
    "Num Lock State at Power-On,Off",
    "NumLock on at boot,Disable",
    "Numlock state at boot,Off",
    "Prompt for Admin password on F9 (Boot Menu),Enable",
    "Prompt for Admin password on F11 (System Recovery),Enable",
    "Prompt for Admin password on F12 (Network Boot),Enable",
    "PXE Internal IPV4 NIC boot,Enable",
    "PXE Internal IPV6 NIC boot,Enable",
    "PXE Internal NIC boot,Enable",
    "Wake On LAN,Boot to Hard Drive",
    "Swap Fn and Ctrl (Keys),Disable"
)
#===================================================================
#===================================================================
```


#List of settings to be configured =================================
#===================================================================
$Settings = (
    "Deep S3,Off",
    "Deep Sleep,Off",
    "S4/S5 Max Power Savings,Disable",
    "S5 Maximum Power Savings,Disable",
    "Fingerprint Device,Disable",
    "Num Lock State at Power-On,Off",
    "NumLock on at boot,Disable",
    "Numlock state at boot,Off",
    "Prompt for Admin password on F9 (Boot Menu),Enable",
    "Prompt for Admin password on F11 (System Recovery),Enable",
    "Prompt for Admin password on F12 (Network Boot),Enable",
    "PXE Internal IPV4 NIC boot,Enable",
    "PXE Internal IPV6 NIC boot,Enable",
    "PXE Internal NIC boot,Enable",
    "Wake On LAN,Boot to Hard Drive",
    "Swap Fn and Ctrl (Keys),Disable"
)
#===================================================================
#===================================================================
A full list of configurable settings can be exported from a device by calling the script with the GetSettings parameter. The CsvPath parameter can also be specified to output the list of settings to a CSV file.

You can then sort through the exported settings and either save them as a CSV file or add them to the $Settings array in the body of the script.

When the script runs, it will write to a log file. By default, this log file will be named Manage-HPBiosSettings.Log. If the script is being run during a task sequence, the log file will be located in the _SMSTSLogPath. Otherwise, the log file will be located in ProgramData\ConfigJonScripts\HP. The log file name and path can be changed using the LogFile parameter. Note that the log file path will always be set to _SMSTSLogPath when run during a task sequence.

The script has logic built-in to detect if settings were already set correctly, were successfully set, failed to set, or were not found on the device. The script will output these counts to the screen at the end. More detailed information about the settings will be written to the log file.



I’ve noticed that over the years, HP BIOS setting names have not remained consistent. Because of this, there can be multiple different ways to specify the same setting across different HP models. I have included a few example settings files in my GitHub. These settings files contain commonly configured HP BIOS settings that cover a wide range of HP hardware models.

Settings_CSV_SecureBoot,csv – Contains settings for enabling UEFI and Secure Boot
Settings_CSV_TPM,csv – Contains settings for enabling and activating TPM
Settings_CSV_General.csv – Contains other common settings
Settings_In-Script_All.txt – Contains common settings formatted for use in the body of the script
Examples
The script can be run as a standalone script in Windows, or as a part of a Configuration Manager task sequence. It can also be run in the full Windows OS or in WinPE.

Here are a few examples of calling the script from a PowerShell prompt.


```
#Set BIOS settings supplied in the script
Manage-HPBiosSettings.ps1 -SetSettings -SetupPassword ExamplePassword
 
#Set BIOS settings supplied in a CSV file
Manage-HPBiosSettings.ps1 -SetSettings -CsvPath C:\Temp\Settings.csv -SetupPassword ExamplePassword
 
#Output a list of current BIOS settings to the screen
Manage-HPBiosSettings.ps1 -GetSettings
 
#Output a list of current BIOS settings to a CSV file
Manage-HPBiosSettings.ps1 -GetSettings -CsvPath C:\Temp\Settings.csv

```


Here is an example of calling the script during a task sequence. In this example the settings are specified in the body of the script, so the script can be stored directly in the task sequence step. Also the setup password is set, so the SetupPassword parameter is specified.




In this second example, the script is being called from a package and the settings are being supplied from a CSV file.


Additional Reading
If you’re looking for other options for managing HP BIOS settings, check out these links. The HP BIOS Configuration Utility is a tool provided by HP, it’s been around for years, and works well. The HP Client Management Script Library contains PowerShell scripts written by HP to manage BIOS settings. This is a good place to start if you want to write your own HP PowerShell solution. For information on configuring HP BIOS passwords using PowerShell, see my post HP BIOS Password Management.
