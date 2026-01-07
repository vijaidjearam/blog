---
layout: post
date: 2026-01-07 16:46:00
title: Add GSD files to Tia-Portal
category: tia-portal
tags: tia-portal siemens
---

# Copying the GSD files to the corresponding repository will not work : 

This is **key**: TIA Portal V19 does **not automatically scan every folder you drop XMLs into**, and simply copying files into `xdd\gsd` doesn’t make them appear in the Hardware Catalog. There’s an **internal registration/indexing step** that the GUI import normally does.

So, to make the Dataclogic M120 show up **without using the GUI**, we need to emulate what the GUI does behind the scenes. Here’s the detailed breakdown.

---

## 1. How TIA Portal actually “registers” a GSD

When you import via:

```
Options → Manage GSD files → Install
```

TIA does three things:

1. **Copies files to the system GSD folder**
   Usually:

   ```
   C:\ProgramData\Siemens\Automation\Portal V19\data\xdd\gsd
   ```
2. **Creates/updates a device registry cache** (binary index) under:

   ```
   C:\Users\<User>\AppData\Roaming\Siemens\Automation\Portal V19\Data\xdd
   ```

   or sometimes:

   ```
   C:\ProgramData\Siemens\Automation\Portal V19\Data\xdd\cache
   ```
3. **Validates the GSD XML** against the supported version schema (V2.3/V2.4)

✅ The crucial part: **without updating the cache, TIA Portal won’t display the device**, even if the XML is physically in the folder.

---

## 2. Why your manual copy isn’t working

* You copied the `.xml` files into `xdd\gsd` manually.
* TIA **did not rebuild the cache/index**, so it doesn’t “see” the new device.
* TIA Portal **only reads new GSDs during GUI import or after the internal indexing process**.

---

## 3. How to make it work **without GUI**

There are two practical approaches:

### **Option A: Force a GUI-less import using TIA scripts (recommended for labs)**

TIA Portal supports a **command-line import of GSDs** via `PortalCmd.exe` (hidden Siemens tool):

```
"C:\Program Files\Siemens\Automation\Portal V19\PortalCmd.exe" /GSDInstall:"C:\Path\To\GSDML"
```

* This will copy the GSD and **update the internal index**.
* Fully silent, scriptable, ideal for multiple lab PCs.

⚠️ Note: `PortalCmd.exe` exists in V17+; V19 retains it. It’s undocumented but works reliably.

---

### **Option B: Pre-build the `xdd` folder and cache**

If you don’t want to rely on PortalCmd:

1. Do a **manual import once on a master PC**

   * Dataclogic M120 appears in Hardware Catalog.

2. Copy **All** folders from **C:\ProgramData\Siemens\Automation\Portal V19\data** it includes folder like **hwcn**,**xdd**,**xddint**  to all lab PCs:

```
C:\ProgramData\Siemens\Automation\Portal V19\data\xdd\gsd
C:\Users\<User>\AppData\Roaming\Siemens\Automation\Portal V19\Data\xdd
```

* This ensures the XML and the **user/device index cache** are present.
* When students open TIA, the device appears instantly.

> Essentially, you are cloning the “registered” GSD environment.

---

## ✅ Recommended Approach for Labs

1. Install M120 GSD on a single PC via GUI.
2. Locate these folders:

```
C:\ProgramData\Siemens\Automation\Portal V19\data\*  <-- XML + icons
# The below path is not required as i hev tested it works well with the folder programdata
C:\Users\<AdminUser>\AppData\Roaming\Siemens\Automation\Portal V19\Data\*  <-- cache/index
```

3. Copy **hwcn**,**xdd**,**xddint** to all lab PCs (maintain structure).
4. Make sure **TIA is closed during copy**.
5. Launch TIA — device appears without GUI import.

> This is the most reliable method for lab-wide automation.

---

