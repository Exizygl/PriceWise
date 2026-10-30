# Architecture technique

## 1. Objectif

Ce document décrit l'architecture technique générale de PriceWise Games, les technologies retenues et les responsabilités des différentes parties de l'application.

L'objectif est de disposer d'une architecture simple, maintenable et adaptée à un projet personnel évolutif.

## 2. Stack technique

| Couche | Technologie |
|---|---|
| Frontend | React |
| Langage frontend | TypeScript |
| Outil de développement frontend | Vite |
| Backend | NestJS |
| Langage backend | TypeScript |
| Communication | API REST |
| Base de données | PostgreSQL |
| ORM | Prisma |
| Authentification | E-mail/mot de passe, Google, Steam |

Les versions exactes seront définies au moment de l'initialisation du projet.

## 3. Architecture générale

L'application suit une architecture client-serveur.

Le frontend communique avec le backend au moyen d'une API REST. Le backend centralise la logique métier, l'accès aux données, l'authentification, la collecte des prix et la gestion des notifications.

PostgreSQL stocke les données de l'application. Prisma assure l'accès aux données depuis le backend.

## 4. Responsabilités des composants

### 4.1 Frontend — React

Le frontend est responsable de :
- L'affichage de l'interface utilisateur.
- La navigation entre les pages.
- Les formulaires d'inscription et de connexion.
- La recherche et la consultation des jeux.
- La gestion des produits suivis.
- La configuration des alertes.
- L'affichage des historiques de prix.

Le frontend ne doit pas contenir de secrets ni être responsable des décisions métier sensibles.

### 4.2 Backend — NestJS

Le backend est responsable de :
- L'exposition de l'API REST.
- L'authentification et l'autorisation.
- La validation des données reçues.
- L'application des règles métier.
- La gestion des utilisateurs et des suivis.
- La récupération et l'enregistrement des prix.
- La détection des baisses.
- La création et l'envoi des notifications.

### 4.3 Base de données — PostgreSQL

La base de données stocke notamment :
- Les utilisateurs.
- Les produits et leurs relations.
- Les observations de prix.
- Les suivis personnels.
- Les préférences d'alerte.
- Les alertes et leur état d'envoi.

Les données partagées du catalogue et les données personnelles des utilisateurs doivent rester clairement distinguées.

### 4.4 Accès aux données — Prisma

Prisma est utilisé pour :
- Définir le modèle de données.
- Effectuer les requêtes à la base.
- Gérer les migrations.
- Maintenir une structure cohérente entre le code et la base de données.

## 5. Organisation fonctionnelle du backend

Le backend pourra être organisé autour des modules suivants :

| Module | Responsabilité |
|---|---|
| Auth | Inscription, connexion et authentification |
| Users | Gestion des comptes |
| Games | Catalogue et recherche de produits |
| Tracking | Gestion des produits suivis |
| Prices | Observations et historique des prix |
| Price Collection | Récupération périodique des prix |
| Alerts | Détection et création des alertes |
| Notifications | Envoi et suivi des e-mails |

Cette organisation pourra évoluer selon les besoins réels du projet.

## 6. Collecte des prix

La collecte des prix est une tâche exécutée automatiquement selon une fréquence quotidienne cible.

Le processus général est le suivant :

1. Identifier les produits à actualiser.
2. Récupérer les informations de prix depuis une source compatible.
3. Valider les données reçues.
4. Enregistrer les observations valides.
5. Comparer les observations avec les prix précédents.
6. Identifier les baisses admissibles.
7. Déclencher le traitement des alertes.

Une collecte échouée ne doit pas créer de fausse observation.

La solution technique de planification et la source de données devront être validées avant leur implémentation.

## 7. Gestion des notifications

Les notifications sont traitées indépendamment de la collecte des prix.

Le processus général est le suivant :

1. Une baisse admissible est détectée.
2. Une alerte est créée.
3. L'alerte est placée dans une file d'attente.
4. Le système tente d'envoyer l'e-mail.
5. Le résultat de l'envoi est enregistré.
6. Les échecs temporaires donnent lieu à de nouvelles tentatives dans les limites configurées.

Le système doit éviter les notifications en double.

## 8. Sécurité

Les principes suivants doivent être respectés :
- Les mots de passe sont stockés sous forme hachée.
- Les secrets et clés API restent côté serveur.
- Les routes privées nécessitent une authentification.
- Les permissions sont vérifiées côté backend.
- Les données d'un utilisateur ne sont accessibles qu'à cet utilisateur ou aux traitements autorisés.
- Les données reçues par l'API sont validées.
- Les connexions et échanges sensibles utilisent HTTPS en production.

## 9. Déploiement

L'application devra pouvoir être déployée avec :
- Un frontend accessible publiquement.
- Un backend accessible par le frontend.
- Une base PostgreSQL.
- Un mécanisme d'exécution des tâches planifiées.
- Un service d'envoi d'e-mails.

Le choix des hébergeurs et des fournisseurs reste à confirmer en fonction du budget cible de 5 € par mois maximum.

## 10. Contraintes et décisions en attente

Les points suivants restent à valider :
- Hébergement du frontend et du backend.
- Hébergement de PostgreSQL.
- Solution de planification des collectes quotidiennes.
- Source de données des prix Steam et conditions d'utilisation.
- Fournisseur d'e-mails.
- Gestion des sauvegardes.
- Stratégie de journalisation et de surveillance.
- Faisabilité du budget mensuel.

## 11. Principes d'évolution

L'architecture doit rester suffisamment simple pour une première version.

Les évolutions futures pourront notamment inclure :
- D'autres boutiques de jeux.
- D'autres marchés et devises.
- Des préférences de notification supplémentaires.
- Des fonctionnalités de comparaison de produits.
- Des statistiques de prix plus avancées.

Ces évolutions ne doivent pas compliquer inutilement la première version.
