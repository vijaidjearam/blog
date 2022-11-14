---
layout: post
date: 2022-11-14 14:48:39
title: Insight Tech Console Backup WOL
category: Insight
tags: Insight-Tech-console Insight 
---
# Insight Tech Console Backup WOL 

The Pc list for WOL is stored in the registry in the following path:

```
HKEY_LOCAL_MACHINE\Software\WOW6432Node\Insight Tech Console\WOL_List
```

Once all the PC's are dynamically auto-populated in the Insight Tech console app, Go to the following registry and backup the registry key as .reg file.
On executing the .reg file all the pcs can be imported to the Insight Tech console in case of data loss or after new installation.
😋 Thats it!... 😄
