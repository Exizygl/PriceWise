
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
