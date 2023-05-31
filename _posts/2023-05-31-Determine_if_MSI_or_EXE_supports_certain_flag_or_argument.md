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
setup.exe /s /v"/qb /log logfile.txt ADDLOCAL=ALL LICENSE_SERVER=myhostname"
```
