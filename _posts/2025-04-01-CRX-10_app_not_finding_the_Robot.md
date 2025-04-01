---
layout: post
date: 2025-04-01 12:09:48
title: CRX-10 app not finding the Robot
category: Fanuc
tags: fanuc robot crx10
---

## CRX-10 app not finding the Robot

Source : [Link](https://forum.diy-robotics.com/hc/en-us/community/posts/5133745297300-CRX-10-app-not-finding-the-Robot)

After launching the app in the android tablet, you will get a message check cable connection followed by waiting for the connection
![image](https://github.com/user-attachments/assets/b6cc32f2-9409-4980-8d69-bc54144119e0)


## Resolution:

The link between the robot controller and the tablet uses the Ethernet protocol.

In order for it to work properly, you must ensure that **WIFI is NOT enabled** on the tablet. 

You must also ensure that its IP address is set to static and that it is **1.1.0.12**.

|  Item 	| Value  	| 
|---	|---	|
| IP Address  	|  1.1.0.12 	|
|  Netmask 	|   255.255.255.0	|
|  DNS 	|  192.168.1.1 	|
|   Default Gateway	|   192.168.1.1	|

![image](https://github.com/user-attachments/assets/f446c30a-b3d9-4a5f-8e72-816eff4079ce)
