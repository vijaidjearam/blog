---
layout: post
date: 2021-12-07 11:45:00
title: Installing Fusioninventory Plugin
category: GLPI
tags: glpi fusioninventory
---
# Installez et configurez le plugin FusionInventory
Credit: https://openclassrooms.com/fr/courses/1730516-gerez-votre-parc-informatique-avec-glpi/5994176-installez-le-plugin-et-l-agent-fusioninventory
## Installez le plugin
Le plugin est téléchargeable sur le site Internet dédié aux plugins, [à cette adresse](https://github.com/fusioninventory/fusioninventory-for-glpi/releases).
Mettez à jour votre système :
```
apt-get update && apt-get upgrade
```
Retournez dans le répertoire des sources et téléchargez le plugin FusionInventory:
```
cd /usr/src
wget https://github.com/fusioninventory/fusioninventory-for-glpi/releases/download/glpi9.5%2B3.0/fusioninventory-9.5+3.0.tar.bz2
apt-get install bzip2
tar xjvf fusioninventory-9.5+3.0.tar.bz2
```
Attribuez les droits d'accès au serveur web :
```
chown -R www-data:www-data /var/www/html/glpi/plugins
```
Préparez la compatibilité du répertoire pour être visible dans GLPI
```
cd /var/www/html/glpi/plugins
mv fusioninventory-for-glpi-glpi9.3-1.3/ fusioninventory/
```
