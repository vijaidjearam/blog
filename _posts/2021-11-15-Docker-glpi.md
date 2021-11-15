---
layout: post
date: 2021-11-15 09:11:00
title: Docker-Glpi
category: Glpi
tags: glpi inventory powerbi
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


