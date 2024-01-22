---
layout: post
date: 2024-01-22 14:37:41
title: docker-stack-nexus
category: docker-stacks
tags: docker stacks config
---

```yml
version: '3.3'
services:
    nexus1:
        ports:
            - '8082:8081'
        container_name: nexus1
        volumes:
            - chocolatey1-container-volume:/nexus-data
            - "/etc/timezone:/etc/timezone:ro"
            - "/etc/localtime:/etc/localtime:ro"
        image: sonatype/nexus3:latest
        networks:
            - network1
    nexus2:
        ports:
            - '8083:8081'
        container_name: nexus2
        volumes:
            - chocolatey2-container-volume:/nexus-data
            - "/etc/timezone:/etc/timezone:ro"
            - "/etc/localtime:/etc/localtime:ro"
        image: sonatype/nexus3:latest
        networks:
            - network1
networks:
  network1:
    name: nginx-proxy-manager_default
    external: true
volumes:
  chocolatey1-container-volume:
    external: true
    name: chocolatey1-container-volume
  chocolatey2-container-volume:
    external: true
    name: chocolatey2-container-volume
```

nginx load balancer config :

```conf
events {}
http {
    upstream backend {
        server nexus1:8081;
        server nexus2:8081;
    }
    
    server {
        listen 80;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_http_version 1.1;
        proxy_next_upstream error invalid_header http_500 http_502 http_404 http_503 http_504 non_idempotent;
        location / {
        if ($http_user_agent ~ "Chocolatey Command Line")
          {
            proxy_pass http://backend;
          }
        if ($http_user_agent !~ "Chocolatey Command Line")
          {
            proxy_pass http://nexus1:8081;
          }    
    }
}
}
```



