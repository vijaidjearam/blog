---
layout: post
date: 2025-02-26 15:57:34
title: Unable to update windows due to SSL error
category: windows-update windows11
tags: windows11 windows-update
---

Source : (https://learn.microsoft.com/en-us/answers/questions/1571547/error-connecting-to-update-service-80072f8f)[https://learn.microsoft.com/en-us/answers/questions/1571547/error-connecting-to-update-service-80072f8f]

- Unable to update windows and its struck on a certain date 
- get-windowsupdatelog generated the logfile from etl files.
- The windows log points to the following error

```
2025/02/26 15:35:10.4405516 6508  4364  Agent           *FAILED* [80072F8F] wuauengcore.dll, C:\__w\1\s\src\Client\lib\DownloadFile\DownloadSession.cpp @853
2025/02/26 15:35:10.4405592 6508  4364  SLS             Complete the request URL HTTPS://slscr.update.microsoft.com/SLS/{9482F4B4-E343-43B6-B170-9A65BC822C77}/x64/10.0.26100.2454/0?CH=509&L=fr-FR;en-US&P=&PT=0x30&WUA=1309.2410.30012.0&MK=Dell+Inc.&MD=OptiPlex+7060 with [80072F8F] and http status code[0] and send SLS events.
2025/02/26 15:35:10.4405640 6508  4364  SLS             *FAILED* [80072F8F] GetDownloadedOnWeakSSLCert
2025/02/26 15:35:10.4415261 6508  4364  SLS             *FAILED* [80072F8F] Method failed [CSLSClient::GetResponse:660]
2025/02/26 15:35:10.4415318 6508  4364  Agent           *FAILED* [80072F8F] wuauengcore.dll, C:\__w\1\s\src\Client\lib\EndpointProviders\EndpointProviders.cpp @1842
2025/02/26 15:35:10.4415332 6508  4364  Agent           *FAILED* [80072F8F] wuauengcore.dll, C:\__w\1\s\src\Client\lib\EndpointProviders\EndpointProviders.cpp @1387
2025/02/26 15:35:10.4415346 6508  4364  Agent           *FAILED* [80072F8F] wuauengcore.dll, C:\__w\1\s\src\Client\lib\EndpointProviders\EndpointProviders.cpp @1398
2025/02/26 15:35:10.4415364 6508  4364  Agent           *FAILED* [80072F8F] Method failed [CAgentServiceManager::DetectAndToggleServiceState:3020]
2025/02/26 15:35:10.4415379 6508  4364  Agent           *FAILED* [80072F8F] SLS sync failed during service registration (cV: VP2R5H8DTUCyQ6J7.0.1.0.0.)
```
- After googling , the above source article had a temporary fix.
- Importing the reg entries from a working computer's HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\SystemCertificates fixed the issue.
