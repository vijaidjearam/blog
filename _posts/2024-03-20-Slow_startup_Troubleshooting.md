---
layout: post
date: 2024-03-20 16:38:25
title: Slow startup Troubleshooting
category: windows-finetuning
tags: slow startup windows performance
---
## Slow start up can be troubleshooted via windows performance analysis tool available in windows ADK
- Download the latest version of Windows ADK and install the windows performance toolkit.
- Link: [Télécharger le Windows ADK 10.1.26100.2454 (décembre 2024)](https://go.microsoft.com/fwlink/?linkid=2289980)

![image](https://github.com/vijaidjearam/blog/assets/1507737/4e70471e-5e65-491d-b8f4-2238bd8b2589)

### Windows performance recorder
 - Launch the windows performance recorder
 - Select First level triage and in the resource analysis select CPU usage, Disk IO Activity, File IO activity, Registry IO activity, Networking IO activity and Heap Usage
 - In the Performance Scenario  select Boot from the Dropdown a,d in the detail level select the verbose option from the drop down.
 - ![image](https://github.com/vijaidjearam/blog/assets/1507737/39b496cb-1860-4bc8-950e-56f3382a9385)
 - Click on the start button to capture the trace.
 - Once the trace is completed the file are saved in user/documents/WPRFiles

### Windows performance Analyser
- Launch the windows performance analyser
- Open the WPR file created earlier by the windows performance recorder
- Apply the [Boot-Logon-GPO-basic.wpaprofile](https://github.com/itoleck/WindowsPerformance/blob/main/ETW/Tools/WPT/WPA/Profiles/Boot-Logon-GPO-Basic.wpaProfile)
- Goto the Analysis tab and check the time duration taken for "Authentication to desktop Ready" this would give you the exact time taken after the user enters the credentials and get to tsee the desktop screen.
- Make sure all the process during this period is windows related and its better to move all the thirdy party to start after this phase.
- ![image](https://github.com/vijaidjearam/blog/assets/1507737/b2f23d4d-4113-4ed0-9c92-96cdbcbdf276)




  




