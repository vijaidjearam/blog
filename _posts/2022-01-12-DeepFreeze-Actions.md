---
layout: post
date: 2022-01-12 10:06:00
title: DeepFreeze-Actions
category: DeepFreeze
tags: DeepFreeze
---
# DeepFreeze Actions

## Create Scheduled Task For Shutdown
```xml
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

```xml
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

## Update default Profile 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
   <ACTION2>
      <CAPTION>
         <FRENCH>Update Default Profile</FRENCH>
         <ENGLISH>Update Default Profile</ENGLISH>
      </CAPTION>
      <FILEMENU>Y</FILEMENU>
      <POPUPMENU>Y</POPUPMENU>
      <SUBITEMS />
      <PARAMS />
      <SYNC />
      <LOG />
      <EXECUTE>winrs -r:%%WKSNAME%% cmd /k defprof user</EXECUTE>
      <WORKDIR>C:\windows\system32</WORKDIR>
   </ACTION2>
</CUSTOMDEFINEDACTIONS>
```
## Set Paging File Size for All Drives to be Automatically Managed

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
   <ACTION2>
      <CAPTION>
         <FRENCH>Set Page files to Automatically managed by windows</FRENCH>
         <ENGLISH>Set Page files to Automatically managed by windows</ENGLISH>
      </CAPTION>
      <FILEMENU>Y</FILEMENU>
      <POPUPMENU>Y</POPUPMENU>
      <SUBITEMS />
      <PARAMS />
      <SYNC />
      <LOG />
      <EXECUTE>winrs -r:%%WKSNAME%% cmd /k wmic computersystem where name="%computername%" set AutomaticManagedPagefile=True</EXECUTE>
      <WORKDIR>C:\windows\system32</WORKDIR>
   </ACTION2>
</CUSTOMDEFINEDACTIONS>
```

## wsus spoof to stop Windows updates

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION3>
		<CAPTION>
			<ENGLISH>wsus spoof stop Windows updates</ENGLISH>
			<FRENCH>wsus spoof stop Windows updates</FRENCH>
		</CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SILENT>Y</SILENT>
		<SUBITEMS/>
		<PARAMS>
		</PARAMS>
		<SYNC/>
		<LOG/>
		<EXECUTE>psexec.exe \\%%IP%% cmd /k reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate /v AcceptTrustedPublisherCerts /t Reg_DWORD /d 1 /f &amp;&amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate /v DisableWindowsUpdateAccess /t Reg_DWORD /d 1 /f &amp;&amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate /v ElevateNonAdmins /t Reg_DWORD /d 0 /f &amp;&amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate /v TargetGroup /t Reg_SZ /d workgroup /f &amp;&amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate /v TargetGroupEnabled /t Reg_DWORD /d 0 /f &amp;&amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate /v WUServer /t Reg_SZ /d http:\\195.83.128.20 /f &amp;&amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate /v WUStatusServer /t Reg_SZ /d http:\\195.83.128.20 /f &amp;&amp; reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer /v NoWindowsUpdate /t Reg_DWORD /d 1 /f &amp;&amp; reg add "HKLM\SYSTEM\Internet Communication Management\Internet Communication" /v DisableWindowsUpdateAccess /t Reg_DWORD /d 1 /f &amp;&amp; reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Policies\WindowsUpdate /v DisableWindowsUpdateAccess /t Reg_DWORD /d 1 /f &amp;&amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v AUOptions /t Reg_DWORD /d 5 /f &amp;&amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v AutoInstallMinorUpdates /t Reg_DWORD /d 0 /f &amp;&amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v DetectionFrequencyEnabled /t Reg_DWORD /d 0 /f &amp;&amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v NoAutoRebootWithLoggedOnUsers /t Reg_DWORD /d 1 /f &amp;&amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v NoAutoUpdate /t Reg_DWORD /d 1 /f &amp;&amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v RebootRelaunchTimeout /t Reg_DWORD /d 1440 /f &amp;&amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v RebootWarningTimeout /t Reg_DWORD /d 30 /f &amp;&amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v RescheduleWaitTimeEnabled /t Reg_DWORD /d 0 /f &amp;&amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v UseWUServer /t Reg_DWORD /d 1 /f </EXECUTE>
		<WORKDIR>C:\WINDOWS\system32</WORKDIR>
	</ACTION3>
</CUSTOMDEFINEDACTIONS>

```

## wsus spoof stop Windows updates ver2 with username and password

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION3>
		<CAPTION>
			<ENGLISH>wsus spoof stop Windows updates</ENGLISH>
			<FRENCH>wsus spoof stop Windows updates</FRENCH>
		</CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SILENT>Y</SILENT>
		<SUBITEMS/>
		<PARAMS>
			<username>
				<VAR>%username%</VAR>
				<CAPTION>
					<ENGLISH>user:</ENGLISH>
					<FRENCH>user:</FRENCH>
				</CAPTION>
			</username>
			<password>
				<VAR>%userpassword%</VAR>
				<CAPTION>
					<ENGLISH>password:</ENGLISH>
					<FRENCH>password:</FRENCH>
				</CAPTION>
			</password>
		</PARAMS>
		<SYNC/>
		<LOG/>
		<EXECUTE>psexec \\%%IP%% -u:%username% -p:%userpassword% reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate /v AcceptTrustedPublisherCerts /t Reg_DWORD /d 1 /f &amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate /v DisableWindowsUpdateAccess /t Reg_DWORD /d 1 /f &amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate /v ElevateNonAdmins /t Reg_DWORD /d 0 /f &amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate /v TargetGroup /t Reg_SZ /d workgroup /f &amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate /v TargetGroupEnabled /t Reg_DWORD /d 0 /f &amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate /v WUServer /t Reg_SZ /d http:\\195.83.128.20 /f &amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate /v WUStatusServer /t Reg_SZ /d http:\\195.83.128.20 /f &amp; reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer /v NoWindowsUpdate /t Reg_DWORD /d 1 /f &amp; reg add "HKLM\SYSTEM\Internet Communication Management\Internet Communication" /v DisableWindowsUpdateAccess /t Reg_DWORD /d 1 /f &amp; reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Policies\WindowsUpdate /v DisableWindowsUpdateAccess /t Reg_DWORD /d 1 /f &amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v AUOptions /t Reg_DWORD /d 5 /f &amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v AutoInstallMinorUpdates /t Reg_DWORD /d 0 /f &amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v DetectionFrequencyEnabled /t Reg_DWORD /d 0 /f &amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v NoAutoRebootWithLoggedOnUsers /t Reg_DWORD /d 1 /f &amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v NoAutoUpdate /t Reg_DWORD /d 1 /f &amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v RebootRelaunchTimeout /t Reg_DWORD /d 1440 /f &amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v RebootWarningTimeout /t Reg_DWORD /d 30 /f &amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v RescheduleWaitTimeEnabled /t Reg_DWORD /d 0 /f &amp; reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v UseWUServer /t Reg_DWORD /d 1 /f &amp; pause</EXECUTE>
		<WORKDIR>C:\WINDOWS\system32</WORKDIR>
	</ACTION3>
</CUSTOMDEFINEDACTIONS>

```


## wsus to microsoft update

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION3>
		<CAPTION>
			<ENGLISH>wsus to microsoft update</ENGLISH>
			<FRENCH>wsus to microsoft update</FRENCH>
		</CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SILENT>Y</SILENT>
		<SUBITEMS/>
		<PARAMS>
		</PARAMS>
		<SYNC/>
		<LOG/>
		<EXECUTE>psexec.exe \\%%IP%% cmd /k reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate /v DisableWindowsUpdateAccess /t Reg_DWORD /d 0 /f &amp;&amp;reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer /v NoWindowsUpdate /t Reg_DWORD /d 0 /f &amp;&amp;reg add "HKLM\SYSTEM\Internet Communication Management\Internet Communication" /v DisableWindowsUpdateAccess /t Reg_DWORD /d 0 /f &amp;&amp;reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Policies\WindowsUpdate /v DisableWindowsUpdateAccess /t Reg_DWORD /d 0 /f &amp;&amp;reg delete HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate /v WUServer /f &amp;&amp;reg delete HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate /v WUStatusServer /f &amp;&amp;reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v UseWUServer /t Reg_DWORD /d 0 /f &amp;&amp;reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v AUOptions /t Reg_DWORD /d 3 /f &amp;&amp;reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v AutoInstallMinorUpdates /t Reg_DWORD /d 1 /f &amp;&amp;reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v DetectionFrequencyEnabled /t Reg_DWORD /d 1 /f &amp;&amp;reg add HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v NoAutoUpdate /t Reg_DWORD /d 0 /f &amp;&amp;sc config wuauserv start=auto &amp;&amp;net start wuauserv</EXECUTE>
		<WORKDIR>C:\ProgramData\chocolatey\bin</WORKDIR>
	</ACTION3>
</CUSTOMDEFINEDACTIONS>

```

## Activation Office 2016

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION2>
		<CAPTION>
			<FRENCH>Activation Office 2016 Windows 7 64 bits-psexec</FRENCH>
			<ENGLISH></ENGLISH>
     </CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SUBITEMS/>
		<SYNC/>
		<LOG/>
		<PARAMS/>
		<EXECUTE>psexec.exe \\%%IP%% cmd /c cscript "c:\program Files (x86)\Microsoft Office\Office16\ospp.vbs" /act</EXECUTE>
		<WORKDIR>C:\windows</WORKDIR>
	</ACTION2>
</CUSTOMDEFINEDACTIONS>
```

## Add Student group to Local ADMIN

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION2>
		<CAPTION>
			<FRENCH>Add Student group to Local ADMIN</FRENCH>
			<ENGLISH>Add Student group to Local ADMIN</ENGLISH>
		</CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SUBITEMS/>
		<SYNC/>
		<LOG/>
		<PARAMS>
			<username>
				<VAR>%username%</VAR>
				<CAPTION>
					<ENGLISH>user:</ENGLISH>
					<FRENCH>user:</FRENCH>
				</CAPTION>
			</username>
			<password>
				<VAR>%userpassword%</VAR>
				<CAPTION>
					<ENGLISH>password:</ENGLISH>
					<FRENCH>password:</FRENCH>
				</CAPTION>
			</password>
		</PARAMS>
		<EXECUTE>psexec.exe \\%%IP%% -u %username% -p %userpassword% cmd /K net localgroup administrateurs ad-urca\985-Student /ADD &amp;&amp; net localgroup administrateurs ad-urca\985-Employee /ADD &amp;&amp;net localgroup administrateurs ad-urca\985-Faculty /ADD </EXECUTE>
		<WORKDIR>C:\windows</WORKDIR>
	</ACTION2>
</CUSTOMDEFINEDACTIONS>

```

## KIOSK -using scancode (disabling keys)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION3>
		<CAPTION>
			<ENGLISH>kiosk-2020</ENGLISH>
			<FRENCH>kiosk-2020</FRENCH>
		</CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SILENT>Y</SILENT>
		<SUBITEMS/>
		<SYNC/>
		<LOG/>
		<PARAMS>
			<URL>
				<VAR>%URL%</VAR>
				<CAPTION>
					<ENGLISH>URL:</ENGLISH>
					<FRENCH>URL:</FRENCH>
				</CAPTION>
			</URL>
		</PARAMS>
		<EXECUTE>psexec.exe \\%%IP%% cmd /k net user jpo jpo /add /passwordchg:no &amp; REG ADD "HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultUserName /t REG_SZ /d jpo /f &amp; REG ADD "HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultPassword /t REG_SZ /d jpo /f &amp; REG ADD "HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon" /v AutoAdminLogon /t REG_SZ /d 1 /f &amp; REG ADD "HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Shell /t REG_SZ /d "C:\Progra~2\Google\Chrome\Application\chrome.exe -kiosk %URL%" /f &amp; REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Keyboard Layout" /v "Scancode Map" /t REG_BINARY /d 0000000000000000080000000000380000001d0000005be000001de000005ce000003b0000003e0000000000 /f &amp; shutdown /r /f /t 5</EXECUTE>
		<WORKDIR>C:\WINDOWS</WORKDIR>
	</ACTION3>
</CUSTOMDEFINEDACTIONS>

```

## Disable kiosk

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION3>
		<CAPTION>
			<ENGLISH>disable kiosk</ENGLISH>
			<FRENCH>disable kiosk</FRENCH>
		</CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SILENT>Y</SILENT>
		<SUBITEMS/>
		<SYNC/>
		<LOG/>
		<PARAMS/>
		<EXECUTE>winrs -r:%%WKSNAME%% cmd /k net user jpo /delete &amp; REG DELETE "HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultUserName /f &amp; REG DELETE "HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultPassword /f &amp; REG ADD "HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon" /v AutoAdminLogon /t REG_SZ /d 0 /f &amp; REG ADD "HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Shell /t REG_SZ /d explorer.exe /f &amp; REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Keyboard Layout" /v "Scancode Map" /f &amp; shutdown /r /f /t 5</EXECUTE>
		<WORKDIR>C:\WINDOWS</WORKDIR>
	</ACTION3>
</CUSTOMDEFINEDACTIONS>

```

## DELL set bios password

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION2>
		<CAPTION>
			<FRENCH>Set bios password</FRENCH>
			<ENGLISH>Set bios password</ENGLISH>
		</CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SUBITEMS/>
		<PARAMS>
			<biospassword>
				<VAR>%biospwd%</VAR>
				<CAPTION>
					<ENGLISH>Bios Password:</ENGLISH>
					<FRENCH>Bios Password:</FRENCH>
				</CAPTION>
			</biospassword>
		</PARAMS>
		<SYNC/>
		<LOG/>
		<EXECUTE>winrs -r:%%WKSNAME%% cmd /k C:\PROGRA~2\Dell\COMMAN~2\X86_64\cctk.exe --setuppwd=%biospwd%</EXECUTE>
		<WORKDIR>C:\windows\system32</WORKDIR>
	</ACTION2>
</CUSTOMDEFINEDACTIONS>

```

## DELL clear bios password

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION2>
		<CAPTION>
			<FRENCH>clear bios password</FRENCH>
			<ENGLISH>clear bios password</ENGLISH>
		</CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SUBITEMS/>
		<PARAMS>
			<biospassword>
				<VAR>%biospwd%</VAR>
				<CAPTION>
					<ENGLISH>Bios Password:</ENGLISH>
					<FRENCH>Bios Password:</FRENCH>
				</CAPTION>
			</biospassword>
		</PARAMS>
		<SYNC/>
		<LOG/>
		<EXECUTE>winrs -r:%%WKSNAME%% cmd /c C:\PROGRA~2\Dell\COMMAN~2\X86_64\cctk.exe --setuppwd= --valsetuppwd=%biospwd%</EXECUTE>
		<WORKDIR>C:\windows\system32</WORKDIR>
	</ACTION2>
</CUSTOMDEFINEDACTIONS>

```

## DELL disable uefi network stack

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION2>
		<CAPTION>
			<FRENCH>disable uefi network stack</FRENCH>
			<ENGLISH>disable uefi network stack</ENGLISH>
		</CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SUBITEMS/>
		<PARAMS>
			<biospassword>
				<VAR>%biospwd%</VAR>
				<CAPTION>
					<ENGLISH>Bios Password:</ENGLISH>
					<FRENCH>Bios Password:</FRENCH>
				</CAPTION>
			</biospassword>
		</PARAMS>
		<SYNC/>
		<LOG/>
		<EXECUTE>winrs -r:%%WKSNAME%% cmd /c C:\PROGRA~2\Dell\COMMAN~2\X86_64\cctk.exe --UefiNwStack=disable --valsetuppwd=%biospwd%</EXECUTE>
		<WORKDIR>C:\windows\system32</WORKDIR>
	</ACTION2>
</CUSTOMDEFINEDACTIONS>

```

## DELL Enable uefi network stack

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION2>
		<CAPTION>
			<FRENCH>Enable uefi network stack</FRENCH>
			<ENGLISH>Enable uefi network stack</ENGLISH>
		</CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SUBITEMS/>
		<PARAMS>
			<biospassword>
				<VAR>%biospwd%</VAR>
				<CAPTION>
					<ENGLISH>Bios Password:</ENGLISH>
					<FRENCH>Bios Password:</FRENCH>
				</CAPTION>
			</biospassword>
		</PARAMS>
		<SYNC/>
		<LOG/>
		<EXECUTE>winrs -r:%%WKSNAME%% cmd /c C:\PROGRA~2\Dell\COMMAN~2\X86_64\cctk.exe --UefiNwStack=enable --valsetuppwd=%biospwd%</EXECUTE>
		<WORKDIR>C:\windows\system32</WORKDIR>
	</ACTION2>
</CUSTOMDEFINEDACTIONS>

```

## wakeup via DELL bios

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION2>
		<CAPTION>
			<FRENCH>wake up locally</FRENCH>
			<ENGLISH>wake up locally</ENGLISH>
		</CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SUBITEMS/>
		<PARAMS>
			<SelectDays>
				<VAR>%SelectDays%</VAR>
				<CAPTION>
					<ENGLISH>SelectDays (mon,sun):</ENGLISH>
					<FRENCH>SelectDays (mon,sun):</FRENCH>
				</CAPTION>
			</SelectDays>
			<SelectHours>
				<VAR>%SelectHours%</VAR>
				<CAPTION>
					<ENGLISH>SelectHours:</ENGLISH>
					<FRENCH>SelectHours:</FRENCH>
				</CAPTION>
			</SelectHours>
			<SelectMinutes>
				<VAR>%SelectMinutes%</VAR>
				<CAPTION>
					<ENGLISH>SelectMinutes:</ENGLISH>
					<FRENCH>SelectMinutes:</FRENCH>
				</CAPTION>
			</SelectMinutes>
			<Password>
				<VAR>%biospassword%</VAR>
				<CAPTION>
					<ENGLISH>Password</ENGLISH>
					<FRENCH>Password</FRENCH>
				</CAPTION>
			</Password>
		</PARAMS>
		<SYNC/>
		<LOG/>
		<EXECUTE>winrs -r:%%WKSNAME%% cmd /c C:\PROGRA~2\Dell\COMMAN~2\X86_64\cctk.exe --valsetuppwd=%biospassword% --autoon=selectdays:%SelectDays% --autoonhr=%SelectHours% --autoonmn=%SelectMinutes%</EXECUTE>
		<WORKDIR>C:\windows\system32</WORKDIR>
	</ACTION2>
</CUSTOMDEFINEDACTIONS>

```

## disable wakeup via DELL bios

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION2>
		<CAPTION>
			<FRENCH>disable wake up locally</FRENCH>
			<ENGLISH>disable wake up locally</ENGLISH>
		</CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SUBITEMS/>
		<PARAMS>
			<Password>
				<VAR>%biospassword%</VAR>
				<CAPTION>
					<ENGLISH>Password</ENGLISH>
					<FRENCH>Password</FRENCH>
				</CAPTION>
			</Password>
		</PARAMS>
		<SYNC/>
		<LOG/>
		<EXECUTE>winrs -r:%%WKSNAME%% cmd /k C:\PROGRA~2\Dell\COMMAN~2\X86_64\cctk.exe --valsetuppwd=%biospassword% --autoon=disable</EXECUTE>
		<WORKDIR>C:\windows\system32</WORKDIR>
	</ACTION2>
</CUSTOMDEFINEDACTIONS>
```

## Change insight Channel

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION2>
		<CAPTION>
			<FRENCH>Insight Channel</FRENCH>
			<ENGLISH>Insight Channel</ENGLISH>
		</CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SUBITEMS/>
		<PARAMS>
			<channel>
				<VAR>%channel%</VAR>
				<CAPTION>
					<ENGLISH>Channel:</ENGLISH>
					<FRENCH>Channel:</FRENCH>
				</CAPTION>
			</channel>
		</PARAMS>
		<SYNC/>
		<LOG/>
		<EXECUTE>winrs -r:%%WKSNAME%% cmd /k reg add HKLM\SOFTWARE\WOW6432Node\Insight /v Channel /t REG_DWORD /d %channel% /f</EXECUTE>
		<WORKDIR>C:\windows</WORKDIR>
	</ACTION2>
</CUSTOMDEFINEDACTIONS>
```

## F-Secure Reset UID

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION3>
		<CAPTION>
			<ENGLISH>F-Secure Reset UID</ENGLISH>
			<FRENCH>F-Secure Reset UID</FRENCH>
		</CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SILENT>Y</SILENT>
		<SUBITEMS/>
		<PARAMS/>
		<SYNC/>
		<LOG/>
		<EXECUTE>winrs -r:%%WKSNAME%% cmd /k &quot;C:\Program Files (x86)\F-Secure\Client Security\BusinessSuite\resetuid.exe&quot; RESETUID RANDOMGUID</EXECUTE>
		<WORKDIR>C:\WINDOWS\system32</WORKDIR>
	</ACTION3>
</CUSTOMDEFINEDACTIONS>
```

## Powerbutton to shutdown PC

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION2>
		<CAPTION>
			<FRENCH>Powerbutton to shutdown PC</FRENCH>
			<ENGLISH>Powerbutton to shutdown PC</ENGLISH>
		</CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SUBITEMS/>
		<PARAMS/>
		<SYNC/>
		<LOG/>
		<EXECUTE>winrs -r:%%WKSNAME%% cmd /k &quot;powercfg -setacvalueindex SCHEME_CURRENT 4f971e89-eebd-4455-a8de-9e59040e7347 7648efa3-dd9c-4e3e-b566-50f929386280 3&quot;</EXECUTE>
		<WORKDIR>C:\windows\system32</WORKDIR>
	</ACTION2>
</CUSTOMDEFINEDACTIONS>
```

## Force Fusion Inventory

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION2>
		<CAPTION>
			<FRENCH>Force Fusion Inventory</FRENCH>
			<ENGLISH>Force Fusion InventorY</ENGLISH>
		</CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SUBITEMS/>
		<PARAMS/>
		<SYNC/>
		<LOG/>
		<EXECUTE>winrs -r:%%WKSNAME%% cmd /k c:\progra~1\Fusioninventory-agent\fusioninventory-agent.bat</EXECUTE>
		<WORKDIR>C:\windows\system32</WORKDIR>
	</ACTION2>
</CUSTOMDEFINEDACTIONS>
```
## disable kiosk - assigned access

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION3>
		<CAPTION>
			<ENGLISH>disable kiosk - assigned access</ENGLISH>
			<FRENCH>disable kiosk - - assigned access</FRENCH>
		</CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SILENT>Y</SILENT>
		<SUBITEMS/>
		<SYNC/>
		<LOG/>
		<PARAMS/>
		<EXECUTE>psexec \\%%WKSNAME%% cmd /k net user jpo /delete &amp; REG DELETE &quot;HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon&quot; /v DefaultUserName /f &amp; REG DELETE &quot;HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon&quot; /v DefaultPassword /f &amp; REG ADD &quot;HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon&quot; /v AutoAdminLogon /t REG_SZ /d 0 /f &amp; powershell -command Clear-AssignedAccess</EXECUTE>
		<WORKDIR>C:\WINDOWS</WORKDIR>
	</ACTION3>
</CUSTOMDEFINEDACTIONS>
```
## create user JPO

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
   <ACTION2>
      <CAPTION>
         <FRENCH>create user JPO</FRENCH>
         <ENGLISH>create user JPO</ENGLISH>
      </CAPTION>
      <FILEMENU>Y</FILEMENU>
      <POPUPMENU>Y</POPUPMENU>
      <SUBITEMS />
      <PARAMS />
      <SYNC />
      <LOG />
      <EXECUTE>psexec \\%%WKSNAME%% cmd /k net user jpo jpo /add /passwordchg:no &amp;&amp; reg add &quot;HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon&quot; /v &quot;AutoAdminLogon&quot; /t REG_SZ /d &quot;1&quot; /f &amp;&amp; reg add &quot;HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon&quot; /v &quot;DefaultUserName&quot; /t REG_SZ /d &quot;jpo&quot; /f &amp;&amp; REG ADD &quot;HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon&quot; /v DefaultPassword /t REG_SZ /d &quot;jpo&quot; /f </EXECUTE>
      <WORKDIR>C:\windows\system32</WORKDIR>
   </ACTION2>
</CUSTOMDEFINEDACTIONS>
```
## kiosk-2020

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION3>
		<CAPTION>
			<ENGLISH>kiosk-2020</ENGLISH>
			<FRENCH>kiosk-2020</FRENCH>
		</CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SILENT>Y</SILENT>
		<SUBITEMS/>
		<SYNC/>
		<LOG/>
		<PARAMS>
			<URL>
				<VAR>%URL%</VAR>
				<CAPTION>
					<ENGLISH>URL:</ENGLISH>
					<FRENCH>URL:</FRENCH>
				</CAPTION>
			</URL>
		</PARAMS>
		<EXECUTE>psexec.exe \\%%IP%% cmd /k net user jpo jpo /add /passwordchg:no &amp; REG ADD "HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultUserName /t REG_SZ /d jpo /f &amp; REG ADD "HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultPassword /t REG_SZ /d jpo /f &amp; REG ADD "HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon" /v AutoAdminLogon /t REG_SZ /d 1 /f &amp; REG ADD "HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Shell /t REG_SZ /d "C:\Progra~2\Google\Chrome\Application\chrome.exe -kiosk %URL%" /f &amp; REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Keyboard Layout" /v "Scancode Map" /t REG_BINARY /d 0000000000000000080000000000380000001d0000005be000001de000005ce000003b0000003e0000000000 /f &amp; shutdown /r /f /t 5</EXECUTE>
		<WORKDIR>C:\WINDOWS</WORKDIR>
	</ACTION3>
</CUSTOMDEFINEDACTIONS>
```
## disable kiosk-2020

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Deep Freeze Exported Custom Action file-->
<CUSTOMDEFINEDACTIONS>
  <ACTION3>
		<CAPTION>
			<ENGLISH>disable kiosk-2020</ENGLISH>
			<FRENCH>disable kiosk-2020</FRENCH>
		</CAPTION>
		<FILEMENU>Y</FILEMENU>
		<POPUPMENU>Y</POPUPMENU>
		<SILENT>Y</SILENT>
		<SUBITEMS/>
		<SYNC/>
		<LOG/>
		<PARAMS/>
		<EXECUTE>winrs -r:%%WKSNAME%% cmd /k net user jpo /delete &amp; REG DELETE &quot;HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon&quot; /v DefaultUserName /f &amp; REG DELETE &quot;HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon&quot; /v DefaultPassword /f &amp; REG ADD &quot;HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon&quot; /v AutoAdminLogon /t REG_SZ /d 0 /f &amp; REG ADD &quot;HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon&quot; /v Shell /t REG_SZ /d explorer.exe /f &amp; REG DELETE &quot;HKLM\SYSTEM\CurrentControlSet\Control\Keyboard Layout&quot; /v &quot;Scancode Map&quot; /f &amp; shutdown /r /f /t 5</EXECUTE>
		<WORKDIR>C:\WINDOWS</WORKDIR>
	</ACTION3>
</CUSTOMDEFINEDACTIONS>
```





