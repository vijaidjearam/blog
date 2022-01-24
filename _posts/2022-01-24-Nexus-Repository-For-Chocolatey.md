---
layout: post
date: 2022-01-24 16:45:00
title: Nexus Repository For Chocolatey
category: chocolatey
tags: chocolatey powershell
---

## Installation:

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

## nexus-configuration

|   |   |   
|---|---|
| InstallDirectory  |  C:\ProgramData\nexus |  
| WorkingDirectory |  C:\ProgramData\sonatype-work\nexus3 |  
| TemporaryDirectory  | C:\ProgramData\sonatype-work\nexus3\tmp   |  

## Config on Nexus Repository

create three repository for chocolatey: chocolatey-proxy, chocolatey-hosted, chocolatey-group.

![image](https://user-images.githubusercontent.com/1507737/150816577-a01e8b7b-44d3-4f70-9090-35a27eb5b4ee.png)

create repository chocolatey-proxy

![image](https://user-images.githubusercontent.com/1507737/150816677-ab643911-6b76-4f9b-a472-118abb0d7eaf.png)

![image](https://user-images.githubusercontent.com/1507737/150816713-c9a4bad1-af92-4224-aa69-27cc732d43e1.png)

![image](https://user-images.githubusercontent.com/1507737/150816743-02b1390d-8432-4c3a-9437-317b6af38732.png)

click save
for the chocolatey-hosted( this is for uploading internal package)
click on create new repository -> select nuget(hosted) -> in the name field type chocolatey-hosted
now for Chocolatey-group
click create repository and choose nuget(group)
name it as chocolatey-group 
add chocolatey-proxy and chocolatey-hosted as members
