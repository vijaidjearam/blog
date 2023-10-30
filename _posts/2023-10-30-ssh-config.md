---
layout: post
date: 2023-10-30 14:17:55
title: SSH configuration
category: ssh
tags: ssh
---

# SSH 

## To configure SSH initially.

1. In the command prompt, type the following:

```
ssh-keygen
```
![image](https://github.com/vijaidjearam/blog/assets/1507737/154a560a-ac89-4d44-a533-1a2172dc914e)

2. By default, the system will save the keys to C:\Users\your_username/.ssh/id_rsa.

3. You’ll be asked to enter a passphrase. Hit Enter to skip this step.

4. The system will generate the key pair, and display the key fingerprint and a randomart image.

5. Open your file browser.

6. Navigate to C:\Users\your_username/.ssh.

7. You should see two files. The identification is saved in the id_rsa file and the public key is labeled id_rsa.pub. This is your SSH key pair.

## Allow password login

SSH root login is disabled by default as a security feature

Open the /etc/ssh/sshd_config file with administrative privileges and change the following line:

![image](https://github.com/vijaidjearam/blog/assets/1507737/f2c3ed77-d7ed-41f0-a3ee-cd8a0d0c2a17)

```
FROM:
#PermitRootLogin prohibit-password
TO:
PermitRootLogin yes
```

Restart SSH service:

```
sudo systemctl restart ssh
```

## copy public key to server

The following command will copy the public key to the server.
```
ssh-copy-id user@remote-host
```
Now try to login with 

```
ssh root@server
```

