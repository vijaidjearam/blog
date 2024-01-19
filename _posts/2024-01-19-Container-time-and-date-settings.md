---
layout: post
date: 2024-01-19 15:26:14
title: Container-time-and-date-settings
category: docker
tags: docker ubuntu time 
---
# Container Time and date for logs 

## Set the correct timezone in the host

- Ubuntu
```bash
timedatectl set-timezone Europe/Paris
timedatectl
```
-containers

Update your docker-compose.yml with the following lines.

```yml
volumes:
    - "/etc/timezone:/etc/timezone:ro"
    - "/etc/localtime:/etc/localtime:ro"
```
