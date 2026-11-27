# P83 — Addendum daté du 25/11/2026 au registre des décisions PO relatives au STOCK

**Statut : DÉCISIONS CONFIRMÉES par le Product Owner (confirmation explicite du 25/11/2026) : Q-1 à Q-12 et H-1 à H-6, « conformément aux recommandations présentées » dans la fiche P82, avec trois précautions complémentaires (C-P82-1 à C-P82-3).**
**Portée : décisions métier uniquement.** Cet addendum n'autorise ni développement, ni migration, ni test, ni déploiement. La réalisation du STOCK et la migration 0022 exigent une **autorisation PO distincte** et une mission distincte.
Données fictives uniquement. Aucun secret ni code.

Cet addendum est l'**évolution datée** prévue par le registre (§12) : il **complète** `docs/P81_REGISTRE_DECISIONS_PO_STOCK.md` sans en modifier ni en effacer aucune entrée. Les documents P81 et P82 restent inchangés ; en cas de divergence de **statut** (question « en attente »), cet addendum fait foi.

Sources : `docs/P82_SPECIFICATIONS_FINALES_STOCK.md` (« les spécifications », fiche §1, règles V1-Rn, V2-Rn, INV-n), `docs/P82_PLAN_REALISATION_STOCK_0022.md` (« le plan »), `docs/P82_RAPPORT_EXECUTION.md`, `docs/P81_REGISTRE_DECISIONS_PO_STOCK.md` (« le registre »).

Libellés : **DÉCISION CONFIRMÉE** · **INTERPRÉTATION CONFIRMÉE** · **PRÉCAUTION CONFIRMÉE** · **OUVERT** · **NON VÉRIFIÉ** · **À TRAITER** · **PROPOSITION** (non décidée).

---

## 1. Confirmation

| Élément | Valeur |
|---|---|
| Date | 25/11/2026 |
| Auteur | Product Owner (confirmation explicite transmise pour la mission P83) |
| Objet | Q-1 à Q-12 et H-1 à H-6 approuvés conformément aux recommandations de la fiche P82 §1 ; trois précautions complémentaires ajoutées |
| Forme | Les recommandations de la fiche valent texte des décisions : conditions, limites et références de la fiche sont reprises ci-dessous sans réduction |
| Effet | Les questions Q-1 à Q-12 ne sont plus en attente d'arbitrage PO (§8) |
| Limite | Aucune recommandation **technique** non soumise au PO n'est transformée en décision (§7, §9) |

## 2. Synthèse des décisions

| Réf. | Décision confirmée | Règles P82 | Statut |
|---|---|---|---|
| Q-1 | Sorties manuelles exclusivement depuis DISPONIBLE | V1-R9, INV-5 | DÉCISION CONFIRMÉE |
| Q-2 | Motifs limités et encadrés pour les lots bloqués, rappelés ou périmés | V1-R10 | DÉCISION CONFIRMÉE |
| Q-3 | Réservations honorées par ordre d'ancienneté | V1-R4, V1-R5 | DÉCISION CONFIRMÉE |
| Q-4 | Signalement interne à l'application, audit persistant, aucune notification externe | V1-R6 | DÉCISION CONFIRMÉE |
| Q-5 | Levée de quarantaine réservée à l'ADMIN (pharmacien titulaire), authentification forte | V1-R7 | DÉCISION CONFIRMÉE |
| Q-6 | Levée du blocage d'un lot : même autorité et même authentification forte | V1-R7 | DÉCISION CONFIRMÉE |
| Q-7 | Corrections et contre-passations non bloquées par les réservations | V2-R7 | DÉCISION CONFIRMÉE |
| Q-8 | Toute nouvelle entrée de stock naît en DISPONIBLE | V2-R4, INV-4 | DÉCISION CONFIRMÉE |
| Q-9 | Corrections négatives soumises aux droits et restrictions prévus dans P82 | V2-R3, V2-R5 | DÉCISION CONFIRMÉE |
| Q-10 | Contre-passation de réception limitée à la quantité encore DISPONIBLE | V2-R6 | DÉCISION CONFIRMÉE |
| Q-11 | Passage en PERTE irréversible | V2-R8 | DÉCISION CONFIRMÉE |
| Q-12 | Motifs normalisés conservés dans une table de référence | spécifications §8.3 | DÉCISION CONFIRMÉE |

Identifiants stables pour les futurs plans et tests : `Q-1`…`Q-12`, `H-1`…`H-6`, `C-P82-1`…`C-P82-3`, complétant `DP1-R1`…, `C1`…, `O-n` du registre.

---

## 3. Décisions détaillées (conditions, limites, conséquences)

### Q-1 — Sorties manuelles uniquement depuis DISPONIBLE *(DÉCISION CONFIRMÉE ; V1-R9, INV-5)*
- **Décision** : les sorties manuelles (retrait, destruction d'un périmé) ne partent que de l'état DISPONIBLE. Aucune sortie directe depuis EN_CONTROLE, QUARANTAINE ou PERTE avant le lot ultérieur.
- **Condition** : une quantité EN_CONTROLE repasse d'abord en DISPONIBLE (passage ordinaire, H-6) pour être sortie.
- **Limite** : les sorties depuis QUARANTAINE et la destruction des quantités en PERTE restent **REPORTÉES** (DP1-R7).
- **Conséquences** : INV-5 (aucune sortie — vente, sortie manuelle, contre-passation — hors DISPONIBLE ; seules les corrections négatives en EN_CONTROLE ou QUARANTAINE avec droit restreint entament un autre état, jamais PERTE, voir Q-9). Cette décision **confirme la lecture de cohérence du registre §10.3 (V-1)**, qui exigeait la confirmation du PO.

### Q-2 — Lots bloqués, rappelés ou périmés *(DÉCISION CONFIRMÉE ; V1-R10)*
- **Décision** : une sortie manuelle depuis DISPONIBLE d'un lot BLOQUE, RAPPELE ou périmé n'est possible qu'avec un motif de retrait parmi **RAPPEL, PEREMPTION, CASSE**.
- **Limite** : les motifs **USAGE_INTERNE** et **AUTRE** sont **refusés** sur ces lots.
- **Conséquence** : traduit « retrait ou destruction » (DP4-R4) en liste de motifs normalisés (DP4-R5). La quantité d'un lot périmé (DISPONIBLE, non vendable) se sort selon la même liste.

### Q-3 — Réservations par ancienneté *(DÉCISION CONFIRMÉE ; V1-R4, V1-R5)*
- **Décision** : lorsqu'une réservation devient impossible à honorer, la priorité est **par ancienneté** : les plus anciennes sont honorées, les plus récentes sont signalées.
- **V1-R4** : pour un produit, les réservations actives sont classées par ancienneté de création (puis identifiant) ; chacune est « honorée » tant que le cumul réservé, jusqu'à elle comprise, reste inférieur ou égal au **disponible** du produit ; les suivantes sont « non honorables » (code `stock.reservation_unfulfillable`, avec quantité disponible et quantité manquante).
- **V1-R5** : la validation d'une vente adossée à une réservation R est refusée si le disponible moins les réservations **plus anciennes que R** ne couvre pas sa quantité ; une vente sans réservation déduit toutes les réservations actives. **Conséquence confirmée : modification du comportement existant** (`sales/plan.ts` l. 115 et déclencheur 0016 l. 323, numéros du relevé du 25/11/2026). Le repli prévu par la fiche (conserver la règle actuelle) **n'est pas retenu**.
- **Maintenu** : une réservation n'est jamais modifiée ni annulée automatiquement (V1-R3) ; la sécurité sanitaire prime (DP3-R3).

### Q-4 — Signalement interne *(DÉCISION CONFIRMÉE ; V1-R6)*
- **Décision** : signalement **dans l'application** avec **trace d'audit persistante** ; **aucun envoi externe** (aucune infrastructure de notification dans le code : recherche négative de P82).
- **Contenu** (V1-R6) : (a) à chaque passage, blocage ou correction, identifiants, comptoirs, auteurs et quantités des réservations rendues non honorables sont inscrits dans la charge de l'entrée d'audit chaînée ; (b) la liste des réservations affiche l'indicateur et la cause ; (c) l'auteur est averti à son prochain accès par un refus nommé `stock.reservation_unfulfillable`, jamais générique ; (d) le tableau d'alertes du stock affiche le nombre de réservations non honorables.
- **Limite** : toute notification externe (courriel, SMS, autre) est hors décision.

### Q-5 — Levée de QUARANTAINE *(DÉCISION CONFIRMÉE ; V1-R7, spécifications §4.3)*
- **Décision** : la levée de QUARANTAINE (QUARANTAINE vers DISPONIBLE, DP1-R3) est un droit de **gouvernance** réservé à l'**ADMIN, qui est le pharmacien titulaire**, non délégable.
- **Conséquence acceptée** : les droits de gouvernance existants exigent une **authentification forte** (commentaire `governance/catalog.ts` l. 36) ; la levée n'est donc pas possible avec le seul code d'accès (PIN).
- **Écarté** : l'alternative « droit métier non délégable à habilitation pharmacien » (qui aurait ouvert aux adjoints habilités). Lecture : DP1-R3 (« pharmacien titulaire ou ADMIN ») s'entend littéralement comme **ADMIN seul**.
- **Traçabilité** : passage et audit avec le nom de l'autorité. Le nom technique du droit (`stock.quarantine.release`) est une proposition du plan, à fixer en réalisation.

### Q-6 — Levée du blocage d'un lot *(DÉCISION CONFIRMÉE ; V1-R7)*
- **Décision** : la levée d'un blocage (BLOQUE ou RAPPELE vers ACTIF) relève de la **même autorité** et de la **même authentification forte** que Q-5. Cela comble la lacune signalée du registre.
- **Maintenu** : le blocage ou rappel d'un lot reste un droit **métier distinct** (proposé `stock.lot.block`, non accordé par défaut) ; événement de statut append-only et audit.

### Q-7 — Corrections et contre-passations face aux réservations *(DÉCISION CONFIRMÉE ; V2-R7)*
- **Décision** : corrections et contre-passations enregistrent la réalité physique ; elles ne sont **jamais bloquées par une réservation** (DP4-R1 ne vise que les sorties manuelles).
- **Conséquence** : elles déclenchent le calcul et la trace de V1-R6 (Q-4).

### Q-8 — Toute entrée naît en DISPONIBLE *(DÉCISION CONFIRMÉE ; V2-R4, INV-4)*
- **Décision** : toute nouvelle entrée de stock naît en DISPONIBLE, **y compris une correction positive** ; aucune entrée directe en EN_CONTROLE, QUARANTAINE ou PERTE.
- **Conséquence** : un excédent constaté dans un autre état s'enregistre **en deux temps tracés** : correction positive en DISPONIBLE, puis passage d'état.
- **Conditions de la correction positive** : motif ECART_COMPTAGE, ERREUR_SAISIE ou AUTRE (avec précision) ; garde de quantité attendue ; signalement dans le rapport d'écarts (H-2).
- **Limite** : O-5 (réception « à contrôler », entrée directe en EN_CONTROLE) **reste OUVERT** ; cette décision ne le tranche pas.

### Q-9 — Corrections négatives par état *(DÉCISION CONFIRMÉE ; V2-R3, V2-R5)*
- **DISPONIBLE** : droit `stock.adjust` ; motif parmi ECART_COMPTAGE, ERREUR_SAISIE, AUTRE (avec précision) ; garde de quantité attendue appliquée à l'état visé ; quantité inférieure ou égale au disponible de l'état.
- **EN_CONTROLE ou QUARANTAINE** : mêmes conditions **et** droit restreint (habilitation pharmacien, non délégable ; proposé `stock.adjust.nondisponible`), signalé dans les rapports.
- **Lot périmé, BLOQUE ou RAPPELE, ou mise à zéro d'un lot entier** : droit restreint ci-dessus.
- **PERTE : jamais** (garde de la base, INV-6).
- **Interdiction de la destruction déguisée** (V2-R5) : une correction n'accepte pas les motifs CASSE, PEREMPTION, RAPPEL, USAGE_INTERNE ; liste de motifs de correction fermée, **sans motif de destruction** ; l'état visé est toujours explicite (V2-R2) ; produit ARCHIVE refusé.
- **Rapports** : corrections négatives exposées (cumul par lot, produit, motif et auteur ; aucun seuil, H-4) ; rapprochement signalant tout lot dont les corrections négatives cumulées dépassent la quantité reçue.
- **Précaution associée** : C-P82-1 (§5).

### Q-10 — Contre-passation de réception *(DÉCISION CONFIRMÉE ; V2-R6)*
- **Décision** : la contre-passation ne reprend que la quantité **actuellement DISPONIBLE** du lot, écrite dans l'état DISPONIBLE du lot d'origine ; plafond = minimum de (i) quantité reçue moins déjà reprise et (ii) quantité DISPONIBLE.
- **Refus** : nommé `purchase.reversal_state`, avec le détail par état (reprenable, disponible, en contrôle, en quarantaine, en perte). L'**annulation intégrale** est refusée si une seule ligne a une part hors DISPONIBLE.
- **Conséquence** : EN_CONTROLE se reprend après réintégration en DISPONIBLE ; QUARANTAINE après levée (Q-5) ; une quantité en PERTE **attend le lot ultérieur** (DP1-R7).
- **Précaution associée** : C-P82-2 (§5).

### Q-11 — Passage en PERTE irréversible *(DÉCISION CONFIRMÉE ; V2-R8)*
- **Décision** : le passage en PERTE n'est **pas annulable** (conséquence de DP1-R4) : aucun mouvement, correction ni contre-passation ne rend une quantité PERTE au DISPONIBLE. Le PO accepte cette conséquence.
- **Garde-fous** : confirmation explicite, quantité et irréversibilité affichées, droit de passage non accordé par défaut ; en cas de doute, EN_CONTROLE (réversible).
- **Limite** : toute annulation exigerait de modifier DP1-R4 (décision écrite du PO et nouvelle version du registre).
- **Précaution associée** : C-P82-3 (§5).

### Q-12 — Motifs normalisés en table de référence *(DÉCISION CONFIRMÉE ; spécifications §8.3)*
- **Décision** : forme technique des motifs = **table de référence** (ajout d'un motif sans reconstruire `stock_movements`), plutôt qu'une liste figée par `CHECK`. **Clôt O-11** pour la forme.
- **Contenu retenu** : motifs de sortie manuelle (DP4-R5) CASSE, PEREMPTION, RAPPEL, USAGE_INTERNE, AUTRE (précision obligatoire) ; motifs de correction ECART_COMPTAGE, ERREUR_SAISIE, AUTRE (précision), sans motif de destruction ; historique non rétroactif (`reason_code` nul sur les mouvements existants, V2-R9).
- **Réserve technique, non décisionnelle** : le comportement réel de l'ajout de colonne sur table `STRICT` est **NON VÉRIFIÉ** (O-7).

---

## 4. Interprétations confirmées (H-1 à H-6)

Les interprétations de la fiche P82 §1 sont **confirmées** sans modification. Elles précisent le sens d'entrées du registre ; elles ne modifient pas le texte du registre.

| Réf. | Interprétation confirmée | Entrée du registre précisée | Statut |
|---|---|---|---|
| **H-1** | « Réception physique (commande ou livraison) » = réception **rattachée à une commande fournisseur**. Une **livraison sans commande** relève de la **régularisation** (justificatif obligatoire, droit distinct). | DP5-R1, DP5-R2 | INTERPRÉTATION CONFIRMÉE |
| **H-2** | « Correction positive justifiée et contrôlée » = **motif obligatoire** + **garde-fou de quantité attendue** (dispositif existant) + **signalement dans le rapport d'écarts**. **Aucun second contrôleur obligatoire.** | DP5-R3 | INTERPRÉTATION CONFIRMÉE |
| **H-3** | « Ancienneté » de l'indicateur « en attente de destruction » = **date du plus ancien passage en PERTE** concerné (par lot). Valable **tant qu'aucune sortie de PERTE n'existe** ; à revoir au lot ultérieur (DP1-R7). | C2 | INTERPRÉTATION CONFIRMÉE |
| **H-4** | **Aucun seuil chiffré d'alerte** sur les corrections négatives (**REPORTÉ**) ; le rapport affiche les **cumuls par lot et par motif** (V2-R5 d : aussi par produit et par auteur). | V2-R5, C3 | INTERPRÉTATION CONFIRMÉE |
| **H-5** | DP4-R6 (« QUARANTAINE ou PERTE ») est lu comme couvrant **aussi le passage en EN_CONTROLE et le blocage ou rappel de lot** : **mêmes protections de sécurité** ; une réservation n'empêche aucun de ces actes (V1-R1, INV-10). | DP4-R6, DP3-R3 | INTERPRÉTATION CONFIRMÉE |
| **H-6** | La **réintégration EN_CONTROLE vers DISPONIBLE** relève du **droit de passage ordinaire** ; DP1-R3 ne vise que la **levée de QUARANTAINE** (Q-5). | DP1-R3 | INTERPRÉTATION CONFIRMÉE |

---

## 5. Précautions complémentaires confirmées (C-P82-1 à C-P82-3)

Ces trois précautions s'ajoutent aux décisions Q-9, Q-10 et Q-11 ; elles les **renforcent** sans les remplacer.

| Réf. | Précaution | Renforce | Statut |
|---|---|---|---|
| **C-P82-1 — Corrections négatives** | Une correction négative **ne peut jamais servir à faire disparaître artificiellement** un produit **périmé, cassé ou destiné à la destruction**. Elle doit correspondre à un **véritable écart de comptage**. | Q-9 ; V2-R5 ; C3 | PRÉCAUTION CONFIRMÉE |
| **C-P82-2 — Contre-passation** | Le **refus d'une annulation intégrale** lorsque tout ou partie de la quantité n'est plus DISPONIBLE doit être **expliqué clairement à l'utilisateur**, avec **affichage des quantités concernées**. | Q-10 ; V2-R6 (`purchase.reversal_state`) | PRÉCAUTION CONFIRMÉE |
| **C-P82-3 — Passage en PERTE** | **Avant validation**, afficher une **confirmation explicite** présentant la **quantité concernée** et les **conséquences irréversibles** de l'opération. | Q-11 ; V2-R8 | PRÉCAUTION CONFIRMÉE |

**Articulation avec P82 (OBSERVATION, aucune décision nouvelle)** :
- **C-P82-1** reprend et durcit V2-R5 (pas de motif de destruction dans les corrections, interdiction en PERTE, droit restreint, rapports). Les motifs de correction confirmés par Q-9 et Q-12 (ECART_COMPTAGE, ERREUR_SAISIE, AUTRE avec précision) **ne sont pas modifiés** par cette précaution. **VIGILANCE V-5** : l'application de « véritable écart de comptage » aux motifs ERREUR_SAISIE et AUTRE (par exemple affichage séparé des corrections de motif AUTRE dans le rapport) est à instruire dans le plan de réalisation ; réduire la liste des motifs serait une **modification de Q-9/Q-12**, donc une décision écrite du PO.
- **C-P82-2** : P82 prévoit déjà le refus nommé `purchase.reversal_state` avec détail par état. La précaution impose que ce détail soit **restitué à l'utilisateur** en langage clair. **VIGILANCE V-6** : l'écran concerné relève de la mission d'interface (lot R7, optionnelle et distincte) ; le plan de réalisation doit préciser comment la précaution est tenue **avant** cette mission (message de refus lisible et complet côté commande). Ne pas supposer que l'interface existante restitue le détail : **NON VÉRIFIÉ**.
- **C-P82-3** : P82 prévoyait la confirmation explicite comme garde-fou (V2-R8) ; elle devient **obligatoire**. **PROPOSITION technique (non décidée)** : exiger côté commande un paramètre de confirmation explicite portant la quantité, afin que la précaution tienne même sans nouvel écran. Même réserve V-6 pour l'affichage.

---

## 6. Contrôle de cohérence

### 6.1 Avec le registre P81 (DP-1 à DP-6, C1 à C4)

| Décision | Entrées du registre | Résultat |
|---|---|---|
| Q-1 | DP4-R4, DP4-R5, DP1-R7 (REPORTÉ), C3 | Cohérent. Lève la vigilance **V-1** (lecture §10.3 confirmée). Les sorties de QUARANTAINE et la destruction des pertes restent REPORTÉES |
| Q-2 | DP4-R4, DP4-R5 | Cohérent : traduit « retrait ou destruction » en motifs RAPPEL, PEREMPTION, CASSE |
| Q-3 | DP3-R3, DP4-R1, DP3-R1 | Cohérent : sécurité sanitaire prioritaire ; réservation par produit inchangée. **Modifie** le comportement actuel de validation (V1-R5), conséquence acceptée |
| Q-4 | DP3-R3 (« signalée à son auteur »), DP1-R2 | Cohérent : signalement interne tracé |
| Q-5 | DP1-R3 | Cohérent par **précision** : « pharmacien titulaire ou ADMIN » est lu comme la même personne, l'ADMIN (spécifications §4.3) ; non délégable. Le texte du registre n'est pas modifié. Règle du 17/11/2026 (ADMIN conserve ses droits métier) préservée ; la levée est le seul acte placé en gouvernance |
| Q-6 | DP1-R3, DP1-R5 | Cohérent : comble la lacune (levée d'un blocage) ; DP1-R5 (aucun transfert automatique) inchangé |
| Q-7 | DP4-R1, DP4-R6, DP1-R10, C3 | Cohérent : DP4-R1 ne vise que les sorties manuelles |
| Q-8 | DP5-R1 à DP5-R5, DP1-R6 | Cohérent : aucune des quatre entrées ne cible un autre état ; un passage d'état n'augmente pas le physique. O-5 non tranché |
| Q-9 | DP1-R10, C3, DP1-R7 | Cohérent sous réserve du **point d'attention** ci-dessous |
| Q-10 | C3, DP1-R4, DP1-R7 | Cohérent : la contre-passation tient compte de l'état actuel (C3) |
| Q-11 | DP1-R4, C1, C2 | Cohérent : confirme DP1-R4. Conséquence connue (déjà dans C1) : un produit qui détient une quantité en PERTE reste non archivable jusqu'au lot ultérieur ; l'indicateur C2 la rend visible |
| Q-12 | DP4-R5, O-11 | Cohérent : **clôt O-11 pour la forme** (table de référence) ; contenu des motifs = DP4-R5 |
| H-1 à H-6 | DP5-R1 à R3, C2, DP4-R6, DP1-R3 | Cohérent (§4) |
| C-P82-1 à 3 | C3, DP1-R4 | Cohérent : renforcent C3 et DP1-R4, n'en modifient aucun terme |
| DP-2 (lot obligatoire médicament), DP-6 (candidat hors dossier officiel, essais sur copies fictives, promotion et DEMO distinctes) | — | **Non touchés** par cet addendum. DP-6 reste intégralement en vigueur |

**Point d'attention (Q-9 et DP1-R7)** : Q-9 autorise des corrections négatives en EN_CONTROLE et QUARANTAINE (droit restreint), alors que les sorties de QUARANTAINE sont REPORTÉES (DP1-R7). La cohérence repose sur la lecture de P82 : une correction **constate un écart de comptage** ; ce n'est **pas** une sortie. C-P82-1 confirme cette lecture et interdit l'usage d'une correction pour faire disparaître un produit périmé, cassé ou à détruire. Aucune contradiction retenue ; la tenue de cette précaution est à contrôler par les tests (V2-T, plan §9.3) et le rapport de corrections.

### 6.2 Avec les spécifications P82 et les invariants

| Domaine | Invariant ou règle P82 | Résultat |
|---|---|---|
| Stock physique | INV-3 (somme des états = somme du journal), INV-2 (aucun niveau négatif par état) | Cohérent : aucune décision ne crée une seconde source de vérité ; passages et corrections restent des mouvements |
| FEFO | V1-R2 (le FEFO ne lit que DISPONIBLE, lots non BLOQUE/RAPPELE/périmés) ; DP3-R2 (CAT-10 reformulé) | Cohérent avec Q-3 : l'ancienneté ne change pas le choix du lot, seulement l'ordre d'honorabilité des réservations |
| Réservations | V1-R1, V1-R3, INV-10 ; Q-3, Q-7, H-5 | Cohérent : une réservation ne bloque ni passage, ni blocage de lot, ni correction ; elle n'est jamais modifiée automatiquement |
| Traçabilité | INV-1 (journal immuable), V2-R9, V2-R10, V1-R6 | Cohérent : toute rectification est un nouveau mouvement lié à son acte, attribué et horodaté ; audit chaîné |
| Archivage | C1 (interdit tant qu'une quantité physique subsiste, y compris QUARANTAINE et PERTE) | Cohérent avec Q-1, Q-10, Q-11 : aucune de ces décisions ne permet de vider QUARANTAINE ou PERTE avant le lot ultérieur, sauf corrections négatives restreintes (Q-9) en QUARANTAINE |
| Destruction | DP1-R7 (REPORTÉ), V2-R5, INV-6 (aucun mouvement négatif en PERTE) | Cohérent : aucune destruction ni sortie de PERTE ; aucune destruction déguisée (C-P82-1) |
| Corrections, contre-passations | V2-R3 à V2-R8, INV-4, INV-5 | Cohérent (Q-8 à Q-11) |
| CAT-11 | DP1-R9, V-3 | **Non touché** : voir §7 |

**Contradictions relevées entre les décisions confirmées et le registre P81 : aucune.** Deux précisions de lecture sont enregistrées (Q-5 sur DP1-R3 ; H-5 sur DP4-R6) ; elles ne modifient pas le texte du registre.

---

## 7. Points non confirmés par cette confirmation (conservés explicitement)

Aucun des points ci-dessous n'est décidé, approuvé ou rendu implicite par la confirmation du 25/11/2026. Ils gardent le statut qu'ils avaient dans le registre ou dans P82.

| Réf. | Point | Statut conservé | Observation |
|---|---|---|---|
| **O-5** | Réception « à contrôler » (entrée directement en EN_CONTROLE) | **OUVERT** | Q-8 (toute entrée naît en DISPONIBLE) ne le tranche pas ; une éventuelle décision future le ferait par addendum |
| **O-6** | Retour client en contrôle d'abord | **OUVERT / REPORTÉ** | DP5-R6 inchangé |
| **O-10** | Prérequis du Lot 2 (CAT-14 à CAT-16) | **OUVERT** | Sans effet sur le plan P82 |
| **O-7** | Comportement réel du moteur D1 sur `ALTER TABLE … ADD COLUMN` (table `STRICT`, `CHECK`, clé étrangère) | **NON VÉRIFIÉ** | Question technique, non décision PO ; à lever lors du premier essai sur copie jetable (lot R2 du plan) |
| **O-8** | Volumétrie réelle et coût des déclencheurs | **NON VÉRIFIÉ** | Même traitement (mesure sur jeu synthétique en R2) |
| **O-9** | État de la migration 0021 en DEMO | **OUVERT** | Concerne seulement la décision future d'appliquer la 0022 à la DEMO (G7) ; DP6-R2 et DP6-R7 inchangés |
| **A-T1** | Couplage code et schéma : aucune version livrable construite entre R4 et G6 ; promotion (G6) et livraison du code ensemble ; test de couplage écrit en R3 | **À TRAITER** (arbitrage technique, conditions de P82 plan §4.4) | Non soumis comme question Q ; **non approuvé** par la confirmation |
| **A-T2** | Mémoire disponible insuffisante pour les fichiers de test D1 (RR-1) : arrêt de la mission et arbitrage du PO ; seuil d'arrêt disque proposé 800 Mo | **À TRAITER** (conditions de P82 plan §12) | Le seuil de 800 Mo reste une HYPOTHÈSE, non confirmée |
| **V-3** | Formule CAT-11 et convention de P82 (les contre-passations de réception sont des corrections nettes ; les retours clients sont déduits des sorties nettes ; spécifications §8.2) | **VIGILANCE, NON APPROUVÉ** | Aucune décision PO ne l'approuve. La formule du registre (DP1-R9) reste la seule décision ; son écriture exacte avec la convention de P82 est à instruire en R1 sur la photographie 0021 et, si elle diffère du texte du registre, à soumettre au PO |
| **V-4** | Couplage code et schéma avant promotion | **VIGILANCE** | Traité par A-T1 |
| **V-5** | Application de C-P82-1 aux motifs ERREUR_SAISIE et AUTRE (§5) | **VIGILANCE (nouvelle)** | Plan de réalisation |
| **V-6** | Restitution de C-P82-2 et C-P82-3 à l'utilisateur avant la mission d'interface (§5) | **VIGILANCE (nouvelle)** | Plan de réalisation ; interface existante NON VÉRIFIÉE |

**Autres points inchangés** : hors périmètre du registre §9 (règles réglementaires de retour D62, D63, D75 ; prépaiements patients ; fuseau horaire de la péremption ; réservation pour reliquat d'ordonnance) ; décisions de portes G6 (promotion de 0022) et G7 (application à la DEMO) ; mission d'interface R7 ; réalisation du STOCK.

## 8. Mise à jour du statut des questions traitées

| Élément | Statut avant (P82) | Statut à compter du 25/11/2026 |
|---|---|---|
| Q-1 à Q-12 | Recommandation soumise au PO | **DÉCISION CONFIRMÉE** : plus en attente d'arbitrage |
| H-1 à H-6 | Interprétation par défaut, non bloquante | **INTERPRÉTATION CONFIRMÉE** |
| V-1 (registre §10.3) | VIGILANCE (lecture à confirmer) | **Levée** : lecture confirmée par Q-1 et Q-2 |
| V-2 (registre §10.3) | VIGILANCE (état visé non précisé) | **Levée** : état visé et traitement de l'état actuel décidés par Q-8 à Q-11 ; les modalités techniques restent à réaliser |
| O-11 (registre §9) | OUVERT | **Traité pour la forme** (Q-12 : table de référence). Le contenu des motifs reste celui de DP4-R5 et de V2-R5 |
| Lacune « levée d'un blocage de lot » | Non couverte par le registre | **Comblée** par Q-6 |
| C-P82-1 à C-P82-3 | — | **PRÉCAUTION CONFIRMÉE** |
| DP1-R3 | DÉCIDÉ (texte « pharmacien titulaire ou ADMIN ») | Texte inchangé ; précisé par Q-5 (ADMIN seul, authentification forte) |
| DP4-R6 | DÉCIDÉ | Texte inchangé ; précisé par H-5 |
| DP1-R7 | REPORTÉ | **Inchangé** |

Les documents P82 qui présentent encore Q-1 à Q-12 comme « à arbitrer » décrivent l'état **avant** la confirmation ; pour le statut, cet addendum fait foi.

## 9. Ce que cet addendum n'est pas

- Il **ne transforme aucune recommandation technique en décision PO** : les noms de droits (`stock.state.transfer`, `stock.lot.block`, `stock.adjust.nondisponible`, `stock.regularize`, `stock.quarantine.release`), le modèle (colonne d'état, en-têtes de passage, tables de statut), le séquencement R0 à R7, les gardes de la base, les contrôles B14 à B22 et le seuil disque de 800 Mo restent des **PROPOSITIONS du plan P82**, à instruire et à relire en réalisation.
- Il **n'autorise** aucun changement de code, aucune création ni exécution de migration 0022, aucune modification de base, aucun test, aucune intervention sur la DEMO ou systemd, aucune promotion (G6), aucune application (G7), aucun déploiement, aucune opération Git manuelle, aucun nettoyage ni réparation du serveur.
- Les **huit conditions de DP-6** du registre restent en vigueur (`docs/P81_REGISTRE_DECISIONS_PO_STOCK.md` §7).
- **Condition générale** : la réalisation du STOCK et la migration 0022 restent soumises à une **autorisation PO distincte** et à une mission distincte. Cet addendum n'engage aucune mission suivante.

## 10. Évolution

Toute modification d'une décision de cet addendum exige une décision écrite du PO et un nouvel addendum daté ; les entrées existantes ne sont pas effacées. Les points OUVERTS, NON VÉRIFIÉS ou À TRAITER (§7) ne passent à DÉCIDÉ que sur décision écrite du PO.
