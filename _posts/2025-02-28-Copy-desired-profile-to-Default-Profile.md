---
layout: single
date: 2025-02-28 15:51:12
title: Copy a desired profile to default profile
category: windows11
tags: defaultprofile windows11
---


Use defprof to copy the desired profile

```cmd
defprof administrateur /q /noappx
```
Use the /noappx switch to avoid installing Appxprovisioned apps which increases the boot time.

Not using the /noappx switch creates "C:\Users\Default\AppData\Local\ForensiT" which has the configuration to deploy the appx apps for every new user login.

The chrome pinned extensions could not be automated via google chrome policy. 

To fix this issue, you could enable and pin the extension in a profile and copy the user profile "\appdata\local\Google" -> default profile "\appdata\local\Google"


🔍 What is the ForensiT Folder?
---

When DefProf copies a user profile, it creates temporary data in C:\Users\Default\AppData\Local\ForensiT.

This folder stores logs, AppX registration data, and migration details related to user profile cloning.

Normally, Windows does not need this folder after the profile has been copied successfully.

🚀 Why Deleting It Speeds Up Login
---

The ForensiT folder contains unnecessary AppX data, which Windows processes when a new user logs in.

This delays first logins because:

Windows tries to re-register AppX packages stored in AppData\Local\ForensiT.

It can cause delays in setting up UWP (Universal Windows Platform) apps, like Edge, OneDrive, and Calculator.

✅ Deleting it forces Windows to re-register AppX apps correctly without stale data.

🛠️ How to Safely Delete ForensiT After Using DefProf
---

🔴 Any Risks in Deleting It?
---

🚨 No major risks if the profile is already copied and working correctly.

⚠ Only delete it AFTER logging in and verifying that the new profile loads correctly.

🎯 Final Verdict
---

✅ Safe to delete after profile migration

✅ Improves first login speed

✅ No negative impact on profile functionality




