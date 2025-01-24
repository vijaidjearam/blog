---
layout: post
date: 2025-01-24 14:41:13
title: Recover data using Duplicati
category: duplicati
tags: docker container duplicati
---
Points to remember while recovering using Duplicati.

## Scenario 1 : The docker containers are up and runing 

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

## Scenario 2: The docker container couldnt be started for X reasons.

In this scenario, you spin up a new duplicati instance from another server or another LXC host

After loging in to Duplicati click on restore
![image](https://github.com/user-attachments/assets/5ac649b1-0e42-455b-8597-50470bb04bc8)

### Method 1 : Direct restore from backup files
 - ![image](https://github.com/user-attachments/assets/9898e2de-aa5f-4657-8a14-34adbf8eccec)
 - In the Storage type select SFTP (SSH)
 - ![image](https://github.com/user-attachments/assets/8c3471e6-587a-4d53-844e-cce7bbe8df4b)
 - Fill the following info like Server, port = 22 , path os server = '/file-share', username and password.
 - ![image](https://github.com/user-attachments/assets/b47c041a-7a72-4dcf-b88d-e8753abca460)
 - If yo have set Encryption , please fill the passphrase
 - ![image](https://github.com/user-attachments/assets/3c05ee4c-bcf8-4357-b50f-0df5460d6339)
 - The next window will point to the folders to restore, select the appropriate folder to restore
 - ![image](https://github.com/user-attachments/assets/ecb0059c-a796-42b8-9796-212cdb46571f)
 - The next step is to restore the file to a location, I have asked to restore to a folder backups in the local host
 - ![image](https://github.com/user-attachments/assets/7fdcaf14-e55e-4e28-82ba-248a52be20fd)
 - click on Restore to complete the restore
   
### Method 2 : Restore from configuration
  - This method would work if you have backed up the configuartion file
  - ![image](https://github.com/user-attachments/assets/de896d9d-46fa-4505-ba28-19618d23df6b)
  - choosse the configuration file and hit import, you will see the infos autopopulated as below from the config file:
  - ![image](https://github.com/user-attachments/assets/12b637b7-6d0d-4d03-b56e-451db911991a)
  - The next window will point to the folders to restore, select the appropriate folder to restore
  - ![image](https://github.com/user-attachments/assets/ecb0059c-a796-42b8-9796-212cdb46571f)
  - The next step is to restore the file to a location, I have asked to restore to a folder backups in the local host
  - ![image](https://github.com/user-attachments/assets/7fdcaf14-e55e-4e28-82ba-248a52be20fd)
  - click on Restore to complete the restore








