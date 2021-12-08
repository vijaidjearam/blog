---
layout: post
date: 2021-12-07 11:45:00
title: Installing Fusioninventory Plugin
category: Glpi
tags: glpi fusioninventory
---

# Installez et configurez le plugin FusionInventory

Credit: [Openclassroom](https://openclassrooms.com/fr/courses/1730516-gerez-votre-parc-informatique-avec-glpi/5994176-installez-le-plugin-et-l-agent-fusioninventory)

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
mv /usr/src/fusioninvnetory fusioninventory/
```
## Finalisez l’installation sur l’interface web

Revenons à présent dans GLPI. Connectez-vous avec le compte glpi (le super administrateur du serveur) :
![loginpage](https://user.oc-static.com/upload/2019/01/29/15487725681875_image25.png)
Une fois connecté, rendez vous dans la rubrique suivante : Configuration > Plugins :
![pluginsmenu](https://user.oc-static.com/upload/2019/01/29/15487727238486_image11.png)
Si les manipulations sur Linux sont correctes, vous devriez voir apparaître la fenêtre suivante :
![plugin fusion inventory](https://user.oc-static.com/upload/2019/01/29/15487727999289_image21.png)
Si vous la voyez ainsi, c’est que le plugin FusionInventory est prêt à être installé dans GLPI. Cliquez sur *Installer* pour continuer.  
L’installation est faite dans la base de données. Vous pouvez cliquer maintenant sur le bouton *Activer*.  
Ça y est ! Le plugin FusionInventory est installé. Il ne nous reste plus qu'à le configurer.

## Configurez le plugin FusionInventory

Rendez-vous dans : Administration > FusionInventory.
Nous voici enfin dans le menu de configuration !
![fusioninvnetory](https://user.oc-static.com/upload/2019/01/29/15487729341367_image19.png)
Dans l’onglet “Général”, vous aurez accès aux différentes options de configuration.
Par défaut, votre FusionInventory est tout à fait fonctionnel tel quel ! Toutefois, sachez que vous pourrez y configurer le délai de contact des agents, ou même encore les modules (outils) de Fusion actifs par défaut.

## Résolvez le problème de la crontab

La première chose qui est censée nous sauter au yeux, c’est le message d’alerte du cron de GLPI. Il est dû à une absence de cron.php du GLPI dans le cron de Linux.
Pour résoudre ce souci, faites la manipulation suivante dans le shell de Linux en compte root :
```
crontab -u www-data -e
```
À la fin de celui-ci, ajoutez la ligne suivante et enregistrez ensuite :
```
* * * * * * /usr/bin/php /var/www/html/glpi/front/cron.php &>/dev/null
```
Une fois fini, on relance le daemon du cron :
```
/etc/init.d/cron restart
```
Retournez ensuite sur la page web de GLPI et allez dans le menu : Configuration > Actions Automatiques.
Dans la liste (souvent en page 2), cherchez l’action automatique nommée TaskScheduler 
![Action automatic](https://user.oc-static.com/upload/2019/01/29/15487733998798_image40.png)
Cliquez dessus pour ouvrir le menu et cliquez ensuite sur le bouton *Exécuter* :
![Task Scheduler](https://user.oc-static.com/upload/2019/01/29/1548773509188_image17.png)
Si vous retournez dans : Administration > FusionInventory, le message d’erreur en jaune devrait avoir disparu !




