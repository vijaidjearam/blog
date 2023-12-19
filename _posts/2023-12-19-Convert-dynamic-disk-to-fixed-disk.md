---
layout: post
date: 2023-12-19 10:03:22
title: Convert from Dynamic VHD/VHDX Disk Format to / from Fixed in Hyper-V
category: Hyper-v
tags: virtualization
---

Convert from Dynamic VHD/VHDX Disk Format to / from Fixed in Hyper-V

```powershell
Convert-VHD –Path c:\VM\my-vhdx.vhdx –DestinationPath c:\New-VM\new-vhdx.vhdx –VHDType Dynamic
```
