---
layout: post
date: 2022-04-29 08:24:01
title: GLPI Upgrade
category: GLPI
tags: glpi inventory
---

## When you would like to upgrade to the new version of Glpi or new version of Fusion inventory.

For instance if you have installed Glpi 9.5.7 and fusion invnetory 9.5.30 the folder where the data resides is Glpi957fusion9530
![image](https://user-images.githubusercontent.com/1507737/165894866-eb639f4f-c23c-4813-9016-be894afdc125.png)
The structure inside the folder is as follows:
![image](https://user-images.githubusercontent.com/1507737/165895099-f3ac920b-d7d0-46e1-bbce-72caaa9f0443.png)

Copy the entire Folder and rename it to the next version ex: glpi957fusion9530 -> glpi958fusion9530
Delete the **glpi-frontend**
ssh into the docker host and change the user group for the Mariadb-master and MariaDB-slave folder as follows

```
sudo chown -R 1001:1001 mariadb-master
sudo chown -R 1001:1001 mariadb-master
```

Make the following changes in the stack code

```
  mariadb:
    image: 'bitnami/mariadb:latest'
    container_name: mariadb
    hostname: mariadb
    ports:
      - '3306:3306'
    volumes:
      #- /portainer/appdata/glpi957fusion9530/mariadb-master:/bitnami/mariadb
      - /portainer/appdata/glpi958fusion9530/mariadb-master:/bitnami/mariadb
    environment:
      - MARIADB_REPLICATION_MODE=master
      - MARIADB_REPLICATION_USER=
      - MARIADB_REPLICATION_PASSWORD=
      - MARIADB_ROOT_PASSWORD=
      - MARIADB_USER=
      - MARIADB_PASSWORD=
      - MARIADB_DATABASE=
  
  mariadb-slave:
    image: 'bitnami/mariadb:latest'
    container_name: mariadb-slave
    ports:
      - '3307:3306'
    depends_on:
      - mariadb
    volumes:
      #- /portainer/appdata/glpi957fusion9530/mariadb-slave:/bitnami/mariadb
      - /portainer/appdata/glpi958fusion9530/mariadb-slave:/bitnami/mariadb
    environment:
      - MARIADB_REPLICATION_MODE=slave
      - MARIADB_REPLICATION_USER=
      - MARIADB_REPLICATION_PASSWORD=
      - MARIADB_MASTER_HOST=mariadb
      - MARIADB_MASTER_PORT_NUMBER=3306
      - MARIADB_MASTER_ROOT_PASSWORD=
  
  #GLPI Container
  glpi:
    image: diouxx/glpi
    container_name : glpi
    hostname: glpi
    depends_on:
      - mariadb
      - mariadb-slave
    ports:
      - "80:80"
    volumes:
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
      #- /portainer/appdata/glpi957fusion9530/glpi-frontend/var/www/html/glpi/:/var/www/html/glpi
      - /portainer/appdata/glpi958fusion9530/glpi-frontend/var/www/html/glpi/:/var/www/html/glpi
    environment:
      - TIMEZONE=Europe/Paris
      - VERSION_GLPI=9.5.8
    restart: always

```

Update the stack

Check the mariadb and mariadbslave container is up with no errors

When you launch GLPI choose the upgrade option and point to the **glpi** database.

if glpi page is not server, check the user group /var/www/html/glpi folder changeit as follows.

If the glpi says the that it couldnt contact the sql server: make sure the mariadbcontainer is running in the same network of Glpi
![image](https://user-images.githubusercontent.com/1507737/165898164-e10acc86-6d55-4094-8758-1c63dfc61c5a.png)



```
sudo chown -R www-data:www-data /var/www/html
```
Copy the /var/www/html/glpi/files folder from glpi957fusion9530 -> glpi958fusion9530  (This contains all the PDF files uploaded into the GLPI)

Install [fusioninventory plugin](https://vijaidjearam.github.io/blog/glpi/2021/12/07/Installing-Fusioninventory-Plugin.html)

Go to the Duplicati and edit the configuration to backup the newfolder glpi957fusion9530 -> glpi958fusion9530







