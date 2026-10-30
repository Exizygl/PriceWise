# PriceWise Games

Application web de suivi des prix des jeux PC sur Steam et de notification des baisses de prix.

## Présentation

PriceWise Games permet aux utilisateurs de suivre les prix des jeux Steam, de consulter leur historique et de recevoir des alertes par e-mail lorsqu'une baisse de prix correspond à leurs préférences.

### Périmètre initial

- Boutique : Steam
- Plateforme : PC
- Marché : France
- Devise : EUR
- Collecte des prix : quotidienne (objectif)

## Fonctionnalités prévues

- Création de compte et authentification.
- Recherche de jeux et consultation des fiches produits.
- Suivi personnalisé des jeux.
- Définition d'un prix cible ou activation des alertes pour toute réduction.
- Consultation de l'historique des prix.
- Notifications par e-mail.
- Gestion des suivis et du compte utilisateur.

## Technologies

| Partie | Technologie |
|---|---|
| Frontend | React, TypeScript, Vite |
| Backend | NestJS, TypeScript |
| API | REST |
| Base de données | PostgreSQL |
| ORM | Prisma |

## Structure du projet

La structure définitive sera précisée lors de l'initialisation du code.

Organisation envisagée :

```text
pricewise-games/
├── docs/
│   ├── business-rules.md
│   ├── functional-specifications.md
│   └── technical-architecture.md
├── frontend/
├── backend/
├── .gitignore
└── README.md
```

## Installation

Les instructions d'installation seront ajoutées lorsque le frontend, le backend et la base de données auront été initialisés.

### Prérequis envisagés

- Node.js
- npm
- PostgreSQL
- Git

## Configuration

Les variables d'environnement nécessaires seront documentées lors de la mise en place des services.

Les secrets, mots de passe et clés API ne doivent jamais être commités dans le dépôt Git.

## Lancement

Les commandes de démarrage seront ajoutées après la création des applications frontend et backend.

## Documentation

Les documents de référence sont disponibles dans le dossier `docs/` :

- `business-rules.md` : règles métier de l'application.
- `functional-specifications.md` : fonctionnalités et parcours utilisateur.
- `technical-architecture.md` : architecture et choix techniques.

## Budget cible

Le budget d'exploitation visé est de 5 € par mois maximum.

Cette contrainte reste à valider selon les fournisseurs, les quotas et les besoins réels d'hébergement.

## Statut du projet

**Phase actuelle :** préparation et documentation.

Les prochaines étapes sont :
1. Finaliser la documentation.
2. Initialiser le dépôt et les applications.
3. Mettre en place PostgreSQL et Prisma.
4. Développer l'authentification.
5. Développer le catalogue et le suivi des produits.
6. Implémenter la collecte des prix.
7. Implémenter les alertes par e-mail.
