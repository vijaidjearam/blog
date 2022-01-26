---
layout: post
date: 2022-01-26 12:26:00
title: INITIATE SCCM CLIENT AGENT ACTIONS USING COMMAND LINE
category: sccm
tags: sccm powershell cmd
---

Credits: https://www.manishbangia.com/initiate-sccm-client-actions-cmd-line/

We can initiate SCCM Client agent actions by going to Configuration Manager Properties & clicking on Action Tab. However, we can do the same using command line and PowerShell commands. These commands can be executed on Local as well remote systems.

Every action stated under actions tab has a specific Trigger Schedule ID. The list of these ID’s are as follows:

CLIENT AGENT TRIGGER SCHEDULE ID
|||
|--- |--- |
|Client Agent Trigger Schedule ID|Client Action Name|
|{00000000-0000-0000-0000-000000000021}|Machine policy retrieval & Evaluation Cycle|
|{00000000-0000-0000-0000-000000000022}|Machine policy evaluation cycle|
|{00000000-0000-0000-0000-000000000003}|Discovery Data Collection Cycle|
|{00000000-0000-0000-0000-000000000002}|Software inventory cycle|
|{00000000-0000-0000-0000-000000000001}|Hardware inventory cycle|
|{00000000-0000-0000-0000-000000000113}|Software updates scan cycle|
|{00000000-0000-0000-0000-000000000114}|Software updates deployment evaluation cycle|
|{00000000-0000-0000-0000-000000000031}|Software metering usage report cycle|
|{00000000-0000-0000-0000-000000000121}|Application deployment evaluation cycle|
|{00000000-0000-0000-0000-000000000026}|User policy retrieval|
|{00000000-0000-0000-0000-000000000027}|User policy evaluation cycle|
|{00000000-0000-0000-0000-000000000032}|Windows installer source list update cycle|
|{00000000-0000-0000-0000-000000000010}|File collection|

![image](https://user-images.githubusercontent.com/1507737/151155683-bd766d66-b125-4354-be54-825c25670075.png)

## INITIATE COMMAND LINE ON LOCAL SYSTEM

First you need to open command prompt with elevated privileges. Run the following commands:

||||
|--- |--- |--- |
|Client Action Name (For Local System)|Client Agent Trigger Schedule ID|SCCM / MECM log files to verify|
|Machine policy retrieval & Evaluation Cycle|WMIC /namespace:\\root\ccm path sms_client CALL TriggerSchedule "{00000000-0000-0000-0000-000000000021}" /NOINTERACTIVE|PolicyAgent.log|
|Machine policy evaluation cycle|WMIC /namespace:\\root\ccm path sms_client CALL TriggerSchedule "{00000000-0000-0000-0000-000000000022}" /NOINTERACTIVE||
|Discovery Data Collection Cycle|WMIC /namespace:\\root\ccm path sms_client CALL TriggerSchedule "{00000000-0000-0000-0000-000000000003}" /NOINTERACTIVE|InventoryAgent.log|
|Software inventory cycle|WMIC /namespace:\\root\ccm path sms_client CALL TriggerSchedule "{00000000-0000-0000-0000-000000000002}" /NOINTERACTIVE|Inventoryagent.log|
|Hardware inventory cycle|WMIC /namespace:\\root\ccm path sms_client CALL TriggerSchedule "{00000000-0000-0000-0000-000000000001}" /NOINTERACTIVE|Inventoryagent.log|
|Software updates scan cycle|WMIC /namespace:\\root\ccm path sms_client CALL TriggerSchedule "{00000000-0000-0000-0000-000000000113}" /NOINTERACTIVE|ScanAgent.log|
|Software updates deployment evaluation cycle|WMIC /namespace:\\root\ccm path sms_client CALL TriggerSchedule "{00000000-0000-0000-0000-000000000114}" /NOINTERACTIVE|ScanAgent.log,UpdateDeployment.log|
|Software metering usage report cycle|WMIC /namespace:\\root\ccm path sms_client CALL TriggerSchedule "{00000000-0000-0000-0000-000000000031}" /NOINTERACTIVE|Mtrmgr.log|
|Application deployment evaluation cycle|WMIC /namespace:\\root\ccm path sms_client CALL TriggerSchedule "{00000000-0000-0000-0000-000000000121}" /NOINTERACTIVE|Appdiscovery.log, AppIntentEval|
|User policy retrieval|WMIC /namespace:\\root\ccm path sms_client CALL TriggerSchedule "{00000000-0000-0000-0000-000000000026}" /NOINTERACTIVE||
|User policy evaluation cycle|WMIC /namespace:\\root\ccm path sms_client CALL TriggerSchedule "{00000000-0000-0000-0000-000000000027}" /NOINTERACTIVE||
|Windows installer source list update cycle|WMIC /namespace:\\root\ccm path sms_client CALL TriggerSchedule "{00000000-0000-0000-0000-000000000032}" /NOINTERACTIVE||
|File collection|WMIC /namespace:\\root\ccm path sms_client CALL TriggerSchedule "{00000000-0000-0000-0000-000000000010}" /NOINTERACTIVE||
