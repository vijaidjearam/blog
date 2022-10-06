---
layout: post
date: 2022-10-06 17:09:00
title: Factory Reset your Aastra phone
category: phone
tags: phone aastra
---

# How to factory reset an Aastra 6 series
[source](https://www.3cx.com/sip-phones/factory-reset-aastra/) 
![image](https://user-images.githubusercontent.com/1507737/194350694-8dd3bb6f-3929-4860-bb45-87a79626cb9e.png)
* Applicable to Aastra 6751i, 6753i, 6755i, 6757i, 6730i, 6731i, 6739i
* Factory Reset your Aastra phone
  * If you know the Admin Password
  * If you do not know the Admin Password
Important: These phones are outdated and no longer supported by 3CX. This guide is available for informational purposes only and will not be updated

## Applicable to Aastra 6751i, 6753i, 6755i, 6757i, 6730i, 6731i, 6739i
Follow the steps below to factory reset your Aastra device in order to bring back the factory default settings. This must be done before provisioning your Aastra phone in case the device has residual settings of a previous configuration.

There are 2 ways for doing this depending on whether you know the admin password or not.

## Factory Reset your Aastra phone

If you know the Admin Password
![image](https://user-images.githubusercontent.com/1507737/194351132-d79c6855-56ec-4ac8-b4a3-e22cf5cfe787.png)
* Press **“Setting”:**
* Press **“Admin Menu (5)”** (using default pass: 22222) > **“Factory Defaults (4)”** > **“Restore Defaults.”**
* After the restart, your phone will be successfully reset

If you do not know the Admin Password
* Download the most recent firmware for your phone version (from AASTRA website) and unzip the actual firmware file under the root location (ex: C:\tftp).

![image](https://user-images.githubusercontent.com/1507737/194351315-d33a1ee3-bd09-4057-b84b-faa88006ca04.png)
* Download and install PumpKIN TFTP application.
* Connect an ethernet cable from the **“PC”** port on the phone directly to your machine:

![image](https://user-images.githubusercontent.com/1507737/194351486-926c0183-989b-454f-ac1c-99b91a3b4b52.png)
  * By holding down keys **“1”** and **“#”** plug in the Power DC on the phone and boot it up until it gets in Web Recovery mode.

![image](https://user-images.githubusercontent.com/1507737/194351596-204d1d60-f929-4ca8-af8d-201bb1d953f2.png)
  * As soon as the IP address appears (ex: 192.168.9.21), configure your local machine with Static IP address to be in the same subnet (ex: 192.168.9.22).
* Start TFTP, select **“Option menu”** > **“Server”** tab, and under **“TFTP download path”** point towards the location of your firmware folder (ex: C:\tftp).

![image](https://user-images.githubusercontent.com/1507737/194351777-78852d18-cff6-4fb1-b1d3-883ee6f58eda.png)

* Press **“OK”** and make sure the check box “Server is running” is ticked.
* Point your web browser to the IP address displayed on the phone:
  * **“Filename”** = firmware file with its extension type ONLY (ex: 51.st), **“Upgrade Type”** = **“Firmware”**, **“Download Protocol”** = **“TFTP”**, **“TFTP Server”** = IP address of your local machine where firmware is located and TFTP Server **“PumpKIN”** is waiting to accept connection and send file (ex: 192.168.9.22),

![image](https://user-images.githubusercontent.com/1507737/194351920-231a6c36-bc59-40a9-bebc-e538792be450.png)
  * Press **“Download Firmware”** button to complete the process.
