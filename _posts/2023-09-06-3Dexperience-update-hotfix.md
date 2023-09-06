---
layout: post
date: 2023-09-06 12:38:46
title: 3Dexperince how to update Hotfix.
category: 3DExperience
tags: 3DExperience GMP
---

# How to patch the hotfix in 3Dexperience

## Parameters configured in the dashboard

Go to Members Tab and click the option Configure Apps Installation.

![image](https://github.com/vijaidjearam/blog/assets/1507737/0226e515-d7db-4e12-9da2-51b49a9a0990)


The opions Lock access to install optional content for all members and Lock access to install app updates for all members has been enabled to avoid users handling with patching hotfixes.

![image](https://github.com/vijaidjearam/blog/assets/1507737/23839c8c-46ff-4b3d-9b5d-e6f9cc4bb101)

The network path for the caching hotfix can be configured via the network path filepath.
Configuring the network path avoids each client going to the internet to download the hotfix content. 
All the clients would contact the configured path to get the hotfix content.

![image](https://github.com/vijaidjearam/blog/assets/1507737/7076b148-caf3-4f30-901f-20b6cfff2b38)

Here in our scenario, the teacher pc is used for caching.

The hotfix are stored in Thawed partition.

After rebooting the teacher pc in thawed mode launch the app to download the hotfix. 

Note: login to client using the local domain admin account to get the access to the shared folder (ex: local-test01)

The clients patching can now be triggered and can be completed successfully.




