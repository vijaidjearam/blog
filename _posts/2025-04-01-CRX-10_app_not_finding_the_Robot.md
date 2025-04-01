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


## Resolution:

The link between the robot controller and the tablet uses the Ethernet protocol.
In ordre for it to work properly, you must ensure that WIFI is NOT enabled on the tablet. 
You must also ensure that its IP address is set to static and that it is 1.1.0.12.
