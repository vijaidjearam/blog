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
		<SUBITEMS/>
		<PARAMS/>
		<SYNC/>
		<LOG/>
		<EXECUTE>winrs -r:%%WKSNAME%% cmd /k schtasks /ru "SYSTEM" /RL HIGHEST /create /sc DAILY /tn &quot;scheduled-shutdown&quot; /tr &quot;c:\windows\system32\shutdown.exe -s -f -t 0&quot; /st 19:00</EXECUTE>
		<WORKDIR>C:\windows\system32</WORKDIR>
	</ACTION2>
</CUSTOMDEFINEDACTIONS>
```



