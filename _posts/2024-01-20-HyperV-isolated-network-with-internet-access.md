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
New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 49
New-NetNat -Name NatNetwork -InternalIPInterfaceAddressPrefix 192.168.0.0./24
```
![image](https://petri-media.s3.amazonaws.com/2017/02/Hyper-VNATSwitch.png)

Any virtual machine that runs on the virtual switch will use an IPv4 address in the 192.168.0.0 address range. The machines will route to the LAN via the management OS NIC and NAT, the same way that your laptop or tablet accesses the Internet via the router in your home. As far as the LAN is concerned, these machines are accessing the LAN from the single LAN IP address of the host.
