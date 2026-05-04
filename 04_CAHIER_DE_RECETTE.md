# Cahier de Recette et Tests - Annuaire Active Directory

> **Auteur :** Edib Saoud
> **Date :** 01/2025
> **Version :** 1.0
> **Statut :** Validé

---

## 1. Objectifs de la Recette

L'objectif de ce document est de valider le bon fonctionnement de l'infrastructure d'authentification centralisée basée sur Windows Server 2022 et le domaine `Iris.local`. Il certifie que la jonction des clients, la résolution DNS et l'application des GPO sont pleinement opérationnelles.

## 2. Tableau de Recette Active Directory

| N° | Catégorie | Scénario de Test | Procédure | Résultat Attendu | Statut |
|:---|:---|:---|:---|:---|:---:|
| **T1** | **Réseau** | Résolution DNS | Depuis un poste client local (Windows 10) non encore joint au domaine, exécuter `nslookup Iris.local`. | Le retour de la commande affiche l'adresse IP `192.168.10.10` en réponse, prouvant que le service DNS de SRV1-DC répond. | ✅ Validé |
| **T2** | **Domaine** | Jonction de client | Suivre la procédure de jonction (voir Mode Opératoire) depuis un PC sous Windows 10 Pro. | Succès de l'authentification avec le compte Admin et apparition du message "Bienvenue dans le domaine". L'ordinateur apparaît dans l'OU par défaut de l'AD. | ✅ Validé |
| **T3** | **Auth.** | Connexion Étudiant | Depuis le client joint, entrer les identifiants d'un compte créé dans l'OU `Etudiants`. | Ouverture du bureau Windows (création du profil local la première fois). | ✅ Validé |
| **T4** | **Sécurité** | Changement MDP imposé | Se connecter avec un compte ayant la case "L'utilisateur doit changer le mot de passe..." cochée. | L'écran d'accueil bloque l'accès et force l'utilisateur à créer un nouveau mot de passe conforme aux règles de sécurité. | ✅ Validé |
| **T5** | **GPO (1)** | Restrictions Étudiants | Depuis une session Étudiant, tenter d'ouvrir `cmd.exe` (l'invite de commandes) et le Panneau de configuration. | Un message d'erreur indique que ces fonctionnalités ont été désactivées par l'administrateur système. | ✅ Validé |
| **T6** | **GPO (2)** | Lecteurs Professeurs | Se déconnecter, puis ouvrir une session avec un compte de l'OU `Professeurs`. Ouvrir l'Explorateur de fichiers. | L'invite de commandes est accessible, et le lecteur réseau (P:) "Partage_Profs" est automatiquement monté dans l'explorateur. | ✅ Validé |

---

## 3. Synthèse de la Recette

L'ensemble des objectifs du cahier des charges a été atteint :
- L'authentification centralisée est opérationnelle et empêche l'accès aux comptes locaux non autorisés.
- Le découpage en Unités Organisationnelles permet une ségrégation propre des privilèges (les Professeurs ont des droits et des accès que les Étudiants n'ont pas).
- La structure Microsoft est stable, performante, et offre un cadre sécurisé pour la gestion du parc informatique pédagogique.

**Décision finale :** L'annuaire `Iris.local` est validé et prêt à être déployé sur l'ensemble du parc de l'école.
