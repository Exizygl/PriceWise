
# PriceWise Games — Référentiel des règles de gestion

**Fichier cible :** `docs/business-rules.md`  
**Version :** 0.1 — Document de travail  
**Statut :** En cours de validation  
**Périmètre :** Première version publique de PriceWise Games, consacrée aux jeux PC vendus sur Steam en France, avec des prix exprimés en euros.

---

## 1. Objet du document

Ce document définit les règles métier que PriceWise Games doit respecter, indépendamment des choix techniques utilisés pour les implémenter.

Il sert de référence pour :
- concevoir les fonctionnalités ;
- définir le modèle de données ;
- développer les traitements de collecte et de notification ;
- écrire les tests métier ;
- vérifier la cohérence du comportement de l’application.

### 1.1. Statuts des règles

Chaque règle possède un statut permettant de distinguer une décision acquise d'un point à résoudre.

- **VALIDÉE** : décision métier déjà retenue.
- **À PRÉCISER** : principe retenu, mais comportement détaillé à définir.
- **À VALIDER TECHNIQUEMENT** : dépend d’une contrainte externe, notamment de la source de données Steam ou de l’hébergement.
- **À DÉCIDER** : arbitrage métier encore nécessaire.

Une règle validée métier peut néanmoins nécessiter une validation technique avant sa mise en production.

### 1.2. Périmètre fonctionnel

La première version couvre :
- la gestion des comptes utilisateurs ;
- la consultation du catalogue Steam ;
- le suivi individuel des produits ;
- l’historique des prix observés ;
- la détection des promotions et des baisses de prix ;
- les alertes personnalisées par e-mail ;
- la collecte mutualisée des prix.

L’ajout d’autres boutiques, d’autres marchés et d’autres devises est envisagé ultérieurement, mais ne fait pas partie du périmètre initial.

---

## 2. Définitions métier

| Terme | Définition |
|---|---|
| **Jeu (`Game`)** | Œuvre vidéoludique, indépendamment des éditions et contenus permettant de l’acheter. |
| **Produit (`Product`)** | Élément commercialisable : jeu de base, édition spécifique, DLC ou bundle. |
| **Boutique (`Store`)** | Plateforme de vente sur laquelle un produit est proposé. Steam est la première boutique prise en charge. |
| **Marché (`Market`)** | Zone commerciale déterminant notamment la disponibilité, le prix et la devise. Le marché français est le marché initial. |
| **Observation de prix (`PriceObservation`)** | Prix effectivement relevé à un instant donné pour un produit sur un marché. |
| **Prix de référence (`ReferencePrice`)** | Prix retenu comme base de comparaison pour calculer une réduction. |
| **Promotion (`Promotion`)** | Période durant laquelle un produit bénéficie d’un prix promotionnel identifié. |
| **Suivi (`Tracking`)** | Association entre un utilisateur et un produit, avec ses propres paramètres d’alerte. |
| **Préférences de notification** | Paramètres qui déterminent les événements de prix susceptibles de déclencher une alerte pour un suivi. |
| **Événement de prix (`PriceEvent`)** | Changement de prix ou événement commercial détecté par PriceWise Games. |
| **Notification (`Notification`)** | Alerte destinée à un utilisateur à la suite d’un événement de prix pertinent. |
| **Collecte (`CollectionRun`)** | Exécution d’un processus chargé de récupérer et d’enregistrer les prix. |

---

## 3. Comptes et accès

### RG-001 — Création d’un compte

**Statut : VALIDÉE**

L’accès aux fonctionnalités personnalisées nécessite un compte utilisateur.

Un visiteur non connecté peut consulter les fonctionnalités publiques qui ne nécessitent pas d’identification, mais ne peut pas gérer ses propres suivis ni recevoir d’alertes personnalisées.

### RG-002 — Méthodes d’authentification

**Statut : VALIDÉE**

L’application doit proposer les méthodes suivantes :
- adresse e-mail et mot de passe ;
- connexion avec Google ;
- connexion avec Steam.

Chaque méthode doit permettre d’identifier un compte de manière fiable.

### RG-003 — Vérification de l’adresse e-mail

**Statut : VALIDÉE**

Une adresse e-mail doit être vérifiée avant de pouvoir être utilisée pour recevoir des notifications.

Un utilisateur connecté avec Steam ou Google ne doit pas être considéré comme disposant d’une adresse vérifiée sans preuve suffisante de cette vérification.

### RG-004 — Réinitialisation du mot de passe

**Statut : VALIDÉE**

Un utilisateur disposant d’un mot de passe doit pouvoir demander sa réinitialisation sans que l’application révèle si une adresse e-mail est associée à un compte.

La procédure doit reposer sur un mécanisme de vérification temporaire et sécurisé.

### RG-005 — Isolation des données utilisateurs

**Statut : VALIDÉE**

Un utilisateur ne peut consulter ou modifier que ses propres suivis, préférences, notifications et informations personnelles, sauf données explicitement publiques.

Toute opération nécessitant une autorisation doit être contrôlée côté serveur.

### RG-006 — Association de plusieurs méthodes de connexion

**Statut : À PRÉCISER**

Un même utilisateur pourra potentiellement utiliser plusieurs méthodes de connexion.

L’association de comptes existants devra être explicitement sécurisée. La simple égalité de deux adresses e-mail ne devra pas suffire, à elle seule, à associer des comptes lorsque cela ne permet pas de prouver le contrôle des identités concernées.

---

## 4. Catalogue et identité des produits

### RG-007 — Identification d’un produit commercialisable

**Statut : VALIDÉE**

Chaque produit suivi doit correspondre à un élément commercialisable identifiable sur la boutique source.

L’identité du produit doit être suffisamment précise pour éviter de mélanger les prix de deux éditions ou contenus différents.

### RG-008 — Distinction entre jeu et produit

**Statut : VALIDÉE**

Un jeu et les produits commerciaux qui lui sont associés sont deux notions distinctes.

Un jeu peut posséder plusieurs produits : jeu de base, éditions différentes, DLC ou bundles.

Le prix d’un produit ne doit jamais être automatiquement attribué à tous les autres produits du même jeu.

### RG-009 — Relations entre produits

**Statut : VALIDÉE**

Une relation entre un jeu, une édition, un DLC ou un bundle ne doit être enregistrée que si elle repose sur une information suffisamment fiable.

En cas de doute, l’application doit conserver des produits distincts plutôt que d’inventer une relation.

### RG-010 — Disponibilité d’un produit

**Statut : À PRÉCISER**

Un produit indisponible, retiré de la vente ou dont les données ne peuvent plus être récupérées ne doit pas être traité comme un produit dont le prix est nul.

L’application devra distinguer au minimum :
- un prix valide ;
- une indisponibilité confirmée ;
- une donnée absente ou inexploitable ;
- une erreur de collecte.

Les règles exactes de gestion de ces états restent à définir.

---
