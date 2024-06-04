---
layout: post
date: 2024-06-04 11:49:00
title: Fog Client unable to install CA certificate Error
category: Fog
tags: Fog chocolatey
---
During install of Fog client you might encounter the following error: 

![image](https://github.com/vijaidjearam/blog/assets/1507737/3abffe66-53f1-49f0-933c-245cc4a199cd)

This occurs because it is unable to contact the server.

During the installation process the setup will ask for the server address, as below:

![image](https://github.com/vijaidjearam/blog/assets/1507737/be246998-a1b7-4285-9650-4fe739b03062)

While proceeding to the next step , the wireshark sniffing shows that the client contacts the server using the servername provided during the setup to download the certificates.

![image](https://github.com/vijaidjearam/blog/assets/1507737/7e1241e7-9642-4340-acf6-8c940330130c)

After downloading the certificates, its installed the client maching which can be found in the certificate Manager (shortcut command : certmgr.msc)

![image](https://github.com/vijaidjearam/blog/assets/1507737/ac31b3ce-069a-4b5b-af69-c33a530e5a08)


Resolution:

- Try to check if you could ping to the server using the FQDN.
- if ping fails add FQDN to the host file in "c:\windows\system32\drivers\etc"
