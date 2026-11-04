---
date: 2026-11-04 10:35:00
title: Proxmox Backup Server VM with a Synology NFS Datastore
category: backup
tags: proxmox backup 
---

## Part 1: Create the PBS VM

1. Download the latest **Proxmox Backup Server ISO** from proxmox.com. In the PVE web UI, go to **local → ISO Images → Upload**.
2. Click **Create VM** and use these settings:
   - **OS:** PBS ISO, type Linux 6.x
   - **System:** q35 machine, VirtIO SCSI single controller, QEMU Guest Agent enabled
   - **Disk:** 32 GB on local storage. This disk only holds the OS. Backups go to the NFS share.
   - **CPU/RAM:** 2–4 cores, at least 4 GB RAM
   - **Network:** VirtIO on a bridge that can reach the Synology. Use a storage VLAN if you have one.
3. Start the VM and run the installer. Set a **static IP** (for example, 192.168.1.50), the hostname and the root password.
4. Open `https://192.168.1.50:8007`. Then update the system from the shell:
   ```
   apt update && apt full-upgrade -y
   apt install qemu-guest-agent nfs-common -y
   ```

## Part 2: Create the NFS share on Synology (DSM 7)

1. Go to **Control Panel → File Services → NFS**. Check **Enable NFS service** and set the maximum protocol to **NFSv4.1**.
2. Go to **Control Panel → Shared Folder → Create**:
   - **Name:** `pbs-datastore`
   - Turn off the **Recycle Bin**. PBS manages its own chunk cleanup.
   - Turn off **data checksum/compression** if you're asked. PBS already compresses and checks its data.
3. Edit the folder, open **NFS Permissions → Create**, and set:
   - **Hostname/IP:** `192.168.1.50` (the PBS IP only, not a whole subnet)
   - **Privilege:** Read/Write
   - **Squash:** **No mapping**. This step matters: PBS has to own its files as the `backup` user (UID/GID 34). "Map all users to admin" makes the datastore setup fail.
   - **Security:** sys
   - Check **Enable asynchronous**
   - Check **Allow connections from non-privileged ports**
   - Check **Allow users to access mounted subfolders**
4. Write down the **mount path** shown at the bottom, for example `/volume1/pbs-datastore`.

## Part 3: Mount the share in PBS

Mount the share **inside the PBS VM**, not as PVE storage:

```
mkdir -p /mnt/synology-pbs
echo "192.168.1.10:/volume1/pbs-datastore /mnt/synology-pbs nfs vers=4.1,hard,noatime,_netdev,x-systemd.automount 0 0" >> /etc/fstab
systemctl daemon-reload
mount -a
df -h /mnt/synology-pbs
```

What the mount options do:
- `hard` prevents silent data corruption if the NAS stops responding.
- `_netdev` and `automount` make sure the share mounts after the network is up.

## Part 4: Set permissions and add the datastore

1. Give the folder to the PBS service user (`backup`, UID/GID 34) and test that it can write:
   ```
   chown -R backup:backup /mnt/synology-pbs
   chmod 750 /mnt/synology-pbs
   sudo -u backup touch /mnt/synology-pbs/test && rm /mnt/synology-pbs/test
   ```
   If the test fails, check that the squash setting is **No mapping** and that the IP in the NFS rule is correct.
2. Create the datastore. In the web UI, go to **Datastore → Add Datastore**, enter Name: `synology` and Backing Path: `/mnt/synology-pbs`. Or run:
   ```
   proxmox-backup-manager datastore create synology /mnt/synology-pbs
   ```
   This creates more than 65,000 chunk folders, so it takes a few minutes over NFS.
3. On the datastore, set up:
   - **Prune** jobs (for example, keep 7 daily, 4 weekly and 6 monthly)
   - A **Garbage Collection** job (daily)
   - A **Verify** job (weekly)

## Part 5: Connect PVE and back up your VMs

1. In PBS, go to **Configuration → Access Control** and create the user `pve@pbs`. On the datastore's **Permissions** tab, give that user the **DatastoreBackup** role.
2. Copy the PBS **fingerprint** from **Dashboard → Show Fingerprint**.
3. In PVE, go to **Datacenter → Storage → Add → Proxmox Backup Server** and enter:
   - The PBS IP
   - The user `pve@pbs` and its password
   - Datastore: `synology`
   - The fingerprint
   - An encryption key, if you want encrypted backups
4. Go to **Datacenter → Backup → Add**. Choose the PBS storage, a schedule, the VMs to include and **Snapshot** mode.
5. Run the job once by hand and confirm the backup shows up under **Datastore → Content** in PBS.

**Tips:**
- Don't back up the PBS VM to itself.
- Store its config (`/etc/proxmox-backup`) somewhere else.
- Use a dedicated 10GbE or VLAN link to the Synology if you can.