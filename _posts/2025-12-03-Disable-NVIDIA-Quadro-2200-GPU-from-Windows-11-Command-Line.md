---
date: 2025-12-03 17:08:00
title: Disable NVIDIA Quadro 2200 GPU from Windows 11 Command Line
category: Nvidia
tags: sinutrain Nvidia windows
---

# Disable NVIDIA Quadro 2200 GPU from Windows 11 Command Line

## ✅ Step 1 --- Install DevCon

DevCon is included with the **Windows Driver Kit (WDK)**.

After installation, DevCon is typically located at:

    C:\Program Files (x86)\Windows Kits\10\Tools\x64\devcon.exe

## ✅ Step 2 --- Use the Device Instance ID

Your GPU hardware ID:

    PCI\VEN_10DE&DEV_1C31&SUBSYS_131B1028&REV_A1

## ✅ Step 3 --- Open Command Prompt as Administrator

Search for **cmd**, right‑click, choose **Run as administrator**.

## ✅ Step 4 --- Disable the GPU

Run:

    devcon disable "PCI\VEN_10DE&DEV_1C31&SUBSYS_131B1028&REV_A1"

## 🔄 Re-enable Later
 
    devcon enable "PCI\VEN_10DE&DEV_1C31&SUBSYS_131B1028&REV_A1"

## ℹ️ Notes

✔ If the exact ID doesn’t match, try using a wildcard:
    
    devcon disable "PCI\VEN_10DE&DEV_1C31*"

✔ List matching NVIDIA devices:

    devcon find *VEN_10DE*

✔ Wildcard disable:

    devcon disable "PCI\VEN_10DE&DEV_1C31*"

✔ To get the full instance ID from CMD:

    devmgmt.msc
    → View → Devices by connection → Properties → Details → “Device instance path”.