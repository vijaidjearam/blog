---
layout: post
date: 2026-04-29 12:10:00
title: Configuring Default User Profile for Third-Party Applications
category: default-profile
tags: default-profile windows
---
# How to Pre-Configure Software Settings for All New Users on Windows 11

## Overview

Many Windows applications store user-specific settings in the `AppData` folder (either `Roaming` or `Local`). When a new user logs in, the software usually creates default settings. To pre-configure these settings for all future users (e.g., for a shared computer), you can copy the desired configuration from a template user’s profile into the **Default User profile**. Additionally, you must set proper permissions using `icacls` so that both the `Users` group and `Administrators` group can read/write to these folders.

This guide works for any software that stores its configuration in `%APPDATA%` (Roaming) or `%LOCALAPPDATA%` (Local).

---

## Prerequisites

- You have a **template user account** (e.g., `TemplateUser`) with the software already configured as desired.
- You are logged in as **Administrator**.
- Hidden files and folders are visible (enable via File Explorer → View → Show → Hidden items).

---

## Step 1: Identify the Software’s AppData Folders

Determine which AppData folders the software uses. Common locations:

| Variable | Typical Path |
|----------|--------------|
| `%APPDATA%` | `C:\Users\[Username]\AppData\Roaming\[SoftwareName]` |
| `%LOCALAPPDATA%` | `C:\Users\[Username]\AppData\Local\[SoftwareName]` |

Check both Roaming and Local folders. Some software uses only one.

---

## Step 2: Copy Folders to the Default User Profile

The **Default User profile** (`C:\Users\Default`) is the template for all new user accounts created on that machine.

### Method A – Using Robocopy (Recommended)

Open **Command Prompt as Administrator** and run:

```cmd
:: Copy Roaming folder (if exists)
robocopy "C:\Users\TemplateUser\AppData\Roaming\[SoftwareName]" "C:\Users\Default\AppData\Roaming\[SoftwareName]" /E /COPYALL /DCOPY:DAT

:: Copy Local folder (if exists)
robocopy "C:\Users\TemplateUser\AppData\Local\[SoftwareName]" "C:\Users\Default\AppData\Local\[SoftwareName]" /E /COPYALL /DCOPY:DAT
```

Replace `[SoftwareName]` with the actual folder name (e.g., `CrealityPrint`, `Mozilla`, `Adobe`).

**Explanation of flags:**
- `/E` → Copies subdirectories, including empty ones.
- `/COPYALL` → Copies all file information (attributes, timestamps, security, etc.).
- `/DCOPY:DAT` → Copies directory timestamps.

### Method B – Using File Explorer (Manual)

1. Open File Explorer **as Administrator** (right-click on `explorer.exe` and run as admin).
2. Navigate to `C:\Users\TemplateUser\AppData\Roaming` and copy the software’s folder.
3. Navigate to `C:\Users\Default\AppData\Roaming` and paste.
4. Repeat for `Local` if needed.

---

## Step 3: Set Proper Permissions with icacls

After copying, you need to ensure that the **Users group** (for all standard users) and **Administrators group** have appropriate access. The typical requirement is **Modify** or **Full Control**.

Open **Command Prompt as Administrator** and run the following commands for each copied folder:

```cmd
:: Grant Full Control to Administrators (inheritable)
icacls "C:\Users\Default\AppData\Roaming\[SoftwareName]" /grant Administrators:(OI)(CI)F /T

:: Grant Modify to Users (inheritable)
icacls "C:\Users\Default\AppData\Roaming\[SoftwareName]" /grant Users:(OI)(CI)M /T

:: Repeat for Local folder (if copied)
icacls "C:\Users\Default\AppData\Local\[SoftwareName]" /grant Administrators:(OI)(CI)F /T
icacls "C:\Users\Default\AppData\Local\[SoftwareName]" /grant Users:(OI)(CI)M /T
```

**Explanation of permission flags:**
- `(OI)` = Object Inherit – files inside inherit this permission.
- `(CI)` = Container Inherit – subfolders inherit this permission.
- `F` = Full Control (read, write, modify, delete, change permissions).
- `M` = Modify (read, write, delete, but cannot change permissions/ownership).
- `/T` = Apply recursively to all existing subfolders and files.

> **Why not give Full Control to Users?**  
> Modify is usually sufficient. If the software needs to create subfolders or files inside these directories, Modify allows that. If you encounter permission errors later, you can escalate to `F`.

---

## Step 4: Verify and Test

1. Create a **new local user account** (Settings → Accounts → Other users → Add account).
2. Log in with that new user.
3. Launch the software.
4. Verify that the pre‑configured settings are loaded.

If the software creates its own folder structure on first launch, the pre‑copied folders will be used – but ensure permissions allow the process to read/write.

---

## Common Issues & Solutions

| Issue | Solution |
|-------|----------|
| New user sees default settings (no pre‑configuration) | The software might store settings in a different location (e.g., Registry, `ProgramData`). Check documentation. |
| Permission errors (“Access denied”) | Re‑run the `icacls` commands. Ensure you used `(OI)(CI)` and `/T`. |
| Folders not copied to new user profile | Verify that the Default profile was copied correctly. Sometimes the software creates folders only on first launch; ensure you copied the correct template user's folders. |
| Administrator cannot access the software’s settings | The Administrators group may not have explicit permissions. The commands above grant Full Control to Administrators. |

---

## Additional Considerations

- If the software stores settings in **both Roaming and Local**, you must copy both.
- Some software also uses **`%PROGRAMDATA%`** (e.g., `C:\ProgramData\[SoftwareName]`) for machine‑wide settings. Those are not per‑user and should be pre‑configured separately.
- To apply these settings to **existing users** as well, you would need to copy the folders into each existing user’s AppData and set permissions individually (not covered here).

---

## Summary of Commands (Quick Reference)

Replace `[SoftwareName]` with your actual folder name.

```cmd
:: Copy Roaming
robocopy "C:\Users\TemplateUser\AppData\Roaming\[SoftwareName]" "C:\Users\Default\AppData\Roaming\[SoftwareName]" /E /COPYALL /DCOPY:DAT

:: Copy Local
robocopy "C:\Users\TemplateUser\AppData\Local\[SoftwareName]" "C:\Users\Default\AppData\Local\[SoftwareName]" /E /COPYALL /DCOPY:DAT

:: Permissions for Roaming
icacls "C:\Users\Default\AppData\Roaming\[SoftwareName]" /grant Administrators:(OI)(CI)F /T
icacls "C:\Users\Default\AppData\Roaming\[SoftwareName]" /grant Users:(OI)(CI)M /T

:: Permissions for Local (if copied)
icacls "C:\Users\Default\AppData\Local\[SoftwareName]" /grant Administrators:(OI)(CI)F /T
icacls "C:\Users\Default\AppData\Local\[SoftwareName]" /grant Users:(OI)(CI)M /T
```

---

This documentation can be adapted for any application that uses `AppData` for per‑user settings. Test thoroughly before deploying across many machines.