---
layout: post
date: 2025-05-23 15:53:00
title: Automount Rclone Remote at System Startup
category: rclone
tags: rclone jellyfin
---
# Automount Rclone Remote at System Startup (Using systemd)

To automount your Rclone remote at system startup, follow these steps:

---

## 1. Create a systemd Service Unit

Create a systemd unit file (example: `/etc/systemd/system/rclone-debridlink.service`).

```ini
[Unit]
Description=Rclone Mount for DebridLink Seedbox
After=network-online.target
Wants=network-online.target

[Service]
Type=notify
ExecStart=/usr/bin/rclone mount debridlink:/Seedbox /mnt/debridlink \\
  --allow-other \\
  --dir-cache-time 10s \\
  --vfs-cache-mode full \\
  --vfs-cache-max-size 500M \\
  --vfs-read-chunk-size 8M \\
  --vfs-read-chunk-size-limit 128M \\
  --vfs-fast-fingerprint \\
  --no-modtime \\
  --read-only \\
  --allow-non-empty \\
  --log-level INFO \\
  --log-file /var/log/rclone.log
ExecStop=/bin/fusermount -u /mnt/debridlink
Restart=on-failure
User=<your-username>
Group=<your-group>
TimeoutSec=30

[Install]
WantedBy=multi-user.target
```

Replace `<your-username>` and `<your-group>` with the appropriate values (you can find them using `whoami` and `id -gn`).

---

## 2. Enable FUSE Permissions

Ensure your user is part of the `fuse` group (if applicable):

```bash
sudo usermod -aG fuse <your-username>
```

Also install necessary FUSE packages (if missing):

```bash
sudo apt install fuse3
```

---

## 3. Enable and Start the Service

Enable and start your systemd service:

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable rclone-debridlink.service
sudo systemctl start rclone-debridlink.service
```

You can check the status with:

```bash
systemctl status rclone-debridlink.service
```

---

✅ Your Rclone mount should now automatically mount on each system boot.
