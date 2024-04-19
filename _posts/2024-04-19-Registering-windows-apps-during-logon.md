---
layout: post
date: 2024-04-19 12:21:58
title: Registering windows apps screen appears during logon
category: defprof
tags: defprof windows 
---

Registering windows apps screen appears during logon

Issue:  On Every new user logon registering windows apps screen appears which slows down the logon time.

Resolution : 

  - The ForensiT AppX Management Service has to be deleted, which can be done using the following command

```Batch
 ./defprof /DeleteAppxService
```
