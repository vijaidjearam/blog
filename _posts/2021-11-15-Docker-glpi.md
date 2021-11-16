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
5.Now you can spin up the two new containers with docker-compose command
* Run this command to download the current stable release of Docker Compose:
    ```
    sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    ```
* Apply executable permissions to the binary:
    ```
    sudo chmod +x /usr/local/bin/docker-compose
    ```
* Test the install
    ```
    docker-compose --version
    ```
* navigate to the docker-compose.yml directory and exceute the following command as below
    ```
    docker-compose up -d
    ```
* Check the status of the docker container.
    ```
    docker ps -a
    ```
    ![image](https://user-images.githubusercontent.com/1507737/141952594-96a8c330-c7b2-4204-8e21-51a4d5a0d3e3.png)
* Check the network mode of the container (ex: glpi-916)
  ```
  docker inspect glpi-916
  ```
  The ouptut is a json and in the json check the network mode to check the container is attached to which network.
  ![image](https://user-images.githubusercontent.com/1507737/141953392-b41acdc8-f8e8-4e2e-bcf2-9062f4ee3df6.png)
  
  Now use the following command to list the networks available.
  ```
  docker network ls
  ```
  ![image](https://user-images.githubusercontent.com/1507737/141953620-cf9744e8-1d4e-4210-800e-1bd698c98f68.png)
  
  you will find the docker_default network listed, to check the status of the docker_default network use the following command below
  ```
  docker network inspect docker_default
  ```
  ![image](https://user-images.githubusercontent.com/1507737/141954734-db56bc9d-5716-4d42-83c5-c59c2b2e8b9b.png)
  
  check if both the containers are attached to the current network.
  
  
    
# Upgrade to the latest version of Glpi
1.Lets take the scenario of upgrading version 916 -> 920 ,create *glpi-update-916-920.sh* as follows:
```
#!/bin/bash
docker stop $(docker ps -a -q)
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
2. make file executalble via the following command below
```
chmod +x glpi-update-916-920.sh
```
3.Exceute the file 
```
./glpi-update-916-920.sh
```
4. Lets take the scenario of upgrading from 920-> 921 copy *glpi-update-916-920.sh* -> *glpi-update-920-921.sh* as follows:
```
#!/bin/bash
docker stop $(docker ps -a -q)
docker network rm glpi-net-920
docker network create \
  --driver=bridge \
  --subnet=172.0.0.1/24 \
  --ip-range=172.0.0.1/24 \
  --gateway=172.0.0.254 \
  glpi-net-921
mkdir /var/lib/mysql-921
cp -a /var/lib/mysql-920/. /var/lib/mysql-921
docker run \
--name mysql-921 \
--hostname mysql-921  \
-e MYSQL_ROOT_PASSWORD=diouxx \
-e MYSQL_DATABASE=glpidb \
-e MYSQL_USER=glpi_user \
-e MYSQL_PASSWORD=glpi \
--volume /var/lib/mysql-921:/var/lib/mysql \
--network glpi-net-921 \
--ip 172.0.0.2 \
-p 3306:3306 \
-d mysql:5.7.23
docker run \
--name glpi-921 \
--hostname glpi-921 \
--volume /var/www/html/glpi-921:/var/www/html/glpi \
--volume /etc/timezone:/etc/timezone:ro \
--volume /etc/localtime:/etc/localtime:ro \
-e VERSION_GLPI=9.2.1 \
-e TIMEZONE=Europe/Brussels \
--network glpi-net-921 \
--ip 172.0.0.3 \
--add-host mysql:172.0.0.2 \
-p 80:80 \
-d diouxx/glpi
```
In the scenario above we have created a bridged network *glpi-net-921* and we have connected both the containers to the *glpi-net-921*.
We have provided static ip to each container so that we can address it later
Note: The glpi calls the database using the name mysql inside the container. The database container name in our case msql-921, if we start the application we will have an error regarding name resolution. To overcome this issue we have multiple ways
1. Using docker-compose.yml as we have in the first scenario, docker takes care of the rest.
2. Using the *--link mysql-921:mysql* in the docker run command while creating docker run command as follows:
```
docker run \
--name glpi-921 \
--hostname glpi-921 \
--volume /var/www/html/glpi-921:/var/www/html/glpi \
--volume /etc/timezone:/etc/timezone:ro \
--volume /etc/localtime:/etc/localtime:ro \
-e VERSION_GLPI=9.2.1 \
-e TIMEZONE=Europe/Brussels \
--link mysql-921:mysql
--ip 172.0.0.3 \
--add-host mysql:172.0.0.2 \
-p 80:80 \
-d diouxx/glpi
```

Note: The --link parameter in the docker run command doesnt work as expected if you create a custom bridge network. You can use the --link parameter with out connecting to a custom network and docker takes care of the rest for name resolution.

While using the --link with out connecting to custom network. Docker does the name resolution by adding the host name and ip address automatically to /etc/hosts 

to check the above, you can get into the container using the following command.

```
 docker exec -it glpi-921 bash
 cat /etc/hosts
```
 3. Using *--add-host mysql:172.0.0.2* in the docker run command. Here the addhost paramater adds the name resolution manually to /etc/hosts file.
 Here we can take the advantage of custom network and static IP for the containers.
 
 :imp: So for the future upgrade simply copy the file *glpi-update-920-921.sh* -> *glpi-update-921-922.sh*.
 
 :imp: Inside the file first replace 921 -> 922 and then 920 -> 921 save and execute the script.
 
#Rollback Glpi to the previous version
 1. Lets take the scenario of rolling back 956 -> 921. create the following script *glpi-rollback-956-921.sh*.
 ```
 #!/bin/bash
docker stop $(docker ps -a -q)
docker network rm glpi-net-956
docker network create \
  --driver=bridge \
  --subnet=172.0.0.1/24 \
  --ip-range=172.0.0.1/24 \
  --gateway=172.0.0.254 \
  glpi-net-921
docker run \
--name mysql-921 \
--hostname mysql-921  \
-e MYSQL_ROOT_PASSWORD=diouxx \
-e MYSQL_DATABASE=glpidb \
-e MYSQL_USER=glpi_user \
-e MYSQL_PASSWORD=glpi \
--volume /var/lib/mysql-921:/var/lib/mysql \
--network glpi-net-921 \
--ip 172.0.0.2 \
-p 3306:3306 \
-d mysql:5.7.23
docker run \
--name glpi-921 \
--hostname glpi-921 \
--volume /var/www/html/glpi-921:/var/www/html/glpi \
--volume /etc/timezone:/etc/timezone:ro \
--volume /etc/localtime:/etc/localtime:ro \
-e VERSION_GLPI=9.2.1 \
-e TIMEZONE=Europe/Brussels \
--network glpi-net-921 \
--ip 172.0.0.3 \
--add-host mysql:172.0.0.2 \
-p 80:80 \
-d diouxx/glpi
 ```
 2. On executing the above script remove the glpi-net-956 and creates a new network for the previous version, spins up the new container conneting to the respective volumes.
 
 
