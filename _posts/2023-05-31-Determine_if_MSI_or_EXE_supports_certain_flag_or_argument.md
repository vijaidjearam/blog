---
layout: post
date: 2023-05-31 12:55:00
title: Determine if MSI/EXE supports certain flag/argument
category: Automation 
tags: python automation windows
---

# Determine if MSI/EXE supports certain flag/argument.


Just run through the installer with logging turned on and it will show you all of the possible parameters that the specific MSI accepts.

For example:

```
msiexec /log logfile.txt /i installer.msi
```

Run through the entire installer and the logfile.txt will show you the passable parameters as "Property(S)" or "Property(C)" with the name in all caps.

If you want less text to sift through in the log file, you can set the log setting to log only the properties:

```
<msi_name> /lp! <msi_property_logfile>
or

msiexec /lp! <msi_property_logfile> /i <msi_name>
```

When the package is included in an EXE bootstrapper, the command line no longer uses "msiexec". For example, the command line can look like this:

```
setup.exe /s /v"/qb /log logfile.txt"
```

Check the log file you will find all the properties and switches as shown 

```
Property(S): DiskPrompt = [1]
Property(S): UpgradeCode = {553E9642-0563-479F-BC64-FD30E96CB1D6}
Property(S): ProductCode = {9603284E-977A-4C85-9855-5B789FFF081B}
Property(S): VersionNT = 603
Property(S): ProgramFilesFolder = C:\Program Files (x86)\
Property(S): TARGETDIR = C:\
Property(S): ALLUSERSPROFILE = C:\ProgramData\
Property(S): CommonAppDataFolder = C:\ProgramData\
Property(S): Preselected = 1
Property(S): UILevel = 3
Property(S): DATABASE = C:\WINDOWS\Installer\240366.msi
Property(S): Privileged = 1
Property(S): LICENSE_SERVER = myhostname
```
