# Dossier de Choix Technique - Annuaire Active Directory

> **Auteur :** Edib Saoud
> **Date :** 01/2025
> **Version :** 1.0
> **Statut :** Validé

---

## 1. Contexte et Problématique

### Situation de l'existant
L'école disposait de postes clients (Windows 10/11) en "Workgroup" (groupe de travail). L'authentification se faisait via des comptes locaux.
- **Risques identifiés :** Mots de passe faibles, pas de blocage des sessions en cas d'absence, aucune centralisation pour la révocation des droits, impossibilité de déployer massivement des configurations.

### Besoin
Mettre en place une authentification centralisée et une gestion de flotte (stratégies de sécurité unifiées) pour les Étudiants, les Professeurs et les Administrateurs.

---

## 2. Choix d'Architecture

### 2.1 Système d'Exploitation et Service d'Annuaire

| **Solution** | **Avantages** | **Inconvénients** | **Choix** |
|:---|:---|:---|:---:|
| **Samba4 (Linux)** | Gratuit, léger | Déploiement complexe des GPO avancées | ❌ |
| **Windows Server 2022 (AD DS)** | Intégration native avec Windows 10/11, gestion simplifiée des GPO, standard de l'industrie | Nécessite une licence Windows Server | ✅ |

**Décision :** La technologie **Microsoft Active Directory Domain Services (AD DS)** couplée au rôle **DNS** a été retenue car elle représente le standard absolu en entreprise pour la gestion des parcs Windows. 

### 2.2 Paramètres d'Infrastructure

- **Nom du serveur (Hostname) :** `SRV1-DC`
- **Nom du domaine (Forêt) :** `Iris.local`
- **Adresse IP statique :** `192.168.10.10 /24`
- **Passerelle :** `192.168.10.254`
- **DNS Principal :** `127.0.0.1` (Le serveur sera lui-même serveur DNS pour la zone locale).

---

## 3. Modélisation de l'Annuaire (OUs)

Afin d'appliquer des stratégies de sécurité différentes selon le public, une structure en Unités Organisationnelles (OU) a été conçue :

```text
Iris.local (Racine du domaine)
│
├── 📁 IRIS_Utilisateurs
│   ├── 📁 Etudiants      (Droits restreints)
│   ├── 📁 Professeurs    (Droits intermédiaires)
│   └── 📁 Admins_IT      (Privilèges élevés)
│
└── 📁 IRIS_Ordinateurs
    ├── 📁 Salles_Cours
    └── 📁 Postes_Admins
```

---

## 4. Analyse des Risques et Atténuation

| Risque Identifié | Impact | Probabilité | Mesure de prévention |
|:---|:---:|:---:|:---|
| **Perte du service AD (Crash de `SRV1-DC`)** | Critique | Faible | Déploiement ultérieur recommandé d'un contrôleur de domaine secondaire (RODC ou DC2) pour la tolérance de pannes. |
| **Conflit DNS (Les clients ne trouvent pas le domaine)** | Élevé | Moyen | S'assurer que le DHCP distribue bien `192.168.10.10` comme serveur DNS principal à tous les postes. |
| **GPO bloquante (ex: blocage total du menu Démarrer)** | Élevé | Moyen | Toujours tester une nouvelle GPO sur l'OU "Etudiants_Test" avant de l'appliquer à toute l'école. |
| **Usurpation de droits** | Fort | Faible | Application du principe du moindre privilège (RBAC). Seule l'OU "Admins_IT" fait partie du groupe *Admins du domaine*. |
