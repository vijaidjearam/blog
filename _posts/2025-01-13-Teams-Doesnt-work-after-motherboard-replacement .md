---
layout: post
date: 2025-01-13 08:54:06
title: Microsoft 365 Apps activation error: Trusted Platform Module malfunctioned
category: Microsoft365 
tags: Teams microsoft365
---

## When a motherboard has been replaced, Teams may not work and could give you an error stating "Trusted Platform Module malfunctioned"

Source: [Link](https://learn.microsoft.com/en-us/office/troubleshoot/activation/tpm-malfunctioned)

Here are the steps followed to fix the issue:

Remove Office credentials:
 - From Start, type credential manager, and then select Credential Manager from the search results.
 - Select Windows credentials.
 - If there are any credentials for MicrosoftOffice16, select the arrow next to them and then select Remove.
 - Close Credential Manager.
 - From Start, select Settings (the gear icon) > Accounts > Access work or school.
 - If the account you use to sign in to office.com is listed there, but it isn’t the account you use to sign in to Windows, select it, and then select Disconnect.
 - Restart the device and try to activate Microsoft 365 again.

Clear the Trusted Platform Module (TPM):
 - From Start, select Settings (the gear icon) > Update & Security > Windows Security > Device Security.
 - Under Security processor, select Security processor details > Security processor troubleshooting.
 - Select Clear TPM.
 - Restart the device and try to activate Microsoft 365 again.

Remove the device from the Active directory and join it again.

Check BrokerPlugin process:
 - Open File Explorer, and put the following location in the address bar: %LOCALAPPDATA%\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Accounts
 - Press CTRL + A to select all.
 - Right-click in the selected files and choose Delete.
 - Put the following location in the File Explorer address bar: %LOCALAPPDATA%\Packages\Microsoft.Windows.CloudExperienceHost_cw5n1h2txyewy\AC\TokenBroker\Accounts
 - Select all files and delete them.
 - Restart the device.
