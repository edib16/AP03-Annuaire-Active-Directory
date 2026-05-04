# Mode Opératoire - Gestion Active Directory

> **Auteur :** Edib Saoud
> **Date :** 01/2025
> **Version :** 1.0
> **Cible :** Support Informatique Niveau 1 / SISR

---

## 1. Création d'un Nouvel Utilisateur

**Cas d'usage :** Arrivée d'un nouvel étudiant ou d'un nouveau formateur à l'école.

1. Se connecter à `SRV1-DC` avec un compte administrateur du domaine.
2. Ouvrir la console **Utilisateurs et ordinateurs Active Directory** (Menu Démarrer > Outils d'administration).
3. Dérouler `Iris.local` > `IRIS_Utilisateurs` et faire un clic droit sur l'OU correspondante (ex: `Etudiants`).
4. Sélectionner **Nouveau** > **Utilisateur**.
5. Renseigner le Prénom, Nom et Nom d'ouverture de session (ex: `j.dupont`).
6. Renseigner un mot de passe temporaire (ex: `Iris2025!`) et cocher la case **L'utilisateur doit changer le mot de passe à la prochaine ouverture de session**.
7. Valider. L'utilisateur peut maintenant se connecter sur n'importe quel poste du domaine.

---

## 2. Réinitialisation d'un Mot de Passe

**Cas d'usage :** Un utilisateur a oublié son mot de passe ou a verrouillé sa session suite à 5 tentatives échouées.

1. Dans la console AD, utiliser la barre de recherche en haut ou trouver manuellement l'utilisateur dans son OU.
2. Faire un clic-droit sur l'utilisateur > **Réinitialiser le mot de passe...**
3. Entrer le nouveau mot de passe temporaire.
4. Cocher **Déverrouiller le compte de l'utilisateur** (si le compte était bloqué).
5. Cocher **L'utilisateur doit changer le mot de passe à la prochaine ouverture de session**.
6. Cliquer sur OK. L'accès est rétabli immédiatement.

---

## 3. Joindre un Ordinateur Client au Domaine

**Cas d'usage :** Déploiement d'un nouveau PC dans les salles de cours.

1. Allumer le PC client (Windows 10 Pro ou Windows 11 Pro).
2. Vérifier que la carte réseau du client récupère bien l'adresse DNS du contrôleur de domaine (`192.168.10.10`).
   - *Test rapide :* Ouvrir le terminal (`cmd`) et taper `ping Iris.local`. Le ping doit résoudre vers 192.168.10.10.
3. Ouvrir les paramètres Windows > Système > Informations système > **Paramètres avancés du système**.
4. Dans l'onglet **Nom de l'ordinateur**, cliquer sur **Modifier...**
5. Cocher **Domaine** et saisir `Iris.local`.
6. Une fenêtre d'authentification s'ouvre : saisir les identifiants d'un compte Administrateur du domaine.
7. Un message "Bienvenue dans le domaine Iris.local" s'affiche.
8. Redémarrer l'ordinateur. L'écran de connexion proposera de se connecter à "IRIS".

---

## 4. Forcer la mise à jour des Politiques de Sécurité (GPO)

**Cas d'usage :** Vous venez de créer une nouvelle GPO sur le serveur et souhaitez l'appliquer immédiatement sur un poste client sans attendre le rafraîchissement automatique (qui prend environ 90 minutes).

1. Sur le poste client, faire `Windows + R` et taper `cmd`.
2. Taper la commande suivante :
```cmd
gpupdate /force
```
3. Attendre le message de confirmation indiquant que la mise à jour de la stratégie utilisateur et ordinateur a réussi.
4. *Si demandé, redémarrer le poste ou fermer la session pour appliquer certaines stratégies critiques.*
