---
layout: post
date: 2025-05-30 18:40
title: Extend Logical Volume in Ubuntu
category: proxmox 
tags: ubuntu extenddiskspace containers
---


# Steps to Extend Logical Volume in Ubuntu

This guide outlines the steps to extend a logical volume in Ubuntu to utilize the full disk space.

## Prerequisites
- Access to a terminal with `sudo` privileges
- `cloud-guest-utils` package installed (for `growpart`)

## Steps

### Step 1: Check the Disk Layout
Verify the current disk layout to ensure that the additional space is available and correctly recognized by the system.

```bash
sudo fdisk -l /dev/sda
```

### Step 2: Check Volume Group
Check the volume group to see how much free space is available.

```bash
sudo vgdisplay
```

### Step 3: Install `growpart`
If `growpart` is not installed, install it using the following commands:

```bash
sudo apt update
sudo apt install cloud-guest-utils
```

### Step 4: Extend the Partition
Use `growpart` to extend the partition to include the new disk space. Replace `/dev/sda` and `3` with your disk and partition number if different.

```bash
sudo growpart /dev/sda 3
```

### Step 5: Resize the Physical Volume
Resize the physical volume to include the new space:

```bash
sudo pvresize /dev/sda3
```

### Step 6: Extend the Logical Volume
Extend the logical volume to use all available free space in the volume group. Replace `/dev/ubuntu-vg/ubuntu-lv` with your logical volume path if different.

```bash
sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
```

### Step 7: Resize the Filesystem
Resize the filesystem to utilize the new space. For an ext4 filesystem, use:

```bash
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv
```

### Step 8: Verify the Changes
Check the disk space again to ensure that the changes have taken effect:

```bash
df -h
```

## Conclusion
By following these steps, you should be able to extend your logical volume to utilize the full disk space available.
