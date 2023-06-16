---
layout: post
date: 2023-06-15 09:11:00
title: Tia-portal-Please-reboot-before-installing-any-more-programs
category: tia-portal
tags: tia-portal windows install
---
# Message "Please reboot before installing any more programs"

source: [link](https://support.industry.siemens.com/cs/document/8861819/message-please-reboot-before-installing-any-more-programs-?dti=0&lc=en-BH)

How do you disable the message "Please reboot before installing any more programs"? 

You can solve the problems by making changes in the Registry.

Description
The Microsoft Windows operating system has registered one or more write-protected files for deleting or renaming. 
This is the cause of the behavior described above and cannot be influenced by the relevant software.

Note
In general, no warranty can be given for direct changes made in the Registry because this is entirely the responsibility of the user. 
In any event, you are advised to make a backup of the Registry before undertaking the actions described here. Furthermore, these settings are computer-specific. 
This means that when you copy the project to another computer, you must make the settings again.


Instructions

* Open the MS Windows Registry Editor by simultaneously pressing the "[Windows] + [R]" buttons.
* Enter "regedit" in the input field and click OK.
* In the Registry Editor you navigate to the path "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Session Manager".
* Check whether the value name "PendingFileRenameOperations" is in the path.
* Delete the entire "PendingFileRenameOperations" entry and then close Registry Editor (not just the contents).
* After that, close the registry editor.
Then immediately start the program installation without prior restarting.
