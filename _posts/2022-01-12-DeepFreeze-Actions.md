---
layout: post
date: 2022-01-12 10:06:00
title: DeepFreeze-Actions
category: DeepFreeze
tags: DeepFreeze
---
# DeepFreeze Actions

## Create Scheduled Task For Shutdown
```
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
   <ACTION2>
      <CAPTION>
         <FRENCH>Create Scheduled shutdown Task 19:00</FRENCH>
         <ENGLISH>Create Scheduled shutdown Task 19:00</ENGLISH>
      </CAPTION>
      <FILEMENU>Y</FILEMENU>
      <POPUPMENU>Y</POPUPMENU>
      <SUBITEMS />
      <PARAMS />
      <SYNC />
      <LOG />
      <EXECUTE>winrs -r:%%WKSNAME%% cmd /k schtasks /ru "SYSTEM" /RL HIGHEST /create /sc DAILY /tn "scheduled-shutdown" /tr "c:\windows\system32\shutdown.exe -s -f -t 0" /st 19:00</EXECUTE>
      <WORKDIR>C:\windows\system32</WORKDIR>
   </ACTION2>
</CUSTOMDEFINEDACTIONS>
```

## Modify the time in Schedule Task For Shutdown

```
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
   <ACTION3>
      <CAPTION>
         <ENGLISH>Modify scheduled-shutdown time to : 19:05</ENGLISH>
         <FRENCH>Modify scheduled-shutdown time to : 19:05</FRENCH>
      </CAPTION>
      <FILEMENU>Y</FILEMENU>
      <POPUPMENU>Y</POPUPMENU>
      <SILENT>Y</SILENT>
      <SUBITEMS />
      <PARAMS />
      <SYNC />
      <LOG />
      <EXECUTE>winrs -r:%%WKSNAME%% cmd /k SchTasks /Change /TN scheduled-shutdown /ENABLE /ST 19:05</EXECUTE>
      <WORKDIR>C:\WINDOWS\system32</WORKDIR>
   </ACTION3>
</CUSTOMDEFINEDACTIONS>
```



