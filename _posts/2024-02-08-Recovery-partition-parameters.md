---
layout: post
date: 2024-02-08 11:01:04
title: Recovery Partition Parameters
category: recovery
tags: recovery windows
---

# process to hide and mark it as windows recovery patrition

list volume 
N° volume   Ltr  Nom          Fs     Type        Taille   Statut     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C   OS           NTFS   Partition    203 G   Sain       Démarrag
  Volume 1     Y   CLONEZILLA   FAT32  Partition    500 M   Sain
  Volume 2     Z   backup       NTFS   Partition     28 G   Sain
  Volume 3         WINRE        NTFS   Partition    300 M   Sain       Masqué
  Volume 4         SYSTEM       FAT32  Partition    100 M   Sain       Système

## In my case :
    
```Batch
sel vol 1
remove letter=Y
set id="de94bba4-06d1-4d40-a16a-bfd50179d6ac"
gpt attributes=0x9000000000000001    
sel vol 2
remove letter=Z
set id="de94bba4-06d1-4d40-a16a-bfd50179d6ac"
gpt attributes=0x9000000000000001    
```

note : 0x9000000000000001    is a cummulatiive value of 
GPT_ATTRIBUTE_PLATFORM_REQUIRED 0x0000000000000001
GPT_BASIC_DATA_ATTRIBUTE_NO_DRIVE_LETTER 0x8000000000000000
GPT_BASIC_DATA_ATTRIBUTE_READ_ONLY 0x1000000000000000

## to reverse:

```Batch
sel vol 1
set id="ebd0a0a2-b9e5-4433-87c0-68b6b72699c7"
gpt attributes=0x0000000000000001  
assign letter=Y
sel vol 2
set id="ebd0a0a2-b9e5-4433-87c0-68b6b72699c7"
gpt attributes=0x0000000000000001  
assign letter=Z
```
