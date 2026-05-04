# Procédure d'Installation - Serveur Active Directory

> **Auteur :** Edib Saoud
> **Date :** 01/2025
> **Version :** 1.0
> **Statut :** Validé

---

## 1. Pré-requis Système

- Machine physique ou virtuelle avec au moins 2 vCPU, 4 Go de RAM et 60 Go de disque.
- Fichier ISO de **Windows Server 2022** (Standard ou Datacenter).
- Connexion réseau configurée.

---

## 2. Phase 1 : Installation et Préparation (Post-Instal)

1. Installer Windows Server 2022 (Expérience utilisateur / Interface graphique).
2. Ouvrir les paramètres réseau et configurer l'adresse IP statique :
   - **Adresse IPv4 :** `192.168.10.10`
   - **Masque de sous-réseau :** `255.255.255.0`
   - **Passerelle par défaut :** `192.168.10.254`
   - **Serveur DNS préféré :** `127.0.0.1` (Le serveur lui-même)
3. Ouvrir le *Gestionnaire de serveur* > **Serveur local**.
4. Changer le nom de l'ordinateur en `SRV1-DC` et redémarrer la machine.

---

## 3. Phase 2 : Promotion en Contrôleur de Domaine (AD DS)

1. Depuis le *Gestionnaire de serveur*, cliquer sur **Ajouter des rôles et des fonctionnalités**.
2. Sélectionner l'installation basée sur un rôle ou une fonctionnalité.
3. Cocher les rôles **Services AD DS** et **Serveur DNS**. Valider les fonctionnalités additionnelles requises.
4. Lancer l'installation.
5. Une fois terminée, cliquer sur le drapeau d'alerte jaune en haut et sélectionner **Promouvoir ce serveur en contrôleur de domaine**.
6. Dans l'assistant de configuration :
   - Sélectionner **Ajouter une nouvelle forêt**.
   - Nom de domaine racine : `Iris.local`.
   - Laisser les niveaux fonctionnels de forêt et de domaine par défaut (Windows Server 2016+).
   - Définir un mot de passe de restauration des services d'annuaire (DSRM).
7. Suivre l'assistant jusqu'à l'installation finale. Le serveur redémarrera automatiquement.

---

## 4. Phase 3 : Structuration de l'Annuaire (OUs)

Ouvrir l'outil **Utilisateurs et ordinateurs Active Directory** (`dsa.msc`).

1. Faire un clic-droit sur le domaine `Iris.local` > Nouveau > **Unité d'organisation**.
2. Créer l'arborescence suivante :
   - `IRIS_Utilisateurs`
     - `Etudiants`
     - `Professeurs`
     - `Admins_IT`
   - `IRIS_Ordinateurs`
     - `Salles_Cours`
3. Créer les groupes de sécurité globaux : `GG_Etudiants`, `GG_Profs`, `GG_Admins`.

---

## 5. Phase 4 : Stratégies de Groupe (GPO)

Ouvrir l'outil **Gestion de stratégie de groupe** (`gpmc.msc`).

### 5.1 GPO : Restrictions Étudiants
- **Emplacement :** Liée à l'OU `Etudiants`.
- **Paramètres configurés :**
  - *Configuration utilisateur > Stratégies > Modèles d'administration > Panneau de configuration* : "Interdire l'accès au Panneau de configuration et à l'application Paramètres du PC" (Activé).
  - *Système* : "Empêcher l'accès à l'invite de commandes" (Activé).

### 5.2 GPO : Lecteur Réseau Professeurs
- **Emplacement :** Liée à l'OU `Professeurs`.
- **Paramètres configurés :**
  - *Configuration utilisateur > Préférences > Paramètres Windows > Mappages de lecteurs*.
  - Création d'un lecteur `P:` pointant vers le chemin UNC `\\SRV1-DC\Partage_Profs`.

*(Pour forcer l'application immédiate de la politique sur le domaine, ouvrir une invite de commandes en tant qu'administrateur et exécuter `gpupdate /force`).*
