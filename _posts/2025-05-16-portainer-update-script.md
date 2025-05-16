---
layout: post
date: 2025-05-16 11:52:00
title: Update Portainer on Ubuntu with Docker
category: portainer
tags: docker portainer
---
# Update Portainer on Ubuntu with Docker

This script stops and removes the existing Portainer container, pulls the latest image, and restarts Portainer using Docker.

## Script: `update_portainer.sh`

```bash
#!/bin/bash

# Update Portainer using Docker

echo "Stopping existing Portainer container..."
docker stop portainer

echo "Removing existing Portainer container..."
docker rm portainer

echo "Pulling the latest Portainer image..."
docker pull portainer/portainer-ce:lts

echo "Restarting Portainer with the latest image..."
docker run -d \
  -p 8000:8000 \
  -p 9443:9443 \
  --name portainer \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:lts

echo "Portainer update complete."
```

## Make the Script Executable

```bash
chmod +x update_portainer.sh
```

## Run the Script

```bash
./update_portainer.sh
```

> ⚠️ Adjust container name, ports, or volume paths if your setup differs.
