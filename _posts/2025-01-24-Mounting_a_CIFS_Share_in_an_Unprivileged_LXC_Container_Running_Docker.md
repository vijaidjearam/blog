---
layout: post
date: 2025-01-24 13:55:20
title: Mounting a CIFS Share in an Unprivileged LXC Container Running Docker
category: FileSharing
tags: filesharing docker proxmox cifs
---

This guide details how to mount a CIFS (SMB) share within an unprivileged LXC container running on Proxmox and make it accessible to a Docker container.

## Table of Contents
1. [Introduction](#introduction)
2. [Privileged vs Unprivileged Containers](#privileged-vs-unprivileged-containers)
3. [Steps to Mount CIFS in Unprivileged Containers](#steps-to-mount-cifs-in-unprivileged-containers)
   - [Mount CIFS on Proxmox Host](#mount-cifs-on-proxmox-host)
   - [Determine UID/GID Mapping](#determine-uidgid-mapping)
   - [Bind Mount to LXC Container](#bind-mount-to-lxc-container)
   - [Adjust Permissions](#adjust-permissions)
   - [Access CIFS in Docker](#access-cifs-in-docker)
4. [Troubleshooting](#troubleshooting)

---

## Introduction
CIFS (Common Internet File System) is a popular protocol for network file sharing. When working with LXC containers in Proxmox, mounting CIFS shares requires careful handling, especially with unprivileged containers due to UID/GID mapping.

---

## Privileged vs Unprivileged Containers
### Privileged Containers
- **Definition:** Run with root privileges directly on the host.
- **Pros:** Easier access to host resources.
- **Cons:** Higher security risk as container escape could compromise the host.

### Unprivileged Containers
- **Definition:** User IDs (UIDs) and group IDs (GIDs) inside the container are mapped to a range of IDs on the host, providing isolation.
- **Pros:** Safer and more secure.
- **Cons:** Requires extra configuration for resource access.

---

## Steps to Mount CIFS in Unprivileged Containers

### 1. Mount CIFS on Proxmox Host
1. **Install CIFS Utilities:**
   ```bash
   apt-get update
   apt-get install cifs-utils
   ```

2. **Create a Mount Point:**
   ```bash
   mkdir -p /mnt/cifs_share
   ```

3. **Mount the CIFS Share:**
   ```bash
   mount -t cifs //server_ip/share_name /mnt/cifs_share -o username=your_username,password=your_password,uid=100000,gid=100000,vers=3.0
   ```
   - Replace `server_ip` with the CIFS server's IP address.
   - Replace `share_name`, `your_username`, and `your_password` with your share and credentials.
   - Adjust `uid` and `gid` based on the container's mapping.

4. **Optional: Automate Mounting:**
   Add the following line to `/etc/fstab`:
   ```
   //server_ip/share_name /mnt/cifs_share cifs username=your_username,password=your_password,uid=100000,gid=100000,vers=3.0 0 0
   ```

### 2. Determine UID/GID Mapping
Check the UID/GID mapping for the unprivileged container. These are defined in `/etc/subuid` and `/etc/subgid` on the Proxmox host.

Example:
```bash
cat /etc/subuid | grep root
cat /etc/subgid | grep root
```
Output:
```
root:100000:65536
```
This indicates:
- Container UID 0 maps to host UID 100000.
- Container GID 0 maps to host GID 100000.

### 3. Bind Mount to LXC Container
Edit the container configuration file, typically located at `/etc/pve/lxc/<container_id>.conf`:

1. Open the configuration file:
   ```bash
   nano /etc/pve/lxc/100.conf
   ```

2. Add the following line:
   ```
   mp0: /mnt/cifs_share,mp=/mnt/cifs_share
   ```

3. Save and restart the container:
   ```bash
   pct stop 100
   pct start 100
   ```

### 4. Adjust Permissions
Ensure the mounted share has the correct permissions for the container's UID/GID:

```bash
chown -R 100000:100000 /mnt/cifs_share
```

### 5. Access CIFS in Docker
Finally, mount the CIFS share in the Docker container using volumes:

```bash
docker run -v /mnt/cifs_share:/mnt/cifs_share your_docker_image
```

---

## Troubleshooting
1. **`mount error(2): No such file or directory`**
   - Verify the CIFS server IP and share name.
   - Ensure the share is accessible using:
     ```bash
     smbclient -L //server_ip -U your_username
     ```

2. **Permission Issues:**
   - Ensure the UID/GID options match the container's mapped UID/GID.
   - Verify ownership with:
     ```bash
     ls -ln /mnt/cifs_share
     ```

3. **SMB Version Errors:**
   - Specify the SMB protocol version explicitly (e.g., `vers=3.0`).

---

By following these steps, you can securely mount a CIFS share in an unprivileged LXC container and make it accessible to Docker. This approach ensures compatibility and security within the Proxmox environment.
