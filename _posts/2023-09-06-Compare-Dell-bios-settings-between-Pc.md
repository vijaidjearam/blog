---
layout: post
date: 2023-09-06 15:44:55
title: Compare Dell Bios parameters between Pcs
category: BIOS
tags: dell bios
---

This Powershell script compares the parameters of Bios between Pcs

```
Function Get_Dell_BIOS_Settings
	{
		BEGIN {}
		PROCESS 
			{
			$Script:Selected_Manufacturer = "Dell"
			If (Get-Module -ListAvailable -Name DellBIOSProvider)
				{} 
			Else 
				{
					Install-Module -Name DellBIOSProvider -Force -SkipPublisherCheck -Confirm:$false
				}		 
			get-command -module DellBIOSProvider | out-null
			$Script:Get_BIOS_Settings = get-childitem -path DellSmbios:\ | select-object category | 
			foreach {
			get-childitem -path @("DellSmbios:\" + $_.Category)  | select-object attribute, currentvalue 
			# get-childitem -path @("DellSmbios:\" + $_.Category)  | select attribute, currentvalue, possiblevalues, PSPath 
			}		
				$Script:Get_BIOS_Settings = $Get_BIOS_Settings |  % { New-Object psobject -Property @{
					Setting = $_."attribute"
					Value = $_."currentvalue"
					}}  | select-object Setting, Value 
				$Get_BIOS_Settings
			}
		END{ }			
	}

#Compare-Object Get_Dell_BIOS_Settings (Invoke-Command -ComputerName "test" Get_Dell_BIOS_Settings) -IncludeEqual
```
