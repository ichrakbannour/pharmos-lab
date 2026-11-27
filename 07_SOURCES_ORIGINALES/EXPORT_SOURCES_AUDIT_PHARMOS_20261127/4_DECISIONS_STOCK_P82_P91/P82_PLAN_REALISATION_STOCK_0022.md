# P82 — Plan de réalisation STOCK et migration 0022 (technique, séquencé)

**Statut : PROPOSITION de plan. Rien n'est créé, appliqué ni exécuté.** Aucun fichier SQL 0022 n'existe ; les extraits sont **illustratifs**. Ce plan s'appuie sur le registre officiel (`docs/P81_REGISTRE_DECISIONS_PO_STOCK.md`, « le registre »), sur les règles proposées dans `docs/P82_SPECIFICATIONS_FINALES_STOCK.md` (« les spécifications », règles `V1-Rn`, `V2-Rn`, `S-n`, `INV-n`, questions `Q-n`) et sur le plan historique `docs/P81_PLAN_MIGRATION_0022.md` (« plan P81 », non modifié, référencé et non recopié).
**La réalisation reste interdite sans autorisation distincte du PO et mission distincte** (registre §11).
Libellés : FAIT VÉRIFIÉ (relu dans le dépôt le 2026-11-25) · HYPOTHÈSE · PROPOSITION · NON VÉRIFIÉ · OUVERT · REPORTÉ.

---

## 1. Conditions de démarrage (portes G0)

| # | Condition | État |
|---|---|---|
| G0-1 | Le PO répond par écrit à la fiche Q-1 à Q-12 (spécifications §1) ; les réponses sont consignées dans un **addendum daté du registre** (registre §12). | À faire (PO) |
| G0-2 | Autorisation explicite et distincte de réaliser, avec le périmètre de la mission (voir §7 : lots R0 à R6). | À faire (PO) |
| G0-3 | Les huit conditions de DP-6 sont reprises (§11). | Prévu |
| G0-4 | Ressources vérifiées au démarrage et avant chaque essai (§12). | Prévu |

**Conditionnalité des réponses du PO** : une réponse différente de la recommandation modifie le plan comme suit.

| Réponse | Effet sur le plan |
|---|---|
| Q-3 refusée (garder le blocage mutuel actuel) | pas de V1-R5 : le déclencheur de réservation garde « toutes les autres réservations » ; V1-R4 reste en lecture seule ; scénario V1-S2 réécrit |
| Q-5 variante « habilitation pharmacien » | droit métier non délégable `requiresHabilitation: 'pharmacien'` au lieu d'un droit de gouvernance ; pas d'authentification forte exigée |
| Q-9 refusée | pas de droit `stock.adjust.nondisponible` ; les corrections sur états non disponibles sont refusées (tout excédent ou manque hors DISPONIBLE passe par un passage d'état) |
| Q-10 refusée | la contre-passation garde sa garde actuelle (niveau du lot) : risque d'écart avec C3, à documenter |
| Q-11 refusée | modification de DP1-R4 : nouvelle décision du registre, hors de ce plan |
| Q-12 variante `CHECK` | pas de table `stock_reason_codes` ; liste figée ; toute évolution = reconstruction de `stock_movements` (non recommandé) |
| Q-1 ou Q-2 refusée | V1-R9/V1-R10 à réécrire ; tests V1-S5/S6 modifiés |

---

## 2. Principes et écarts avec le plan P81

Les principes P81 (plan P81 §2) restent : journal = seule source de vérité, aucune ligne réécrite, additif, stock négatif impossible par état et par lot, FEFO atomique conservé, séparation catalogue → adoption → commande → réception, fichier candidat hors dossier officiel.

**Écarts proposés par P82 par rapport au plan P81 §3** (tous soumis à la fiche PO ou sans effet métier) :

| # | Écart | Raison |
|---|---|---|
| E-a | Ajout de la colonne nullable `stock_movements.reason_code` (clé étrangère vers `stock_reason_codes`) et de la table de référence. | O-11 / Q-12 ; une liste en `CHECK` est immodifiable sans reconstruction |
| E-b | Ajout de l'en-tête immuable `stock_issue_overrides` pour la dérogation ADMIN (DP4-R2). **La sortie dérogatoire garde la source `stock.manual`** et référence l'en-tête par `source_ref` : le regroupement des sorties manuelles (`stock/service.ts` l. 532-563) reste valide. *(Corrige la mention d'une source dédiée dans les spécifications §8.4.)* | FAIT VÉRIFIÉ : ce regroupement filtre `source = 'stock.manual'` |
| E-c | Le déclencheur de réservation remplacé ne retient que les réservations **plus anciennes** que celle de la vente (Q-3) et couvre les sorties manuelles. | V1-R5, DP4-R1 |
| E-d | Une garde de base par état et un déclencheur « aucune sortie hors DISPONIBLE » (INV-5, INV-6). | DP1-R7 |
| E-e | Le jeu de données de test préalable à 0021 est une **fixture SQL figée** (option (a) du plan P81 §4.6). | évite de figer 1 258 lignes de services |
| E-f | Sources nouvelles : `stock.transfer`, `stock.regularization` (régularisation hors commande, DP5-R2). | `source` n'a qu'une contrainte de longueur 3-40 |

---

## 3. Dépendances et fichiers concernés (relevé du 2026-11-25)

### 3.1 Schéma et migration

| Fichier | Rôle | Action |
|---|---|---|
| `pharmos/migrations/pharmacy/` (0001 à 0021) | dossier officiel, **chargé automatiquement** | **aucune modification** avant la décision G6 |
| `pharmos/migrations_candidates/pharmacy/0022_stock_etats.sql` | fichier candidat (le dossier n'existe pas aujourd'hui) | à créer en R2 ; jamais lu par les outils de production |
| `pharmos/src/adapters/file/migrationFiles.ts` | lit uniquement les fichiers `NNNN_nom.sql` d'un dossier | **non modifié** |
| `pharmos/src/platform/migrations.ts` | `applyMigrations`, `verifyHistory` (somme de contrôle) | non modifié |
| `pharmos/src/platform/tenants.ts` l. 222 | refuse l'ouverture d'une base avec migrations en attente (`tenant.migrations_pending`) | non modifié ; voir risque §6 |

### 3.2 Code applicatif (mission de réalisation)

| Fichier | Lignes (indicatif) | Adaptation | Règles |
|---|---|---|---|
| `src/stock/service.ts` (1 019 l.) | `levelsSql` l. 153, `levelOf` l. 247, `fefoFor`/`issuePlan` l. 927-970, `alerts` l. 606, `receive` l. 726, `issue` l. 821, `adjust` l. 976 | niveaux par état, FEFO partagé, passages, statut de lot, sorties manuelles (motifs, réservations, dérogation), corrections par état, alertes | S-1 à S-13, V1, V2 |
| `src/stock/` (nouveaux modules proposés) | — | `levels.ts` (calcul partagé disponible/vendable/par état), `transfer.ts`, `lotStatus.ts`, `reasons.ts` | INV-2 à INV-8 |
| `src/sales/plan.ts` (248 l.) | `reservedOthers` l. 115, `stockView` l. 134, `fefo` l. 154, `buildPlan` l. 206 | utiliser le calcul partagé ; réservations par ancienneté ; vocabulaire P81 (tâche R-A1) | V1-R4/R5, CAT-10 |
| `src/sales/service.ts` (1 024 l.) | `reserve` l. 561-590, `listReservations` l. 625, `operate` l. 905 (retours) | indicateur « non honorable » ; retours en DISPONIBLE (inchangé) | V1-R6, DP5-R4 |
| `src/purchasing/service.ts` (1 401 l.) | `operateReceipt` l. 1277-1380 (niveau l. 1376), l. 343, l. 554 | contre-passation limitée au DISPONIBLE ; seuil et catalogue national sur le disponible | V2-R6 |
| `src/catalog/service.ts` (1 019 l.) | `createProduct` l. 408-462, adoption l. 972, archivage l. 557-571, quantité l. 97 | suivi par lot obligatoire pour MÉDICAMENT ; message d'archivage par état | DP2-R1, C1 |
| `src/governance/catalog.ts` (110 l.) | l. 29-37 | droits `stock.state.transfer`, `stock.lot.block`, `stock.adjust.nondisponible`, `stock.regularize` (métier) ; `stock.quarantine.release` (gouvernance) | §4.3 spécifications |
| `src/entry/stockRoutes.ts` | l. 47-61 | routes de passage d'état, statut de lot, états par lot, indicateur de destruction | — |
| `src/entry/demoSeed.ts`, `demoMigrate.ts` | — | **non modifiés** ; vérifier seulement qu'ils ne chargent pas le dossier candidat | DP6-R2 |
| `ui/public/on-premise/js/stock.js`, `ui/saas-src/src/pages/Stock.jsx`, `ui/saas-src/src/lib/stockApi.js` | — | écrans : états, passages, lots bloqués, motifs ; **mission d'interface distincte** (lot R7 optionnel) | — |

**FAIT VÉRIFIÉ** : la DEMO en cours (PID 5638) s'exécute depuis une **copie de version** (`/home/user/.pharmos-private/releases/p53ui-fb956a9/pharmos`), pas depuis le dépôt `webapp/pharmos`. Une modification du dépôt n'atteint donc pas la DEMO ; la copie de version ne contient que les migrations jusqu'à 0020 (observation). Aucune étape de ce plan ne touche ce répertoire.

### 3.3 Tests existants à relire ou adapter (liste de départ, à confirmer en R1)

| Domaine | Fichiers | Impact attendu |
|---|---|---|
| Parité de schéma | `tests/lot1b/schema_parite.test.ts` (l. 21 : 22 identifiants) | 22 → 23 **à la promotion seulement** ; en phase candidate, un test dédié compare le jeu officiel + candidat |
| Historique « en avance » | `tests/p33/fleet_inventory.test.ts` l. 95, `tests/p34/release_unverified.test.ts` l. 52 (ligne `0022` factice) | **à la promotion** : scénario à réécrire avec un numéro libre (0099) ; effet exact NON VÉRIFIÉ |
| Droits | `tests/ui5/collab_http.test.ts` l. 79 (`permissions.length` = 50) | **+5 droits** (4 métier + 1 gouvernance) → 55 ; modification justifiée au rapport |
| Création de MÉDICAMENT | `tests/ui4/scenarios_catalog.ts` (l. 54, 75, 78, 93, 102, 457, 479…), `tests/ui4/catalog_http.test.ts` l. 143, `tests/p20/scenarios_adoption.ts` (adoption), `tests/p43/tva_p432.test.ts` l. 225 (NON VÉRIFIÉ : création par service ou par SQL) | DP2-R1 : un médicament sans suivi par lot est désormais refusé ; ces scénarios doivent passer `tracksLots: true`. **Modifications de tests nécessaires**, listées une à une au rapport |
| Insertions directes dans le journal | `tests/p19/scenarios_suppliers.ts`, `tests/p43/ventes_p43.test.ts`, `tests/p45/reception_p45.test.ts`, `tests/ui3/scenarios_stock.ts` (2), `tests/ui4/scenarios_catalog.ts` (2), `tests/p54/legacy_purchasing_0020.ts` (2, copie figée) | l'état par défaut DISPONIBLE rend ces insertions valides ; à vérifier par exécution, **sans modification attendue** |
| Instantanés `SELECT * FROM stock_movements` | `tests/ui3/scenarios_stock.ts` l. 666 et 678, `tests/ui4/scenarios_catalog.ts` l. 62, `tests/p20/scenarios_suppliers_p20.ts` l. 162 et 200 | comparaison avant/après sur la même base : insensible aux colonnes ajoutées |
| FEFO, ventes, achats, catalogue | voir §9.5 | non-régression ciblée |
| Banc D1 | `tests/lot0c/d1Env.ts` (`makeD1Env(capacity)`), `tests/ui3/stock_d1.test.ts` (capacité 32) | chaque nouveau fichier D1 déclare sa capacité et reste à marge ≥ 5 |
| Jeu de migrations de test | `tests/support/env.ts` `migrationSets()` l. 27-32 | **une seule modification de support** : lecture facultative d'un dossier candidat (§4.2) |

### 3.4 Dépendances entre lots

`R0` (addendum, gel) → `R1` (fixture 0021, relevé des lectures) → `R2` (candidat + premier essai sur copie) → `R3` (tests rouges) → `R4` (code) → `R5` (non-régression) → `R6` (rapport) ; `R7` (interface) dépend de R4 et d'une décision distincte ; **G6** (promotion) et **G7** (application à la DEMO) restent des décisions séparées (registre DP6-R7).

---

## 4. Conception du candidat 0022

Le schéma de base est celui du plan P81 §3 (colonnes `bucket` et `transfer_id`, en-tête `stock_state_transfers`, événements `stock_lot_status_events`, vues `stock_levels` et `stock_lot_received`). Cette section ne décrit que les **compléments P82**. Tous les extraits sont illustratifs, non validés, jamais exécutés.

### 4.1 Objets du candidat

| Objet | Action | Remarque |
|---|---|---|
| `stock_movements.bucket` | `ADD COLUMN … NOT NULL DEFAULT 'DISPONIBLE'` avec `CHECK` sur les quatre états | aucune ligne existante réécrite (INV-9) |
| `stock_movements.transfer_id` | `ADD COLUMN` nullable, clé étrangère vers `stock_state_transfers` | défaut nul obligatoire pour une clé étrangère ajoutée par `ALTER TABLE` |
| `stock_movements.reason_code` | `ADD COLUMN` nullable, clé étrangère vers `stock_reason_codes` | écart E-a ; précédent : 0008 (`supplier_id`) |
| `stock_reason_codes` | table de référence, amorcée par `INSERT` (motifs de sortie et de correction, spécifications §8.3) | `STRICT`, immuable en code ; une ligne s'ajoute par migration ultérieure |
| `stock_state_transfers` | en-tête immuable d'un passage (quantité, motif, auteur, date, états) | plan P81 §3.2 |
| `stock_lot_status_events` | événements append-only ; **colonne `seq` par lot, unique** (même patron que `sale_operations`), statut courant = plus grand `seq` | évite de dépendre de l'ordre des identifiants |
| `stock_issue_overrides` | en-tête immuable de dérogation ADMIN (auteur, motif, produit, réservations concernées) | écart E-b |
| Index | `(product_id, lot_id, bucket)` ; unique `(transfer_id, bucket)` où `transfer_id` non nul ; unique `(lot_id, seq)` sur les statuts | existants intacts |
| Vues | `stock_levels`, `stock_lot_received`, `stock_reservations_state` (réservations non honorables, **calculée**, lecture seule) | jamais de table modifiable |

### 4.2 Déclencheurs (propositions ; rôle, règle, test)

| Déclencheur | Règle | Test |
|---|---|---|
| `stock_movements_no_negative` (0005, **inchangé**) | garde physique par produit et lot | existant |
| `stock_movements_no_negative_bucket` (nouveau) | un mouvement négatif ne rend jamais négative la somme de **son** produit/lot/état (INV-2) | ST-01-d, ST-04-c |
| `stock_movements_bucket_rules` (nouveau) | `ENTREE` : état DISPONIBLE ; `SORTIE` : état DISPONIBLE ; mouvement `CORRECTION` positif hors DISPONIBLE : seulement avec `transfer_id` ; **tout mouvement négatif en PERTE refusé** (INV-6) ; correction négative sans `transfer_id` en EN_CONTROLE ou QUARANTAINE : source `stock.manual` seulement (V2-R3 b ; le droit est contrôlé en code) ; `purchasing.reversal` : DISPONIBLE (V2-R6) | CAT-11-d, V2-T1 à T6 |
| `stock_movements_transfer_coherence` (nouveau) | un mouvement portant `transfer_id` : type `CORRECTION`, source `stock.transfer`, en-tête existant, même produit et lot, état conforme (`−q` dans l'état de départ, `+q` dans l'état d'arrivée) (INV-7) | ST-04-d |
| `stock_state_transfers_matrix` (nouveau) | paires d'états autorisées (registre §2) ; refus de tout départ de PERTE (DP1-R4) | ST-04-d |
| `stock_state_transfers_*_immutable`, `stock_lot_status_events_*_immutable`, `stock_issue_overrides_*_immutable` | pas de `UPDATE` ni `DELETE` (INV-1) | B12 du plan P81 |
| `stock_movements_lot_status` (nouveau) | vente `sales.sale` sur un lot BLOQUE ou RAPPELE refusée (INV-8) | CAT-10-b |
| `stock_movements_sales_reserved` (0016 l. 323, **remplacé** par `DROP TRIGGER` puis `CREATE TRIGGER`, précédents : 0004, 0017, 0019, 0021) | (a) vente : somme **DISPONIBLE** du produit après le mouvement ≥ réservations actives **plus anciennes** que la réservation de la vente (toutes, sans réservation) ; (b) sortie manuelle `stock.manual` : somme DISPONIBLE ≥ **toutes** les réservations actives, sauf si `source_ref` désigne une dérogation existante ; (c) **aucune** autre source n'est visée (INV-10) | CAT-10-d, V1-S1, V1-S5 |
| `stock_movements_manual_reason` (nouveau) | une sortie `stock.manual` de lot périmé, BLOQUE ou RAPPELE exige un `reason_code` parmi PEREMPTION, RAPPEL, CASSE (V1-R10) ; une correction n'accepte aucun code de sortie (V2-R5 a) | V1-S6, V2-T4 |

**Points d'attention (FAIT VÉRIFIÉ ou HYPOTHÈSE)**
- **Ordre d'exécution des déclencheurs** d'un même événement : non garanti par SQLite. Le plan ne s'appuie sur **aucun ordre** : chaque déclencheur est indépendant et chaque refus est atomique (HYPOTHÈSE à vérifier au premier essai).
- **Complétude d'une paire de passage** : SQLite ne permet pas de contrôle différé en fin de transaction. La complétude repose sur l'**unité de travail unique** du code (deux mouvements + en-tête ou rien) et sur un **contrôle d'intégrité** (§10 : chaque en-tête a exactement deux mouvements de somme nulle). Le contrôle est exécuté après chaque scénario de test.
- **Chaque déclencheur du candidat est une instruction unique** : `hasMultipleStatements` (`kernel/sqlText.ts` l. 52) refuse tout fichier qui en fusionne plusieurs ; chaque instruction est séparée par `--> statement-breakpoint` (convention des migrations existantes).
- **Comportement d'un ancien binaire** (qui ignore `bucket`) : ses écritures tombent en DISPONIBLE ; la garde par état refuse toute sortie entamant une quantité non disponible : échec sûr (HYPOTHÈSE du plan P81 §3.3, test ST-01-e).

### 4.3 Chargement du candidat par les tests, sans toucher au dossier officiel

| Élément | Proposition |
|---|---|
| Dossier | `pharmos/migrations_candidates/pharmacy/0022_stock_etats.sql` (le nom de dossier n'est lu par **aucun** chemin de production : `DEFAULT_MIGRATIONS_DIR` pointe sur `pharmos/migrations/`) |
| Chargement | modification **unique** de `tests/support/env.ts` `migrationSets()` : paramètre facultatif `candidates` ; ajoute les sources du dossier candidat après les migrations officielles et avant la fixture `9001` ; **le numéro 9001 reste après 0022** (tri par identifiant : `defineMigrationSet` trie, l. 12) |
| Banc D1 | `tests/lot0c/d1Env.ts` appelle `migrationSets(true)` : à étendre de la même manière |
| Garde de non-fuite | test dédié : aucun module de `src/` n'importe `migrations_candidates`, `composeCore` charge le seul dossier officiel, les outils de flotte (`fleet/inventory.ts`) et `demoMigrate.ts` ignorent le dossier candidat |
| Parité de schéma | `schema_parite.test.ts` reste à 22 identifiants **sans** candidat ; un test de parité distinct couvre « officiel + candidat » (23) |

### 4.4 Couplage code et schéma (point de vigilance V-4, nouveau)

**FAIT VÉRIFIÉ** : le code de `src/` s'exécute contre les migrations du **dossier officiel**. Tant que 0022 est candidate, un code qui lit `bucket` échouerait (« no such column ») avec la composition de production. **Conséquences** : (a) pendant R4-R5, le code n'est exécuté que par les tests, qui chargent le candidat ; (b) **aucune version livrable ne doit être construite** à partir du dépôt entre R4 et G6 ; la DEMO s'exécute depuis une copie de version distincte (§3.2) et n'est pas concernée ; (c) la **promotion (G6) et la livraison du code** forment un seul ensemble ; (d) un test de couplage vérifie que la composition de production (dossier officiel seul) **refuse** de démarrer avec le nouveau code, plutôt que d'échouer plus tard ; ce test est écrit en R3 ; (e) une alternative (détection du schéma au démarrage, double chemin de code) est écartée : double surface de test, risque de lecture par oubli. **Arbitrage technique proposé au PO : A-T1** (rapport P82).

---

## 5. Adaptation des données existantes

| Sujet | Règle |
|---|---|
| Lignes existantes de `stock_movements` | aucune réécriture ; `bucket` = DISPONIBLE par défaut ; `transfer_id` et `reason_code` nuls (INV-9, V2-R9) |
| Lots existants | aucun événement de statut = ACTIF ; lots périmés restent en DISPONIBLE et non vendables par leur date ; **passage en PERTE jamais automatique** (plan P81 §4.3) |
| Produits sans suivi par lot (`lot_id` nul) | ils portent leur état comme les autres (états sur `lot_id` nul) ; aucun statut de lot |
| Médicaments existants sans suivi par lot | signalés « à régulariser » (DP2-R4) par une **requête de lecture** ; aucune conversion ; aucune écriture |
| Réservations existantes | immuables, inchangées ; le calcul « non honorable » est dérivé |
| Entrées manuelles historiques (`stock.manual`, sans pièce) | conservées ; non refusées rétroactivement ; comptées comme « reçu » côté CAT-11 (spécifications §8.2) |
| Historique de migrations | une ligne 0022 avec somme de contrôle ; les sommes de 0001 à 0021 **inchangées** ; une migration promue est immuable (correction = 0023) |
| `_migrations` des bases de la flotte | non modifiées avant G7 ; la 0021 reste à examiner (O-9) |
| Jeu de test préalable | **fixture SQL figée à 0021** (écart E-e), produite en R1 **avant toute modification de code** : réceptions, ventes multi-lots, retours, corrections, contre-passations, réservations actives et expirées, lots périmés, produits non suivis, lots rappelés (données fictives) |

---

## 6. Risques liés aux migrations automatiques (FAIT VÉRIFIÉ, à ne pas perdre de vue)

| # | Risque | Constat | Parade |
|---|---|---|---|
| RM-1 | Un fichier posé dans `migrations/pharmacy/` est **appliqué automatiquement** (`tenants.ts` `migrateAll` l. 233, création d'officine, `orchestrator.ts`, `demoMigrate.ts` manuel) | relu en P81 §1 | fichier candidat hors dossier officiel (DP6-R1) ; test de non-fuite (§4.3) ; **aucune écriture** dans `migrations/pharmacy/` avant G6 |
| RM-2 | Une base dont l'historique est « en avance » est refusée (`migration.unknown_applied`) | `verifyHistory` l. 53-60 ; `fleet/inventory.ts` l. 64 | la 0022 n'est jamais appliquée à une base réelle avant G7 ; les essais utilisent des bases jetables |
| RM-3 | Deux tests simulent une ligne « 0022 » inconnue (position 22) | `tests/p33/fleet_inventory.test.ts` l. 95 ; `tests/p34/release_unverified.test.ts` l. 52 | adaptés **à la promotion**, numéro libre ; en phase candidate ils restent verts (le dossier officiel est inchangé) |
| RM-4 | Somme de contrôle d'une migration promue figée | `verifyHistory` l. 58 | candidat relu et validé avant G6 ; toute erreur ultérieure = 0023 |
| RM-5 | `ALTER TABLE ADD COLUMN … NOT NULL DEFAULT … CHECK` sur table `STRICT` avec lignes, **en D1 réel** | NON VÉRIFIÉ (O-7) ; le banc D1 local utilise le moteur D1 de Miniflare (même famille, pas la production) | mesure au premier essai ; arrêt si divergence entre SQLite, D1 local |
| RM-6 | Verrouillage d'écriture pendant l'`ALTER` et coût des déclencheurs sur gros volume | volumétrie réelle inconnue (O-8) | mesure sur jeu synthétique volumineux en R2 (aucune donnée réelle) |
| RM-7 | `0021` pas appliquée à la DEMO : la 0022 s'appliquerait derrière elle | O-9 OUVERT | hors plan ; condition de G7 |
| RM-8 | Reconstruction impossible de `stock_movements` | le candidat n'en a pas besoin (aucun nouveau type, motifs en table) | principe additif conservé |
| RM-9 | Retour arrière du **code** après le premier passage d'état | plan P81 §8.1 : interdit sans restauration | aucun retour de code après usage des états ; restauration de sauvegarde si l'application réelle a lieu (G7) |
| RM-10 | Couplage code et schéma avant promotion | §4.4 | pas de version livrable entre R4 et G6 ; promotion et code ensemble |

---

## 7. Séquencement optimal : lots de réalisation R0 à R7

Principe : **séparer ce qui ne change pas le comportement** (calcul partagé, FEFO unique, fixture) **de ce qui le change** (états, réservations par ancienneté, motifs), pour isoler toute régression. Chaque lot s'achève sur une porte de contrôle ; une porte non franchie arrête la suite (§10.2).

| Lot | Contenu | Fichiers principaux | Porte de sortie |
|---|---|---|---|
| **R0** | Addendum daté du registre avec les réponses Q-1 à Q-12 ; autorisation de réaliser ; sommes de contrôle de référence (documents, `pharmos/src`, `pharmos/migrations`) ; relevé d'état (Git, disque, mémoire, DEMO) | `docs/` seulement | addendum signé du PO ; relevé enregistré |
| **R1** | **Fixture SQL figée à 0021** et son contrôle ; **relevé confirmé des 20 lectures** `SUM(delta)` (plan P81 §3.5) classées physique/disponible ; liste exhaustive des tests à modifier | `tests/fixtures/` (nouveau), relevé dans le rapport | fixture rejouable sur copie jetable ; classement des 20 lectures accepté |
| **R2** | **Candidat 0022** hors dossier officiel ; chargement par les tests (§4.3) ; **premier essai sur copie jetable** (contrôles A1-A8 puis B1-B13 du plan P81 §5) ; levée de O-7 (moteur) et O-8 (volumétrie synthétique) ; rejouabilité | `migrations_candidates/`, `tests/support/env.ts`, `tests/lot0c/d1Env.ts` | tous contrôles B vrais, sur **SQLite fichiers, mémoire et D1 local** ; aucune ligne perdue ni modifiée |
| **R3** | **Tests rouges** : ST-01, ST-04, CAT-10, CAT-11, V1-S1 à S7, V2-T1 à T8, étanchéité (INV-10), couplage (§4.4) | `tests/p82/` (nouveau) | tests écrits, **échouent pour la bonne raison** (fonction absente), aucun test existant touché |
| **R4a** | **Calcul partagé** disponible/vendable/par état et **FEFO unique** (remplace `fefoFor`, `issuePlan` et `fefo`) **sans changer le comportement** (tout reste DISPONIBLE) ; correction de nommage (R-A1) | `src/stock/levels.ts` (nouveau), `src/stock/service.ts`, `src/sales/plan.ts` | non-régression complète ciblée (§9.5) : **0 régression** |
| **R4b** | Passages d'état, statut de lot, droits, audits, routes, levée par gouvernance | `src/stock/transfer.ts`, `lotStatus.ts`, `src/governance/catalog.ts`, `src/entry/stockRoutes.ts` | ST-01, ST-04, CAT-10 verts ; V1-S3, S4, S7 verts |
| **R4c** | Sorties manuelles : motifs normalisés, réservations bloquantes, dérogation ADMIN, écart FEFO tracé ; réservations par ancienneté (si Q-3) et indicateur « non honorable » | `src/stock/service.ts`, `src/sales/plan.ts`, `src/sales/service.ts` | V1-S1, S2, S5, S6 verts |
| **R4d** | Corrections par état, correction positive en DISPONIBLE, contre-passation limitée au DISPONIBLE, régularisation hors commande (justificatif obligatoire, droit distinct) | `src/stock/service.ts`, `src/purchasing/service.ts` | V2-T1 à T8 verts ; CAT-11 vert |
| **R4e** | Catalogue : suivi par lot obligatoire pour MÉDICAMENT ; signalement « à régulariser » ; message d'archivage par état ; alertes de péremption par état ; indicateur « en attente de destruction » ; lectures disponible (seuils, propositions de commande) | `src/catalog/service.ts`, `src/purchasing/service.ts`, `src/stock/service.ts` | DP2, C1, C2, DP1-R11 verts |
| **R5** | **Non-régression ciblée** (§9.5), un fichier à la fois ; adaptation justifiée des tests listés (§3.3) ; contrôles d'intégrité complets sur copie après scénarios | `tests/` | **0 régression** ; chaque modification de test listée et justifiée |
| **R6** | Rapport de réalisation avec commandes et résultats ; sommes de contrôle finales ; confirmation d'absence d'action interdite | `docs/` | rapport complet |
| **R7** (option) | Interface : états, passages, statut de lot, motifs, indicateurs | `ui/public/on-premise/js/stock.js`, `ui/saas-src/src/pages/Stock.jsx`, `stockApi.js` | **mission distincte** ; hors périmètre de R0-R6 |
| **G6** | Promotion de 0022 dans `migrations/pharmacy/` + adaptation de `schema_parite` (22 → 23) et des deux tests « 0022 inconnue » | — | **décision distincte du PO** (registre DP6-R7) |
| **G7** | Application à la DEMO ou à une base réelle (sauvegarde vérifiée, service arrêté, 0021 d'abord : O-9) | — | **décision distincte du PO** (registre DP6-R7) |

**Pourquoi cet ordre.** (1) R1 avant tout code : la fixture doit refléter le comportement actuel (plan P81 §4.6). (2) R2 avant R3/R4 : le schéma conditionne tout et ses inconnues techniques (O-7, O-8) peuvent faire changer la conception. (3) R3 avant R4 : les tests rouges définissent « terminé ». (4) R4a sans changement de comportement : isole les 20 lectures et l'unification des deux FEFO de tout effet métier. (5) R4b avant R4c : les états existent avant que les sorties et les réservations s'y réfèrent. (6) R4d après R4c : corrections et contre-passations s'appuient sur les gardes par état et les motifs. (7) Catalogue en R4e : il touche la création de produits, donc la plus grande surface de tests existants.

---

## 8. Points de contrôle par lot (ce qui est vérifié avant de passer au lot suivant)

| Lot | Contrôles obligatoires avant la porte |
|---|---|
| R0 | Addendum présent ; `git status` : aucun fichier de code modifié ; sommes de contrôle de `pharmos/src` et `pharmos/migrations` relevées ; 0 tag, 0 stash ; DEMO non touchée |
| R1 | La fixture se rejoue deux fois avec le même résultat ; empreinte de la fixture consignée ; aucun code modifié depuis R0 (sommes de contrôle identiques) |
| R2 | Contrôles A1-A8 avant, B1-B13 et B14-B22 après (§10.1) ; **dossier `migrations/pharmacy/` identique à R0** (somme de contrôle) ; dossier candidat non lu par la composition de production |
| R3 | Chaque test rouge échoue pour une raison attendue (symbole, colonne ou garde absent), pas pour un défaut du test ; liste des tests mémorisée |
| R4a | Exécution de toute la non-régression ciblée (§9.5) : résultats identiques à la ligne de base R0 (mêmes nombres de tests réussis) |
| R4b à R4e | Les tests verts du lot sont exécutés **avec** la non-régression du domaine touché ; aucun test existant modifié sans entrée au rapport |
| R5 | Toute la non-régression ciblée, un fichier à la fois ; contrôles d'intégrité complets sur copie après les scénarios de référence |
| R6 | Rapport complet ; sommes de contrôle finales ; `git diff --stat -- pharmos/migrations/pharmacy` vide ; DEMO inchangée (PID, `/health`) |

**Ligne de base à relever en R0** (avant tout changement) : nombre de tests réussis par fichier de la non-régression (§9.5), pour comparer sans ambiguïté en R4a et R5. En P80 : `tests/ui3/stock_d1.test.ts` 22/22 en 373 s ; `tests/p80/stock_regles_p80.test.ts` 8/8 (relevés historiques, à rejouer en R0 sur l'état de départ, pas à supposer).

---

## 9. Plan de tests

### 9.1 Principe
Les cas ST-01, ST-04, CAT-10 et CAT-11 sont ceux du plan P81 §6.2 (ST-01-a à e, ST-04-a à d, CAT-10-a à d, CAT-11-a à d), **adaptés** par les décisions du registre (reformulation de CAT-10, identité de CAT-11 : DP1-R9, DP3-R2) et **complétés** par les cas V-1 et V-2 ci-dessous. Chaque identifiant figure dans le nom du test pour être retrouvé par recherche. **Aucun test n'est écrit ni exécuté en P82** (C4 : scénarios préparés avant le code, exécutés exclusivement sur copies fictives autorisées).

### 9.2 Cas ajoutés pour V-1 (spécifications §4.4)
V1-S1 à V1-S7 (réservation et passage, ancienneté, rappel, concurrence, sortie manuelle sur quantité réservée, sortie hors DISPONIBLE, retour à l'honorable) — repris tels quels du document des spécifications.

### 9.3 Cas ajoutés pour V-2

| Id | Cas | Résultat attendu |
|---|---|---|
| V2-T1 | Lot de 10 : 6 DISPONIBLE, 4 EN_CONTROLE ; correction négative de 2 en DISPONIBLE avec quantité attendue 6 | acceptée ; DISPONIBLE = 4 ; EN_CONTROLE = 4 inchangé ; la garde « attendue » porte sur l'état visé (attendue 10 refusée : `stock.level_changed`) |
| V2-T2 | Correction négative en EN_CONTROLE par un collaborateur sans le droit restreint, puis avec | refusée sans le droit ; acceptée avec ; marquée « droit restreint » à l'audit |
| V2-T3 | Correction négative en PERTE (code et insertion directe dans la base) | refusée par le code **et** par la base (INV-6) ; aucune ligne écrite |
| V2-T4 | Correction avec motif CASSE, PEREMPTION, RAPPEL ou USAGE_INTERNE | refusée ; ces actes passent par sortie manuelle ou passage en PERTE |
| V2-T5 | Correction positive : état visé DISPONIBLE accepté ; insertion directe d'un mouvement positif en QUARANTAINE sans passage | la première acceptée ; la seconde refusée par la base (INV-4) |
| V2-T6 | Réception de 10 ; 4 passent en QUARANTAINE ; contre-passation de 7 puis de 6 ; annulation intégrale | 7 refusée (`purchase.reversal_state`, détail par état) ; 6 acceptée en DISPONIBLE ; annulation intégrale refusée tant qu'une part est hors DISPONIBLE |
| V2-T7 | Contre-passation avec réservation active sur le produit | acceptée (jamais bloquée par une réservation) ; réservation devenue « non honorable » tracée à l'audit |
| V2-T8 | Rapprochement : après T1 à T7, identité CAT-11 par lot, aucune modification de ligne existante, audit complet (état visé, attendu, compté, delta, motif, auteur, droit) | identité exacte ; INV-1 vérifié ; chaque opération a son entrée d'audit chaînée |
| V2-T9 | Quantité en PERTE : toute tentative de sortie, correction, contre-passation | refus nommé, renvoi au lot ultérieur ; indicateur C2 inchangé |
| V2-T10 | Indicateur « en attente de destruction » et alertes de péremption sur quantités EN_CONTROLE, QUARANTAINE, PERTE | quantité et ancienneté exactes ; alertes présentes pour les trois états (DP1-R11) |

### 9.4 Cas complémentaires (DP2, DP5, C1)

| Id | Cas | Résultat attendu |
|---|---|---|
| DP2-T1 | Création et adoption d'un MÉDICAMENT sans suivi par lot | refusées ; avec suivi : acceptées ; dispositif médical et autres types : facultatif |
| DP2-T2 | Médicament existant sans suivi par lot | signalé « à régulariser » ; aucun mouvement ni conversion |
| DP5-T1 | Les quatre entrées autorisées et leurs refus | chacune écrit en DISPONIBLE ; toute autre augmentation refusée (régularisation sans justificatif refusée) |
| DP5-T2 | Commande, sélection catalogue, adoption, réservation, passage d'état | **aucune** augmentation du physique (reprise des garde-fous P45, P54, P66, P80 + passage d'état) |
| C1-T1 | Archivage d'un produit avec quantité en QUARANTAINE ou PERTE | refusé ; message détaillant la répartition par état |
| MIG-T1 | Non-fuite du candidat et couplage code/schéma (§4.3 et §4.4) | composition de production sans candidat : refus net ; aucun accès au dossier candidat par `src/` |
| MIG-T2 | Parité SQLite fichiers, mémoire, D1 local sur « officiel + candidat » | schémas identiques |

### 9.5 Non-régression ciblée (un fichier par commande, jamais la suite complète)

| Domaine | Fichiers | Remarque |
|---|---|---|
| Stock (FEFO, sortie multi-lots atomique, stock négatif, concurrence, droits) | `tests/ui3/stock_sqlite.test.ts`, `tests/ui3/stock_d1.test.ts` (22 scénarios, capacité 32, durée de l'ordre de 6 min), `tests/ui3/stock_http.test.ts`, `tests/ui3/dep01_stock.test.ts`, `tests/p80/stock_regles_p80.test.ts`, `tests/p30/stock_p30_droits_couts.test.ts`, `tests/p27/stock_fiche_adoptee_maj.test.ts`, `tests/p29/*` | D1 : un fichier à la fois (mémoire, §12) |
| Ventes, réservations, retours, annulations | `tests/p43/ventes_p43.test.ts`, `ventes_http.test.ts`, `tva_p432.test.ts`, `dep01_sales.test.ts` | le déclencheur de réservation est remplacé |
| Commandes, réceptions, contre-passations | `tests/p31/*`, `tests/p32/receive_idempotence.test.ts`, `tests/p44/achats_p44.test.ts`, `tests/p45/*`, `tests/p54/*` (dont `migration_0021.test.ts`), `tests/p66/*` (mémoire/fichiers et D1) | preuves « commande ≠ stock » |
| Catalogue, adoption, fournisseurs | `tests/ui4/*`, `tests/p19/*`, `tests/p20/*`, `tests/p21/reference_http.test.ts` | impact DP2-R1 (§3.3) |
| Ordonnances | `tests/p49/*` | lit le stock |
| Gouvernance, droits, interface collaborateurs | `tests/lot1b/*`, `tests/ui5/collab_http.test.ts` (compteur 50 → 55) | nouveaux droits |
| Flotte, parité, publication | `tests/lot1b/schema_parite.test.ts`, `tests/p33/*`, `tests/p34/*`, `tests/demo/migration_0007.test.ts` | inchangés en phase candidate ; adaptés à G6 |
| Bancs | `tests/lot0c/*` | si `d1Env.ts` est modifié |

Critère : **0 régression par rapport à la ligne de base R0** ; toute modification de test est listée, justifiée et revue au rapport. Les tests d'`acceptance` complets et les `ci_*.sh` (qui enchaînent des suites entières) ne sont **pas** lancés (DP-6 condition 6).

### 9.6 Bancs et capacité
Chaque cas existe sur trois bancs : mémoire, fichiers, D1 local. Capacité D1 : `makeD1Env(capacity)` ; une officine D1 par scénario, jamais réutilisée (`tenant.capacity_exceeded`, `d1Provider.ts` l. 66). **Règle de dimensionnement** : capacité du fichier = nombre d'officines créées + 5 de marge (observation P80). Les nouveaux fichiers D1 de `tests/p82/` sont découpés pour rester ≤ 32 officines chacun.

---

## 10. Contrôles d'intégrité, conditions d'arrêt et retour arrière

### 10.1 Contrôles

Les contrôles **A1-A8 (avant)** et **B1-B13 (après)** sont ceux du plan P81 §5, repris sans changement. P82 ajoute, après migration **et** après chaque scénario de passage d'état :

| # | Contrôle | Attendu |
|---|---|---|
| B14 | Chaque en-tête `stock_state_transfers` a exactement deux mouvements `CORRECTION` de source `stock.transfer`, de somme nulle, dans les états annoncés | 100 % ; 0 orphelin |
| B15 | Aucun niveau négatif par produit/lot/**état** | 0 ligne |
| B16 | Aucun mouvement négatif en état PERTE | 0 ligne |
| B17 | Σ des états = ancien `SUM(delta)` par produit et par lot (INV-3) | égalité exacte |
| B18 | Identité CAT-11 par lot et par produit sans lot (spécifications §8.2) | égalité exacte, côté « reçu » calculé depuis `purchase_receipt_lines` |
| B19 | Aucun mouvement positif hors DISPONIBLE sans `transfer_id` | 0 ligne |
| B20 | Réservations : nombre et empreinte inchangés par les passages (immuabilité) | identiques |
| B21 | `migrations/pharmacy/` : somme de contrôle identique à R0 ; 0001 à 0021 : sommes `_migrations` identiques | identique |
| B22 | La composition de production ne lit pas le dossier candidat ; 0 base réelle ouverte | vrai |

### 10.2 Conditions d'arrêt (une seule suffit) — reprise de plan P81 §8.2 + P82
1. Un contrôle A, B1-B22 hors tolérance (égalité exacte). 2. Une ligne perdue, modifiée ou ajoutée dans une table existante par la migration. 3. Une somme de contrôle de migration déjà appliquée qui diverge. 4. Un niveau négatif (produit, lot ou état). 5. Une chaîne d'audit rompue. 6. Une régression sur la non-régression par rapport à la ligne de base R0. 7. Toute lecture ou écriture visant la DEMO, un répertoire réel, la copie de version de la DEMO ou un fichier de codes. 8. Disque libre ou mémoire disponible sous les seuils (§12), ou durée anormale (suspicion d'`OOM`). 9. Une réponse du PO manquante ou ambiguë qui change le résultat attendu d'un test. 10. Écriture dans `migrations/pharmacy/` avant G6.
**En cas d'arrêt** : consigner l'état exact, **ne rien réparer**, informer le PO, ne pas poursuivre (DP6-R5).

### 10.3 Stratégie de retour arrière (DP6-R6)

| Situation | Retour arrière |
|---|---|
| Essai sur copie jetable (R2 à R5) | **abandonner la copie** ; relancer sur une copie neuve après correction du candidat (non officiel) ; aucun fichier supprimé sans décision séparée du PO |
| Candidat refusé à l'examen | ne pas promouvoir ; rien n'est déployé ; le dossier candidat reste en place |
| Code modifié (R4) | le code reste sous contrôle de version ; **aucune opération Git manuelle** : la sauvegarde automatique conserve l'historique ; un abandon se décide par le PO |
| Après G6 | toute erreur de schéma = migration 0023 ; la 0022 n'est jamais éditée |
| Après G7 (hors plan) | sauvegarde vérifiée avant, service arrêté, outil refusant si un serveur tient l'environnement, **restauration** en cas d'échec de contrôle, pas de réparation sur place |
| Retour du code après usage des états | **interdit sans restauration** (plan P81 §8.1) |

---

## 11. Les huit conditions de DP-6 (registre §7) — application dans ce plan

| N° | Condition | Application | Contrôle |
|---|---|---|---|
| 1 | DP-1 à DP-5 décidés par écrit | Remplie par le registre ; **la réalisation exige en plus G0-1 et G0-2** | R0 |
| 2 | Essais uniquement sur bases temporaires fictives créées par les tests | tous les essais passent par `makeEnv`, `openEnvAt`, `makeD1Env` (répertoires temporaires) ; fixture fictive ; jamais DEMO, jamais fichiers de codes | B22 ; arrêt 7 |
| 3 | Fichier hors dossier officiel ; promotion = décision distincte | `migrations_candidates/pharmacy/` ; promotion = G6 | B21 ; arrêt 10 |
| 4 | Aucune application à la DEMO ni à une base réelle ; accord préalable sur 0021 | aucune étape de R0-R7 ne l'applique ; G7 distinct ; O-9 OUVERT | B22 |
| 5 | Contrôles d'intégrité avant et après ; arrêt immédiat | §10.1 (A, B1-B22) ; §10.2 | chaque essai |
| 6 | Ressources : un fichier de test à la fois, disque et mémoire vérifiés avant chaque essai, aucune suite complète | §12 | avant chaque lancement |
| 7 | Retour arrière prévu avant l'essai ; copie défaillante abandonnée ; aucun retour de code après usage des états sans restauration | §10.3 | revue avant R2 |
| 8 | Aucun commit, push ou publication manuel ; compte rendu complet avec commandes et résultats | sauvegarde automatique seulement ; rapport R6 | `git status`, tags, stash relevés |

---

## 12. Ressources et modalités d'exécution (hôte 2 Go de RAM, 2 cœurs, disque à 97 %)

| Sujet | Règle proposée |
|---|---|
| Lancement | `ulimit -c 0 ; umask 077` ; commande longue : `setsid -f … > journal 2>&1 < /dev/null` ; aucune commande au premier plan au-delà d'environ 120 s ; **un seul fichier de test à la fois**, jamais deux en parallèle |
| Disque | relevé avant chaque lancement ; au dernier relevé de P82 : 1 173 032 Ko libres (97 %) ; **seuil d'arrêt proposé : 800 Mo libres** (HYPOTHÈSE, à confirmer par le PO) ; aucun nettoyage hors dossiers temporaires créés par les tests eux-mêmes |
| Mémoire | relevé avant chaque lancement ; **OBSERVATION** : au dernier relevé de P82, mémoire disponible ≈ 650 Mo (DEMO et autres processus résidents), alors que le test D1 du stock a été observé à environ 1,45 Go de RAM en P80. **RISQUE RR-1** : le lancement des fichiers D1 pourrait ne pas être possible dans l'état actuel de l'hôte ; **aucune action** (pas d'arrêt de la DEMO, pas de réparation) n'est prévue par ce plan ; si la mémoire disponible est insuffisante au moment du lot R2/R5, la mission **s'arrête** et le PO arbitre (arbitrage A-T2) |
| Cœurs | un seul processus de test à la fois |
| Données | exclusivement fictives, créées par les tests ; aucune lecture de `.pharmos-private` ni de fichiers de codes |
| Core dump | `pharmos/core` (750 096 384 o) jamais touché ; `ulimit -c 0` partout |
| Capacité D1 | §9.6 |
| Journaux | enregistrés hors dépôt (répertoire de travail dédié) ; résultats et commandes copiés au rapport R6 |

---

## 13. Ce que ce plan n'autorise pas

Aucun code, aucune migration (ni candidate ni officielle), aucune base, aucun test, aucune intervention sur la DEMO ou systemd, aucun déploiement, aucune promotion (G6), aucune application (G7), aucune opération Git manuelle, aucun nettoyage ou réparation. Il ne tranche ni O-5, ni O-6, ni O-9, ni O-10, ni les règles réglementaires de retour. Le lot R7 (interface) et le lot ultérieur (sorties de QUARANTAINE, destruction de PERTE) sont hors périmètre.
