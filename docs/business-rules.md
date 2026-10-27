# Règles métier

## 1. Objectif du document

Ce document définit les règles métier de PriceWise Games : gestion des comptes, suivi des jeux, collecte et historique des prix, déclenchement des alertes et conservation des données.

Il sert de référence pour les spécifications fonctionnelles, le développement et les tests.

Les contraintes techniques ou réglementaires qui restent à vérifier sont identifiées dans la section 10.

## 2. Comptes et utilisateurs

### RG-001 — Création d'un compte

L'accès aux fonctionnalités personnalisées nécessite un compte utilisateur.

### RG-002 — Authentification

Les méthodes d'authentification prévues sont :
- Adresse e-mail et mot de passe.
- Compte Google.
- Compte Steam.

### RG-003 — Vérification de l'adresse e-mail

La création de compte par e-mail nécessite une vérification de l'adresse e-mail.

La réinitialisation du mot de passe doit être possible pour les comptes utilisant cette méthode d'authentification.

La connexion via Steam ne garantit pas à elle seule qu'une adresse e-mail vérifiée est disponible pour l'envoi d'alertes.

### RG-004 — Isolation des utilisateurs

Chaque utilisateur ne peut consulter et modifier que ses propres suivis, préférences et données personnelles.

Un utilisateur ne doit jamais pouvoir modifier les données d'un autre utilisateur sans autorisation explicite.

## 3. Catalogue des jeux et produits

### RG-005 — Catalogue partagé

Le catalogue des produits est commun à tous les utilisateurs.

Un produit n'est pas dupliqué simplement parce que plusieurs utilisateurs souhaitent le suivre.

### RG-006 — Distinction entre jeux et produits

Le modèle métier distingue un jeu de ses produits commerciaux.

Un produit peut notamment représenter :
- Un jeu de base.
- Une édition spécifique.
- Un DLC.
- Un bundle.

### RG-007 — Relations entre produits

Les relations entre un jeu, ses éditions, ses DLC et ses bundles ne sont enregistrées que lorsque la relation est suffisamment fiable.

Un bundle ou un DLC ne doit pas être assimilé automatiquement au jeu de base.

### RG-008 — Recherche et identification

Chaque produit doit pouvoir être identifié de manière suffisamment précise pour éviter de mélanger les prix de produits différents.

Les identifiants fournis par Steam sont utilisés lorsqu'ils sont disponibles et adaptés au besoin.

## 4. Collecte et historique des prix

### RG-009 — Marché initial

Le premier périmètre couvre le marché français de Steam, avec l'euro comme devise de référence.

L'ajout d'autres marchés ou devises relève d'une évolution ultérieure.

### RG-010 — Collecte quotidienne

La fréquence cible est d'une collecte par jour pour chaque produit et marché pris en charge.

Cette fréquence reste soumise à la faisabilité technique, aux conditions d'utilisation de la source et aux capacités de l'hébergement.

### RG-011 — Collecte mutualisée

Les prix sont collectés au niveau du produit et du marché, puis partagés entre les utilisateurs.

La collecte ne doit pas être répétée séparément pour chaque utilisateur qui suit le même produit.

### RG-012 — Enregistrement d'un prix

Chaque relevé valide doit conserver au minimum :
- Le produit concerné.
- Le marché.
- La devise.
- Le montant observé.
- La date et l'heure de l'observation.

Les montants sont stockés dans une représentation monétaire précise, par exemple en centimes d'euro, et non en nombres à virgule flottante.

### RG-013 — Historique des prix

Les relevés valides alimentent l'historique partagé du produit.

L'historique ne doit pas contenir de prix historiques inventés ou reconstruits sans source fiable.

Les données ne peuvent être présentées comme observées par PriceWise qu'à partir du moment où les observations réelles ont commencé.

### RG-014 — Échec de collecte

Une collecte échouée ne crée aucun relevé fictif.

Le dernier prix valide reste disponible, accompagné de sa date d'observation.

### RG-015 — Comparaison après un échec

Après une ou plusieurs collectes échouées, le prochain prix valide est comparé au dernier prix valide enregistré.

Une baisse peut donc être détectée même si plusieurs jours se sont écoulés entre deux observations réussies.

PriceWise ne prétend pas connaître l'instant exact auquel la baisse s'est produite si aucune observation ne permet de l'établir.

### RG-016 — Absence de données valides

Lorsqu'aucun prix valide n'est disponible, PriceWise ne doit pas inventer de prix ni déclencher une alerte de baisse sur une comparaison impossible.

La première observation valide sert de référence initiale, sous réserve des règles propres au suivi concerné.

## 5. Baisses de prix et promotions

### RG-017 — Enregistrement des baisses

Toute diminution de prix constatée entre deux observations valides est enregistrée comme une baisse de prix.

### RG-018 — Classification des promotions

Une baisse est classée comme promotion uniquement si les données disponibles permettent de l'établir de manière fiable.

Une diminution constatée ne suffit pas, à elle seule, à prouver qu'une promotion est en cours.

### RG-019 — Baisse non classifiée

Si la nature d'une baisse ne peut pas être déterminée de manière fiable, elle reste non classifiée.

Le système ne doit pas présenter cette baisse comme une promotion sur la base d'une supposition.

### RG-020 — Préférences par type de baisse

Les utilisateurs pourront configurer leurs préférences de notification selon les types de baisse pris en charge.

Le comportement exact lorsque le type d'une baisse est inconnu reste à préciser. Une baisse non classifiée ne doit pas être automatiquement assimilée à une promotion ou à une baisse non promotionnelle.

## 6. Suivi des produits et prix cibles

### RG-021 — Suivi indépendant

Chaque utilisateur gère sa propre liste de produits suivis.

Les préférences de suivi d'un utilisateur n'affectent pas celles des autres utilisateurs.

### RG-022 — Préférences par produit

Chaque produit suivi possède ses propres paramètres d'alerte.

Les paramètres d'un produit ne s'appliquent pas automatiquement aux autres produits suivis.

### RG-023 — Modes d'alerte

Deux modes sont prévus :
- **Prix cible :** l'utilisateur définit un prix maximal auquel il souhaite être alerté.
- **Toute réduction :** l'utilisateur souhaite être alerté des baisses admissibles sans définir de prix cible.

Lorsqu'un prix cible est défini, le mode « Toute réduction » ne s'applique pas simultanément.

### RG-024 — Déclenchement avec un prix cible

Lorsqu'un prix cible est défini, une baisse ne déclenche une alerte que si le prix observé atteint ou passe sous ce seuil, conformément aux règles de suivi.

Le prix doit être comparé à la référence pertinente pour ce suivi.

### RG-025 — Première activation d'un suivi

L'ajout d'un produit ne déclenche pas d'alerte immédiate simplement parce que le dernier prix connu est déjà inférieur ou égal au prix cible.

Le système attend une nouvelle baisse admissible observée après l'activation du suivi.

### RG-026 — Prix de référence initial

Lorsqu'un suivi est créé, le dernier prix valide disponible sert de référence initiale si un tel prix existe.

Si aucun prix valide n'existe, la première observation réussie établit la référence initiale.

Cette référence permet de distinguer une variation ultérieure d'une situation déjà présente au moment de l'activation.

### RG-027 — Modification du prix cible

Une modification du prix cible ne déclenche pas d'alerte à elle seule, même si le prix actuel respecte le nouveau seuil.

Le nouveau seuil est appliqué aux observations ultérieures.

Le système utilise le prix observé au moment de la modification comme nouvelle référence de comparaison. Les événements antérieurs ne sont pas rejoués pour générer une alerte.

### RG-028 — Désactivation des alertes

Lorsqu'un utilisateur désactive les alertes d'un suivi, aucune nouvelle alerte n'est déclenchée pour ce suivi pendant la période de désactivation.

Les préférences du suivi sont conservées.

### RG-029 — Réactivation des alertes

À la réactivation, le prix courant connu devient la nouvelle référence de comparaison.

Les baisses survenues pendant la désactivation ne sont pas rattrapées.

Une nouvelle baisse admissible observée après la réactivation peut déclencher une alerte.

### RG-030 — Prix inchangé

Un prix identique au prix de référence ne constitue pas une nouvelle baisse et ne déclenche pas d'alerte de baisse.

### RG-031 — Nouvelle baisse après remontée

Lorsqu'un prix est remonté au-dessus du prix cible, une nouvelle baisse admissible vers ou sous ce seuil peut déclencher une nouvelle alerte.

Exemple avec un prix cible de 25 € :
- Le prix passe de 30 € à 24 € : une alerte peut être déclenchée.
- Le prix remonte à 28 € : aucune alerte de baisse n'est déclenchée.
- Le prix redescend à 24 € : une nouvelle alerte peut être déclenchée.

### RG-032 — Fréquence des observations et variations manquées

La collecte quotidienne peut ne pas observer toutes les variations intermédiaires.

Les alertes reposent sur les données effectivement observées. Le système ne doit pas prétendre avoir détecté des variations qui n'apparaissent pas dans les relevés disponibles.

### RG-033 — Seuils et pourcentages

Le prix cible est exprimé dans la devise du marché concerné.

Un pourcentage de réduction peut être affiché à titre informatif s'il peut être calculé à partir de données fiables. Il ne constitue pas une condition de déclenchement, sauf ajout ultérieur d'une règle explicite.

## 7. Notifications par e-mail

### RG-034 — Conditions d'envoi

Une notification par e-mail est créée lorsqu'un événement répond aux règles d'alerte du suivi et que les préférences de l'utilisateur autorisent cette notification.

### RG-035 — Adresse de destination

L'envoi utilise une adresse e-mail disponible et autorisée pour les notifications.

La connexion via Steam seule ne garantit pas la disponibilité d'une adresse e-mail vérifiée.

### RG-036 — Mise en attente des alertes

Une alerte créée est conservée dans le système jusqu'à son envoi réussi ou jusqu'à l'atteinte de la limite de tentatives définie.

### RG-037 — Réessais d'envoi

En cas d'échec temporaire, l'envoi est réessayé automatiquement.

Le nombre maximal de tentatives et le délai entre celles-ci restent à définir lors de la conception technique.

### RG-038 — Prévention des doublons

Une même alerte ne doit pas générer plusieurs e-mails simplement parce que l'envoi a été réessayé.

Le système doit conserver un identifiant ou un mécanisme équivalent permettant de reconnaître l'alerte et de suivre son état d'envoi.

### RG-039 — Échec définitif

Lorsque la limite de tentatives est atteinte sans envoi réussi, l'alerte est marquée comme ayant échoué.

Cet état doit rester identifiable pour le suivi technique et le diagnostic.

### RG-040 — Traçabilité des envois

Le système conserve les informations nécessaires au suivi des notifications : événement associé, statut d'envoi et résultats des tentatives.

Les données techniques conservées doivent être limitées à ce qui est nécessaire au fonctionnement et au diagnostic du service.

## 8. Suppression de compte et conservation des données

### RG-041 — Suppression du compte

L'utilisateur doit pouvoir demander la suppression de son compte.

La procédure de suppression doit empêcher toute utilisation ultérieure du compte supprimé.

### RG-042 — Suppression des données personnelles et des suivis

La suppression du compte entraîne la suppression des suivis personnels, des préférences d'alerte et des données personnelles associées qui peuvent légalement être supprimées.

Les éventuelles obligations légales de conservation doivent être respectées.

### RG-043 — Conservation des données partagées

Le catalogue commun et l'historique partagé des prix sont conservés lors de la suppression d'un compte, car ils ne dépendent pas d'un seul utilisateur.

La suppression d'un utilisateur ne doit pas altérer les suivis ou les données des autres utilisateurs.

### RG-044 — Arrêt des notifications

Après la suppression du compte, aucune nouvelle alerte ne doit être envoyée à cet utilisateur.

Les notifications en attente doivent être annulées ou rendues définitivement non envoyables pour ce compte.

### RG-045 — Journaux et sauvegardes

Les journaux techniques, sauvegardes et éventuelles données résiduelles doivent faire l'objet d'une politique de conservation adaptée aux obligations légales et aux besoins de sécurité.

Les délais et modalités précis restent à définir.

## 9. Sécurité, fiabilité et coûts

### RG-046 — Protection des mots de passe

Les mots de passe ne doivent jamais être stockés en clair. Ils doivent être protégés au moyen d'un mécanisme de hachage adapté.

### RG-047 — Protection des secrets

Les clés d'API, secrets d'authentification et identifiants de services doivent rester côté serveur et ne pas être exposés au frontend.

### RG-048 — Fiabilité des données

Le système ne doit pas créer de relevés, de promotions ou d'événements de baisse qui ne sont pas étayés par des données fiables.

### RG-049 — Objectif budgétaire

L'objectif initial est de maintenir les coûts d'exploitation à 5 € par mois ou moins.

Cet objectif constitue une contrainte de conception et non une garantie de coût. Il doit être vérifié en tenant compte de l'hébergement, de la base de données, de la collecte et du service d'e-mail.

## 10. Points restant à vérifier ou à préciser

Les sujets suivants ne sont pas considérés comme définitivement tranchés.

### P-001 — Conditions de collecte Steam

Vérifier les sources de prix accessibles, les conditions d'utilisation applicables, les limites de requêtes et la faisabilité d'une collecte quotidienne automatisée.

### P-002 — Classification et préférences de notification

Définir précisément le comportement lorsque le type d'une baisse est inconnu et que l'utilisateur a choisi de recevoir uniquement certains types de notifications.

### P-003 — Politique de réessai des e-mails

Fixer le nombre maximal de tentatives, les délais entre tentatives et les conditions d'arrêt des réessais.

### P-004 — Conservation des données résiduelles

Définir les délais applicables aux journaux, sauvegardes et éventuelles données devant être conservées pour des raisons légales ou de sécurité.

### P-005 — Fournisseur d'e-mails

Choisir le fournisseur de messagerie transactionnelle et vérifier ses tarifs, ses quotas et ses conditions.

### P-006 — Faisabilité du budget

Vérifier que l'ensemble des services nécessaires peut fonctionner dans l'objectif budgétaire de 5 € par mois.

### P-007 — Données historiques et limites de stockage

Définir les mesures techniques nécessaires pour conserver l'historique des prix de manière fiable, tout en maîtrisant les coûts et les obligations de conservation applicables.

## 11. Principe général

En cas de doute sur la nature d'une donnée, PriceWise privilégie la transparence : il conserve les observations fiables, distingue les faits des classifications incertaines et n'envoie des alertes que lorsque les conditions métier sont satisfaites.

Toute modification ultérieure de ces règles doit être répercutée dans les spécifications fonctionnelles et les tests associés.
