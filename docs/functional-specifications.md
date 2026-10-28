# Spécifications fonctionnelles

## 1. Objectif

Ce document décrit les fonctionnalités attendues de PriceWise Games, les principaux parcours utilisateur et les critères permettant de vérifier le comportement de l'application.

Les règles métier détaillées sont définies dans `docs/business-rules.md`. En cas de modification d'une règle métier, les spécifications fonctionnelles et les tests associés doivent être mis à jour.

## 2. Périmètre fonctionnel

### 2.1 Inclus dans la première version

- Création de compte et authentification.
- Recherche de jeux et consultation des fiches produits.
- Suivi de produits Steam.
- Configuration des préférences d'alerte par produit.
- Définition d'un prix cible ou activation du mode « Toute réduction ».
- Collecte quotidienne des prix, sous réserve de faisabilité technique.
- Consultation de l'historique des prix disponibles.
- Création et envoi d'alertes par e-mail.
- Modification et suppression des suivis.
- Suppression du compte utilisateur.

### 2.2 Hors périmètre initial

Sauf décision ultérieure, la première version ne prévoit pas :
- La prise en charge de plusieurs boutiques autres que Steam.
- La prise en charge de plusieurs marchés autres que le marché français.
- L'achat de jeux directement depuis PriceWise Games.
- La garantie de détection de toutes les variations de prix entre deux collectes.
- La classification des promotions lorsque les données disponibles ne permettent pas de le faire de manière fiable.

## 3. Acteurs

### Utilisateur non authentifié

Peut accéder aux pages publiques et aux fonctionnalités de consultation qui ne nécessitent pas de compte.

### Utilisateur authentifié

Peut gérer son compte, rechercher des produits, suivre des jeux, configurer ses alertes et consulter les données associées.

### Système PriceWise Games

Collecte et enregistre les prix, compare les observations, applique les préférences des utilisateurs et gère les notifications.

## 4. Fonctionnalités

### F-001 — Création de compte

**Objectif :** permettre à un nouvel utilisateur de créer un compte.

**Parcours nominal :**
1. L'utilisateur ouvre le formulaire d'inscription.
2. Il fournit les informations demandées.
3. Le système valide les informations.
4. Le système crée le compte.
5. Si l'inscription utilise une adresse e-mail, un message de vérification est envoyé.
6. L'utilisateur vérifie son adresse avant d'utiliser les fonctionnalités qui exigent un compte vérifié.

**Critères d'acceptation :**
- Une inscription valide permet de créer un compte.
- Une inscription invalide affiche un message explicite.
- Un compte ne peut pas être créé en réutilisant une adresse déjà associée à un compte lorsque cette adresse doit être unique.
- Les mots de passe ne sont jamais stockés en clair.

### F-002 — Connexion et récupération du compte

**Objectif :** permettre à un utilisateur d'accéder à son compte.

**Parcours nominal :**
1. L'utilisateur choisit une méthode d'authentification disponible.
2. Il fournit les informations demandées ou utilise le fournisseur externe.
3. Le système vérifie l'authentification.
4. L'utilisateur accède à son espace personnel.

**Critères d'acceptation :**
- Une authentification valide ouvre une session.
- Une authentification invalide n'accorde aucun accès.
- La réinitialisation du mot de passe est disponible pour les comptes concernés.
- Les erreurs ne divulguent pas d'informations sensibles sur les comptes.

**Méthodes prévues :** e-mail et mot de passe, Google et Steam.

### F-003 — Recherche de produits

**Objectif :** permettre à l'utilisateur de trouver un jeu ou un produit Steam.

**Parcours nominal :**
1. L'utilisateur saisit un nom ou un terme de recherche.
2. Le système recherche les produits correspondants.
3. Les résultats présentent les informations nécessaires pour distinguer les produits.
4. L'utilisateur sélectionne un produit.

**Critères d'acceptation :**
- Les résultats correspondent raisonnablement à la recherche.
- Les produits distincts ne sont pas confondus.
- Un DLC, un bundle et une édition spécifique sont identifiables comme tels lorsque les données le permettent.
- Une recherche sans résultat affiche un état explicite.

### F-004 — Consultation d'une fiche produit

**Objectif :** présenter les informations disponibles sur un produit.

**La fiche peut notamment afficher :**
- Le nom du produit.
- Son type et les informations d'identification disponibles.
- Le prix actuellement connu et la date de son observation.
- L'historique des prix disponibles.
- L'état du suivi pour l'utilisateur connecté.

**Critères d'acceptation :**
- La date de dernière observation est visible.
- Un prix indisponible ou ancien n'est pas présenté comme un prix fraîchement vérifié.
- L'historique ne contient pas de données historiques inventées.
- Les informations affichées correspondent au produit sélectionné.

### F-005 — Ajouter un produit au suivi

**Objectif :** permettre à l'utilisateur de suivre un produit.

**Parcours nominal :**
1. L'utilisateur ouvre une fiche produit.
2. Il sélectionne l'action permettant de suivre le produit.
3. Le système crée le suivi personnel.
4. L'utilisateur configure ses préférences d'alerte.

**Critères d'acceptation :**
- Le suivi est associé à l'utilisateur authentifié.
- Un suivi déjà existant n'est pas dupliqué involontairement.
- L'ajout d'un produit ne déclenche pas d'alerte immédiate simplement parce que son prix respecte déjà le prix cible.
- Le dernier prix valide connu sert de référence initiale lorsqu'il existe.
- La première observation valide sert de référence lorsqu'aucun prix n'est disponible.

### F-006 — Configurer les alertes d'un suivi

**Objectif :** permettre à l'utilisateur de définir les conditions d'alerte pour chaque produit suivi.

**Modes prévus :**
- **Prix cible :** l'utilisateur indique un prix maximal souhaité.
- **Toute réduction :** l'utilisateur souhaite être averti des baisses admissibles sans définir de prix cible.

**Critères d'acceptation :**
- Les préférences sont enregistrées séparément pour chaque produit suivi.
- Le mode « Toute réduction » ne s'applique pas simultanément à un suivi qui possède un prix cible.
- Le changement de prix cible ne déclenche pas d'alerte à lui seul.
- Les événements antérieurs au changement de préférence ne sont pas rejoués pour créer des alertes.
- La désactivation des alertes suspend les nouveaux déclenchements pour le suivi concerné.

### F-007 — Détecter une baisse de prix

**Objectif :** identifier les baisses à partir des observations valides.

**Parcours nominal :**
1. Une collecte de prix réussit.
2. Le système enregistre la nouvelle observation.
3. Il la compare au dernier prix valide pertinent.
4. Il détermine si une baisse a eu lieu.
5. Il évalue les préférences des suivis concernés.
6. Il crée les alertes qui satisfont les règles métier.

**Critères d'acceptation :**
- Une collecte échouée ne crée aucun relevé fictif.
- Après un échec, le nouveau prix valide est comparé au dernier prix valide.
- Un prix inchangé ne déclenche pas une nouvelle alerte de baisse.
- Une nouvelle baisse après une remontée peut déclencher une nouvelle alerte.
- Les baisses survenues pendant la désactivation d'un suivi ne sont pas rattrapées après réactivation.
- Les événements identiques ne produisent pas plusieurs alertes pour le même suivi.

### F-008 — Consulter l'historique des prix

**Objectif :** permettre à l'utilisateur de comprendre l'évolution du prix d'un produit.

**Critères d'acceptation :**
- Les observations disponibles sont présentées dans l'ordre chronologique.
- Chaque observation comporte sa date et son prix.
- Les périodes sans collecte réussie ne sont pas remplies par des valeurs inventées.
- Le prix actuellement affiché est associé à sa date de dernière observation.

### F-009 — Envoyer et suivre les alertes par e-mail

**Objectif :** notifier l'utilisateur lorsqu'une condition d'alerte est satisfaite.

**Parcours nominal :**
1. Le système crée une alerte à partir d'un événement admissible.
2. L'alerte est mise en attente d'envoi.
3. Le système tente l'envoi par e-mail.
4. Le résultat est enregistré.
5. En cas d'échec temporaire, le système réessaie dans les limites configurées.
6. En cas de succès, l'alerte est marquée comme envoyée.

**Critères d'acceptation :**
- Une alerte n'est créée que si les conditions métier sont satisfaites.
- Les échecs d'envoi sont identifiables.
- Les tentatives de renvoi ne doivent pas créer plusieurs e-mails pour la même alerte.
- Une limite de tentatives est appliquée.
- Une alerte ayant atteint cette limite est marquée comme ayant échoué.
- Aucune nouvelle alerte ne doit être envoyée à un compte supprimé.

**À préciser :** le fournisseur d'e-mails, le nombre maximal de tentatives et les délais entre les tentatives.

### F-010 — Modifier ou supprimer un suivi

**Objectif :** permettre à l'utilisateur de gérer sa liste de produits suivis.

**Critères d'acceptation :**
- L'utilisateur peut modifier les préférences de ses suivis.
- L'utilisateur peut désactiver ou réactiver les alertes d'un suivi.
- L'utilisateur peut retirer un produit de sa liste.
- La modification d'un suivi n'affecte pas les préférences des autres utilisateurs.
- Le retrait d'un suivi ne supprime pas le produit du catalogue commun ni son historique de prix partagé.

### F-011 — Supprimer un compte

**Objectif :** permettre à l'utilisateur de supprimer son compte.

**Critères d'acceptation :**
- Le compte supprimé ne peut plus être utilisé pour s'authentifier.
- Les suivis, préférences et données personnelles supprimables sont effacés conformément aux règles de conservation applicables.
- Les données partagées du catalogue et l'historique commun des prix sont conservés.
- Les alertes en attente ne peuvent plus être envoyées à l'utilisateur supprimé.
- Les données des autres utilisateurs ne sont pas affectées.

## 5. Parcours utilisateur principaux

### Parcours A — Suivre un jeu avec un prix cible

1. L'utilisateur se connecte.
2. Il recherche un jeu.
3. Il ouvre la fiche du produit.
4. Il ajoute le produit à ses suivis.
5. Il définit un prix cible.
6. PriceWise observe les prix lors des collectes.
7. Si une nouvelle baisse admissible respecte les conditions d'alerte, le système crée une notification.
8. L'utilisateur reçoit un e-mail si l'envoi aboutit.

### Parcours B — Suivre un jeu pour toute réduction

1. L'utilisateur se connecte.
2. Il recherche et sélectionne un produit.
3. Il l'ajoute à ses suivis.
4. Il active le mode « Toute réduction » sans prix cible.
5. PriceWise détecte une baisse admissible.
6. Le système crée une notification si les préférences l'autorisent.

Le traitement des baisses dont la nature est inconnue reste soumis aux règles qui seront précisées pour ce cas.

### Parcours C — Désactiver puis réactiver les alertes

1. L'utilisateur désactive les alertes d'un suivi.
2. Le prix du produit peut évoluer pendant cette période, sans déclencher de nouvelles alertes pour ce suivi.
3. L'utilisateur réactive les alertes.
4. Le prix courant connu devient la nouvelle référence.
5. Une nouvelle baisse admissible observée ultérieurement peut déclencher une alerte.

### Parcours D — Supprimer son compte

1. L'utilisateur demande la suppression de son compte.
2. Le système empêche toute nouvelle utilisation du compte.
3. Les suivis, préférences et données personnelles concernées sont supprimés.
4. Les notifications en attente sont rendues non envoyables.
5. Le catalogue partagé et l'historique commun des prix sont conservés.

## 6. Scénarios de validation des alertes

### Scénario 1 — Le prix reste au-dessus du seuil

**Étant donné** un prix cible de 25 € et un prix observé de 30 €,  
**quand** le prix passe à 27 €,  
**alors** aucune alerte liée au prix cible n'est déclenchée.

### Scénario 2 — Le prix passe sous le seuil

**Étant donné** un prix cible de 25 € et un prix observé de 30 €,  
**quand** le prix passe à 24 €,  
**alors** une alerte admissible est créée.

### Scénario 3 — Le prix diminue encore sous le seuil

**Étant donné** un prix cible de 25 € et un prix observé de 24 €,  
**quand** une nouvelle observation valide indique 22 €,  
**alors** une nouvelle alerte peut être créée, conformément à la règle de baisse continue.

### Scénario 4 — Le prix reste inchangé

**Étant donné** un prix cible de 25 € et un prix observé de 22 €,  
**quand** l'observation suivante indique encore 22 €,  
**alors** aucune nouvelle alerte de baisse n'est créée.

### Scénario 5 — Le prix remonte puis redescend

**Étant donné** un prix cible de 25 € et un prix observé de 24 €,  
**quand** le prix remonte à 28 €, puis redescend à 24 €,  
**alors** la nouvelle baisse admissible peut déclencher une nouvelle alerte.

### Scénario 6 — Le prix respecte déjà le seuil à l'ajout

**Étant donné** un prix cible de 25 € et un dernier prix connu de 24 €,  
**quand** l'utilisateur ajoute le produit à ses suivis,  
**alors** aucune alerte immédiate n'est déclenchée.

### Scénario 7 — Le seuil est modifié

**Étant donné** un prix actuel de 30 € et un prix cible de 25 €,  
**quand** l'utilisateur modifie son prix cible à 35 €,  
**alors** aucune alerte immédiate n'est déclenchée du seul fait de cette modification.

### Scénario 8 — Les alertes sont réactivées

**Étant donné** qu'un suivi a ses alertes désactivées et que le prix baisse pendant cette période,  
**quand** l'utilisateur réactive les alertes,  
**alors** la baisse passée n'est pas rattrapée.

### Scénario 9 — Une collecte échoue

**Étant donné** un dernier prix valide de 30 €,  
**quand** une collecte échoue puis qu'une collecte ultérieure réussit avec un prix de 25 €,  
**alors** le prix de 25 € est comparé au dernier prix valide de 30 € et peut déclencher une alerte admissible.

### Scénario 10 — L'envoi d'un e-mail échoue

**Étant donné** une alerte créée,  
**quand** son premier envoi échoue temporairement,  
**alors** l'alerte est conservée pour réessai et ne doit pas produire plusieurs e-mails simplement à cause des tentatives répétées.

## 7. Exigences générales de qualité

Les fonctionnalités doivent respecter les exigences suivantes :
- **Sécurité :** protéger les comptes, les secrets et les données personnelles.
- **Isolation :** empêcher l'accès non autorisé aux données d'un autre utilisateur.
- **Fiabilité :** ne pas inventer de prix ou de promotions.
- **Traçabilité :** conserver les informations nécessaires pour comprendre les observations et les envois d'alertes.
- **Cohérence :** appliquer les règles métier de manière uniforme dans toute l'application.
- **Maîtrise des coûts :** viser un coût d'exploitation de 5 € par mois ou moins, sous réserve de validation des fournisseurs et des limites techniques.

## 8. Points ouverts

Les sujets suivants doivent être précisés avant ou pendant la mise en œuvre :
- Les données disponibles pour identifier de manière fiable les promotions.
- Le comportement des préférences de notification lorsqu'une baisse reste non classifiée.
- Le fournisseur d'e-mails et sa configuration.
- Le nombre maximal de tentatives d'envoi et leurs délais.
- Les sources de prix Steam, leurs conditions d'utilisation et les limites de collecte.
- Les délais de conservation des journaux et des sauvegardes.
- La faisabilité du budget cible.


