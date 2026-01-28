---
layout: post
date: 2026-01-22 12:19:00
title: Free file sync compare a synology folder to teams folder with exclusion filters
category: freefilesync
tags: freefilesync windows NAS backup
---


**COMPARE-ONLY**, safe for **Synology ↔ Teams**.

## ✅ Compare-Only `.ffs_gui` (Synology ↔ Teams)

👉 **Replace your file entirely** with the content below.

Save as:

```
Synology_vs_Teams_COMPARE_ONLY.ffs_gui
```

---

### 📄 **FreeFileSync config (NO warnings)**

```xml
<?xml version="1.0" encoding="utf-8"?>
<FreeFileSync XmlType="GUI" XmlFormat="18">

    <Notes>Compare-only configuration for Synology NAS ↔ Microsoft Teams (OneDrive local sync)</Notes>

    <Compare>
        <Variant>TimeAndSize</Variant>
        <Symlinks>Exclude</Symlinks>
        <IgnoreTimeShift/>
    </Compare>

    <!-- Synchronization deliberately disabled -->
    <Synchronize>
        <Variant>Custom</Variant>

        <CustomDirections>
            <LeftOnly>None</LeftOnly>
            <RightOnly>None</RightOnly>
            <LeftNewer>None</LeftNewer>
            <RightNewer>None</RightNewer>
        </CustomDirections>

        <DetectMovedFiles>false</DetectMovedFiles>
        <DeletionPolicy>Permanent</DeletionPolicy>

        <VersioningFolder Style="None"/>
    </Synchronize>

    <Filter>
        <Include>
            <Item>*</Item>
        </Include>

        <Exclude>
            <!-- Office / Teams temporary files -->
            <Item>~$*</Item>
            <Item>*.tmp</Item>
            <Item>*.temp</Item>
            <Item>*.bak</Item>
            <Item>*.wbk</Item>
            <Item>*.swp</Item>
            <Item>*.lock</Item>
            <Item>*~</Item>
            <Item>~*.tmp</Item>

            <!-- Synology / NAS metadata -->
            <Item>@eaDir/*</Item>
            <Item>#recycle/*</Item>
            <Item>.recycle/*</Item>
            <Item>.nfs*</Item>
            <Item>.fuse_hidden*</Item>

            <!-- Windows / OneDrive artifacts -->
            <Item>Thumbs.db</Item>
            <Item>desktop.ini</Item>
            <Item>*.lnk</Item>
            <Item>$RECYCLE.BIN/*</Item>
            <Item>System Volume Information/*</Item>

            <!-- macOS clients -->
            <Item>.DS_Store</Item>

            <!-- Cache -->
            <Item>.cache/*</Item>
        </Exclude>

        <SizeMin>0</SizeMin>
        <SizeMax>0</SizeMax>
        <TimeSpan/>
    </Filter>

    <FolderPairs>
        <Pair>
            <!-- Synology NAS (SMB/CIFS) -->
            <Left>\\NAS\SHARE\FOLDER</Left>

            <!-- Microsoft Teams (OneDrive local folder) -->
            <Right>C:\Users\USERNAME\OneDrive - ORGANIZATION\Teams\TEAM_NAME</Right>
        </Pair>
    </FolderPairs>

    <Errors IgnoreErrors="false"/>

    <PostSyncCommand Condition="None"/>

    <MainConfig>
        <FailSafe>true</FailSafe>
        <AutoRetry count="2" delay="5"/>
        <ProgressDialog>true</ProgressDialog>
    </MainConfig>

    <LogFolder/>

    <EmailNotification/>

    <Gui>
        <GridViewType>Difference</GridViewType>
    </Gui>

</FreeFileSync>
```

---

## 🔧 What you MUST edit

Only change these paths:

### Synology

```
\\NAS\SHARE\FOLDER
```

Example:

```
\\192.168.1.20\Projects
```

### Teams (local OneDrive path)

```
C:\Users\USERNAME\OneDrive - ORGANIZATION\Teams\TEAM_NAME
```

---

## 🧪 Expected behavior now

✔ No XML warnings
✔ Compare button works immediately
✔ Synchronize does **nothing** (all directions = `None`)
✔ Safe for audits / verification
✔ Stable with CIFS + OneDrive

---

