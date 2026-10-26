# Business-rules

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

## 5. Marchés, prix et historique

### RG-011 — Contexte d’un prix

**Statut : VALIDÉE**

Un prix doit toujours être associé à un produit, une boutique, un marché et une devise.

Deux prix issus de marchés ou de devises différents ne doivent pas être comparés comme s’ils étaient directement équivalents.

### RG-012 — Marché initial

**Statut : VALIDÉE**

La première version prend en charge le marché français de Steam, avec des prix exprimés en euros.

Le modèle métier doit néanmoins permettre d’étendre ultérieurement la prise en charge à d’autres marchés.

### RG-013 — Enregistrement des observations

**Statut : VALIDÉE**

Chaque observation valide doit conserver au minimum :
- le produit concerné ;
- le marché et la devise ;
- le montant observé ;
- la date et l’heure de l’observation ;
- le contexte nécessaire pour interpréter le prix, lorsque celui-ci est disponible de manière fiable.

Les montants doivent être représentés sans perte de précision monétaire. L’utilisation d’unités monétaires mineures, comme les centimes pour l’euro, est retenue pour éviter les erreurs de calcul liées aux nombres décimaux.

### RG-014 — Historique des prix

**Statut : VALIDÉE**

Les observations historiques constituent un historique des prix réellement relevés par PriceWise Games.

Une observation passée ne doit pas être modifiée pour faire correspondre son prix à la valeur actuelle.

Une erreur détectée peut nécessiter une correction contrôlée, mais cette correction ne doit pas effacer silencieusement la traçabilité de l’anomalie.

### RG-015 — Absence d’historique antérieur fictif

**Statut : VALIDÉE**

PriceWise Games ne doit pas présenter comme observés des prix antérieurs au début de sa propre collecte s’ils ne sont pas disponibles auprès d’une source historique fiable et autorisée.

La date de début de l’historique doit être interprétable par l’utilisateur.

### RG-016 — Fraîcheur des données

**Statut : VALIDÉE**

Un prix affiché doit être accompagné d’une indication permettant de connaître la date de sa dernière observation valide.

Une erreur de collecte ne doit pas remplacer le dernier prix valide par une valeur nulle, arbitraire ou supposée.

Un prix ancien peut rester affiché, mais l’application ne doit pas le présenter comme venant d’être vérifié.

### RG-017 — Absence de prix valide

**Statut : À PRÉCISER**

Lorsqu’aucun prix valide n’est disponible, l’application doit indiquer que le prix est inconnu ou indisponible.

Elle ne doit pas assimiler cette situation à un prix gratuit ou à une baisse de prix.

---

## 6. Références de prix et événements

### RG-018 — Calcul d’une réduction

**Statut : VALIDÉE**

Un pourcentage de réduction ne peut être calculé que si le prix de référence et le prix comparé sont valides, comparables et exprimés dans la même devise.

La formule retenue est :

\[
\text{Réduction (\%)} =
\frac{\text{Prix de référence} - \text{Prix actuel}}
{\text{Prix de référence}} \times 100
\]

Le prix de référence doit être strictement supérieur à zéro.

L’arrondi utilisé pour l’affichage devra être défini dans les spécifications fonctionnelles. Les calculs de décision devront conserver une précision suffisante.

### RG-019 — Référence utilisée pour qualifier une baisse

**Statut : À DÉCIDER**

Le prix de référence utilisé pour distinguer une promotion d’une baisse permanente doit provenir d’une source fiable et d’une règle explicite.

Le dernier prix observé ne doit pas être automatiquement considéré comme le prix de référence d’une promotion.

Il reste à définir les sources autorisées pour la référence, notamment en l’absence de prix de référence fourni de manière fiable par Steam.

### RG-020 — Classification des événements de prix

**Statut : À PRÉCISER**

PriceWise Games doit distinguer les événements métier suivants :
- début ou détection d’une promotion ;
- baisse permanente du prix ;
- hausse du prix ;
- fin de promotion ;
- changement de prix dont la nature ne peut pas être déterminée avec suffisamment de certitude.

Une variation observée ne doit pas être qualifiée de baisse permanente uniquement parce que le prix a diminué entre deux collectes.

### RG-021 — Comparabilité des observations

**Statut : VALIDÉE**

Une variation de prix ne doit être interprétée comme un changement de prix que si les observations concernent le même produit et le même contexte de marché.

Un changement de devise, de marché ou d’identité de produit ne doit pas générer artificiellement un événement de baisse ou de hausse.

---

## 7. Promotions

### RG-022 — Identification d’une promotion

**Statut : À VALIDER TECHNIQUEMENT**

Une promotion doit être associée à un produit et à un marché précis.

Lorsqu’elles sont disponibles de manière fiable, les informations suivantes peuvent être conservées :
- prix de référence ;
- prix promotionnel ;
- pourcentage de réduction ;
- identifiant de promotion fourni par la source ;
- dates de début et de fin annoncées par la source ;
- première et dernière détection par PriceWise Games.

Les dates annoncées par la source et les dates observées par PriceWise Games doivent rester distinctes.

### RG-023 — Dates de promotion inconnues

**Statut : VALIDÉE**

Si la source ne fournit pas de dates de début ou de fin fiables, PriceWise Games ne doit pas inventer ces dates.

L’application peut conserver les dates de première et de dernière détection, sans les présenter comme les dates officielles de la promotion.

### RG-024 — Nouvelle période promotionnelle

**Statut : VALIDÉE**

Une nouvelle période promotionnelle peut constituer un nouvel événement, même si le produit retrouve un prix promotionnel identique à celui d’une période précédente.

L’identité d’une promotion ne doit donc pas dépendre uniquement de son montant.

Les critères exacts permettant de déterminer qu’une période est nouvelle restent à préciser, notamment lorsque la source ne fournit aucun identifiant de promotion.

### RG-025 — Baisse supplémentaire pendant une promotion

**Statut : À DÉCIDER**

Si le prix baisse davantage pendant une promotion déjà active, il reste à définir si cette variation constitue :
- une mise à jour de la promotion existante ;
- un nouvel événement de prix ;
- ou les deux, selon les besoins de l’historique et des notifications.

Cette décision devra éviter de multiplier les alertes pour une même situation sans valeur ajoutée pour l’utilisateur.

---

## 8. Suivis et préférences de notification

### RG-026 — Suivi individuel d’un produit

**Statut : VALIDÉE**

Un utilisateur peut ajouter un produit à sa liste de suivis.

Chaque suivi possède ses propres paramètres et ne doit pas modifier les préférences d’un autre utilisateur.

L’ajout de plusieurs produits en une seule action est autorisé, mais chaque produit doit disposer d’un suivi indépendant.

### RG-027 — Unicité d’un suivi

**Statut : À PRÉCISER**

Un utilisateur ne doit pas créer accidentellement plusieurs suivis actifs équivalents pour le même produit et le même marché.

Le comportement attendu lorsqu’un utilisateur ajoute à nouveau un produit déjà suivi doit être défini : par exemple, conserver le suivi existant et permettre sa modification plutôt que créer un doublon.

### RG-028 — Préférences par produit

**Statut : VALIDÉE**

Chaque suivi doit pouvoir comporter les paramètres suivants :
- activation ou désactivation des alertes de promotion ;
- activation ou désactivation des alertes de baisse permanente ;
- prix cible ;
- réduction minimale souhaitée.

Ces paramètres sont propres au suivi concerné.

### RG-029 — Prix cible

**Statut : VALIDÉE**

Un prix cible représente le montant maximal auquel l’utilisateur souhaite être alerté pour le produit suivi.

Une condition de prix cible est satisfaite lorsque le prix valide retenu pour la comparaison est inférieur ou égal au prix cible.

Le prix comparé doit correspondre au produit et au marché suivis.

### RG-030 — Réduction minimale

**Statut : À PRÉCISER**

Une réduction minimale définit le seuil de réduction à partir duquel une notification liée à une réduction peut être envoyée.

Ce seuil ne peut être évalué que lorsque le prix de référence est fiable et que le calcul de réduction est possible.

Le comportement exact lorsque le prix de référence est inconnu reste à définir.

### RG-031 — Combinaison des critères d’alerte

**Statut : À DÉCIDER**

Les préférences de prix cible et de réduction minimale doivent pouvoir coexister.

Il reste à définir précisément la combinaison de ces critères pour chaque type d’événement, notamment :
- si l’atteinte du prix cible suffit à déclencher une alerte, indépendamment du seuil de réduction ;
- si le seuil de réduction s’applique aux promotions, aux baisses permanentes ou aux deux ;
- si une notification peut être envoyée lorsqu’un critère est satisfait mais que l’autre ne l’est pas.

Cette règle devra être formalisée avant le développement du moteur de notifications.

### RG-032 — Ajout d’un produit déjà sous le prix cible

**Statut : VALIDÉE**

Lorsqu’un utilisateur ajoute un produit et que son dernier prix valide connu satisfait déjà son prix cible, une alerte initiale doit être déclenchée.

Cette alerte ne doit pas dépendre de la survenue d’une nouvelle baisse après l’ajout.

### RG-033 — Activation d’une préférence déjà satisfaite

**Statut : À PRÉCISER**

Lorsqu’un utilisateur active une préférence alors que la condition correspondante est déjà satisfaite, le comportement retenu est de permettre une alerte initiale.

Les conditions exactes de cette alerte devront être définies pour éviter qu’une simple modification de paramètres ne déclenche plusieurs messages identiques.

### RG-034 — Modification et suppression d’un suivi

**Statut : À PRÉCISER**

La modification des préférences d’un suivi ne doit affecter que ce suivi.

La suppression d’un suivi doit empêcher les nouvelles alertes associées à ce suivi.

Le sort des notifications déjà créées ou en cours d’envoi doit être défini afin d’éviter qu’une notification ne soit envoyée après la suppression du suivi.

---

## 9. Notifications et absence de doublons

### RG-035 — Pertinence d’une notification

**Statut : VALIDÉE**

Une notification ne peut être générée que lorsqu’un événement de prix satisfait les préférences applicables au suivi de l’utilisateur.

Un événement qui ne satisfait pas les conditions configurées ne doit pas produire d’alerte.

### RG-036 — Mutualisation des événements

**Statut : VALIDÉE**

Un événement de prix détecté pour un produit et un marché est partagé entre les utilisateurs concernés.

Chaque utilisateur reçoit une notification uniquement si ses propres paramètres rendent cet événement pertinent.

L’application ne doit pas collecter séparément le même prix pour chaque utilisateur qui suit le produit.

### RG-037 — Déduplication des alertes

**Statut : VALIDÉE**

Pour un même utilisateur, un même suivi, un même événement et un même canal, l’application ne doit pas générer plusieurs notifications équivalentes.

La règle de déduplication doit tenir compte de l’identité de l’événement, et non uniquement du montant du prix.

### RG-038 — Absence de rappels répétitifs

**Statut : VALIDÉE**

Tant qu’une même situation de prix reste active, PriceWise Games ne doit pas renvoyer automatiquement des rappels identiques à intervalles réguliers.

Un nouvel événement peut néanmoins justifier une nouvelle notification si les règles métier l’autorisent.

### RG-039 — État d’une notification

**Statut : VALIDÉE**

Une notification doit permettre de distinguer au minimum :
- sa création ;
- son état d’envoi ;
- son succès ou son échec de livraison ;
- les éventuelles tentatives de nouvel envoi.

Une notification créée ne doit pas être considérée comme délivrée simplement parce qu’elle existe en base de données.

### RG-040 — Échec temporaire de livraison

**Statut : À PRÉCISER**

Un échec temporaire de livraison doit pouvoir déclencher une nouvelle tentative selon une politique définie.

Un échec permanent ne doit pas entraîner des tentatives illimitées.

Le nombre maximal de tentatives, les délais entre celles-ci et la classification des erreurs seront précisés dans la conception technique.

### RG-041 — Absence de doublon lors d’une nouvelle tentative

**Statut : VALIDÉE**

Une nouvelle tentative d’envoi ne doit pas créer une nouvelle notification métier ni provoquer plusieurs livraisons réussies de la même alerte lorsque l’état de la première tentative est connu.

Le système devra gérer les situations où le résultat d’un envoi est incertain.

### RG-042 — Canal de notification initial

**Statut : VALIDÉE**

Le canal initial de notification est l’e-mail.

L’ajout de notifications dans l’application, de notifications push ou d’autres canaux pourra être étudié ultérieurement sans remettre en cause les règles de pertinence des événements.

---

## 10. Collecte des prix et fiabilité

### RG-043 — Collecte mutualisée

**Statut : VALIDÉE**

La collecte des prix doit être mutualisée par produit, boutique et marché.

Le nombre d’utilisateurs suivant un produit ne doit pas multiplier le nombre de collectes nécessaires pour obtenir son prix.

### RG-044 — Fréquence cible

**Statut : À VALIDER TECHNIQUEMENT**

La fréquence cible initiale est d’une collecte quotidienne.

Cette fréquence devra être confirmée après validation de la source de données, des limites d’accès et des capacités de l’hébergement.

Elle constitue une fréquence de collecte planifiée, et non une garantie que chaque prix sera actualisé exactement toutes les vingt-quatre heures.

### RG-045 — Prévention des collectes concurrentes

**Statut : VALIDÉE**

Plusieurs exécutions simultanées ne doivent pas produire de collectes concurrentes non maîtrisées pour un même périmètre.

La conception technique devra prévoir un mécanisme empêchant les traitements planifiés ou relancés de provoquer des doublons et des conflits.

### RG-046 — Enregistrement du résultat d’une collecte

**Statut : VALIDÉE**

Une collecte doit permettre de connaître son résultat et de distinguer :
- une collecte réussie ;
- une collecte partiellement réussie ;
- une collecte échouée ;
- un produit ignoré ou indisponible.

Les informations nécessaires au diagnostic des échecs doivent être conservées sans enregistrer inutilement de données sensibles.

### RG-047 — Échec de collecte

**Statut : VALIDÉE**

Un échec de collecte ne doit pas être interprété comme une variation de prix.

Aucune nouvelle observation de prix valide ne doit être créée à partir d’une donnée absente, invalide ou non vérifiée.

Le dernier prix valide reste consultable, avec une indication appropriée de son ancienneté.

### RG-048 — Source des données Steam

**Statut : À VALIDER TECHNIQUEMENT**

La source utilisée pour récupérer les prix et les informations commerciales doit être examinée avant le développement de la collecte.

L’examen doit porter notamment sur :
- la disponibilité des données nécessaires ;
- la stabilité de la source ;
- les conditions d’utilisation applicables ;
- les limites de requêtes ;
- les marchés et devises pris en charge ;
- la disponibilité des dates et identifiants de promotion ;
- les conditions de conservation et de réutilisation des données.

Aucune donnée nécessaire au fonctionnement du produit ne doit être supposée disponible tant que sa disponibilité n’a pas été vérifiée.

### RG-049 — Respect des limites de la source

**Statut : VALIDÉE**

La collecte doit respecter les limites et conditions applicables à la source.

En cas de limitation, d’indisponibilité ou de changement de format, l’application doit privilégier un échec contrôlé plutôt que d’enregistrer des données incorrectes.

### RG-050 — Détection des changements de source

**Statut : À PRÉCISER**

La conception devra prévoir des contrôles permettant de détecter les données manquantes, incohérentes ou inattendues après un changement de la source.

Une variation inhabituelle ne doit pas automatiquement être traitée comme une baisse réelle sans validation suffisante.

---

## 11. Sécurité et protection des données

### RG-051 — Protection des identifiants

**Statut : VALIDÉE**

Les mots de passe ne doivent jamais être stockés en clair.

Les secrets d’authentification et les clés des fournisseurs externes ne doivent pas être exposés au navigateur ni publiés dans le dépôt Git.

### RG-052 — Contrôle des autorisations

**Statut : VALIDÉE**

Les droits d’accès doivent être vérifiés côté serveur pour chaque opération protégée.

Masquer une action dans l’interface ne constitue pas un contrôle d’autorisation suffisant.

### RG-053 — Minimisation des données

**Statut : VALIDÉE**

PriceWise Games ne doit collecter que les données personnelles nécessaires au fonctionnement des comptes, des suivis, de la sécurité et des notifications.

Les données d’authentification reçues d’un fournisseur externe doivent être limitées à celles qui sont nécessaires.

### RG-054 — Suppression d’un compte

**Statut : À DÉCIDER**

L’utilisateur devra disposer d’un moyen de demander la suppression de son compte.

Les règles de suppression ou d’anonymisation des suivis, notifications et autres données associées restent à définir.

Les obligations légales applicables et les éventuelles durées de conservation justifiées devront être prises en compte.

### RG-055 — Conservation des données

**Statut : À DÉCIDER**

L’historique des prix est destiné à être conservé sans limite prédéfinie dans le périmètre initial.

Cette orientation doit être conciliée avec les contraintes de stockage, les obligations de protection des données et les conditions applicables aux données issues de sources externes.

La conservation des données personnelles doit faire l’objet d’une politique distincte et justifiée.

---

## 12. Contraintes métier transverses

### RG-056 — Cohérence des données

**Statut : VALIDÉE**

Les données utilisées pour une décision métier doivent être cohérentes entre elles.

Une notification ne doit pas être générée à partir d’un prix invalide, d’une devise incompatible ou d’un produit incorrectement identifié.

### RG-057 — Traçabilité des décisions

**Statut : VALIDÉE**

Les traitements qui modifient l’état métier — observation d’un prix, détection d’un événement, création d’une notification et résultat de livraison — doivent laisser suffisamment de traces pour permettre de comprendre ce qui s’est produit.

La traçabilité ne justifie pas la conservation de données personnelles non nécessaires.

### RG-058 — Limite budgétaire

**Statut : VALIDÉE**

L’architecture initiale doit viser un coût d’exploitation maximal de **5 € par mois**.

Ce plafond est une contrainte de conception et non une garantie de coût tant que les fournisseurs, les quotas et les besoins réels ne sont pas validés.

Une décision technique entraînant un dépassement prévisible doit être identifiée avant sa mise en œuvre.

---

## 13. Décisions à prendre avant les spécifications fonctionnelles définitives

Les points suivants doivent être arbitrés ou vérifiés avant de figer le comportement de l’application.

| Priorité | Sujet | Question à résoudre |
|---|---|---|
| **P0** | Source Steam | Quelle source permet de récupérer les prix, les marchés, les promotions et les éventuelles dates, dans des conditions acceptables ? |
| **P0** | Prix de référence | Quel prix sert de référence pour calculer une réduction et distinguer une baisse permanente d’une promotion ? |
| **P0** | Combinaison des alertes | Le prix cible est-il indépendant du seuil de réduction ? À quels événements le seuil s’applique-t-il ? |
| **P0** | Collecte | L’hébergement retenu permet-il une collecte quotidienne fiable et persistante dans le budget ? |
| **P1** | Promotions | Comment déterminer la fin d’une promotion et l’apparition d’une nouvelle période lorsque la source fournit peu d’informations ? |
| **P1** | Baisse pendant une promotion | Une réduction supplémentaire produit-elle une nouvelle alerte ? |
| **P1** | Préférences activées | Quelle alerte initiale est envoyée lorsqu’une préférence est activée alors que son critère est déjà satisfait ? |
| **P1** | Doublons de suivis | Quel comportement adopter si l’utilisateur ajoute un produit qu’il suit déjà ? |
| **P1** | Échecs d’e-mail | Combien de tentatives, quels délais et quels critères d’abandon retenir ? |
| **P1** | Suppression du compte | Quelles données supprimer, anonymiser ou conserver, et pendant combien de temps ? |

**P0** désigne un sujet bloquant pour le cadrage du cœur métier. **P1** désigne un sujet à résoudre avant de finaliser les règles concernées ou de livrer la fonctionnalité correspondante.

---

## 14. Critères de validation du référentiel

Ce référentiel sera considéré comme suffisamment stabilisé lorsque :

1. Chaque règle nécessaire au MVP aura un comportement non ambigu.
2. Les règles validées seront distinguées des hypothèses techniques.
3. Les critères de déclenchement et de déduplication des alertes seront définis sans contradiction.
4. Les règles de collecte seront compatibles avec les données réellement disponibles.
5. Les règles de conservation, de sécurité et de suppression des données seront suffisamment définies.
6. Les règles retenues pourront être traduites en critères d’acceptation et en tests.

Les évolutions ultérieures devront modifier ce référentiel de façon explicite plutôt que de laisser les règles métier se disperser dans le code ou dans le README.
