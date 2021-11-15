---
layout: post
date: 2021-11-15 09:11:00
title: Docker-Glpi
category: Glpi 
tags: glpi inventory docker
---
# Install Docker Engine on Ubuntu
## Set up the repository            
1.Update the apt package index and install packages to allow apt to use a repository over HTTPS:
``` 
    sudo apt-get update
    
    sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```
2.Add Docker’s official GPG key:
``` 
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
3.Use the following command to set up the stable repository. To add the nightly or test repository, add the word nightly or test (or both) after the word stable in the commands below. Learn about nightly and test channels.
``` 
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
## Install Docker Engine
1.Update the apt package index, and install the latest version of Docker Engine and containerd, or go to the next step to install a specific version:
```
 sudo apt-get update

 sudo apt-get install docker-ce docker-ce-cli containerd.io
```
2.Verify that Docker Engine is installed correctly by running the hello-world image.
```
 sudo docker run hello-world
```
# Glpi Docker Configuring
1. Create directory Docker in the root
```
mkdir /docker
```
2.Create a docker-compose.yml inside /docker 
```
nano docker-compose.yml
```
3. here is the content of the docker-compose.yml
```
version: "3.2"

services:
#Mysql Container
  mysql:
    image: mysql:5.7.23
    container_name: mysql-916
    hostname: mysql-916
    volumes:
      - /var/lib/mysql-916:/var/lib/mysql
    env_file:
      - ./mysql.env
    restart: always

#GLPI Container
  glpi:
    image: diouxx/glpi
    container_name : glpi-916
    hostname: glpi-916
    ports:
      - "80:80"
    volumes:
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
      - /var/www/html/glpi-916:/var/www/html/glpi
    environment:
      - TIMEZONE=Europe/Brussels
      - VERSION_GLPI=9.1.6
    restart: always
```
4. Create mysql.env as below
```
MYSQL_ROOT_PASSWORD=diouxx
MYSQL_DATABASE=glpidb
MYSQL_USER=glpi_user
MYSQL_PASSWORD=glpi
```
5.create glpi-update-916-920.sh
```
  GNU nano 4.8                                                                           glpi.sh                                                                                     
#!/bin/bash
docker stop $(docker ps -a -q)
#docker network rm glpi-net-916
docker network create \
  --driver=bridge \
  --subnet=172.0.0.1/24 \
  --ip-range=172.0.0.1/24 \
  --gateway=172.0.0.254 \
  glpi-net-920
mkdir /var/lib/mysql-920
cp -a /var/lib/mysql-916/. /var/lib/mysql-920
docker run \
--name mysql-920 \
--hostname mysql-920  \
-e MYSQL_ROOT_PASSWORD=diouxx \
-e MYSQL_DATABASE=glpidb \
-e MYSQL_USER=glpi_user \
-e MYSQL_PASSWORD=glpi \
--volume /var/lib/mysql-920:/var/lib/mysql \
--network glpi-net-920 \
--ip 172.0.0.2 \
-p 3306:3306 \
-d mysql:5.7.23
docker run \
--name glpi-920 \
--hostname glpi-920 \
--volume /var/www/html/glpi-920:/var/www/html/glpi \
--volume /etc/timezone:/etc/timezone:ro \
--volume /etc/localtime:/etc/localtime:ro \
-e VERSION_GLPI=9.2 \
-e TIMEZONE=Europe/Brussels \
--network glpi-net-920 \
--ip 172.0.0.3 \
--add-host mysql:172.0.0.2 \
-p 80:80 \
-d diouxx/glpi
```
6. make file executalble via the following command below
```
chmod +x glpi-update-916-920.sh
```
7.
