---
layout: post
date: 2025-05-06 08:49:00
title: Change the Metrics of the Network adapater
category: network
tags: network powershell
---

## Powershell script to change the metrics of the network adapter

Some software, such as Siemens, installs virtual network adapters. By default, Windows automatically manages network metrics, which can cause delays during startup. In some cases, the system attempts to obtain a network address for the virtual adapter before the physical one, significantly delaying network availability for users. To address this issue, the following script manually sets the network metric values to prioritize the physical adapter.


```Powershell
# Following pre requisites are required:
# The network cards should be named properly 
# Here in my scenario I have named the physical cards as Pedagogie which I could address it in the script.
# Define the remote computer name
$remotePC = "PC 01"

# Define the adapter names and desired metric values
$pedagogieAdapterName = "Pedagogie"
$siemensAdapterName = "Ethernet 2"

$pedagogieMetricValue = 1
$siemensMetricValue = 100

# Execute the following block remotely
Invoke-Command -ComputerName $remotePC -ScriptBlock {
    param($pedagogieAdapterName, $siemensAdapterName, $pedagogieMetricValue, $siemensMetricValue)

    # Get the network adapter interfaces
    $pedagogieInterface = Get-NetAdapter -Name $pedagogieAdapterName -ErrorAction Stop
    $siemensInterface = Get-NetAdapter -Name $siemensAdapterName -ErrorAction Stop

    # Set the new metric values
    Set-NetIPInterface -InterfaceIndex $pedagogieInterface.ifIndex -InterfaceMetric $pedagogieMetricValue
    Set-NetIPInterface -InterfaceIndex $siemensInterface.ifIndex -InterfaceMetric $siemensMetricValue

    Write-Output "$comp : Metric for adapter '$pedagogieAdapterName' set to $pedagogieMetricValue."
    Write-Output "$comp : Metric for adapter '$siemensAdapterName' set to $siemensMetricValue."
} -ArgumentList $pedagogieAdapterName, $siemensAdapterName, $pedagogieMetricValue, $siemensMetricValue

```