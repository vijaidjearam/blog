---
layout: post
date: 2025-03-28 14:17:35
title: MailMerge avec ThunderBird
category: thunderbird
tags: thunderbird mailmerge email
---

# MailMerge avec Thunderbird

## Table des matières
1. [Installer le module MailMerge sous Thunderbird](#installer-mailmerge)
2. [Créer la base de données avec CALC de OpenOffice ou Excel](#creer-base-donnees)
3. [MergeMail et Thunderbird](#mergemail-thunderbird)
   - [Rédaction de l’emailing et insertion des champs de données](#redaction-emailing)
   - [Envoyer l’email avec MailMerge](#envoyer-email)

---

## <a name="installer-mailmerge"></a> 1. Installer le module MailMerge sous Thunderbird

L’installation de MailMerge dans Thunderbird est simple :

1. Ouvrir **Thunderbird** et aller dans **Outils** > **Modules Complémentaires**.
2. Dans la fenêtre des modules, sélectionner l’onglet **Catalogue** à gauche, puis cliquer sur **Voir tous**.
3. Dans le champ de recherche, entrer **MailMerge** et appuyer sur **Enter**.
4. Une fois le module trouvé, cliquer sur **Ajouter à Thunderbird**.
5. Après l’installation, redémarrer Thunderbird en cliquant sur **Redémarrer maintenant**.
6. Le module sera listé dans **Extensions**, où il peut être activé/désactivé ou supprimé.

![Installation MailMerge](image1.png)

---

## <a name="creer-base-donnees"></a> 2. Créer la base de données avec CALC de OpenOffice ou Excel

La base de données est une liste de destinataires avec leurs informations spécifiques.

1. Ouvrir **OpenOffice Calc** (ou Excel) et créer un nouveau classeur.
2. La première ligne doit contenir les **intitulés des champs** (Nom, Email, etc.).
3. Les lignes suivantes doivent contenir les données de chaque destinataire.
4. Enregistrer le fichier au format **.ods** pour modification ultérieure.
5. Enregistrer également une copie au format **.csv**, en conservant les options par défaut.

Exemple de structure :
```
Nom,Email
Jean Dupont,jean.dupont@example.com
Marie Curie,marie.curie@example.com
```

![Base de données](image2.png)

---

## <a name="mergemail-thunderbird"></a> 3. MergeMail et Thunderbird

### <a name="redaction-emailing"></a> 3.1 Rédaction de l’emailing et insertion des champs de données

1. Cliquer sur **Écrire** pour rédiger un nouvel email.
2. Insérer les champs de données avec une **double accolade** : `{{Nom}}`.
3. Dans le champ "À", utiliser `{{Email}}` pour insérer l’adresse email du destinataire.
4. Renseigner le sujet de l’email.

Exemple :
```
Bonjour {{Nom}},

Nous vous informons de notre prochaine réunion.

Cordialement,
L'équipe.
```

![Rédaction email](image3.png)

---

### <a name="envoyer-email"></a> 3.2 Envoyer l’email avec MailMerge

1. Dans la fenêtre de rédaction, cliquer sur la **flèche noire** à côté du bouton "Envoyer".
2. Sélectionner **MailMerge**.
3. Dans la fenêtre qui s’ouvre, cliquer sur **Parcourir** et sélectionner le fichier **.csv**.
4. Dans le champ **Attachements**, entrer `file://{{Fichiers}}` si des pièces jointes sont à inclure.
5. Cliquer sur **OK** pour envoyer les emails.
6. Vérifier l’envoi dans le dossier **Envoyé** de Thunderbird.

![Envoi avec MailMerge](image4.png)

---

Ce guide vous permet d’utiliser efficacement MailMerge avec Thunderbird pour vos campagnes d’emailing.
