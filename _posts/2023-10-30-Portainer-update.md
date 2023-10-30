---
layout: post
date: 2023-10-30 14:29:56
title: Update portainer
category: portainer
tags: docker portainer
---

To update to the latest version of Portainer Server, use the following commands to stop then remove the old version. Your other applications/containers will not be removed.

```
docker stop portainer
docker rm portainer
```
Now that you have stopped and removed the old version of Portainer, you must ensure that you have the latest version of the image locally. You can do this with a docker pull command:

```
docker pull portainer/portainer-ce:latest
```

Finally, deploy the updated version of Portainer:

```
docker run -d -p 8000:8000 -p 9443:9443 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```
