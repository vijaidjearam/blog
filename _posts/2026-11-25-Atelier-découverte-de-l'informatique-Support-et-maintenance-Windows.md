---
date: 2026-11-25 08:40:00
title: Atelier découverte de l'informatique – Support et maintenance Windows
category: Trainee
tags: Stagiare Trainee
---
# **Atelier découverte de l'informatique – Support et maintenance Windows**

**Durée totale : 4 heures · 2 séances de 2 heures · Niveau débutant · Stagiaire de 13 ans**

L'objectif est de faire découvrir le métier de technicien informatique à travers des démonstrations et des manipulations simples, ludiques et encadrées.

Le programme est organisé en deux séances :

- **Séance 1 – 2 heures :** Découverte du dépannage Windows, des outils de diagnostic et des outils en ligne de commande.
- **Séance 2 – 2 heures :** Sauvegarde, clonage, remplacement de disque, Deep Freeze et découverte de PowerShell.

L'idée est de consacrer environ 30 % du temps aux explications et 70 % aux manipulations et à la découverte.

# **Séance 1 – Découverte du dépannage Windows (2 heures)**

## **1. Découverte de Faronics Insight (15 min)**

![Classroom Management Software Class Control Software Faronics](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/faronics-insight1.jpg)

![Put to the Test: Insight from Faronics  Tech & Learning](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/faronics-insight2.jpg)

![Faronics Insight – Powerful Classroom Management for Modern Learning](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/faronics-insight3.jpg)

![Faronics Insight – Powerful Classroom Management for Modern Learning](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/faronics-insight4.jpg)

![Faronics Insight – Powerful Classroom Management for Modern Learning](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/faronics-insight5.jpg)

![Faronics Insight – Powerful Classroom Management for Modern Learning](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/faronics-insight6.jpg)

![Faronics Insight – Powerful Classroom Management for Modern Learning](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/faronics-insight7.jpg)


### **Qu'est-ce que Faronics Insight ?**

Faronics Insight est un logiciel utilisé dans les salles informatiques pour permettre à un enseignant ou à un technicien de superviser et d'aider les utilisateurs.

### **À quoi sert-il ?**

- Visualiser les écrans des ordinateurs des élèves.
- Superviser les postes d'une salle informatique.
- Envoyer des messages aux utilisateurs.
- Partager l'écran de l'enseignant.
- Contrôler certaines activités.
- Aider à distance un utilisateur.

### **Démonstration pratique**

1. Ouvrir la console Insight.
2. Identifier les ordinateurs présents dans la salle.
3. Sélectionner un poste étudiant.
4. Visualiser son écran.
5. Envoyer un message à un poste.
6. Montrer le partage d'écran de l'enseignant.

### **Questions à poser au stagiaire**

- Pourquoi un enseignant souhaite-t-il visualiser les écrans des élèves ?
- Que peut-on faire si un ordinateur n'apparaît pas dans Insight ?
- Pourquoi faut-il utiliser les outils de contrôle à distance de manière responsable ?

**Objectif :** Comprendre le rôle d'un outil de supervision informatique dans une salle de cours.

## **2. Dépannage Windows et Observateur d'événements (25 min)**

![How to get started with Event Viewer on Windows 11 Windows Central](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/windows-event-viewer1.jpg)
![Windows 11 is slow even in Safe Mode, even after formatting, fresh install and updating - Microsoft Q&A](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/windows-event-viewer2.jpg)

![How to get started with Event Viewer on Windows 11  Windows Central](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/windows-event-viewer3.jpg)

![How to get started with Event Viewer on Windows 11  Windows Central](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/windows-event-viewer4.jpg)

![How to get started with Event Viewer on Windows 11  Windows Central](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/windows-event-viewer5.jpg)


### **Qu'est-ce que le dépannage informatique ?**

Le dépannage consiste à identifier un problème, rechercher sa cause et essayer de trouver une solution.

### **La méthode du technicien**

1. Observer le problème.
2. Poser des questions et recueillir des informations.
3. Rechercher les causes possibles.
4. Tester une solution.
5. Vérifier que le problème est résolu.

### **Exemple**

**Situation :** Un ordinateur se bloque lorsqu'on lance une application.

Que faire ?

1. Demander depuis quand le problème existe.
2. Vérifier si d'autres applications fonctionnent.
3. Consulter l'Observateur d'événements.
4. Rechercher les erreurs éventuelles.
5. Tester une solution.
6. Vérifier le fonctionnement de l'application.

### **L'Observateur d'événements (Event Viewer)**

L'Observateur d'événements est un outil Windows qui enregistre les événements qui se produisent sur un ordinateur.

![L'Observateur d'événements](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/windows-event-viewer5.jpg)

#### **Comment l'ouvrir ?**

1. Appuyer sur `Win + R`.
2. Saisir :

```
eventvwr.msc
```

3. Appuyer sur Entrée.
4. Aller dans :

`Journaux Windows → Système`

### **Les principaux types d'événements**

| Type                          | Signification                                                       |
| ----------------------------- | ------------------------------------------------------------------- |
| **Avertissement (Warning)**   | Un événement inhabituel a été détecté.                              |
| **Erreur (Error)**            | Un problème ou un échec a été enregistré.                           |
| **Information (Information)** | Une information sur le fonctionnement du système a été enregistrée. |

### **Exercice pratique**

Demander au stagiaire de :

- Ouvrir l'Observateur d'événements.
- Ouvrir le journal Système.
- Repérer un Avertissement ou une Erreur.
- Lire le descriptif de l'événement.
- Identifier la date et l'heure de l'événement.

**Objectif :** Savoir où Windows enregistre les problèmes et découvrir comment lire les journaux.

## **3. Découverte de Hiren's BootCD PE (20 min)**

![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-1.jpg)


![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-2.jpg)


![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-3.jpg)

![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-4.jpg)

![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-5.jpg)

![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-6.jpg)

![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-7.jpg)

![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-8.jpg)

### **Qu'est-ce que Hiren's BootCD PE ?**

Hiren's BootCD PE est un environnement Windows démarrable sur clé USB qui contient différents outils de dépannage et de maintenance.

Il est particulièrement utile lorsque Windows ne démarre plus ou lorsqu'un technicien doit intervenir sur un ordinateur sans démarrer le Windows installé.

### **À quoi sert-il ?**

- Diagnostiquer des problèmes informatiques.
- Vérifier les disques.
- Récupérer des fichiers.
- Examiner les partitions.
- Utiliser des outils de maintenance.

### **Démonstration pratique**

1. Démarrer un PC de test ou une machine virtuelle sur Hiren's BootCD PE.
2. Découvrir le bureau et les outils disponibles.
3. Ouvrir un utilitaire de disque ou de partition.
4. Identifier les disques et les partitions.
5. Montrer comment accéder aux fichiers d'un disque de test.

![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-5.jpg)

**Attention :** Ne pas supprimer de partitions, formater de disques ou modifier des mots de passe pendant cette découverte. Utiliser un poste de test.

**Objectif :** Découvrir un environnement de maintenance qui fonctionne indépendamment du Windows installé sur le PC.

## **4. Découverte de l'environnement de récupération Windows (WinRE) (20 min)**

![(5 Methods) How to Enter Windows Recovery Environment?](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/windows-RE-1.jpg)
### **Qu'est-ce que WinRE ?**

WinRE (Windows Recovery Environment) est un environnement de récupération intégré à Windows. Il permet de dépanner et de réparer Windows lorsque le système rencontre des problèmes, notamment au démarrage.

### **Comment accéder à WinRE ?**

Depuis Windows :

1. Ouvrir le menu Démarrer.
2. Cliquer sur le bouton Marche/Arrêt.
3. Maintenir la touche `Shift` (Maj).
4. Cliquer sur Redémarrer.

Autre méthode :

`Paramètres → Système → Récupération → Démarrage avancé`

### **Les principales options de récupération**

| Option                        | Utilité                                                                  |
| ----------------------------- | ------------------------------------------------------------------------ |
| Réparation du démarrage       | Tente de corriger les problèmes empêchant Windows de démarrer.           |
| Restauration du système       | Restaure certains paramètres système à un état antérieur, si disponible. |
| Paramètres de démarrage       | Permet notamment de démarrer Windows en mode sans échec.                 |
| Invite de commandes           | Permet d'effectuer des opérations de dépannage avec des commandes.       |
| Désinstaller des mises à jour | Permet de retirer certaines mises à jour problématiques.                 |

![How to fix your PC's installation using five effective methods on Windows 11 | Windows Central](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/windows-RE-2.jpg)

### **Exercice pratique**

Sur un PC de test ou une machine virtuelle :

1. Accéder à WinRE.
2. Cliquer sur Dépannage.
3. Cliquer sur Options avancées.
4. Identifier les différentes options.
5. Expliquer quelle option pourrait être utilisée si Windows ne démarre plus.

**Objectif :** Savoir accéder aux outils de récupération de Windows et comprendre leur utilité.

## **5. Découverte de CMD – Les commandes Windows (30 min)**

![File:Command Prompt on Windows 11.png - Wikimedia Commons](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/commandline-1.jpg)

### **Qu'est-ce que CMD ?**

CMD (Invite de commandes) permet d'exécuter des commandes directement dans Windows au lieu d'utiliser uniquement les menus et les fenêtres.

### **Les commandes à découvrir**

| Commande             | Utilité                                                         |
| -------------------- | --------------------------------------------------------------- |
| `hostname`           | Affiche le nom de l'ordinateur.                                 |
| `whoami`             | Affiche l'utilisateur connecté.                                 |
| `ipconfig`           | Affiche les informations réseau.                                |
| `ipconfig /all`      | Affiche les informations réseau détaillées.                     |
| `ipconfig /release`  | Libère le bail IPv4.                                            |
| `ipconfig /renew`    | Demande un nouveau bail IPv4.                                   |
| `ipconfig /flushdns` | Vide le cache DNS.                                              |
| `ping`               | Teste la connectivité réseau.                                   |
| `sfc /scannow`       | Vérifie et tente de réparer les fichiers système protégés.      |
| `chkdsk`             | Vérifie le système de fichiers et certains problèmes de disque. |
| `tasklist`           | Affiche les processus en cours.                                 |
| `taskkill`           | Permet de terminer un processus.                                |

### **Exemples**

#### **1. Identifier le nom du PC**

```
hostname
```

#### **2. Identifier l'utilisateur connecté**

```
whoami
```

#### **3. Vérifier la configuration réseau**

```
ipconfig
```

Demander au stagiaire de repérer :

- Adresse IPv4
- Masque de sous-réseau
- Passerelle par défaut

#### **4. Tester la connectivité**

```
ping 8.8.8.8
```

Cette commande permet de tester la communication avec une adresse IP. Une absence de réponse ne signifie pas forcément que la connexion Internet est indisponible.

#### **5. Vérifier les fichiers système Windows**

Ouvrir CMD en tant qu'administrateur :

```
sfc /scannow
```

Cette commande analyse les fichiers système protégés de Windows et tente de réparer ceux qui sont endommagés.

### **Exercice pratique**

Demander au stagiaire de :

- Trouver le nom de l'ordinateur.
- Trouver le nom de l'utilisateur connecté.
- Trouver son adresse IP.
- Trouver sa passerelle.
- Tester la connexion à la passerelle.
- Identifier les processus en cours.
- Expliquer le rôle de `sfc /scannow`.

**Objectif :** Découvrir les commandes essentielles utilisées par les techniciens informatiques.

## **6. Introduction aux outils Sysinternals (10 min)**

![Process Explorer - Screenshots](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/sysinternals-1.jpg)

### **Qu'est-ce que Sysinternals ?**

Sysinternals est une collection d'outils avancés de dépannage et d'administration Windows proposée par Microsoft.

### **Trois outils à découvrir**

| Outil                | Utilité                                                                        |
| -------------------- | ------------------------------------------------------------------------------ |
| **Process Explorer** | Analyse les processus en cours d'exécution.                                    |
| **Autoruns**         | Affiche les programmes et composants configurés pour démarrer automatiquement. |
| **Process Monitor**  | Surveille les activités liées aux fichiers, au registre et aux processus.      |

### **Démonstration pratique**

- Ouvrir Process Explorer.
- Identifier les applications en cours.
- Repérer un processus.
- Observer l'utilisation du processeur et de la mémoire.
- Ouvrir Autoruns et repérer les programmes au démarrage.

**Objectif :** Découvrir des outils plus avancés utilisés par les techniciens.

# **Séance 2 – Maintenance, sauvegarde et automatisation (2 heures)**

## **7. Découverte de Clonezilla (20 min)**

![Clonezilla - Download (Linux) - Softpedia](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla1.jpg)

![7 Best Free Disk Cloning Software For Mac in 2022 \[Top Rated\]](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla2.jpg)

![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla3.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla4.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla5.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla6.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla7.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla8.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla9.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla10.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla11.jpg)

### **Qu'est-ce que Clonezilla ?**

Clonezilla est un logiciel libre de clonage et d'imagerie de disques. Il permet de copier un disque ou une partition et de restaurer cette copie.

### **Quelle est la différence ?**

**Image disque**

Création d'une image du disque, stockée ailleurs, pour pouvoir la restaurer plus tard.

**Clonage de disque**

Copie directe du contenu d'un disque vers un autre disque.

### **Dans quels cas utilise-t-on Clonezilla ?**

- Déployer des ordinateurs avec une configuration identique.
- Créer une image de sauvegarde d'un ordinateur.
- Restaurer un PC après un problème majeur.
- Remplacer un disque défectueux.
- Cloner un PC vers un autre disque.

### **Démonstration pratique**

1. Démarrer Clonezilla.
2. Identifier les disques disponibles.
3. Expliquer les modes `device-image` et `device-device`.
4. Expliquer la différence entre disque source et disque destination.
5. Montrer comment préparer la création d'une image disque.

![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla9.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla10.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla11.jpg)

**Attention :** Clonezilla peut écraser les données du disque de destination. Ne jamais choisir un disque au hasard. Pendant cette séance, rester sur la démonstration des menus sans lancer une opération irréversible.

**Objectif :** Comprendre comment sauvegarder et restaurer des ordinateurs à l'aide d'images disque.

## **8. Sauvegarde, remplacement de disque et restauration (20 min)**

![SSD Upgrade & Installation in Oceanside & Encinitas](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk1.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk2.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk3.jpg)

![Connect the hard drive](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk4.jpg)

![A Basic Guide to Identifying the Major Components in a Desktop System | Dell India](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk5.jpg)

![A Basic Guide to Identifying the Major Components in a Desktop System | Dell India](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk6.jpg)

![A Basic Guide to Identifying the Major Components in a Desktop System | Dell India](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk7.jpg)

![A Basic Guide to Identifying the Major Components in a Desktop System | Dell India](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk8.jpg)

![A Basic Guide to Identifying the Major Components in a Desktop System | Dell India](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk9.jpg)



### **Pourquoi sauvegarder un ordinateur ?**

Un disque peut tomber en panne. Si les données ne sont pas sauvegardées, elles peuvent être perdues.

### **Qu'est-ce qu'une sauvegarde ?**

Une sauvegarde est une copie des données importantes conservée séparément pour pouvoir les récupérer en cas de perte ou de panne.

### **Exemple : remplacement d'un SSD défectueux**

![Objawy uszkodzenia dysku ssd – na co zwrócić uwagę](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk10.jpg)


**Ancien disque**

Disque d'origine

![Western Digital Blue SN580 1TB M.2 NVMe SSD](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk11.jpg)


**Nouveau disque**

Disque de remplacement

### **Les étapes du remplacement d'un disque**

1. Identifier le disque et confirmer le problème.
2. Sauvegarder les données importantes ou créer une image.
3. Éteindre l'ordinateur.
4. Débrancher l'alimentation.
5. Remplacer le disque.
6. Restaurer l'image ou réinstaller Windows.
7. Vérifier que Windows démarre correctement.
8. Vérifier les applications, les données et le matériel.

### **Démonstration pratique**

Montrer au stagiaire :

- Un SSD.
- La différence entre un HDD et un SSD.
- L'emplacement du disque dans un PC de test.
- Comment retirer et remplacer un disque, si possible.
- Comment une image peut être restaurée avec Clonezilla.

**Attention :** Ne jamais ouvrir ou démonter un ordinateur sous tension. Utiliser un ancien PC ou un poste de test et respecter les précautions contre les décharges électrostatiques.

**Objectif :** Comprendre le déroulement d'un remplacement de disque.

## **9. Découverte de Deep Freeze (20 min)**

![Installing Deep Freeze](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/deepfreeze1.jpg)

![Deep Freeze](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/deepfreeze2.jpg)

![Thawed Period](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/deepfreeze3.jpg)



### **Qu'est-ce que Deep Freeze ?**

Deep Freeze est un logiciel de protection des ordinateurs qui permet de conserver une configuration Windows prédéfinie.

Il est souvent utilisé dans les salles informatiques, les établissements scolaires et les ordinateurs en libre-service.

### **Comment fonctionne Deep Freeze ?**

**État Frozen (gelé)**

Configuration protégée

Les modifications réalisées lors de l'utilisation normale sont annulées au redémarrage, selon la configuration de protection et les emplacements concernés.

**État Thawed (dégelé)**

Mode maintenance

Les modifications peuvent être conservées après redémarrage, permettant notamment d'installer des logiciels ou des mises à jour.

### **Exemple**

Un étudiant installe une application sur un ordinateur de la salle.

- Le PC est en état Frozen.
- L'étudiant installe une application.
- Le PC redémarre.
- L'installation est annulée, si les modifications ont été effectuées sur les zones protégées.

### **Les actions courantes du technicien**

- Vérifier l'état Frozen ou Thawed.
- Débloquer la protection temporairement pour la maintenance.
- Installer les mises à jour.
- Redémarrer et vérifier les modifications.
- Réactiver la protection.
- Vérifier que la protection est bien active.

### **Exercice pratique**

Sur un poste de test :

1. Vérifier l'état de Deep Freeze.
2. Expliquer les états Frozen et Thawed.
3. Créer un dossier temporaire sur le bureau lorsque le PC est Frozen.
4. Redémarrer le PC.
5. Vérifier si le dossier existe toujours.
6. Montrer comment un technicien prépare une maintenance.

**Attention :** Ne jamais effectuer ces tests sur un PC de salle en production sans autorisation. Ne pas modifier les mots de passe ou les paramètres de configuration de Deep Freeze.

**Objectif :** Comprendre pourquoi les ordinateurs des salles sont protégés et comment les techniciens réalisent la maintenance.

## **10. Introduction à PowerShell (25 min)**

![Rendering problem, blurry text · Issue #5759 · microsoft/terminal · GitHub](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/powershell1.jpg)

![Rendering problem, blurry text · Issue #5759 · microsoft/terminal · GitHub](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/powershell2.jpg)

![Rendering problem, blurry text · Issue #5759 · microsoft/terminal · GitHub](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/powershell3.jpg)

### **Qu'est-ce que PowerShell ?**

PowerShell est un interpréteur de commandes et un langage de script utilisé pour administrer Windows et automatiser des tâches.

Il est plus puissant que CMD pour de nombreuses tâches, car il manipule des objets et permet d'automatiser des opérations complexes.

### **CMD vs PowerShell**

| CMD                               | PowerShell                               |
| --------------------------------- | ---------------------------------------- |
| Ancien environnement de commandes | Shell moderne et langage de script       |
| Principalement orienté texte      | Manipule des objets structurés           |
| Scripts `.bat`                    | Scripts `.ps1`                           |
| Administration basique            | Administration avancée et automatisation |

### **Les principales commandes PowerShell**

| Commande                 | Utilité                                    |
| ------------------------ | ------------------------------------------ |
| `Get-ComputerInfo`       | Affiche des informations sur l'ordinateur. |
| `Get-Date`               | Affiche la date et l'heure.                |
| `Get-Process`            | Liste les processus en cours.              |
| `Get-Service`            | Liste les services Windows.                |
| `Get-NetIPConfiguration` | Affiche la configuration réseau.           |
| `Get-ChildItem`          | Liste les fichiers et dossiers.            |
| `Get-Location`           | Affiche le répertoire courant.             |
| `Clear-Host`             | Efface l'affichage de la console.          |

### **Exemples**

#### **1. Afficher le nom du PC**

```
$env:COMPUTERNAME
```

#### **2. Afficher la date**

```
Get-Date
```

#### **3. Afficher les processus en cours**

```
Get-Process
```

#### **4. Afficher la configuration réseau**

```
Get-NetIPConfiguration
```

#### **5. Lister les fichiers**

```
Get-ChildItem C:\Windows
```

#### **6. Créer un dossier**

```
New-Item -Path C:\Temp\Stagiaire -ItemType Directory
```

#### **7. Supprimer le dossier de test**

```
Remove-Item C:\Temp\Stagiaire
```

**Attention :** Certaines commandes PowerShell peuvent supprimer des fichiers, modifier des paramètres Windows ou agir sur plusieurs ordinateurs. Toujours lire et comprendre une commande avant de l'exécuter.

### **Mini-exercice**

Demander au stagiaire de :

- Trouver le nom du PC.
- Afficher la date.
- Lister les processus.
- Afficher la configuration réseau.
- Lister les fichiers d'un dossier.
- Créer un dossier de test.
- Supprimer le dossier de test.

**Objectif :** Comprendre les bases de PowerShell et pourquoi les techniciens l'utilisent.

# **11. Quiz de fin de formation (15 min)**

Ce quiz peut être distribué au stagiaire à la fin des 4 heures. Chaque question vaut 1 point.

### **Questions**

**1. À quoi sert Faronics Insight ?**

- A. Installer Windows.
- B. Superviser et gérer les ordinateurs d'une salle.
- C. Créer des partitions.

**2. Où peut-on consulter les erreurs Windows ?**

- A. Dans l'Observateur d'événements.
- B. Dans Paint.
- C. Dans la Calculatrice.

**3. Que signifie un événement de type Erreur dans l'Observateur d'événements ?**

- A. Un problème ou un échec a été enregistré.
- B. L'ordinateur fonctionne toujours normalement.
- C. L'ordinateur a été éteint.

**4. Qu'est-ce que Hiren's BootCD PE ?**

- A. Un jeu.
- B. Un environnement de maintenance démarrable.
- C. Un navigateur Internet.

**5. À quoi sert l'environnement de récupération Windows ?**

- A. À récupérer et dépanner Windows.
- B. À regarder des vidéos.
- C. À créer uniquement des comptes utilisateurs.

**6. Quelle commande permet d'afficher la configuration IP ?**

- A. `hostname`
- B. `ipconfig`
- C. `tasklist`

**7. Que fait la commande&#x20;**`sfc /scannow`**&#x20;?**

- A. Supprime tous les fichiers.
- B. Vérifie et tente de réparer les fichiers système Windows protégés.
- C. Change le nom du PC.

**8. À quoi sert Process Explorer ?**

- A. À analyser les processus en cours.
- B. À installer un disque.
- C. À formater des clés USB.

**9. À quoi sert Clonezilla ?**

- A. Au clonage et à l'imagerie de disques.
- B. À envoyer des emails.
- C. À créer des présentations PowerPoint.

**10. Pourquoi sauvegarder un ordinateur avant de remplacer son disque ?**

- A. Pour préserver les données et pouvoir les récupérer.
- B. Pour ralentir l'ordinateur.
- C. Pour supprimer le clavier.

**11. Quelle est la différence entre Frozen et Thawed dans Deep Freeze ?**

- A. Frozen protège la configuration ; Thawed permet de conserver les modifications.
- B. Frozen éteint le PC ; Thawed l'allume.
- C. Il n'y a aucune différence.

**12. Quelle commande PowerShell permet de lister les processus ?**

- A. `Get-Process`
- B. `Get-Date`
- C. `Get-ChildItem`

**13. Quelle commande PowerShell affiche la configuration réseau ?**

- A. `Get-NetIPConfiguration`
- B. `Get-Service`
- C. `Clear-Host`

**14. Pourquoi faut-il faire attention avec les outils de clonage de disques ?**

- A. Ils peuvent écraser le disque de destination.
- B. Ils suppriment toujours Windows.
- C. Ils ne peuvent pas lire les disques.

**15. Quelle est la première étape d'un dépannage informatique ?**

- A. Formater immédiatement le PC.
- B. Observer le problème et recueillir des informations.
- C. Supprimer tous les journaux.

# **12. Corrigé du quiz**

| Question | Réponse |
| -------- | ------- |
| 1        | B       |
| 2        | A       |
| 3        | A       |
| 4        | B       |
| 5        | A       |
| 6        | B       |
| 7        | B       |
| 8        | A       |
| 9        | A       |
| 10       | A       |
| 11       | A       |
| 12       | A       |
| 13       | A       |
| 14       | A       |
| 15       | B       |

**Évaluation proposée**

- **13 à 15 :** Très bonne compréhension.
- **10 à 12 :** Bonne compréhension.
- **7 à 9 :** Quelques notions à revoir.
- **0 à 6 :** Reprendre certaines démonstrations.

# **13. Checklist du formateur**

Cette checklist permet de suivre les sujets abordés pendant la formation.

## **Séance 1 – 2 heures**

**Dépannage Windows**

- [ ]  Expliquer le rôle d'un technicien informatique.
- [ ] Présenter Faronics Insight.
- [ ] Visualiser un poste étudiant.
- [ ] Envoyer un message via Insight.
- [ ] Expliquer la méthode de dépannage.
- [ ] Ouvrir l'Observateur d'événements.
- [ ] Accéder aux journaux Système.
- [ ] Identifier les Avertissements et Erreurs.
- [ ] Présenter Hiren's BootCD PE.
- [ ] Démarrer sur Hiren's BootCD PE (si possible).
- [ ] Découvrir les outils de maintenance.
- [ ] Présenter l'environnement de récupération Windows.
- [ ] Accéder à WinRE.
- [ ] Identifier la Réparation du démarrage.
- [ ] Identifier la Restauration du système.
- [ ] Identifier l'Invite de commandes dans WinRE.
- [ ] Présenter CMD.
- [ ] Exécuter `hostname`.
- [ ] Exécuter `whoami`.
- [ ] Exécuter `ipconfig`.
- [ ] Exécuter `ipconfig /all`.
- [ ] Exécuter `ping`.
- [ ] Expliquer `sfc /scannow`.
- [ ] Découvrir `tasklist`.
- [ ] Découvrir Process Explorer.
- [ ] Découvrir Autoruns.

## **Séance 2 – 2 heures**

**Maintenance et automatisation**

- [ ] Présenter Clonezilla.
- [ ] Expliquer la différence entre image et clonage.
- [ ] Identifier les disques source et destination.
- [ ] Expliquer la sauvegarde.
- [ ] Expliquer le remplacement d'un SSD.
- [ ] Expliquer la restauration d'un disque.
- [ ] Identifier un HDD et un SSD.
- [ ] Présenter Deep Freeze.
- [ ] Identifier les états Frozen et Thawed.
- [ ] Démontrer la vérification de l'état Deep Freeze.
- [ ] Expliquer le processus de maintenance.
- [ ] Présenter PowerShell.
- [ ] Exécuter `Get- [ ]Date`.
- [ ] Exécuter `Get- [ ]Process`.
- [ ] Exécuter `Get- [ ]NetIPConfiguration`.
- [ ] Exécuter `Get- [ ]ChildItem`.
- [ ] Créer un dossier de test.
- [ ] Supprimer un dossier de test.
- [ ] Faire le quiz.
- [ ] Corriger le quiz.

# **14. Matériel à préparer avant l'arrivée du stagiaire**

| Matériel                        | Utilité                                 |
| ------------------------------- | --------------------------------------- |
| **1 PC de test**                | Démonstrations et exercices.            |
| **Clé USB Hiren's BootCD PE**   | Dépannage hors Windows.                 |
| **Clé USB Clonezilla**          | Démonstration de clonage.               |
| **Ancien SSD/HDD**              | Découverte du matériel.                 |
| **Clé USB**                     | Démonstration de sauvegarde ou d'image. |
| **PC de test avec Deep Freeze** | Démonstration Frozen/Thawed.            |
| **Terminal PowerShell**         | Exercices de commandes.                 |
| **Carnet**                      | Prise de notes du stagiaire.            |

# **15. Conseils pédagogiques pour un stagiaire de 13 ans**

Je ne chercherais pas à lui faire mémoriser toutes les commandes. Le but est de lui donner envie de découvrir l'informatique, de comprendre le fonctionnement d'un ordinateur et de développer une logique de dépannage.

**Les 5 compétences que je souhaite qu'il retienne :**

1. **Observer :** Prendre le temps d'observer le problème avant d'agir.
2. **Investiguer :** Utiliser des outils comme l'Observateur d'événements et CMD pour recueillir des informations.
3. **Protéger :** Comprendre l'importance des sauvegardes et des données.
4. **Réparer :** Découvrir les outils de récupération et les procédures de maintenance.
5. **Automatiser :** Comprendre que PowerShell peut simplifier les tâches répétitives.

Je lui demanderais également de m'expliquer chaque manipulation avec ses propres mots. Cela permet de vérifier qu'il comprend ce qu'il fait plutôt que de simplement reproduire des instructions.

Si tu le souhaites, je peux aussi te préparer une version **PDF prête à imprimer, avec un livret du stagiaire séparé du guide formateur, et un quiz à part**.


## Version : English
# **IT Support Discovery Workshop**

**4 hours total · 2 sessions of 2 hours · Beginner level · 13-year-old trainee**

The goal is to make the trainee discover IT support through practical, safe exercises rather than lengthy explanations. At 13 years old, I would make this a hands-on workshop: explain a concept briefly, demonstrate it, then let the trainee try it.

The workshop is structured as two sessions:

- **Session 1 — 2 hours:** Windows troubleshooting, recovery and command-line basics.
- **Session 2 — 2 hours:** Disk cloning, backup/restoration, Deep Freeze and PowerShell.

# **1. Overall programme**

### **Session 1 — Diagnose & troubleshoot Windows**

2 hours · Observe, investigate and solve problems

- Faronics Insight
- Windows Troubleshooting
- Event Viewer
- Hiren's BootCD PE
- Windows Recovery Environment
- CMD tools (ipconfig, SFC, etc.)
- Introduction to Sysinternals

### **Session 2 — Maintain & restore PCs**

2 hours · Protect data and maintain computers

- Clonezilla
- Backup and restoration
- Disk replacement
- Deep Freeze actions
- Introduction to PowerShell
- Quiz and review

# **SESSION 1 — Windows Troubleshooting (2 hours)**

## **1. Faronics Insight — 15 minutes**

![Classroom Management Software Class Control Software Faronics](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/faronics-insight1.jpg)

![Put to the Test: Insight from Faronics  Tech & Learning](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/faronics-insight2.jpg)

![Faronics Insight – Powerful Classroom Management for Modern Learning](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/faronics-insight3.jpg)

![Faronics Insight – Powerful Classroom Management for Modern Learning](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/faronics-insight4.jpg)

![Faronics Insight – Powerful Classroom Management for Modern Learning](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/faronics-insight5.jpg)

![Faronics Insight – Powerful Classroom Management for Modern Learning](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/faronics-insight6.jpg)

![Faronics Insight – Powerful Classroom Management for Modern Learning](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/faronics-insight7.jpg)

### **What is Faronics Insight?**

Faronics Insight is a classroom management tool used by teachers and IT staff to monitor and manage student computers.

### **What can it do?**

- View students' computer screens.
- Monitor a classroom computer lab.
- Send messages to students.
- Share a teacher screen.
- Restrict or control certain activities.
- Remotely assist students.

### **Practical demonstration**

1. Open the Insight Teacher Console.
2. Identify the student computers.
3. Select a student computer.
4. View its screen.
5. Demonstrate sending a message.
6. Show how the teacher can share a screen.

### **Questions for the trainee**

- Why does the teacher need to see student screens?
- What happens if a student computer is not visible?
- Why should remote control be used responsibly?

**Objective:** Understand the purpose of classroom IT management software.

## **2. Windows Troubleshooting & Event Viewer — 25 minutes**

![How to get started with Event Viewer on Windows 11 Windows Central](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/windows-event-viewer1.jpg)
![Windows 11 is slow even in Safe Mode, even after formatting, fresh install and updating - Microsoft Q&A](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/windows-event-viewer2.jpg)

![How to get started with Event Viewer on Windows 11  Windows Central](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/windows-event-viewer3.jpg)

![How to get started with Event Viewer on Windows 11  Windows Central](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/windows-event-viewer4.jpg)

![How to get started with Event Viewer on Windows 11  Windows Central](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/windows-event-viewer5.jpg)


### **What is troubleshooting?**

Troubleshooting means identifying a problem, understanding its cause and finding a solution.

**The IT technician's method**

1. Observe the problem.
2. Ask questions and collect information.
3. Check the logs and possible causes.
4. Test a solution.
5. Verify that the problem is solved.

### **Example**

**Problem:** A computer freezes when launching an application.

What should we do?

1. Ask when the problem started.
2. Check if other applications work.
3. Check Event Viewer.
4. Identify any relevant errors.
5. Test a solution.
6. Verify that the application works.

### **Event Viewer**

Event Viewer is a Windows tool that records events occurring on a computer.

![L'Observateur d'événements](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/windows-event-viewer5.jpg)

#### **How to open it**

1. Press `Win + R`.
2. Type:

```
eventvwr.msc
```

3. Press Enter.
4. Navigate to:

`Windows Logs → System`

### **Important event types**

| Event           | Meaning                         |
| --------------- | ------------------------------- |
| **Warning**     | Something unusual happened.     |
| **Error**       | Something failed.               |
| **Information** | An event was recorded normally. |

### **Practical exercise**

Ask the trainee to:

- Open Event Viewer.
- Open the System log.
- Find a Warning or Error.
- Read the event description.
- Identify its date and time.

**Objective:** Learn where Windows records problems and how to read basic logs.

## **3. Hiren's BootCD PE — 20 minutes**

![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-1.jpg)


![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-2.jpg)


![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-3.jpg)

![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-4.jpg)

![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-5.jpg)

![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-6.jpg)

![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-7.jpg)

![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-8.jpg)

### **What is Hiren's BootCD PE?**

Hiren's BootCD PE is a bootable Windows-based environment containing various maintenance and troubleshooting tools.

It can be useful when the normal Windows installation cannot start or when a technician needs to inspect or repair a computer.

### **What can it be used for?**

- Diagnose hardware and Windows problems.
- Check disks.
- Copy files from a computer that will not boot.
- Inspect partitions.
- Use various maintenance utilities.

### **Practical demonstration**

1. Boot a test PC or virtual machine from Hiren's BootCD PE.
2. Identify the desktop and available utilities.
3. Open a disk or partition utility.
4. Identify the disks and partitions.
5. Demonstrate browsing files on a test disk.

![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-6.jpg)

![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-7.jpg)

![Software-update: Hiren's BootCD PE 1.0.2 - Computer - Downloads - Tweakers](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/Hiren-Boot-CD-8.jpg)

**Safety:** Do not delete partitions, format disks or modify passwords during this introductory session. Use a test machine.

**Objective:** Discover a maintenance environment that works independently of the installed Windows system.

## **4. Windows Recovery Environment (WinRE) — 20 minutes**

![(5 Methods) How to Enter Windows Recovery Environment?](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/windows-RE-1.jpg)

### **What is WinRE?**

Windows Recovery Environment is a recovery environment built into Windows. It helps troubleshoot and repair Windows when it has problems starting.

### **How to access WinRE**

From Windows:

1. Open Start.
2. Click Power.
3. Hold `Shift`.
4. Click Restart.

Or:

`Settings → System → Recovery → Advanced startup`

### **Important recovery options**

| Option            | Purpose                                                          |
| ----------------- | ---------------------------------------------------------------- |
| Startup Repair    | Attempts to fix startup problems.                                |
| System Restore    | Restores system configuration to a previous point, if available. |
| Startup Settings  | Allows special startup modes, including Safe Mode.               |
| Command Prompt    | Provides command-line access for troubleshooting.                |
| Uninstall Updates | Removes certain problematic updates.                             |

![How to fix your PC's installation using five effective methods on Windows 11 | Windows Central](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/windows-RE-2.jpg)

### **Practical exercise**

On a test machine or virtual machine:

1. Enter WinRE.
2. Open Troubleshoot.
3. Open Advanced Options.
4. Identify each available option.
5. Explain which option you might use if Windows fails to start.

**Objective:** Understand how to access Windows recovery tools and what each option does.

## **5. CMD — Basic Windows command-line tools — 30 minutes**

![File:Command Prompt on Windows 11.png - Wikimedia Commons](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/commandline-1.jpg)

### **What is CMD?**

CMD (Command Prompt) allows us to execute commands directly in Windows instead of using menus and graphical applications.

### **Commands to teach**

| Command              | Purpose                                           |
| -------------------- | ------------------------------------------------- |
| `hostname`           | Displays the computer name.                       |
| `whoami`             | Displays the current user.                        |
| `ipconfig`           | Displays network information.                     |
| `ipconfig /all`      | Displays detailed network configuration.          |
| `ipconfig /release`  | Releases the IPv4 address lease.                  |
| `ipconfig /renew`    | Requests a new IPv4 address lease.                |
| `ipconfig /flushdns` | Clears the DNS resolver cache.                    |
| `ping`               | Tests network connectivity.                       |
| `sfc /scannow`       | Scans and repairs protected Windows system files. |
| `chkdsk`             | Checks a disk/file system.                        |
| `tasklist`           | Displays running processes.                       |
| `taskkill`           | Terminates a process.                             |

### **Examples**

#### **1. Identify the computer**

```
hostname
```

#### **2. Identify the current user**

```
whoami
```

#### **3. Check the network**

```
ipconfig
```

Ask the trainee to identify:

- IPv4 Address
- Subnet Mask
- Default Gateway

#### **4. Test connectivity**

```
ping 8.8.8.8
```

This tests whether the computer can reach an IP address, although a failure doesn't necessarily mean the internet is unavailable.

#### **5. Check Windows system files**

Run Command Prompt as Administrator:

```
sfc /scannow
```

This checks protected Windows system files and attempts repairs where possible.

### **Practical challenge**

Give the trainee these tasks:

- Find the computer's name.
- Find the logged-in user.
- Find the IP address.
- Find the default gateway.
- Ping the gateway.
- Identify running processes.
- Explain what `sfc /scannow` does.

**Objective:** Become familiar with essential Windows command-line tools.

## **6. Introduction to Sysinternals — 10 minutes**

![Process Explorer - Screenshots](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/sysinternals-1.jpg)

### **What is Sysinternals?**

Sysinternals is a collection of advanced Windows troubleshooting and administration utilities provided by Microsoft.

### **Three tools to discover**

| Tool                 | Purpose                                                              |
| -------------------- | -------------------------------------------------------------------- |
| **Process Explorer** | Investigates running processes.                                      |
| **Autoruns**         | Shows applications and components configured to start automatically. |
| **Process Monitor**  | Monitors file, registry and process activity in real time.           |

### **Practical demonstration**

- Open Process Explorer.
- Identify running applications.
- Find a process.
- Observe CPU and memory usage.
- Open Autoruns and identify startup applications.

**Objective:** Discover more advanced tools used by IT technicians.

# **SESSION 2 — Maintenance, Backup & Automation (2 hours)**

## **7. Introduction to Clonezilla — 20 minutes**

![Clonezilla - Download (Linux) - Softpedia](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla1.jpg)

![7 Best Free Disk Cloning Software For Mac in 2022 \[Top Rated\]](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla2.jpg)

![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla3.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla4.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla5.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla6.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla7.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla8.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla9.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla10.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla11.jpg)

### **What is Clonezilla?**

Clonezilla is a free disk imaging and cloning tool. It can copy a disk or partition and restore it later.

### **What is the difference?**

**Disk Image**

Creates an image of a disk and stores it elsewhere for later restoration.

**Disk Clone**

Copies the contents of one disk directly onto another disk.

### **When do we use Clonezilla?**

- Deploy computers with the same configuration.
- Create a backup image of a computer.
- Restore a computer after a major problem.
- Replace a failing disk.
- Clone a computer to another disk.

### **Practical demonstration**

1. Boot Clonezilla.
2. Identify the available disks.
3. Explain `device-image` and `device-device`.
4. Explain the difference between source and destination.
5. Show how a disk image can be created.

![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla9.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla10.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/clonezilla11.jpg)

**Important:** Clonezilla can erase the destination disk. Never select disks randomly. During the introduction, demonstrate the menus without starting an irreversible operation.

**Objective:** Understand how computers can be backed up and restored using disk images.

## **8. Backup, disk replacement and restoration — 20 minutes**

![SSD Upgrade & Installation in Oceanside & Encinitas](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk1.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk2.jpg)
![Clonezilla Live Doc](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk3.jpg)

![Connect the hard drive](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk4.jpg)

![A Basic Guide to Identifying the Major Components in a Desktop System | Dell India](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk5.jpg)

![A Basic Guide to Identifying the Major Components in a Desktop System | Dell India](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk6.jpg)

![A Basic Guide to Identifying the Major Components in a Desktop System | Dell India](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk7.jpg)

![A Basic Guide to Identifying the Major Components in a Desktop System | Dell India](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk8.jpg)

![A Basic Guide to Identifying the Major Components in a Desktop System | Dell India](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk9.jpg)

### **Why do we back up computers?**

A computer's disk can fail. If the data is not backed up, it may be lost.

### **What is a backup?**

A backup is a copy of important data stored separately so that it can be recovered if the original data is lost.

### **Example: Replacing a damaged SSD**

![Objawy uszkodzenia dysku ssd – na co zwrócić uwagę](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk10.jpg)

**Old disk**

Original disk

![Western Digital Blue SN580 1TB M.2 NVMe SSD](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/backupdisk11.jpg)

**New disk**

Replacement

### **Steps to replace a disk**

1. Identify the disk and confirm the problem.
2. Back up important data or create an image.
3. Shut down the computer.
4. Disconnect power.
5. Replace the disk.
6. Restore the disk image or reinstall Windows.
7. Check that Windows starts correctly.
8. Verify applications, data and hardware.

### **Practical demonstration**

Show the trainee:

- An SSD.
- The difference between an HDD and SSD.
- Where the disk is located in a test computer.
- How a disk is removed and replaced, if appropriate.
- How an image can be restored using Clonezilla.

**Safety:** Do not open or disassemble a computer connected to power. Use an old or test machine and follow proper ESD precautions.

**Objective:** Understand the lifecycle of a disk replacement.

## **9. Deep Freeze — 20 minutes**

![Installing Deep Freeze](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/deepfreeze1.jpg)

![Deep Freeze](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/deepfreeze2.jpg)

![Thawed Period](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/deepfreeze3.jpg)

### **What is Deep Freeze?**

Deep Freeze is a computer protection tool that helps preserve a predefined Windows configuration.

It is often used in computer labs, classrooms and public computers.

### **How does it work?**

**Frozen state**

Protected configuration

Changes made during normal use are discarded after a restart, depending on the configured protection and storage settings.

**Thawed state**

Maintenance mode

Changes can be retained after a restart, allowing the IT technician to install updates or modify the computer.

### **Example**

A student installs an application on a classroom computer.

- The computer is Frozen.
- The student installs an application.
- The computer restarts.
- The application installation is reverted, assuming the changes were made on protected storage.

### **Common IT technician actions**

- Check whether the computer is Frozen or Thawed.
- Thaw a computer before maintenance.
- Install updates.
- Restart and verify changes.
- Freeze the computer again.
- Verify that the protection is active.

### **Practical exercise**

On a test computer:

1. Check Deep Freeze status.
2. Explain the Frozen and Thawed states.
3. Create a temporary folder on the desktop while Frozen.
4. Restart the test PC.
5. Check whether the folder remains.
6. Demonstrate how an IT technician prepares for maintenance.

**Safety:** Never perform these tests on a production classroom PC without authorization. Do not modify Deep Freeze passwords or configuration settings.

**Objective:** Understand why classroom computers are protected and how IT technicians maintain them.

## **10. Introduction to PowerShell — 25 minutes**

![Rendering problem, blurry text · Issue #5759 · microsoft/terminal · GitHub](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/powershell1.jpg)

![Rendering problem, blurry text · Issue #5759 · microsoft/terminal · GitHub](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/powershell2.jpg)

![Rendering problem, blurry text · Issue #5759 · microsoft/terminal · GitHub](/blog/assets/images/AtelierdecouvertedeinformatiqueSupportetmaintenanceWindows/powershell3.jpg)

### **What is PowerShell?**

PowerShell is a command-line shell and scripting language used to administer Windows and automate tasks.

It is more powerful than traditional CMD because it works with objects and can automate complex administrative operations.

### **CMD vs PowerShell**

| CMD                            | PowerShell                             |
| ------------------------------ | -------------------------------------- |
| Older command-line environment | Modern shell and scripting language    |
| Mainly text-based commands     | Commands return structured objects     |
| `.bat` files                   | `.ps1` scripts                         |
| Basic administration           | Advanced administration and automation |

### **Important PowerShell commands**

| Command                  | Purpose                             |
| ------------------------ | ----------------------------------- |
| `Get-ComputerInfo`       | Displays computer information.      |
| `Get-Date`               | Displays the current date and time. |
| `Get-Process`            | Lists running processes.            |
| `Get-Service`            | Lists Windows services.             |
| `Get-NetIPConfiguration` | Displays network configuration.     |
| `Get-ChildItem`          | Lists files and folders.            |
| `Get-Location`           | Displays the current directory.     |
| `Clear-Host`             | Clears the console.                 |

### **Examples**

#### **1. Get the computer name**

```
$env:COMPUTERNAME
```

#### **2. Display the current date**

```
Get-Date
```

#### **3. List running processes**

```
Get-Process
```

#### **4. Display network information**

```
Get-NetIPConfiguration
```

#### **5. List files**

```
Get-ChildItem C:\Windows
```

#### **6. Create a folder**

```
New-Item -Path C:\Temp\Trainee -ItemType Directory
```

#### **7. Remove the test folder**

```
Remove-Item C:\Temp\Trainee
```

**Safety:** Explain that some PowerShell commands can delete files, modify Windows settings or affect multiple computers. Always read a command before running it.

### **Mini exercise**

Ask the trainee to:

- Find the computer name.
- Find the current date.
- List running processes.
- Find the network configuration.
- List files in a folder.
- Create a test folder.
- Delete the test folder.

**Objective:** Understand the basics of PowerShell and why IT technicians use it.

## **11. Final quiz — 15 minutes**

You can give this quiz to the trainee at the end of the 4-hour workshop. Suggested scoring: 1 point per question.

### **Quiz**

**1. What is Faronics Insight used for?**

- A. Installing Windows
- B. Managing and monitoring classroom computers
- C. Creating disk partitions

**2. Where can you find Windows system errors?**

- A. Event Viewer
- B. Paint
- C. Calculator

**3. What does an Error event mean in Event Viewer?**

- A. A problem or failure has been recorded
- B. The computer is always working normally
- C. The computer has been shut down

**4. What is Hiren's BootCD PE?**

- A. A game
- B. A bootable maintenance environment
- C. A web browser

**5. What is the purpose of Windows Recovery Environment?**

- A. To recover and troubleshoot Windows
- B. To watch videos
- C. To create user accounts only

**6. What command displays the computer's IP configuration?**

- A. `hostname`
- B. `ipconfig`
- C. `tasklist`

**7. What does&#x20;**`sfc /scannow`**&#x20;do?**

- A. Deletes all files
- B. Checks and repairs protected Windows system files
- C. Changes the computer name

**8. What is Process Explorer used for?**

- A. Investigating running processes
- B. Installing a disk
- C. Formatting USB drives

**9. What is Clonezilla used for?**

- A. Disk imaging and cloning
- B. Sending emails
- C. Creating PowerPoint presentations

**10. Why do we back up a computer before replacing its disk?**

- A. To preserve data and enable recovery
- B. To make the computer slower
- C. To remove the keyboard

**11. What is the difference between Frozen and Thawed in Deep Freeze?**

- A. Frozen protects the configuration; Thawed allows changes to be retained
- B. Frozen turns off the computer; Thawed turns it on
- C. There is no difference

**12. Which PowerShell command lists running processes?**

- A. `Get-Process`
- B. `Get-Date`
- C. `Get-ChildItem`

**13. Which PowerShell command displays network configuration?**

- A. `Get-NetIPConfiguration`
- B. `Get-Service`
- C. `Clear-Host`

**14. Why should we be careful when using disk cloning tools?**

- A. They can overwrite the destination disk
- B. They always delete Windows
- C. They cannot read disks

**15. What is the first step in troubleshooting?**

- A. Immediately format the computer
- B. Observe the problem and collect information
- C. Delete all logs

# **12. Answer key**

| Question | Answer |
| -------- | ------ |
| 1        | B      |
| 2        | A      |
| 3        | A      |
| 4        | B      |
| 5        | A      |
| 6        | B      |
| 7        | B      |
| 8        | A      |
| 9        | A      |
| 10       | A      |
| 11       | A      |
| 12       | A      |
| 13       | A      |
| 14       | A      |
| 15       | B      |

**Suggested assessment**

- **13–15:** Excellent understanding
- **10–12:** Good understanding
- **7–9:** Review a few topics
- **0–6:** Repeat some demonstrations

# **13. Your instructor checklist**

Use this checklist during the session to keep track of the topics covered.

## **Session 1 — 2 hours**

**Windows Troubleshooting**
- [ ] Explain what an IT technician does.
- [ ] Demonstrate Faronics Insight.
- [ ] View a student computer.
- [ ] Send a message through Insight.
- [ ] Explain the troubleshooting methodology.
- [ ] Open Event Viewer.
- [ ] Locate System logs.
- [ ] Identify Warning and Error events.
- [ ] Explain Hiren's BootCD PE.
- [ ] Boot into Hiren's BootCD PE (if available).
- [ ] Discover maintenance utilities.
- [ ] Explain Windows Recovery Environment.
- [ ] Access WinRE.
- [ ] Identify Startup Repair.
- [ ] Identify System Restore.
- [ ] Identify Command Prompt in WinRE.
- [ ] Explain CMD.
- [ ] Run `hostname`.
- [ ] Run `whoami`.
- [ ] Run `ipconfig`.
- [ ] Run `ipconfig /all`.
- [ ] Run `ping`.
- [ ] Explain `sfc /scannow`.
- [ ] Discover `tasklist`.
- [ ] Discover Sysinternals Process Explorer.
- [ ] Discover Autoruns.

## **Session 2 — 2 hours**

**Maintenance & Automation**

- [ ] Explain Clonezilla.
- [ ] Explain disk image vs disk clone.
- [ ] Identify source and destination disks.
- [ ] Explain backup.
- [ ] Explain SSD replacement.
- [ ] Explain disk restoration.
- [ ] Identify an HDD and SSD.
- [ ] Explain Deep Freeze.
- [ ] Identify Frozen and Thawed states.
- [ ] Demonstrate checking Deep Freeze status.
- [ ] Explain the maintenance workflow.
- [ ] Introduce PowerShell.
- [ ] Run `Get- [ ]Date`.
- [ ] Run `Get- [ ]Process`.
- [ ] Run `Get- [ ]NetIPConfiguration`.
- [ ] Run `Get- [ ]ChildItem`.
- [ ] Create a test folder.
- [ ] Remove a test folder.
- [ ] Complete the quiz.
- [ ] Review the answers.

# **14. Suggested materials to prepare beforehand**

| Material                     | Purpose                       |
| ---------------------------- | ----------------------------- |
| **1 test PC**                | Demonstrations and exercises  |
| **Hiren's BootCD PE USB**    | Offline troubleshooting       |
| **Clonezilla USB**           | Disk imaging demonstration    |
| **Old SSD/HDD**              | Physical disk discovery       |
| **USB drive**                | Backup or image demonstration |
| **Deep Freeze test machine** | Frozen/Thawed demonstration   |
| **PowerShell terminal**      | Command exercises             |
| **Notebook**                 | Trainee's notes               |

# **15. My recommendation for a 13-year-old trainee**

Don't try to make the trainee memorise every command. The aim is to develop curiosity, confidence and a basic understanding of what an IT technician does.

**The 5 skills I would like the trainee to leave with:**

1. **Observe:** Look carefully at the problem before doing anything.
2. **Investigate:** Use tools such as Event Viewer and CMD to collect information.
3. **Protect:** Understand backups and why data is important.
4. **Repair:** Discover recovery tools and maintenance procedures.
5. **Automate:** Understand that PowerShell can make repetitive IT tasks easier.

I would also encourage the trainee to explain each task back to you in their own words. That is a better way of checking understanding than simply following instructions.

If you'd like, I can also turn this into a **ready-to-print French training booklet for the stagiaire, with a separate instructor guide and quiz sheet**.
