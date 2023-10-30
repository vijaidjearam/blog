---
layout: post
date: 2021-12-06 16:33:00
title: Select the active network card adapter of the pc
category: Powershell
tags: powershell networking
---
```
Get-WmiObject -Class Win32_NetworkAdapterConfiguration -ComputerName $env:COMPUTERNAME |  where { $_.IpAddress -eq ([System.Net.Dns]::GetHostByName($Inputmachine).AddressList[0]).IpAddressToString }

# to get the mac address of the machine
(Get-WmiObject -Class Win32_NetworkAdapterConfiguration -ComputerName $env:COMPUTERNAME |  where { $_.IpAddress -eq ([System.Net.Dns]::GetHostByName($Inputmachine).AddressList[0]).IpAddressToString }).MACAddress

# to Enable netbios 

(Get-WmiObject -Class Win32_NetworkAdapterConfiguration -ComputerName $env:COMPUTERNAME |  where { $_.IpAddress -eq ([System.Net.Dns]::GetHostByName($Inputmachine).AddressList[0]).IpAddressToString }).settcpipnetbios(1)

# to disable netbios 
(Get-WmiObject -Class Win32_NetworkAdapterConfiguration -ComputerName $env:COMPUTERNAME |  where { $_.IpAddress -eq ([System.Net.Dns]::GetHostByName($Inputmachine).AddressList[0]).IpAddressToString }).settcpipnetbios(2)

# To change the network adapater name
Get-NetAdapter -InterfaceIndex (Get-WmiObject -Class Win32_NetworkAdapterConfiguration -ComputerName $env:COMPUTERNAME |  where { $_.IpAddress -eq ([System.Net.Dns]::GetHostByName($Inputmachine).AddressList[0]).IpAddressToString }).InterfaceIndex | Rename-NetAdapter -NewName pedagogie

# To run the above command from command line

powershell.exe -command "Get-NetAdapter -InterfaceIndex (Get-WmiObject -Class Win32_NetworkAdapterConfiguration -ComputerName $env:COMPUTERNAME |  where { $_.IpAddress -eq ([System.Net.Dns]::GetHostByName($Inputmachine).AddressList[0]).IpAddressToString }).InterfaceIndex | Rename-NetAdapter -NewName pedagogie"

```
