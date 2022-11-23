---
layout: post
date: 2022-11-23 14:28:43
title: Deploy Roboguide package
category: Software-package
tags: software roboguide package
---

# Deploying Roboguide
## Autoit script to install Roboguide

Here is the Autoit Script to automate the installation process of RoboGuide : Install.au3

```
;AutoItSetOption ("TrayIconDebug", 1)
;install FRVRC940
#include <MsgBoxConstants.au3>
Local $iPID = Run(@ScriptDir & '.\FRVRC940\setup.exe')
ConsoleWrite($iPID & @LF)
WinWait("FANUC Robotics Virtual Robot Controller V9.40 Setup","Welcome to the setup for FANUC Robotics Virtual Robot Controller V9.40 9.40188.31.05")
ControlClick("FANUC Robotics Virtual Robot Controller V9.40 Setup","","Button1")
WinWait("FANUC Robotics Virtual Robot Controller V9.40 Setup","Choose Destination Location")
ControlClick("FANUC Robotics Virtual Robot Controller V9.40 Setup","","Button1")
WinWait("FANUC Robotics Virtual Robot Controller - InstallShield Wizard","Select the setup type that best suits your needs")
ControlClick("FANUC Robotics Virtual Robot Controller - InstallShield Wizard","","Button4")
WinWait("FANUC Robotics Virtual Robot Controller V9.40 Setup")
ControlClick("FANUC Robotics Virtual Robot Controller V9.40 Setup","","Button1")
WinWait("FANUC Robotics Shared Utils - InstallShield Wizard","The setup was not able to open permissions on")
ControlClick("FANUC Robotics Shared Utils - InstallShield Wizard","","Button1")
WinWait("FANUC Robotics Virtual Robot Controller - InstallShield Wizard","C:\ProgramData\FANUC\FRVRC Media\V9.40")
ControlClick("FANUC Robotics Virtual Robot Controller - InstallShield Wizard","","Button1")
WinWait("FANUC Robotics Virtual Robot Controller V9.40 terminating.","The setup was not able to open permissions")
ConsoleWrite("Closing process : setup" & @LF)
ProcessClose($iPID)
Local $aProcessList = ProcessList("setup.exe")
ConsoleWrite($aProcessList & @LF)
For $i = 1 To $aProcessList[0][0]
	;MsgBox($MB_SYSTEMMODAL, "", $aProcessList[$i][0] & @CRLF & "PID: " & $aProcessList[$i][1])
	ProcessClose($aProcessList[$i][1])
Next
AutoItSetOption ("WinTitleMatchMode", 3)
;install RoboGuide
ConsoleWrite("Pausing the script for the GUI to Load....." & @LF)
Sleep(10000)
Local $iPID = Run(@ScriptDir & '\setup.exe')
ConsoleWrite("Entering Install RoboGuide" & @LF)
ConsoleWrite("Pausing the script for the GUI to Load....." & @LF)
Sleep(10000)
ConsoleWrite("Starting Install RoboGuide" & @LF)
;ConsoleWrite($iPID)
if (WinExists("FANUC ROBOGUIDE","InstallShield Wizard Complete")) Then
	ConsoleWrite("Checking If Restart Required and taking necessary action"& @LF)
	WinWait("FANUC ROBOGUIDE","InstallShield Wizard Complete")
	ControlCommand("FANUC ROBOGUIDE","No, I will restart my computer later.","[CLASS:Button; INSTANCE:2]","Check","")
	ControlClick("FANUC ROBOGUIDE","Finish","[CLASS:Button; INSTANCE:4]")
	ConsoleWrite("Pausing the script for the GUI to Load....." & @LF)
	Sleep(10000)
	Local $iPID = Run(@ScriptDir & '\setup.exe')
	ConsoleWrite("Pausing the script for the GUI to Load....." & @LF)
	Sleep(10000)
EndIf
if (WinExists("ROBOGUIDE BootStrapper Setup","The following components will be installed on your machine:")) Then
	ConsoleWrite("Installing Pre-requistes necessary for the Roboguide Install" & @LF)
	WinWait("ROBOGUIDE BootStrapper Setup","The following components will be installed on your machine:")
	ControlClick("ROBOGUIDE BootStrapper Setup","&Install","[CLASS:Button; INSTANCE:3]")
	WinWait("ROBOGUIDE BootStrapper Setup","Setup must reboot before proceeding")
	ControlClick("ROBOGUIDE BootStrapper Setup","No","[CLASS:Button; INSTANCE:1]")
	WinWait("ROBOGUIDE Prerequisites Reboot required","The pre-installation process requires a machine reboot. After rebooting, you must re-run this setup to continue the ROBOGUIDE installation")
	ControlCommand("ROBOGUIDE Prerequisites Reboot required","No, I will restart my computer later","[CLASS:Button; INSTANCE:2]","Check","")
	ControlClick("ROBOGUIDE Prerequisites Reboot required","OK","[CLASS:Button; INSTANCE:3]")
	ConsoleWrite("Pausing the script for the GUI to Load....." & @LF)
	Sleep(10000)
	Run(@ScriptDir & '\setup.exe')
	ConsoleWrite("Pausing the script for the GUI to Load....." & @LF)
	Sleep(10000)
EndIf
if (WinExists("FANUC ROBOGUIDE","InstallShield Wizard Complete")) Then
	ConsoleWrite("Checking If Restart Required and taking necessary action"& @LF)
	WinWait("FANUC ROBOGUIDE","InstallShield Wizard Complete")
	ControlCommand("FANUC ROBOGUIDE","No, I will restart my computer later.","[CLASS:Button; INSTANCE:2]","Check","")
	ControlClick("FANUC ROBOGUIDE","Finish","[CLASS:Button; INSTANCE:4]")
	ConsoleWrite("Pausing the script for the GUI to Load....." & @LF)
	Sleep(10000)
	Run(@ScriptDir & '\setup.exe')
	ConsoleWrite("Pausing the script for the GUI to Load....." & @LF)
	Sleep(10000)
EndIf
ConsoleWrite("Begining Installation of RoboGuide, this could take couple of minutes...." & @LF)
WinWait("ROBOGUIDE Setup")
ControlClick("ROBOGUIDE Setup","&Next >","[CLASS:Button; INSTANCE:1]")
WinWait("ROBOGUIDE Setup","LICENSE AGREEMENT")
ControlClick("ROBOGUIDE Setup","","[CLASS:Button; INSTANCE:2]")
WinWait("ROBOGUIDE Setup","Setting destination path")
ControlClick("ROBOGUIDE Setup","","[CLASS:Button; INSTANCE:1]")
WinWait("FANUC ROBOGUIDE","Check which Process Plug-ins to install")
ControlClick("FANUC ROBOGUIDE","","[CLASS:Button; INSTANCE:2]")
WinWait("FANUC ROBOGUIDE","Check which Utility Plug-ins to install")
ControlClick("FANUC ROBOGUIDE","","[CLASS:Button; INSTANCE:2]")
WinWait("FANUC ROBOGUIDE","Check which additional application features you want to install")
ControlClick("FANUC ROBOGUIDE","","[CLASS:Button; INSTANCE:2]")
WinWait("ROBOGUIDE Setup","FANUC Robotics Virtual Robot Selection")
ControlClick("ROBOGUIDE Setup","","[CLASS:Button; INSTANCE:4]")
WinWait("ROBOGUIDE Setup","Start Copying Files")
ControlClick("ROBOGUIDE Setup","","[CLASS:Button; INSTANCE:1]")
WinWait("FANUC Robotics Shared Utils - InstallShield Wizard")
ControlClick("FANUC Robotics Shared Utils - InstallShield Wizard","","[CLASS:Button; INSTANCE:1]")
; Fanuc Robotics Robot Neighborhood Setup
WinWait("FANUC Robotics Shared Utils - InstallShield Wizard")
ControlClick("FANUC Robotics Shared Utils - InstallShield Wizard","","[CLASS:Button; INSTANCE:1]")
;Fanuc ROBOGUIDE 3D Player
WinWait("FANUC ROBOGUIDE 3D Player")
WinWait("ROBOGUIDE 3D Player - InstallShield Wizard")
ControlClick("ROBOGUIDE 3D Player - InstallShield Wizard","","[CLASS:Button; INSTANCE:1]")
;Fanuc Robotics Virtual Robot Controller V9.30 Setup
;WinWait("FANUC Robotics Virtual Robot Controller V9.30 Setup")
WinWait("FANUC Robotics Shared Utils - InstallShield Wizard")
ControlClick("FANUC Robotics Shared Utils - InstallShield Wizard","","[CLASS:Button; INSTANCE:1]")
WinWait("FANUC Robotics Virtual Robot Controller - InstallShield Wizard")
ControlClick("FANUC Robotics Virtual Robot Controller - InstallShield Wizard","","[CLASS:Button; INSTANCE:1]")
;Fanuc Robotics Virtual Robot Controller V9.10 Setup
;WinWait("FANUC Robotics Virtual Robot Controller V9.10 Setup")
WinWait("FANUC Robotics Shared Utils - InstallShield Wizard")
ControlClick("FANUC Robotics Shared Utils - InstallShield Wizard","","[CLASS:Button; INSTANCE:1]")
WinWait("FANUC Robotics Virtual Robot Controller - InstallShield Wizard")
ControlClick("FANUC Robotics Virtual Robot Controller - InstallShield Wizard","","[CLASS:Button; INSTANCE:1]")
;Fanuc Robotics Virtual Robot Controller V8.30 Setup
;WinWait("FANUC Robotics Virtual Robot Controller V8.30 Setup")
WinWait("Self-Registration Error")
ControlClick("Self-Registration Error","","[CLASS:Button; INSTANCE:1]")
WinWait("FANUC Robotics Shared Utils - InstallShield Wizard")
ControlClick("FANUC Robotics Shared Utils - InstallShield Wizard","","[CLASS:Button; INSTANCE:1]")
WinWait("FANUC Robotics Virtual Robot Controller - InstallShield Wizard")
ControlClick("FANUC Robotics Virtual Robot Controller - InstallShield Wizard","","[CLASS:Button; INSTANCE:1]")
;Fanuc Robotics Virtual Robot Controller V8.20 Setup
;WinWait("FANUC Robotics Virtual Robot Controller V8.20 Setup")
WinWait("Self-Registration Error")
ControlClick("Self-Registration Error","","[CLASS:Button; INSTANCE:1]")
;Fanuc Robotics Virtual Robot Controller V8.13 Setup
;WinWait("FANUC Robotics Virtual Robot Controller V8.13 Setup")
WinWait("Self-Registration Error")
ControlClick("Self-Registration Error","","[CLASS:Button; INSTANCE:1]")
;Fanuc Robotics Virtual Robot Controller V8.10 Setup
;WinWait("FANUC Robotics Virtual Robot Controller V8.10 Setup")
WinWait("Self-Registration Error")
ControlClick("Self-Registration Error","","[CLASS:Button; INSTANCE:1]")
WinWait("FANUC Robotics Shared Utils - InstallShield Wizard")
ControlClick("FANUC Robotics Shared Utils - InstallShield Wizard","","[CLASS:Button; INSTANCE:1]")
WinWait("FANUC Robotics Virtual Robot Controller - InstallShield Wizard")
ControlClick("FANUC Robotics Virtual Robot Controller - InstallShield Wizard","","[CLASS:Button; INSTANCE:1]")
;Fanuc Robotics Virtual Robot Controller V7.70 Setup
;WinWait("Fanuc Robotics Virtual Robot Controller V7.70 Setup")
WinWait("Self-Registration Error")
ControlClick("Self-Registration Error","","[CLASS:Button; INSTANCE:1]")
;Fanuc process plugins install
WinWait("ROBOGUIDE - InstallShield Wizard")
ControlClick("ROBOGUIDE - InstallShield Wizard","","[CLASS:Button; INSTANCE:1]")
;WinWait("FANUC ROBOGUIDE")
;ControlClick("FANUC ROBOGUIDE","","[CLASS:Button; INSTANCE:4]")
WinWait("FANUC ROBOGUIDE","InstallShield Wizard Complete")
; Click on Yes, I want to view the Readme File Now
ControlCommand("FANUC ROBOGUIDE","","[CLASS:Button; INSTANCE:1]","UnCheck","")
;click on Finish
ControlClick("FANUC ROBOGUIDE","","[CLASS:Button; INSTANCE:4]")
WinWait("FANUC ROBOGUIDE","InstallShield Wizard Complete")
;select no i will restart my computer later
ControlCommand("FANUC ROBOGUIDE","No, I will restart my computer later","[CLASS:Button; INSTANCE:2]","Check","")
;click on Finish
ControlClick("FANUC ROBOGUIDE","Finish","[CLASS:Button; INSTANCE:4]")
ConsoleWrite("The Install of RoboGuide has completed, moving to reinstall FRVRC940" & @LF)
ConsoleWrite("Pausing the script for the GUI to Load....." & @LF)
Sleep(10000)
;reinstall FRVRC940
Local $iPID = Run(@ScriptDir & '.\FRVRC940\setup.exe')
ConsoleWrite("Reinstalling FRVRC940" & @LF)
WinWait("FANUC Robotics Virtual Robot Controller V9.40 Setup","Welcome to the setup for FANUC Robotics Virtual Robot Controller V9.40 9.40188.31.05")
ControlClick("FANUC Robotics Virtual Robot Controller V9.40 Setup","","Button1")
WinWait("FANUC Robotics Virtual Robot Controller V9.40 Setup","Setting destination path")
ControlClick("FANUC Robotics Virtual Robot Controller V9.40 Setup","","Button1")
WinWait("FANUC Robotics Virtual Robot Controller - InstallShield Wizard","Select the setup type that best suits your needs")
ControlClick("FANUC Robotics Virtual Robot Controller - InstallShield Wizard","","Button4")
WinWait("FANUC Robotics Virtual Robot Controller V9.40 Setup","Start Copying Files")
ControlClick("FANUC Robotics Virtual Robot Controller V9.40 Setup","","Button1")
WinWait("FANUC Robotics Shared Utils - InstallShield Wizard","The setup was not able to open permissions on")
ControlClick("FANUC Robotics Shared Utils - InstallShield Wizard","","Button1")
WinWait("FANUC Robotics Virtual Robot Controller - InstallShield Wizard","C:\ProgramData\FANUC\FRVRC Media\V9.40")
ControlClick("FANUC Robotics Virtual Robot Controller - InstallShield Wizard","","Button1")
WinWait("FANUC Robotics Virtual Robot Controller V9.40 Setup","InstallShield Wizard Complete")
ControlCommand("FANUC Robotics Virtual Robot Controller V9.40 Setup","Yes, I want to view the ReadMe file now.","[CLASS:Button; INSTANCE:1]","UnCheck","")
ControlClick("FANUC Robotics Virtual Robot Controller V9.40 Setup","Finish","[CLASS:Button; INSTANCE:4]")
WinWait("FANUC Robotics Virtual Robot Controller V9.40 Setup","InstallShield Wizard Complete")
;ControlCommand("FANUC Robotics Virtual Robot Controller V9.40 Setup","Yes, I want to restart my computer now.","[CLASS:Button; INSTANCE:1]","Check","")
ControlCommand("FANUC Robotics Virtual Robot Controller V9.40 Setup","No, I will restart my computer later","[CLASS:Button; INSTANCE:2]","Check","")
ControlClick("FANUC Robotics Virtual Robot Controller V9.40 Setup","Finish","[CLASS:Button; INSTANCE:4]")
ConsoleWrite("The installation process has been completed sucessfully" & @LF)

```

An exe has been generated called Install.exe

Calling the Install.exe will automate the process of install.

Copy the **RG_V9_Rev.ZA_ROBOGUIDE_[A08B-9410-J605]** package to to temp directory of the client via Insight send file.

execute the following command to install

```
cmd /k c:\temp\RG_V9_Rev.ZA_ROBOGUIDE_[A08B-9410-J605]\install.exe
```

Here is the Autoit Script to get the license info from the Client PC

```
Opt("WinTitleMatchMode",2)
Run("C:\Program Files (x86)\FANUC\Shared\Utilities\frlicensemanager.exe")
WinWaitActive("FANUC PC Licensing Manager (v9.4188.3103)")
ControlClick("FANUC PC Licensing Manager (v9.4188.3103)","Copy Registation Data &To Clipboard","[CLASS:ThunderRT6CommandButton; INSTANCE:1]","")
sleep(2000)
ControlClick("FANUC PC Licensing Manager (v9.4188.3103)","E&xit Licensing Manager","[CLASS:ThunderRT6CommandButton; INSTANCE:3]","")
Run("notepad.exe")
WinWaitActive("Sans titre - Bloc-notes")
Send("^v")
WinMenuSelectItem("Sans titre - Bloc-notes","","&Fichier","&Enregistrer")
WinWaitActive("Enregistrer sous")
ControlFocus("Enregistrer sous","","[CLASS:Edit; INSTANCE:1]")
ControlSend("Enregistrer sous","","[CLASS:Edit; INSTANCE:1]","c:\temp\license-roboguide.txt")
ControlClick("Enregistrer sous","&Enregistrer","[CLASS:Button; INSTANCE:2]")
WinWaitActive("license-roboguide.txt - Bloc-notes")
WinClose("license-roboguide.txt - Bloc-notes")
```

An exe has been generated called roboguide-license-collect-info.exe

Executing the following command generates the license file info in *c:\temp* called *license-roboguide.txt*

```
cmd /k c:\temp\RG_V9_Rev.ZA_ROBOGUIDE_[A08B-9410-J605]\roboguide-collect-license-info.exe
```

Using Insight Tech console -> collect the file from the client pc stored in c:\temp\license-roboguide.txt

