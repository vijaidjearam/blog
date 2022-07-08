---
layout: post
date: 2022-07-08 10:14:08
title: PartitionWizard Clean Uninstall
category: Windows apps
tags: windows partition
---

# Partition Wizard Clean uninstall

Uninstall partition wizard via chocolatey doesnt do a clean job.

The updatechecker.exe is still left in the PC  in "C:\Program Files\MiniTool Partition Wizard 12"

There is also a scheduled task that needs to be deleted and Which in turn pops up an error message during the startup. 

There error is as foolows: An error has occured in the script on this page.
![googl script error](https://user-images.githubusercontent.com/1507737/177950506-9ec5e5b3-0e24-4c4c-bc23-eee14fced492.jpg)

## Solution:
Delete the scheduled task with the following command:

```
cmd /k schtasks /delete /tn MiniToolPartitionWizard /f
```

Kill the updatechecker which is running in the background:

```
cmd /k taskkill /F /IM updatechecker.exe
```

Delete the folder from program files:

```
cmd /k rmdir /Q /S "C:\Program Files\MiniTool Partition Wizard 12"
```
Thats it there you go :smiley:




