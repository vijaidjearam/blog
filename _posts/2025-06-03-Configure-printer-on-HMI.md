---
layout: post
date: 2025-06-03 12:04:00
title: How to Configure network printer on HMI
category: tia-portal
tags: tia-portal windows siemens
---

## Setting Up a Network Printer on HMI
Source : [Link](https://cache.industry.siemens.com/dl/files/478/92346478/att_1054936/v2/92346478_TCP_IP_Networks_Panel_DOC_v2.0.en.pdf)

- Our architecture is as below:
![image](https://github.com/user-attachments/assets/fb27b6c1-680e-4467-9ec0-e7c1eb009f69)

## Instructions: Printing via network
• Call up the control Panel.
• Open the "Printer Properties" dialog with the "Printer" icon.

– Select the printer type "PCL Laser" under "Printer Language".

– Select the interface "PrintServer" under "Port".

– Enter the network address of the print server under "IP:Port:".

Note:
Many printers have an integrated "print server". This allows you to specify the address of
the printer directly.

Note the correct notation -> "IP address:port number"
(colon between the IP address and port number (Link))
Example: 172.16.34.30:9100

– Select the paper size in the "Paper Size" drop-down box.

– Specify the orientation of the printout under "Orientation":
"Portrait" for vertical format
"Landscape" for horizontal format

– Select the print quality:

To print in draft quality, select "Draft Mode".

To print in color, select "Color".

– Confirm the entries with "OK".

![image](https://github.com/user-attachments/assets/05a0c3a3-022f-4e06-bab6-f3228921f742)

- make sure the network configuration of the HMI is from DHCP
- Note: if IP is set manually , do not forget to fill in the default gateway address. 
