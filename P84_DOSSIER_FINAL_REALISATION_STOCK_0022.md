# P84 — Dossier technique final de réalisation STOCK / migration 0022

**Statut** : dossier de préparation. **Aucun code, aucune migration, aucune base, aucun test n'a été créé ou exécuté en P84.** Ce dossier **ne modifie aucune décision** P81/P82/P83 ; il les consolide pour la réalisation et sépare ce qui est **décidé par le PO** de ce qui est **proposé techniquement**.
**Données** : fictives. **Lecture avec** : `P84_ETUDE_FAISABILITE_AT1_AT2.md` (livraison, ressources) et `P84_MATRICE_TESTS_STOCK_0022.md` (scénarios NEG, REV, PER, V6, CAT, MIG).

---

## 1. Références et hiérarchie des décisions

| Niveau | Contenu | Statut |
|---|---|---|
| 1 | Registre P81 (DP1…DP6, C1…C3, O-n) | **Décisions PO** (référence métier) |
| 2 | Addendum P83 : Q-1…Q-12, H-1…H-6, C-P82-1…3 | **Décisions / interprétations / précautions confirmées** |
| 3 | Spécifications P82 : S-n, V1-Rn, V2-Rn, INV-n | **Règles dérivées** ; deviennent décisions par les réponses Q (P83) |
| 4 | Plan P82 (lots R0…R7, G6, G7, RM-n, B14-B22) | Plan de réalisation (propositions techniques) |
| 5 | Étude P84 (A-T1, A-T2) et ce dossier | **Propositions techniques**, soumises à arbitrage |

**Règle d'étiquetage dans ce dossier** : « **[PO]** » = décision ou règle découlant d'une décision PO ; « **[TECH]** » = proposition technique à valider ; « **[OUVERT]** » = non décidé ; « **[NON VÉRIFIÉ]** » = sans preuve à ce jour.

**Points restant ouverts (P83, non modifiés ici)** : O-5, O-6, O-10 OUVERT ; O-7, O-8 NON VÉRIFIÉ ; O-9 OUVERT (0021 non appliquée à la DEMO) ; A-T1 et A-T2 **analysés en P84, arbitrage demandé** ; V-3 NON APPROUVÉ ; V-4, V-5, V-6 VIGILANCE (V-5 et V-6 spécifiés par la matrice).

---

## 2. États et transitions

### 2.1 Les quatre états **[PO]**

| État | Sens | Vendable | Entrée possible | Sortie manuelle |
|---|---|---|---|---|
| DISPONIBLE (D) | stock normal | oui (lot non bloqué, non périmé) | **seul état d'entrée** | oui |
| EN_CONTROLE (C) | détenu, à vérifier | jamais | non | non |
| QUARANTAINE (Q) | détenu, isolé | jamais | non | non (DP1-R7) |
| PERTE (P) | perdu, en attente de destruction | jamais | non | non (DP1-R7) |

Un produit **sans lot** (`lot_id` nul) est réparti par état de la même façon.

### 2.2 Matrice des transitions **[PO]** (registre §2)

| De \ Vers | D | C | Q | P |
|---|---|---|---|---|
| **D** | — | oui | oui | oui |
| **C** | oui | — | oui | oui |
| **Q** | oui (**ADMIN seul**, gouvernance) | non | — | oui |
| **P** | **non** | **non** | **non** | — |

Notes : la **levée de QUARANTAINE** (Q→D) est réservée à l'ADMIN avec authentification forte (Q-5). La réintégration C→D relève de `stock.state.transfer`. Aucun passage ne part de P.

### 2.3 Règles d'un passage **[PO]** (S-1…S-5) et réalisation **[TECH]**

- Un passage porte **quantité, motif (3-300 car.), auteur, date**, est **atomique** (deux mouvements + en-tête, ou rien) et ne change pas le physique.
- La quantité passée ≤ quantité de l'état de départ (INV-2). **Jamais refusé à cause d'une réservation** (V1-R1).
- **[TECH]** Réalisation : en-tête immuable `stock_state_transfers` + deux `CORRECTION` de source `stock.transfer` (`−q` départ, `+q` arrivée), `transfer_id` commun, dans **une seule unité de travail** avec l'entrée d'audit.
- **[TECH]** Garde de la base : `transfers_matrix` (refus de tout départ de P et de toute paire hors matrice), `transfer_coherence` (INV-7).

### 2.4 Cycle de vie d'une quantité (récapitulatif)

```
ENTREE ──► D ──► C ──► D      (réintégration)
           │      └──► Q ──► D (levée ADMIN)
           │      └──► P (irréversible)
           ├──────► Q ──► P
           └──────► P
```
P est un état terminal jusqu'au lot ultérieur (destruction, REPORTÉ).

---

## 3. Règles de réservation et de sortie

### 3.1 Réservations **[PO]** (DP3, DP4, V1-R1…R8)

| # | Règle |
|---|---|
| RES-1 | Réservation **logique, par produit**, immuable ; fin de vie par événement unique (RELEASED, EXPIRED, CONSUMED). |
| RES-2 | **Une réservation ne bloque jamais** un passage d'état, un blocage de lot, une correction ou une contre-passation (V1-R1, V2-R7). |
| RES-3 | **Aucune réservation n'est modifiée ni annulée automatiquement** ; l'impossibilité est un **état calculé** « non honorable » (V1-R3). |
| RES-4 | Calcul « non honorable » **par ancienneté de création** puis identifiant (V1-R4, Q-3). |
| RES-5 | Vente adossée à la réservation R : refusée si disponible − réservations **plus anciennes que R** < quantité (V1-R5). Vente sans réservation : toutes les réservations actives sont déduites. |
| RES-6 | Signalement : identifiants, comptoirs, auteurs, quantités dans l'**audit** ; indicateur dans la liste ; refus nommé `stock.reservation_unfulfillable` ; compteur dans les alertes. **Aucun envoi externe** (Q-4). |
| RES-7 | Sortie manuelle entamant une quantité réservée : **refusée** sauf **dérogation ADMIN motivée et tracée** (DP4-R1/R2). |

**[TECH]** Réalisation : `stock_movements_sales_reserved` (0016 l. 323) **remplacé** (DROP puis CREATE, précédents 0004/0017/0019/0021) pour (a) vente par ancienneté, (b) sortie manuelle bloquante sauf dérogation, (c) **aucune autre source visée** (INV-10) ; vue calculée `stock_reservations_state` ; en-tête `stock_issue_overrides` pour la dérogation (la sortie garde la source `stock.manual`).

### 3.2 Sorties **[PO]** (Q-1, Q-2, V1-R9, V1-R10)

| # | Règle |
|---|---|
| SOR-1 | **Sorties manuelles uniquement depuis DISPONIBLE.** Une quantité en C repasse d'abord en D. |
| SOR-2 | Motifs normalisés de sortie : CASSE, PEREMPTION, RAPPEL, USAGE_INTERNE, AUTRE (précision obligatoire). |
| SOR-3 | Lot **périmé, BLOQUE ou RAPPELE** : seuls PEREMPTION, RAPPEL, CASSE ; USAGE_INTERNE et AUTRE refusés. |
| SOR-4 | Sortie depuis Q ou P : refus nommé `stock.state_exit_deferred` (lot ultérieur). |
| SOR-5 | Vente : FEFO sur lots D non bloqués, non périmés ; **calcul FEFO unique partagé** (remplace les deux implémentations) ; mode manuel : écart autorisé et tracé (DP4-R3). |
| SOR-6 | Lot BLOQUE ou RAPPELE : jamais vendu (INV-8), refusé par la base pour `sales.sale`. |

---

## 4. Corrections positives et négatives

### 4.1 Principes **[PO]** (Q-7, Q-8, Q-9, C-P82-1)

- Une correction **constate un écart de comptage** sur un **triplet produit / lot / état explicite** ; garde de quantité attendue **à l'état visé** (`stock.level_changed`).
- **Jamais de débordement** vers un autre état ; jamais de déplacement entre états (rôle du passage).
- **C-P82-1** : une correction négative **ne peut jamais faire disparaître** un produit périmé, cassé ou destiné à la destruction ; elle correspond à un **véritable écart de comptage**.

### 4.2 Correction positive **[PO]**

Uniquement en **DISPONIBLE** ; motifs ECART_COMPTAGE, ERREUR_SAISIE, AUTRE (précision) ; signalée au rapport d'écarts (H-2). Excédent constaté ailleurs : correction positive en D puis passage d'état.

### 4.3 Correction négative **[PO]** (V2-R3, V2-R5)

| Cas | Droit | Autres conditions |
|---|---|---|
| D, lot normal | `stock.adjust` | motif ECART_COMPTAGE/ERREUR_SAISIE/AUTRE ; quantité ≤ D ; garde attendue |
| C ou Q | `stock.adjust` **+ `stock.adjust.nondisponible`** (restreint, non délégable) | idem ; signalée aux rapports |
| Lot périmé, BLOQUE ou RAPPELE | droit restreint | idem |
| Mise à zéro d'un lot ou d'un produit | droit restreint | marque « mise à zéro » |
| **P** | **jamais** | INV-6 (garde de la base) |
| Produit ARCHIVE | refusé (inchangé) | — |

### 4.4 Interdiction de la destruction déguisée **[PO]** (V2-R5)

Une correction **refuse** les motifs CASSE, PEREMPTION, RAPPEL, USAGE_INTERNE (**y compris pour l'ADMIN**) ; aucune correction négative en P ; cumuls exposés en **rapport** (lot, produit, motif, auteur ; H-4 : aucun seuil) ; contrôle signalant tout lot dont les corrections négatives cumulées dépassent la quantité reçue.

### 4.5 Réalisation **[TECH]**

- `stock_movements_bucket_rules` : refus de tout mouvement négatif en P (INV-6) ; correction négative sans `transfer_id` en C/Q acceptée seulement pour la source `stock.manual` (le droit restreint est contrôlé **en code**) ; positive hors D seulement avec `transfer_id`.
- `stock_movements_manual_reason` : exige un `reason_code` autorisé ; une correction n'accepte aucun code de sortie.
- Le **droit restreint est contrôlé en code** (la base ne connaît pas les droits) : le test NEG-31/33/34 doit donc passer par le service, et NEG-17 **aussi par la base**.
- **Distinction avec la sortie de QUARANTAINE (V-5)** : une correction en Q est classée « corrections nettes » (source ≠ sortie), reste visible au rapport d'écarts, **ne vaut jamais sortie** ; toute sortie réelle de Q reste refusée (SOR-4). Scénarios NEG-60…68.

---

## 5. Contre-passations de réception

### 5.1 Règles **[PO]** (Q-10, C-P82-2, V2-R6)

| # | Règle |
|---|---|
| CP-1 | La reprise s'écrit dans l'état **DISPONIBLE** du lot d'origine. |
| CP-2 | Quantité reprenable = **min(reçu − déjà repris ; DISPONIBLE du lot)**. |
| CP-3 | Demande > D : refus nommé **`purchase.reversal_state`** avec détail par état (reprenable, D, C, Q, P). |
| CP-4 | **Annulation intégrale** refusée si une seule ligne a une part hors D ; **aucune ligne** n'est reprise (atomicité). |
| CP-5 | C : se reprend après réintégration ; Q : après levée (ADMIN) ; **P : jamais avant le lot ultérieur**. |
| CP-6 | Jamais bloquée par une réservation (V2-R7) ; déclenche le calcul et la trace V1-R6. |
| CP-7 | **C-P82-2** : le refus est **expliqué clairement** avec **affichage des quantités concernées** (service **et** interface : §14). |
| CP-8 | Historique immuable : tout est mouvement compensatoire lié à la ligne de réception (V2-R9). |

### 5.2 Réalisation **[TECH]**

- Le service lit l'état par la vue `stock_levels` avant d'écrire ; les gardes existantes `purchase.reversal_exceeds` et `stock.insufficient` sont **conservées** et leur ordre d'évaluation documenté (REV-12 : le code existant prime quand reçu = repris).
- Charge utile du refus : `{ reprenable, disponible, enControle, quarantaine, perte, recu, dejaRepris }` + `lignes[]` pour l'annulation intégrale. Format exact **[À FIXER en R1]**.
- La base garde `purchasing.reversal` en D (`bucket_rules`).

### 5.3 Cas non traités (lot ultérieur, REPORTÉ)

Retour fournisseur ou destruction d'une quantité en P ; sortie de Q ; retour client « en contrôle » (O-6).

---

## 6. Lots périmés, rappelés, bloqués, en contrôle, quarantaine, perte

| Situation du lot | Vente | Sortie manuelle | Correction négative | Passage d'état | Contre-passation |
|---|---|---|---|---|---|
| Normal (ACTIF, non périmé) | oui (D) | oui (D) | D : `stock.adjust` | oui | oui (D) |
| **Périmé** | non | PEREMPTION/RAPPEL/CASSE | droit restreint | oui (D→P utilisable pour retrait) | oui (D) |
| **BLOQUE** | **non** (INV-8) | RAPPEL/CASSE/PEREMPTION | droit restreint | oui | oui (D) |
| **RAPPELE** | **non** (INV-8) | idem | droit restreint | oui (le **rappel ne transfère rien** : S-7) | oui (D) |
| Quantité **EN_CONTROLE** | non | non (repasser en D) | droit restreint | C→D/Q/P | non |
| Quantité **QUARANTAINE** | non | non (REPORTÉ) | droit restreint | Q→D (**ADMIN**), Q→P | non (après levée) |
| Quantité **PERTE** | non | non (REPORTÉ) | **jamais** | **aucun** | non (REPORTÉ) |

**Statut de lot [PO]** : ACTIF (défaut = absence d'événement), BLOQUE, RAPPELE ; PÉRIMÉ et ÉPUISÉ calculés ; droit `stock.lot.block` pour bloquer/rappeler ; **levée = ADMIN seul**, authentification forte (Q-6) ; événements append-only avec `seq` par lot **[TECH]**.
**Un rappel ne déplace jamais de quantité automatiquement** (DP1-R5) : le pharmacien décide ensuite des passages en Q.
**Archivage [PO]** : interdit tant que le physique (tous états) n'est pas nul (C1) ; le message détaille la répartition par état. **Conséquence connue** : un produit gardant Q ou P reste non archivable (S-13).
**Indicateur « en attente de destruction » [PO]** (C2, H-3) : Σ P et ancienneté, calculés ; aucune destruction automatique.

---

## 7. Droits et contrôles d'accès

### 7.1 Tableau des droits (décision PO vs proposition technique)

| Acte | Droit | Origine | Défaut | Nature |
|---|---|---|---|---|
| Passage D/C/Q/P (hors levée de Q) | `stock.state.transfer` | DP1-R3, S-5 | ADMIN seul (automatique) | **[PO]** le principe ; **[TECH]** le nom exact |
| Levée de QUARANTAINE (Q→D) | `stock.quarantine.release` (gouvernance, non délégable) | Q-5 | ADMIN seul, authentification forte | **[PO]** |
| Blocage ou rappel d'un lot | `stock.lot.block` | S-9 | ADMIN | **[PO]** principe ; **[TECH]** nom |
| Levée d'un blocage ou rappel | même autorité que la levée de Q | Q-6 | ADMIN seul | **[PO]** |
| Correction D | `stock.adjust` (existant) | V2-R3 a | selon rôles existants | **[PO]** |
| Correction C, Q, lot périmé/bloqué/rappelé, mise à zéro | `stock.adjust.nondisponible` (restreint) | Q-9, V2-R3 b-c | non accordé par défaut, non délégable | **[PO]** principe ; **[TECH]** nom |
| Régularisation hors commande | `stock.regularize` (distinct) | DP5 | non accordé par défaut | **[PO]** |
| Dérogation aux réservations (sortie manuelle) | ADMIN, motif tracé | DP4-R2 | ADMIN | **[PO]** |
| Contre-passation de réception | droit achats existant (nom exact à relever en R1) | existant | inchangé | **[NON VÉRIFIÉ]** |
| Lecture des alertes et réservations non honorables | `stock.read` / `sale.create` | V1-R6 | rôles existants | **[PO]** |

### 7.2 Règles de contrôle

| # | Règle | Nature |
|---|---|---|
| D-1 | Les droits sont contrôlés **en code** (la base ne connaît pas les utilisateurs) ; la base garde les **invariants de données** (§8) indépendamment des droits. | **[TECH]** |
| D-2 | **L'ADMIN conserve ses droits métier automatiques** (décision du 17/11/2026) ; la levée de Q et de blocage est le **seul** acte placé en gouvernance. | **[PO]** |
| D-3 | Aucun droit ne permet de contourner un invariant : l'ADMIN lui-même ne peut ni sortir de P, ni corriger P, ni utiliser un motif de destruction en correction. | **[PO]** (C3, C-P82-1) |
| D-4 | Le droit utilisé (ordinaire ou restreint) est inscrit à l'audit de chaque correction. | **[PO]** V2-R10 |
| D-5 | Les nouveaux droits sont ajoutés au **catalogue de gouvernance** (`governance/catalog.ts`) et à `authorize.ts` ; les tests de catalogue existants doivent être adaptés (liste justifiée au rapport). | **[TECH]** |
| D-6 | Un refus de droit est **nommé** et **tracé** ; il n'écrit aucun mouvement. | **[TECH]** (E-1 de la matrice) |
| D-7 | Aucun droit n'est accordé à un rôle existant par la migration (aucune donnée de comptes lue ni modifiée). | **[TECH]** |

---

## 8. Invariants (gardés par la base, vérifiés par les tests)

Repris des spécifications P82 §7, sans modification :

| Réf. | Invariant | Garde (TECH) | Scénarios |
|---|---|---|---|
| INV-1 | Journal, en-têtes de passage, événements de statut et dérogations immuables | déclencheurs `*_no_update`, `*_no_delete` | REV-40, MIG-T6 |
| INV-2 | Aucun niveau négatif par produit/lot ni par produit/lot/état | `no_negative` (0005) + `no_negative_bucket` | NEG-05/06/36, PER-06 |
| INV-3 | Physique = somme du journal ; Σ états = ancien `SUM(delta)` | vues calculées ; aucun stockage | CAT-13/14, MIG-T5 |
| INV-4 | Toute entrée en D ; positif hors D seulement via passage | `bucket_rules` | NEG-22/23, PER-26 |
| INV-5 | Aucune sortie hors D ; correction négative en C/Q avec droit restreint, jamais P | garde par état + règles de source | NEG-30…36, PER-25 |
| INV-6 | Aucun mouvement négatif en P | `bucket_rules` | NEG-17, PER-20…23, PER-27 |
| INV-7 | Un passage = deux `CORRECTION` `stock.transfer`, somme nulle, rattachés à un en-tête conforme | `transfer_coherence`, `transfers_matrix` + contrôle B14 | PER-01…03, PER-22 |
| INV-8 | Lot BLOQUE/RAPPELE non vendable | `lot_status` | SOR-6 (tests V1-S3) |
| INV-9 | Aucune ligne existante réécrite par la migration | valeur par défaut D | MIG-T1, MIG-T5, MIG-T6 |
| INV-10 | Une réservation n'empêche jamais `stock.transfer` ni un événement de statut | déclencheur de réservation limité aux sources `sales.sale` et `stock.manual` (sortie) | V1-S1…S4, NEG-70, REV-20, PER-10 |

**Limite connue [TECH]** : la **complétude** d'une paire de passage ne peut pas être garantie par SQLite (pas de contrôle différé en fin de transaction) ; elle repose sur l'**unité de travail unique** du code et sur le **contrôle B14** exécuté après chaque scénario. **Non vérifié** : l'ordre d'exécution des déclencheurs d'un même événement (aucun ordre supposé).

---

## 9. Modèle de données cible (PROPOSITION conforme au plan P82 §4)

### 9.1 Objets

| Objet | Contenu essentiel | Observations |
|---|---|---|
| `stock_movements.bucket` | `TEXT NOT NULL DEFAULT 'DISPONIBLE'` + `CHECK` 4 états | INV-9 : aucune ligne réécrite |
| `stock_movements.transfer_id` | nullable, clé étrangère vers `stock_state_transfers` | défaut nul imposé par `ALTER TABLE` |
| `stock_movements.reason_code` | nullable, clé étrangère vers `stock_reason_codes` | précédent : 0008 ; **O-7 NON VÉRIFIÉ** (D1 réel) |
| `stock_reason_codes` | `code, kind, label, requires_detail, active` ; amorcée par INSERT | extensible par une ligne (0023 ou gouvernance) |
| `stock_state_transfers` | en-tête immuable : produit, lot, état de départ et d'arrivée, quantité, motif, auteur, date, **marque d'irréversibilité si arrivée = P** | la marque d'irréversibilité est une **proposition** (PER-01, PER-46) |
| `stock_lot_status_events` | `lot_id, seq, statut, motif, auteur, date` ; unique `(lot_id, seq)` | statut courant = plus grand `seq` |
| `stock_issue_overrides` | en-tête immuable de dérogation ADMIN | `stock.manual` conservée comme source |
| Index | `(product_id, lot_id, bucket)` ; unique `(transfer_id, bucket)` si `transfer_id` non nul | existants intacts |
| Vues | `stock_levels`, `stock_lot_received`, `stock_reservations_state` | lecture seule, jamais de table modifiable |

### 9.2 Ce qui n'est **pas** dans le modèle

Aucune colonne de quantité stockée ; aucun nouveau type de mouvement ; aucune table de réservation modifiée ; aucun nouvel événement de réservation ; aucune écriture rétroactive de `bucket` ; aucune table de comptes ou droits.

### 9.3 Calculs dérivés (spécifications §8.2)

Physique, par état, disponible, vendable, en attente de destruction, reçu, corrections nettes, sorties nettes. La formule **CAT-11 (DP1-R9)** est la **référence** (§15).

### 9.4 Questions de conception à **fixer en R1** (avant tout candidat)

| # | Question | Pourquoi ouverte |
|---|---|---|
| M-1 | Nom exact et position des droits, actions d'audit et codes d'erreur nouveaux | non figés par P82 |
| M-2 | Contrôle du « droit restreint » : un seul droit pour C/Q/périmé/zéro, ou séparés | P83 retient un droit unique `stock.adjust.nondisponible` ; confirmer à la lecture du catalogue |
| M-3 | Où stocker la marque « mise à zéro » et « droit restreint » (charge d'audit seule, ou colonne) | éviter une colonne inutile ; préférer l'audit |
| M-4 | Reprise de réception : `reason_code` obligatoire ou nul accepté (REV-41) | la reprise a son motif dans la ligne d'opération |
| M-5 | Format exact de la charge utile `purchase.reversal_state` | impacte l'interface (§14) |
| M-6 | Marque d'irréversibilité : dans l'en-tête ou dans l'audit | affecte PER-46 |

---

## 10. Stratégie de migration 0022 et conservation de l'historique

### 10.1 Principes **[PO]** (DP6) et **[TECH]**

| # | Principe |
|---|---|
| MG-1 | **Additive uniquement** : ajout de colonnes, tables, vues, déclencheurs ; **aucune reconstruction** de `stock_movements` (RM-8). |
| MG-2 | **Conservation intégrale de l'historique** : les mouvements existants valent D par défaut ; **aucune ligne réécrite** ; sommes de contrôle de 0001…0021 inchangées (B21). |
| MG-3 | Le candidat vit **hors dossier officiel** (`pharmos/migrations_candidates/pharmacy/`), n'est jamais lu par la production (B22) ; **aucun fichier** dans `migrations/pharmacy/` avant G6. |
| MG-4 | **Promotion = décision distincte du PO (G6)** ; **application à la DEMO ou à une base réelle = décision distincte (G7)**. |
| MG-5 | Une fois promue, la 0022 **n'est jamais éditée** : toute erreur = 0023 (RM-4). |
| MG-6 | Essais **uniquement sur bases jetables fictives** créées par les tests (`makeEnv`, `openEnvAt`, `makeD1Env`). |
| MG-7 | Chaque instruction du candidat est **unique**, séparée par `--> statement-breakpoint` (`hasMultipleStatements`). |
| MG-8 | Remplacement du déclencheur 0016 par `DROP` puis `CREATE` (précédents : 0004, 0017, 0019, 0021). |

### 10.2 Ordre des instructions du candidat **[TECH]**

1. tables de référence et d'en-têtes (`stock_reason_codes`, `stock_state_transfers`, `stock_lot_status_events`, `stock_issue_overrides`) ;
2. `ALTER TABLE stock_movements ADD COLUMN` (bucket, transfer_id, reason_code) ;
3. amorçage de `stock_reason_codes` ;
4. index ;
5. déclencheurs d'immuabilité ;
6. déclencheurs de règles (`no_negative_bucket`, `bucket_rules`, `transfer_coherence`, `transfers_matrix`, `lot_status`, `manual_reason`) ;
7. remplacement de `stock_movements_sales_reserved` ;
8. vues.

Raison : les clés étrangères pointent vers des tables déjà créées ; les déclencheurs de règles sont posés **après** les colonnes qu'ils lisent ; les vues en dernier.

### 10.3 Conservation de l'historique : contrôles

| Contrôle | Attendu | Scénario |
|---|---|---|
| Nombre de lignes de chaque table existante avant/après | identique | MIG-T1 (A/B) |
| Empreinte des colonnes d'origine de `stock_movements` | identique | MIG-T6 |
| Σ `delta` par produit et par lot avant/après | égale | MIG-T5 |
| Σ des états = ancien `SUM(delta)` | égalité exacte | B17, CAT-14 |
| Réservations : nombre et empreinte | inchangés | B20 |
| Chaîne d'audit | non rompue | arrêt 5 |
| Sommes de contrôle 0001…0021 | identiques | B21 |

### 10.4 Chargement par les tests **[TECH]** (A-T1)

- `migrationSets()` : paramètre facultatif ajoutant les sources candidates **sauf** celles dont l'identifiant existe déjà dans le dossier officiel (sommes égales exigées) → la **promotion devient un déplacement de fichier** (MIG-T3).
- `schema_parite`, `p33`, `p34` : version valable **avant et après** promotion (nombre calculé ; identifiant éloigné pour la ligne « en avance ») ; **aucune assertion relâchée**.
- Garde de démarrage « niveau de schéma requis » : `compose.ts` refuse de démarrer sans l'identifiant requis (MIG-T4) ; **modification d'un fichier d'entrée de production : arbitrage A-T1 point 3**.
- Garde de non-fuite : aucun module de `src/` n'importe `migrations_candidates`.

### 10.5 Contrainte de livraison (A-T1, rappel)

Pas de version livrable construite entre R4a et G6 ; code, migration, tests et spécifications ne forment **une livraison qu'ensemble** (manifeste M2, critère C1…C8). La DEMO s'exécute depuis une **copie de version** limitée à 0020 et n'est pas affectée.

---

## 11. Compatibilité et non-régression

### 11.1 Compatibilité (FAIT VÉRIFIÉ ou HYPOTHÈSE, à confirmer par les tests)

| # | Point | Statut | Contrôle |
|---|---|---|---|
| CO-1 | Les mouvements existants valent D (valeur par défaut) ; les lectures `SUM(delta)` existantes (≈ 20, relevé de plan P81 §3.5) donnent le **même résultat** tant que tout reste en D | HYPOTHÈSE | R1 : classement physique/disponible ; R4a : « aucun changement de comportement » |
| CO-2 | Un ancien binaire (qui ignore `bucket`) écrit en D ; la garde par état refuse toute sortie entamant une quantité non disponible : **échec sûr** | HYPOTHÈSE du plan P81 §3.3 | test ST-01-e |
| CO-3 | Les tests existants chargent le jeu officiel (0001-0021) sans candidat : ils restent inchangés tant que le candidat n'est pas chargé | FAIT VÉRIFIÉ (lecture de `env.ts`) | non-régression R0 vs R5 |
| CO-4 | `defineMigrationSet` trie par identifiant : la fixture 9001 reste après 0022 | FAIT VÉRIFIÉ (plan P82 §4.3) | MIG-T2 |
| CO-5 | `ALTER TABLE ADD COLUMN … NOT NULL DEFAULT … CHECK` sur table `STRICT` avec lignes | **O-7 NON VÉRIFIÉ** en D1 réel | R2 (SQLite, D1 local) ; D1 réel hors périmètre |
| CO-6 | Volumétrie réelle inconnue (verrouillage pendant l'`ALTER`, coût des déclencheurs) | **O-8 NON VÉRIFIÉ** | jeu synthétique volumineux en R2 sous contrainte de ressources (A-T2) |
| CO-7 | La DEMO utilise une copie de version limitée à 0020 | FAIT VÉRIFIÉ | G7 hors mission |

### 11.2 Périmètre de non-régression ciblée (plan P82 §9.5, un fichier à la fois)

| Domaine | Familles de tests à rejouer (références P82) |
|---|---|
| Stock | `tests/ui3/stock_http`, suites stock, FEFO, niveaux, alertes |
| Ventes | `sales` (réservation, vente, retour, correction, annulation) |
| Achats | `purchasing` (réception, contre-passation, propositions de commande) |
| Catalogue | archivage, suivi par lot, alertes de péremption |
| Gouvernance | catalogue de droits, `authorize` |
| Migrations / schéma | `schema_parite`, `p33/fleet_inventory`, `p34/release_unverified`, `lot0c` |
| Interface | `p69/i18n_keys`, suites HTTP `ui5/collab_http` |
| Intégrité | contrôles A1-A8, B1-B22 après chaque scénario |

**Règle** : le nombre de tests réussis de chaque fichier en R5 = celui de R0 + les tests nouveaux ; **0 régression** ; toute modification d'un test existant est **listée et justifiée** (plan P82 §3.3 ; A-T1 point 2). Aucune assertion n'est relâchée : un nombre figé est remplacé par un nombre calculé.

### 11.3 Tests existants à adapter (liste préliminaire, à confirmer en R1)

| Test | Raison | Forme de l'adaptation |
|---|---|---|
| `tests/lot1b/schema_parite.test.ts` | nombre d'identifiants de migration figé | nombre calculé (officiels + fixtures), valable avant et après G6 |
| `tests/p33/fleet_inventory.test.ts` | simule une ligne « 0022 » inconnue | identifiant éloigné (ex. `0099`) |
| `tests/p34/release_unverified.test.ts` | idem | idem |
| `tests/support/env.ts` `migrationSets()` | chargement du candidat | paramètre facultatif + idempotence (A-T1) |
| `tests/lot0c/d1Env.ts` | banc D1 | même extension |
| Tests de catalogue de droits | nouveaux droits | ajout des entrées, sans retirer d'assertion |
| Tests de messages achats / i18n | nouveaux messages | ajout de clés, égalité FR/EN/AR conservée |

---

## 12. Plan de retour arrière

Repris du plan P82 §10.3 et de P81 §8, complété par les contraintes de P84.

| Situation | Retour arrière | Remarques |
|---|---|---|
| Essai sur copie jetable (R2 à R5) | **Abandonner la copie** ; recommencer sur une copie neuve après correction du candidat (non officiel) | aucun fichier supprimé sans décision séparée du PO |
| Candidat refusé à l'examen (avant G6) | **Ne pas promouvoir** ; rien n'est déployé ; le dossier candidat reste | pas de suppression automatique |
| Code modifié (R4) | Le code est sous sauvegarde automatique ; **aucune opération Git manuelle** ; l'abandon d'une modification est une **décision du PO** | le rapport de lot liste les fichiers modifiés et leurs empreintes (M0 → M1) |
| Rupture de livraison partielle (A-T1) | Le **gel de livraison** empêche toute construction à partir du dépôt entre R4a et G6 ; en cas d'arrêt, la DEMO reste sur sa copie de version | manifestes M0/M1/M2 |
| Après G6 (promotion) | Toute erreur de schéma = **migration 0023** ; la 0022 n'est jamais éditée (MG-5) | — |
| Après G7 (application, hors mission) | Sauvegarde vérifiée avant ; service arrêté ; **restauration** si un contrôle échoue ; **pas de réparation sur place** | décision distincte (DP6-R7) |
| **Retour du code après usage des états** | **Interdit sans restauration** (RM-9) : après le premier passage d'état réel, un ancien binaire verrait des quantités hors D | à rappeler à G7 |
| Ressources épuisées pendant un essai | Arrêt immédiat (A-T2 §3.6) ; ne rien supprimer ; rapporter | S-1 à S-7 |

**Principe** : on ne répare pas, on **restaure** ou on **abandonne** une copie. Un retour arrière ne supprime ni sauvegarde, ni rapport, ni preuve.

---

## 13. Critères d'acceptation et conditions d'arrêt

### 13.1 Critères d'acceptation de la réalisation (tous requis)

| # | Critère | Preuve |
|---|---|---|
| AC-1 | Les **huit conditions C1 à C8** de l'étude A-T1 (§2.5) sont vraies simultanément | rapport, manifeste M2 |
| AC-2 | Tous les scénarios **NEG, REV, PER, CAT, MIG** de la matrice P84 sont **PASS** ; chaque test de sensibilité (E-6) était rouge avant R4 | résultats par identifiant |
| AC-3 | Les scénarios **V6-01 à V6-09** (messages, composants) sont PASS ; **V6-10 à V6-19** sont PASS **ou** explicitement listés **NON VÉRIFIÉS** (A-T2, phase 2) | rapport |
| AC-4 | Contrôles d'intégrité **A1-A8 et B1-B22** : égalité exacte, après migration **et** après chaque scénario | rapport |
| AC-5 | **0 régression** de la non-régression ciblée ; liste des tests modifiés justifiée | R5 |
| AC-6 | Invariants INV-1…INV-10 vérifiés **par la base** (insertion directe de lignes interdites refusée) | PER-22, NEG-17, MIG-T1 |
| AC-7 | L'identité CAT-11 est vraie par lot et par produit sans lot, avec la formule de référence (§15) | CAT-01…14 |
| AC-8 | **C-P82-1, C-P82-2, C-P82-3** démontrées par scénarios (NEG-10…19, REV-30…36, PER-04/40…47) | résultats |
| AC-9 | Aucune action interdite : 0 tag, 0 stash, 0 commit manuel, aucune base réelle ouverte, DEMO intacte, dump `core` intact | relevés |
| AC-10 | **Acceptation écrite du PO** sur le rapport de réalisation (G6) | décision |

### 13.2 Conditions d'arrêt (une seule suffit)

Reprise du plan P82 §10.2 (10 conditions) et ajouts P84 :

| # | Condition | Source |
|---|---|---|
| 1 | Un contrôle A ou B1-B22 hors tolérance (égalité exacte) | P82 §10.2 |
| 2 | Une ligne perdue, modifiée ou ajoutée dans une table existante par la migration | P82 |
| 3 | Une somme de contrôle de migration déjà appliquée qui diverge | P82 |
| 4 | Un niveau négatif (produit, lot ou état) | P82 |
| 5 | Une chaîne d'audit rompue | P82 |
| 6 | Une régression par rapport à la ligne de base R0 | P82 |
| 7 | Toute lecture ou écriture visant la DEMO, un répertoire réel, la copie de version de la DEMO ou un fichier de codes | P82 |
| 8 | Disque ou mémoire sous les seuils, ou durée anormale | P82 + A-T2 §3.6 (S-1…S-6) |
| 9 | Réponse du PO manquante ou ambiguë qui change un résultat attendu ; **ou** `compose.ts` à modifier sans arbitrage A-T1 | P82 + A-T1 |
| 10 | Écriture dans `migrations/pharmacy/` avant G6 | P82 |
| 11 | Construction d'une version livrable ou copie de version à partir du dépôt pendant le gel (R4a→G6) | A-T1 (CL) |
| 12 | Manifeste M2 non conforme au candidat validé (C3) ou au dossier officiel (C2) | A-T1 |
| 13 | Échec de création de base temporaire (`ENOSPC`, tmpfs plein) sans `TMPDIR` autorisé | A-T2 S-4 |
| 14 | Apparition d'un tag, d'un stash ou d'un commit manuel | règle permanente |
| 15 | Un test de sensibilité (E-6) qui **passe** avant correction (le test ne prouve rien) | P84 |

**En cas d'arrêt** : consigner l'état exact, **ne rien réparer**, ne rien supprimer, informer le PO, ne pas poursuivre (DP6-R5).

---

## 14. Interface et messages (UI)

### 14.1 Constats de départ (FAIT VÉRIFIÉ, lecture seule)

| # | Constat | Source |
|---|---|---|
| UI-F1 | L'interface **V1** (`ui/public/on-premise/js/*.js`) et **V3** (`ui/saas-src`, React/Vite) existent en parallèle ; les textes FR/EN/AR sont générés par `tools/gen_*_i18n.cjs` depuis `ui/saas-src/src/lib/*Strings.js`. | lecture |
| UI-F2 | L'interface achats ne remplace que la variable `{n}` dans les messages : aucun message n'affiche les quantités par état. | lecture de `purchasing.js` / `purchaseStrings.js` |
| UI-F3 | `purchase.reversal_state`, la confirmation PERTE et les écrans de passage d'état n'existent pas. | recherche |
| UI-F4 | Le plan P82 classe l'interface (R7) comme **mission distincte, hors R0-R6**. | plan P82 §7 |
| UI-F5 | RTL V3 vérifié sur la fiche catalogue seulement ; écrans stock/ventes/commandes V3 non audités en RTL. | P60 |
| UI-F6 | Test d'égalité des clés FR/EN/AR : `tests/p69/i18n_keys.test.ts`. | lecture |

### 14.2 Tension à arbitrer (A-T3)

C-P82-2 et C-P82-3 sont des **précautions confirmées par le PO** : le refus d'annulation intégrale doit être **expliqué à l'utilisateur avec les quantités**, et le passage en PERTE exige une **confirmation explicite avec quantité et conséquences irréversibles**. Si la réalisation livre le service (R4) **sans** l'interface (R7 distinct), alors :
- un refus `purchase.reversal_state` serait affiché par un message générique (précaution C-P82-2 **non respectée côté utilisateur**) ;
- aucune interface ne permettrait de passer en PERTE avec confirmation (la fonction ne serait pas utilisable par l'utilisateur) ou, pire, serait exposée par l'API sans garde visuelle.

### 14.3 Messages obligatoires (cahier des charges, réf. matrice §5.4)

| Réf. | Message / information | Origine |
|---|---|---|
| MSG-1 | Refus de reprise : quantité demandée, **reprenable**, D, C, Q, P, reçu, déjà repris ; marche à suivre | C-P82-2, REV-30…35 |
| MSG-2 | Refus d'annulation intégrale : **lignes** concernées avec la part hors D | C-P82-2, REV-34 |
| MSG-3 | Confirmation PERTE : quantité exacte, produit, lot, état d'origine, texte d'irréversibilité, motif, **deux actions** | C-P82-3, PER-40…47 |
| MSG-4 | Orientation « en cas de doute, EN_CONTROLE (réversible) » | V2-R8 |
| MSG-5 | Refus nommés : `stock.state_exit_deferred`, `stock.reservation_unfulfillable`, `stock.level_changed`, droit manquant, motif interdit en correction | E-1 matrice |
| MSG-6 | Archivage refusé : répartition D/C/Q/P | S-10 |
| MSG-7 | Réservation non honorable : indicateur et cause | V1-R6 |
| MSG-8 | Aucun message ne suggère une sortie depuis Q ou P | DP1-R7, REV-36 |

### 14.4 Règles de réalisation **[TECH]**

1. Un message de refus **porte des variables nommées** (et non `{n}` seul) ; le moteur de remplacement de l'interface est étendu **sans changer** les messages existants (non-régression V6-17).
2. Chaînes FR/EN/AR dans `*Strings.js` ; régénération des fichiers par les outils existants ; **égalité des clés** vérifiée par `tests/p69`.
3. Nombres, codes de lot et quantités isolés (`dir="ltr"` local ou isolation bidi) pour rester lisibles en RTL (V6-12).
4. La confirmation PERTE est une **étape distincte** (pas de validation par une seule action).
5. Les options de passage proposées sont **filtrées** par la matrice et les droits ; l'API reste la garde finale (la base aussi).

### 14.5 Vérifié / à tester (rappel de la matrice §5)

Déjà vérifié : RTL de la fiche catalogue V3 (P60) ; responsive V1 existant (P65) ; égalité des clés (P69). **À tester** : tous les écrans nouveaux ou modifiés, V3 stock/commandes en RTL, lisibilité arabophone (non automatisable), navigateur autre que Chromium.

### 14.6 A-T3 — Définition et recommandation (PROPOSITION)

**A-T3 — Livraison de l'interface minimale obligatoire avant G6.** Le point à traiter : **dans quel périmètre livre-t-on les messages C-P82-2 et C-P82-3 ?**

| Option | Contenu | Avantage | Inconvénient |
|---|---|---|---|
| **A (recommandée)** | Lot **R7a** avant G6 : seulement MSG-1, MSG-2, MSG-3, MSG-4, MSG-5, MSG-6 (V1 **et** V3), + clés FR/EN/AR ; le reste de R7 (écrans complets de gestion des états) reste mission distincte | respecte C-P82-2/3 dès la livraison ; périmètre borné | ajoute un lot ; vérification navigateur en phase 2 |
| B | Service seul en G6 ; interface en mission distincte ensuite ; **PERTE désactivée côté API** jusqu'à l'interface | livraison plus simple | C-P82-3 non utilisable ; refus de reprise générique (C-P82-2 non satisfaite côté utilisateur) |
| C | Interface complète (R7) avant G6 | cohérence maximale | périmètre large, ressources (A-T2) |

**Recommandation : option A.** Sans arbitrage, la réalisation applique **B restreinte** : le service est livré avec les **messages nommés dans les réponses** (charge utile `purchase.reversal_state`, refus de PERTE sans confirmation) mais **sans promouvoir G6** tant que MSG-1 à MSG-4 n'existent pas côté interface ; elle s'arrête à R6 et le rapport signale « A-T3 non arbitré ». C'est l'**arrêt le plus sûr** : aucune précaution confirmée n'est contournée.

**ARBITRAGE A-T3** — Le PO choisit A, B ou C ; ce choix détermine si R7a existe et conditionne G6.

---

## 15. V-3 — Formule CAT-11 (NON APPROUVÉ)

- **Référence** : formule de la fiche P81 (DP1-R9). Elle ne dit pas dans quel membre se rangent contre-passations et retours.
- **Convention de calcul P82** (non décidée) : contre-passations = **corrections nettes** ; retours clients = **déduction des sorties nettes**. Elle n'est **pas une règle métier**.
- **Traitement dans la réalisation** : la convention est **vérifiée par les tests CAT-01…14** (matrice §6), y compris **CAT-12** qui montre que la lecture alternative échoue sur les données. **Aucune approbation n'est présumée** : si un test révèle une incohérence avec la formule de référence, **arrêt** (condition 1) et retour au PO.
- Le côté « reçu » est calculé depuis `purchase_receipt_lines` (source indépendante), jamais depuis la vue testée (CAT-11).
- V-3 reste **NON APPROUVÉ** dans ce dossier ; le rapport de réalisation reproduira le résultat des tests sans le présenter comme une décision.

---

## 16. Conditions de DP-6 et précautions C-P82-1 à C-P82-3

### 16.1 Les huit conditions de DP-6 (registre §7)

| N° | Condition | Application P84 / réalisation | Contrôle |
|---|---|---|---|
| 1 | DP-1 à DP-5 décidés par écrit | remplie (P81, P83) ; **la réalisation exige en plus** l'autorisation explicite du PO | R0 |
| 2 | Essais uniquement sur bases temporaires fictives créées par les tests | MG-6 ; aucun accès à la DEMO ni aux fichiers de codes | B22 ; arrêt 7 |
| 3 | Fichier candidat hors dossier officiel ; promotion distincte | MG-3, MG-4 | B21 ; arrêt 10 |
| 4 | Aucune application à la DEMO ni à une base réelle ; accord préalable sur 0021 | G7 distinct ; O-9 OUVERT | B22 |
| 5 | Contrôles d'intégrité avant et après ; arrêt immédiat | §13 ; A1-A8, B1-B22 | chaque essai |
| 6 | Ressources : un fichier de test à la fois, disque et mémoire vérifiés avant chaque essai, aucune suite complète | **A-T2** : phases 1/2, seuils, `TMPDIR` (étude §3) | avant chaque lancement |
| 7 | Retour arrière prévu avant l'essai ; copie défaillante abandonnée ; aucun retour de code après usage des états sans restauration | §12 | revue avant R2 |
| 8 | Aucun commit, push ou publication manuel ; compte rendu complet | sauvegarde automatique seulement ; rapport R6 ; relevés git/tags/stash | fin de chaque lot |

### 16.2 Précautions C-P82-1 à C-P82-3 — où elles sont garanties

| Précaution | Texte (P83 §5) | Garde technique | Scénarios |
|---|---|---|---|
| **C-P82-1** | une correction négative ne fait jamais disparaître artificiellement un produit périmé/cassé/à détruire ; vrai écart de comptage | motifs de sortie refusés en correction (même ADMIN) ; aucune correction en P ; droit restreint pour C/Q/périmé/zéro ; rapport d'écarts et rapprochement | NEG-10…19, 40…47, 50…52, 60…68 |
| **C-P82-2** | refus d'annulation intégrale expliqué, quantités affichées | `purchase.reversal_state` + charge utile détaillée ; messages MSG-1/2 (V1, V3) | REV-03/04/14/30…36, V6-02 |
| **C-P82-3** | confirmation explicite avant PERTE, quantité et conséquences irréversibles ; refus de retour | confirmation exigée par le service ; matrice sans départ de P ; MSG-3/4 | PER-04/05/20…27/40…47, V6-01 |

**Lien avec A-T3** : C-P82-2 et C-P82-3 ne sont respectées **côté utilisateur** que si l'interface minimale (R7a) est livrée avant G6, d'où l'arbitrage du §14.6.

---

## 17. Risques techniques, dépendances et vigilances

### 17.1 Risques

| # | Risque | Gravité | Parade | Réf. |
|---|---|---|---|---|
| RT-1 | Livraison partielle code/migration (A-T1) | élevée | manifestes, gel, garde de démarrage | étude §2 |
| RT-2 | Saturation mémoire/disque (A-T2), redémarrage et perte de la DEMO (précédent P70) | élevée | phases, seuils, arrêts S-1…S-7 | étude §3 |
| RT-3 | `/tmp` plein : bancs fichier/D1 inutilisables | élevée | `TMPDIR` dédié sur disque (arbitrage) ; **NON TESTÉ** | étude §3.2 |
| RT-4 | `ALTER ADD COLUMN … STRICT` en D1 réel (O-7) | moyenne | essais SQLite/D1 local ; D1 réel hors périmètre | CO-5 |
| RT-5 | Volumétrie inconnue (O-8) | moyenne | jeu synthétique ; mesure R2 | CO-6 |
| RT-6 | Droit restreint contrôlé en code seulement | moyenne | tests de service ; revue du catalogue | D-1 |
| RT-7 | Ordre des déclencheurs non garanti | moyenne | déclencheurs indépendants, refus atomiques | §8 |
| RT-8 | Complétude d'une paire de passage non garantie par SQLite | moyenne | unité de travail unique + B14 | §8 |
| RT-9 | Précautions C-P82-2/3 non respectées côté utilisateur sans interface | **élevée** | A-T3 | §14.6 |
| RT-10 | Retour de code après usage des états | élevée | interdit sans restauration | §12 |
| RT-11 | Deux implémentations FEFO divergentes pendant la transition | moyenne | R4a : calcul unique sans changement de comportement | plan P82 |
| RT-12 | Messages UI : variables non remplacées, RTL illisible | moyenne | V6-08, V6-12 | matrice §5 |

### 17.2 Dépendances

| # | Dépendance | État |
|---|---|---|
| DEP-1 | Autorisation explicite du PO pour lancer la réalisation | **absente** |
| DEP-2 | Arbitrages A-T1, A-T2, A-T3 | **demandés** |
| DEP-3 | O-9 : application de la 0021 à la DEMO (condition de G7) | OUVERT |
| DEP-4 | O-5, O-6, O-10 | OUVERT |
| DEP-5 | Mémoire supplémentaire ou autre hôte pour la phase 2 | à décider |
| DEP-6 | Répertoire `TMPDIR` dédié autorisé | à décider |

### 17.3 Vigilances (non modifiées)

V-3 NON APPROUVÉ ; V-4 (couplage code/schéma : traité par A-T1) ; V-5 (spécifié, NEG-60…68) ; V-6 (spécifié, V6-xx).

---

## 18. Limites de ce dossier

- Aucune mesure ni exécution : tout ce qui touche aux ressources, aux bancs D1 et à l'interface réelle est **NON MESURÉ / NON TESTÉ**.
- Les noms de droits, actions d'audit, codes d'erreur nouveaux et formats de charge utile sont des **propositions** à fixer en R1 (§9.4).
- Les décisions P81/P82/P83 ne sont ni réinterprétées ni modifiées ; toute divergence détectée en réalisation = arrêt et retour au PO.
- Ce dossier **n'autorise ni développement, ni migration, ni promotion** ; il prépare la décision.
