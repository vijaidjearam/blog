---
layout: post
date: 2021-05-12 11:33:00
title: Delete_device_and_driver_windows10
category: Driver
tags: windows10 driver audio
---
# Delete device and driver

In order to delete a device from the device manager we can use pnputil /remove-device command 
but we must know the instance id to delete the correct device 

to filter for example realtek devices go to the device manager and select the properties of the appropriate device and in the details tab select the property class GUID

```
ex: pnputil /enum-devices /class {4d36e96c-e325-11ce-bfc1-08002be10318}
```
this will show the results of all the audio devices 

```
ID d'instance :                 HDAUDIO\FUNC_01&VEN_10EC&DEV_0280&SUBSYS_102805A4&REV_1000\4&23955602&0&0001
Description de l'appareil :          Realtek High Definition Audio
Nom de la classe :                 MEDIA
GUID de la classe :                 {4d36e96c-e325-11ce-bfc1-08002be10318}
Nom du fabricant :           Realtek
Statut :                     Déconnecté
Nom du pilote :                 oem5.inf

ID d'instance :                 HDAUDIO\FUNC_01&VEN_10EC&DEV_0255&SUBSYS_1028085A&REV_1000\4&323a812&0&0001
Description de l'appareil :          Realtek(R) Audio
Nom de la classe :                 MEDIA
GUID de la classe :                 {4d36e96c-e325-11ce-bfc1-08002be10318}
Nom du fabricant :           Realtek
Statut :                     Déconnecté
Nom du pilote :                 oem107.inf
Noms des pilotes d'extension :      oem80.inf
                            oem177.inf

ID d'instance :                 HDAUDIO\FUNC_01&VEN_10EC&DEV_0256&SUBSYS_10280AC7&REV_1000\4&3110e0b5&0&0001
Description de l'appareil :          Realtek(R) Audio
Nom de la classe :                 MEDIA
GUID de la classe :                 {4d36e96c-e325-11ce-bfc1-08002be10318}
Nom du fabricant :           Realtek
Statut :                     Début
Nom du pilote :                 oem339.inf
Noms des pilotes d'extension :      oem192.inf
                            oem338.inf
```

To select the correct instance id find the realtek audio device with the status debut/started.
You can also crosscheck  the same from hardware id properties in the device manager details tab under hardware id

so in my case:

```
pnputil /remove-device "HDAUDIO\FUNC_01&VEN_10EC&DEV_0256&SUBSYS_10280AC7&REV_1000"
pnputil /delete-driver oem339.inf /force
pnputil /delete-driver oem192.inf /force
pnputil /delete-driver oem338.inf /force
```

Note: first delete the device and then delete the drivers.
    
