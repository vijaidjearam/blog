---
layout: post
date: 2025-01-24 14:41:13
title: Recover data using Duplicati
category: duplicati
tags: docker container duplicati
---
Points to remember while recovering using Duplicati.

##Scenario 1 : The docker containers are up and runing 

Duplicati backups the volume data of the container.

During the restoration process you have to use the same version of the container:

example:
```yml
services:
  metabase:
    image: metabase/metabase:Latest -->  image: metabase/metabase:v0.47.7
```
 
if you use the Latest tag in the stack file it might break the system as some of the new files in the latest version could be missing in the backed up data.

This was most prominantly found in database restoration.

```yml
   postgres:
    image: postgres:Latest -- > image: postgres:16
```

So when ever a restoration needs to be done the version in the stack yml file of the container is important.

Scenario 2: The docker container couldnt be started for X reasons.

In this scenario, you spin up a new duplicati instance from another server or another LXC host

After loging in to Duplicati click on restore
