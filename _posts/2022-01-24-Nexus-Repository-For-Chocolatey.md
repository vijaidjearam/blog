---
layout: post
date: 2022-01-24 16:45:00
title: Nexus Repository For Chocolatey
category: chocolatey
tags: chocolatey powershell
---

##Installation: 
choco install nexus-repository
note: if you need to configure nexus to run on port :80 
- download the package and in the chocoinstall.ps1 change the port value to 80 
- create a new package with the modified value .nupkg
- choco pack (path to .nuspec)
- install using the modified package 
- choco install (path to the nupkg)
after installation if the web admin page doesnt load properly
- check if there is any other application is running on port 80
- netstat -ano | find ":80" 
- if thereis any appliocation running on that port , take the necessary actions
- if Port 80 is being used by SYSTEM (PID 4)
- check if there is IIS runing on the system, stop the IIS service or uninstall the role.
	- net stop w3svc
	- net stop was
- stop these services
	- branchecache
	- spouleur d'impression
	- gestion à distance de windows( gestion WSM)
After the installation is completed the password  administrateur is in c:\programdata\sonata-work\nexus3\admin.password.
change the default password 
activate anonymous user -> if not if a user is trying to install application it will demand for username and password.

nexus-configuration

|   |   |   
|---|---|
| installDirectory  |  C:\ProgramData\nexus |  
|  workingDirectory |  C:\ProgramData\sonatype-work\nexus3 |  
| temporaryDirectory  | C:\ProgramData\sonatype-work\nexus3\tmp   |   
