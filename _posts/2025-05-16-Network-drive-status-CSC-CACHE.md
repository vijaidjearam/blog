---
layout: post
date: 2025-05-16 15:05:00
title: Network Drive Status CSC CACHE
category: WindowsShare
tags: NAS windows networkshare SMB
---

# 📁 Désactivation des fichiers hors connexion dans le Centre de synchronisation pour résoudre les conflits CSC CACHE

## 📝 Problème

Un lecteur réseau affiche le statut `CSC CACHE`, ce qui indique que les **fichiers hors connexion** sont activés via le **Centre de synchronisation**. Cela entraîne les problèmes suivants :
![image](https://github.com/user-attachments/assets/39d72756-655b-43c4-9ce3-8f50d5285739)
- Dossiers incomplets ou contenus manquants.
- Conflits de fichiers ou fichiers obsolètes.
- Impossibilité d’accéder à certains fichiers présents sur le serveur.

## ✅ Objectif

Désactiver les **fichiers hors connexion** pour que le lecteur réseau reflète le contenu en temps réel sans mise en cache ni conflit.

---

## 🔧 Étapes à suivre

### Étape 1 : Vérifier le statut dans le Centre de synchronisation

1. Ouvrir le **Panneau de configuration**.
2. Rechercher et ouvrir le **Centre de synchronisation**.
![image](https://github.com/user-attachments/assets/e2a3d421-e348-4218-8fc3-8691339536b1)
3. Dans le menu de gauche, cliquer sur **Gérer les fichiers hors connexion**.
4. Vérifier le statut :
   - Si le message indique que **les fichiers hors connexion sont activés**, passer à l’étape suivante.

---

### Étape 2 : Désactiver les fichiers hors connexion

1. Dans la fenêtre **Fichiers hors connexion**, cliquer sur **Désactiver les fichiers hors connexion**.
![image](https://github.com/user-attachments/assets/168e6efb-95e8-4f6b-9d6f-26ce33dec005)
2. Cliquer sur **OK**.
3. **Redémarrer l’ordinateur** lorsque cela est demandé.

---

### Étape 3 : Vider le cache CSC (optionnel mais recommandé)

Si le lecteur réseau affiche toujours `CSC CACHE` ou si le problème persiste, il est conseillé de vider le cache des fichiers hors connexion.

> ⚠️ **Attention** : cette opération supprimera tous les fichiers en cache. S’assurer que toutes les modifications ont été synchronisées et sauvegardées.

#### a. Modifier le registre pour vider le cache au redémarrage

1. Appuyer sur `Windows + R`, taper `regedit`, puis appuyer sur Entrée.
2. Naviguer jusqu’à la clé suivante :

   ```
   HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\CSC
   ```

3. Créer ou modifier la valeur DWORD suivante :
   - **Nom** : `FormatDatabase`
   - **Valeur** : `1`

4. Fermer l’Éditeur du Registre et **redémarrer l’ordinateur**.

---

### Étape 4 : Vérifier l’accès au lecteur réseau

Après redémarrage :

1. Ouvrir l’**Explorateur de fichiers**.
2. Accéder au lecteur réseau concerné.
3. Vérifier que :
   - Le lecteur n’indique plus `CSC CACHE`.
   - Tous les fichiers et dossiers sont visibles et à jour.

---

