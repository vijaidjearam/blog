---
layout: post
date: 2024-01-20 23:23:17
title: HyperV isolated network with internet access
category: HyperV
tags: Hyperv virtualisation
---
# HyperV isolated network with internet access

source : [petri.com](https://petri.com/using-nat-virtual-switch-hyper-v)

```powershell
New-VMSwitch -SwitchName “NATSwitch” -SwitchType Internal
# get-netadapter to find the interface index for the next set of commands
Get-NetAdapter
New-NetIPAddress -IPAddress 10.0.0.1 -PrefixLength 24 -InterfaceIndex 49
New-NetNat -Name NatNetwork -InternalIPInterfaceAddressPrefix 10.0.0.0/24
```
![image](https://petri-media.s3.amazonaws.com/2017/02/Hyper-VNATSwitch.png)
