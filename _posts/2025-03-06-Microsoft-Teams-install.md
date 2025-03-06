---
layout: post
date: 2025-03-06 10:24:20
title: Deploy the new Microsoft Teams client
category: teams
tags: teams windows microsoft
---

Source: [link](https://learn.microsoft.com/en-us/microsoftteams/new-teams-bulk-install-client#option-1b-download-and-install-new-teams-using-an-offline-installer)


## Option 1A: Download and install new Teams for a single computer
To install new Teams on a single computer with many users, follow these steps:
 - Download the .exe installer. If you have downloaded this file previously confirm you have the latest version by comparing the properties on each file.
 - Open the Command Prompt as an Admin.
 - At the prompt enter: .\teamsbootstrapper.exe -p
A success or fail status displays. If you receive an error, learn more at Common HRESULT values.


## Option 1B: Download and install new Teams using an offline installer
Admins can also use a local teams MSIX to provision new Teams. This option minimizes the amount of bandwidth used for the initial installation. The MSIX can exist in a local path or UNC.

Download the .exe installer.. If you have downloaded this file previously confirm you have the latest version by comparing the properties on each file.
Download the MSIX:
- MSIX x86
- MSIX x64
- ARM64
Open the Command Prompt as an Admin.
Depending on where your MSIX is located, do the following:
For local path, enter: .\teamsbootstrapper.exe -p -o "c:\path\to\teams.msix"

Example:

local path location for offline installer

For UNC, enter: .\teamsbootstrapper.exe -p -o "\unc\path\to\teams.msix"

Example:

offline location using unc

## Option B: Upgrade to the new Teams across your organization
To deploy this installer to a group of computers, or your entire organization, follow these steps:

Download the .exe installer. If you have downloaded this file previously confirm you have the latest version by comparing the properties on each file.
Use Intune, Microsoft Endpoint Configuration Manager, Group Policy, or third-party distribution software, to distribute the installer to your target computers.
Run the installer on each computer.
