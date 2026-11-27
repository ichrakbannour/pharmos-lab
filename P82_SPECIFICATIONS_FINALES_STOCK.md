# P82 — Spécifications finales du module STOCK (règles consolidées, résolution de V-1 et V-2)

**Statut : PROPOSITIONS soumises au PO.** Ce document ne modifie aucune décision du registre officiel `docs/P81_REGISTRE_DECISIONS_PO_STOCK.md` (« le registre », cité `DPn-Rn`, `Cn`, `O-n`). Les règles nouvelles (`V1-Rn`, `V2-Rn`, `S-n`, `INV-n`) n'ont **valeur de décision qu'après réponse écrite du PO** (fiche §1) et consignation dans un addendum daté du registre (registre §12).
**Il n'autorise ni développement, ni migration, ni test, ni déploiement.** Réalisation = autorisation distincte (voir `docs/P82_PLAN_REALISATION_STOCK_0022.md`). Données fictives uniquement. Aucun secret, aucun code.
Libellés : **DÉCIDÉ** (registre) · **FAIT VÉRIFIÉ** (relu dans le dépôt le 2026-11-25) · **OBSERVATION** · **HYPOTHÈSE** · **PROPOSITION** · **NON VÉRIFIÉ** · **OUVERT** · **REPORTÉ**.
Références de code : fichiers sous `pharmos/`, numéros de ligne relevés le 2026-11-25 (indicatifs, à relire à la réalisation).

---

## 1. Fiche de décision PO — les derniers arbitrages (une seule revue)

Chaque question a une **recommandation**. Répondre « je valide les recommandations Q-1 à Q-12 » suffit ; toute réponse différente est à préciser. **Q-3, Q-5, Q-6, Q-9, Q-10, Q-11, Q-12** sont de vraies décisions nouvelles ; **Q-1, Q-2, Q-4, Q-7, Q-8** précisent ou confirment une lecture déduite du registre.

| Réf. | Question | Recommandation | Règle |
|---|---|---|---|
| **Q-1** | **Délimitation DP4-R4 / DP1-R7 / C3 (V-1).** Les sorties manuelles (retrait, destruction d'un périmé) ne partent que de l'état DISPONIBLE. Aucune sortie directe depuis EN_CONTROLE, QUARANTAINE ou PERTE avant le lot ultérieur (une quantité EN_CONTROLE repasse d'abord en DISPONIBLE). | **Confirmer** | V1-R9, INV-5 |
| **Q-2** | **Lot BLOQUE, RAPPELE ou périmé.** Une sortie manuelle depuis DISPONIBLE n'est possible qu'avec un motif de retrait (RAPPEL, PEREMPTION, CASSE) ; refusée pour USAGE_INTERNE et AUTRE. | **Oui** | V1-R10 |
| **Q-3** | **Réservation devenue impossible à honorer.** Priorité **par ancienneté** : les réservations les plus anciennes sont honorées, les plus récentes sont signalées ; la validation d'une réservation ne déduit que les réservations **plus anciennes** qu'elle. Sans cela, plusieurs réservations peuvent se bloquer mutuellement jusqu'à expiration (§4.4). | **Par ancienneté** | V1-R4, V1-R5 |
| **Q-4** | **Notification.** Signalement **dans l'application** (liste des réservations, alertes du pharmacien) avec trace d'audit persistante ; **aucun envoi externe** : il n'existe aucune infrastructure de notification dans le code (recherche négative). | **Interne seulement** | V1-R6 |
| **Q-5** | **Levée de QUARANTAINE (DP1-R3).** Droit de **gouvernance** réservé à l'ADMIN (= pharmacien titulaire), non délégable. Conséquence à accepter : les droits de gouvernance existants exigent une authentification forte (commentaire `governance/catalog.ts` l. 36). Alternative : droit métier non délégable à habilitation « pharmacien », qui ouvre aussi aux adjoints habilités. | **ADMIN seul** (lecture littérale de DP1-R3) | V1-R7 |
| **Q-6** | **Levée d'un blocage de lot** (BLOQUE ou RAPPELE vers ACTIF) : lacune du registre. Même autorité que Q-5 ; le blocage lui-même reste un droit métier distinct. | **Même autorité que Q-5** | V1-R7 |
| **Q-7** | **Corrections et contre-passations face aux réservations.** Elles enregistrent la réalité physique : jamais bloquées par une réservation (DP4-R1 ne vise que les sorties manuelles) ; elles déclenchent le signalement de V1-R6. | **Confirmer** | V2-R7 |
| **Q-8** | **Toute entrée naît en DISPONIBLE**, y compris une correction positive. Un excédent trouvé en quarantaine s'enregistre en deux temps tracés : correction positive en DISPONIBLE, puis passage d'état. | **Oui** | V2-R4, INV-4 |
| **Q-9** | **Corrections négatives par état.** DISPONIBLE : droit `stock.adjust`. EN_CONTROLE, QUARANTAINE, lot périmé/bloqué/rappelé, ou mise à zéro d'un lot entier : **droit restreint** (habilitation pharmacien, non délégable) et marquage dans les rapports. **PERTE : jamais.** Motifs de correction en liste fermée **sans** motif de destruction. | **Oui** | V2-R3, V2-R5 |
| **Q-10** | **Contre-passation de réception.** Elle ne reprend que la quantité **actuellement en DISPONIBLE** du lot. L'annulation intégrale est refusée si une part est hors DISPONIBLE (même garde). Une quantité en QUARANTAINE ou PERTE attend le lot ultérieur. | **Oui** | V2-R6 |
| **Q-11** | **Passage en PERTE par erreur.** Conséquence directe de DP1-R4 : **non annulable**. Le PO accepte-t-il cette conséquence, avec comme garde-fous une confirmation explicite et un droit de passage non accordé par défaut ? (Toute annulation exigerait de modifier DP1-R4.) | **Accepter** | V2-R8 |
| **Q-12** | **O-11 : forme technique des motifs normalisés.** Table de référence (ajout d'un motif sans reconstruire `stock_movements`) plutôt qu'une liste figée par `CHECK` (impossible à modifier sans reconstruire la table). | **Table de référence** | §8.3 |

**Interprétations par défaut** (non bloquantes ; le PO peut s'y opposer) :
- **H-1** : « Réception physique (commande ou livraison) » (DP5-R1) = réception rattachée à une commande fournisseur ; une livraison sans commande relève de la régularisation (DP5-R2).
- **H-2** : « Correction positive justifiée et contrôlée » (DP5-R3) = motif obligatoire + garde-fou de quantité attendue (existant) + signalement dans le rapport d'écarts ; aucun second contrôleur.
- **H-3** : « Ancienneté » de l'indicateur C2 = date du plus ancien passage en PERTE d'un lot (valable tant qu'aucune sortie de PERTE n'existe).
- **H-4** : aucun seuil chiffré d'alerte sur les corrections négatives (REPORTÉ) ; le rapport affiche les cumuls par lot et par motif.
- **H-5** : DP4-R6 (« QUARANTAINE ou PERTE ») est lu comme couvrant aussi le passage en EN_CONTROLE et le blocage/rappel de lot : même effet de sécurité sur le vendable.
- **H-6** : la réintégration EN_CONTROLE vers DISPONIBLE relève du droit de passage ordinaire (DP1-R3 ne vise que la levée de QUARANTAINE).

**Toujours OUVERTS, sans effet sur le plan** : O-5 (réception « à contrôler »), O-6 (retour client en contrôle, REPORTÉ), O-10 (prérequis Lot 2). **O-9** (0021 en DEMO) ne concerne que la décision future d'appliquer la 0022 à la DEMO.

---

## 2. Vocabulaire

| Terme (fiche P81) | Définition | Équivalent actuel dans le code |
|---|---|---|
| **Physique** | Σ de tous les mouvements du produit (ou du lot), tous états | `physical` (`sales/plan.ts` l. 137) |
| **Disponible** | Quantité à l'état DISPONIBLE d'un lot non bloqué, non rappelé, non périmé (produit non suivi : sans condition de lot), **avant** réservations | `sellable` (`plan.ts` l. 150) |
| **Vendable** | Disponible moins les réservations actives des **autres** paniers | `available` (`plan.ts` l. 152) |
| **Non disponible** | EN_CONTROLE + QUARANTAINE + PERTE | n'existe pas |
| **Réservation active** | Non expirée, sans événement terminal (RELEASED, EXPIRED, CONSUMED) | `reservedOthers` (`plan.ts` l. 115) |

**OBSERVATION (FAIT VÉRIFIÉ)** : dans `sales/plan.ts`, `sellable` désigne le **disponible avant réservations** et `available` le **vendable après réservations**, soit l'inverse du vocabulaire P81. Le code et les tests futurs suivent le vocabulaire P81 ; un commentaire ou renommage explicite est prévu au plan (tâche R-A1).

---

## 3. Spécifications finales des quatre états *(DP1, C1, C2)*

### 3.1 Les états

| État | Sens | Vendu / alloué FEFO | Dans le physique | Alertes de péremption (DP1-R11) |
|---|---|---|---|---|
| **DISPONIBLE** | Stock normal | Oui (lot non bloqué, non périmé) | Oui | Oui |
| **EN_CONTROLE** | Détenu, en attente de vérification | **Jamais** | Oui | Oui |
| **QUARANTAINE** | Détenu, isolé (suspicion, rappel) | **Jamais** | Oui | Oui |
| **PERTE** | Détenu, perdu, **en attente de destruction** | **Jamais** | Oui | Oui |

Les états s'appliquent aussi aux produits **non suivis par lot** (`lot_id` nul) : la quantité d'un produit sans lot est répartie par état de la même façon ; seul le statut de lot ne les concerne pas.

### 3.2 Opérations autorisées par état (synthèse ; chaque cellule renvoie à une décision ou une règle)

| Opération | DISPONIBLE | EN_CONTROLE | QUARANTAINE | PERTE |
|---|---|---|---|---|
| Entrée (réception, régularisation, retour revendable, correction positive) | **seul état d'entrée** (DP5, INV-4) | non | non | non |
| Vente / allocation FEFO | oui | non | non | non |
| Sortie manuelle | oui (réservations bloquantes, DP4-R1) | non (Q-1) | non (DP1-R7) | non (DP1-R7) |
| Passage vers un autre état | selon matrice (registre §2) | selon matrice | selon matrice | **non** (DP1-R4) |
| Correction négative d'inventaire | oui | oui, droit restreint (Q-9) | oui, droit restreint (Q-9) | **non** (INV-6) |
| Contre-passation de réception | oui, dans la limite du DISPONIBLE (Q-10) | non | non (lot ultérieur) | non (lot ultérieur) |

### 3.3 Passages d'état

Matrice : celle du registre §2 (DISPONIBLE vers EN_CONTROLE, QUARANTAINE, PERTE ; EN_CONTROLE vers DISPONIBLE, QUARANTAINE, PERTE ; QUARANTAINE vers DISPONIBLE et PERTE ; PERTE vers rien).

| Règle | Contenu | Réf. |
|---|---|---|
| S-1 | Un passage porte **quantité, motif, auteur, date**, est atomique (deux mouvements ou aucun) et ne change pas le physique. | DP1-R2 |
| S-2 | La quantité passée ne peut pas dépasser la quantité présente dans l'état de départ (aucun négatif par état). | INV-2 |
| S-3 | Un passage n'est **jamais refusé à cause d'une réservation**. | V1-R1 |
| S-4 | Confirmation explicite et motif de 3 à 300 caractères (convention des commandes de stock existantes, `stock/service.ts` l. 825). | FAIT VÉRIFIÉ |
| S-5 | Droits : passage = `stock.state.transfer` (métier, jamais accordé par défaut, ADMIN automatique) ; levée de QUARANTAINE = droit de gouvernance (Q-5). | DP1-R3, H-6 |

### 3.4 Statut de lot (distinct de l'état de la quantité)

| Règle | Contenu |
|---|---|
| S-6 | Statuts : **ACTIF** (défaut = absence d'événement), **BLOQUE**, **RAPPELE**. PÉRIMÉ et ÉPUISÉ restent calculés. Statut courant = dernier événement d'une table append-only. |
| S-7 | **Rappel = blocage seul**, sans transfert automatique (DP1-R5). Le pharmacien décide ensuite quelles quantités passent en QUARANTAINE. |
| S-8 | Un lot BLOQUE ou RAPPELE n'est **jamais alloué ni vendu**, quel que soit l'état de ses quantités ; la base le refuse pour `sales.sale`. |
| S-9 | Blocage = droit `stock.lot.block` (métier, non accordé par défaut) ; levée = Q-6. Motif, auteur, date obligatoires. |

### 3.5 Archivage et destruction *(C1, C2, DP1-R7)*

| Règle | Contenu |
|---|---|
| S-10 | **C1** : archivage interdit tant que le **physique** (tous états) n'est pas nul. FAIT VÉRIFIÉ : la règle actuelle lit déjà le physique (`catalog/service.ts` l. 557 et 571) ; elle reste inchangée, seul le message détaille la répartition par état. |
| S-11 | **C2** : indicateur « **en attente de destruction** » = quantité en PERTE et ancienneté (H-3), par produit et par lot, calculé. **Aucune destruction automatique.** |
| S-12 | **Destruction depuis PERTE et sortie de QUARANTAINE : REPORTÉES** (DP1-R7). Aucun mouvement négatif en PERTE (INV-6) ni sortie de QUARANTAINE ; le lot ultérieur remplacera ces gardes. |
| S-13 | **Conséquence connue** : un produit gardant une quantité en QUARANTAINE ou PERTE reste non archivable jusqu'au lot ultérieur. |

---

## 4. Résolution de V-1 — QUARANTAINE / PERTE et réservations

**Rappel du registre.** DP3-R3 : si un rappel ou une mise en quarantaine rend une réservation impossible à honorer, la sécurité sanitaire prime et la réservation est signalée à son auteur. DP4-R6 : les passages en QUARANTAINE ou PERTE ne sont jamais empêchés par une réservation. DP1-R5 : un rappel bloque le lot, sans transfert automatique. DP1-R7 : sorties de QUARANTAINE et destruction de PERTE reportées. V-1 demandait de délimiter DP4-R4 (sortie d'un lot périmé « pour retrait ou destruction ») par rapport à DP1-R7 et C3.

### 4.1 Faits relevés dans le code (FAIT VÉRIFIÉ)

| Constat | Source |
|---|---|
| Une réservation est **logique**, **par produit** (`sale_reservation_lines (reservation_id, product_id, qty)`), immuable, sa fin de vie est un événement unique (RELEASED, EXPIRED, CONSUMED) ; durée 15 min par défaut. | `0016_ventes.sql` l. 107-161 |
| Le seul déclencheur qui lit les réservations (`stock_movements_sales_reserved`) est limité à `source = 'sales.sale'` : un mouvement d'une autre source (donc un passage d'état, une correction, une contre-passation) **n'est aujourd'hui jamais empêché** par une réservation. | `0016` l. 323 |
| À la réservation et à la vente, le plan calcule `sellable` (disponible) puis `available` (disponible moins les réservations des **autres** paniers) ; la sortie manuelle ignore aujourd'hui les réservations. | `sales/plan.ts` l. 132-153, 206 ; `sales/service.ts` l. 581-590 |
| `sale_reservation_events.kind` est borné par un `CHECK` (3 valeurs) : un nouveau type d'événement exigerait de reconstruire la table. | `0016` l. 149-155 |
| Aucun mécanisme de notification (e-mail, SMS, file de messages) n'existe dans `src/` ; les seuls « signalements » sont les listes, alertes et l'audit chaîné. | recherche négative sur `src/` |

### 4.2 Règles proposées (V1-Rn)

| Réf. | Règle | Rattachement |
|---|---|---|
| **V1-R1** | **Une réservation ne bloque jamais** : le passage en EN_CONTROLE, en QUARANTAINE ou en PERTE (H-5), ni le blocage ou rappel d'un lot. Ces opérations s'exécutent même si la quantité vendable tombe sous la quantité réservée. Garantie technique : le déclencheur de réservation reste limité aux sources `sales.sale` et (DP4-R1) `stock.manual` en sortie ; il ne s'applique à **aucune** source de passage (`stock.transfer`). Un test dédié l'impose (CAT-10-d, ST-V1-a). | DP3-R3, DP4-R6 |
| **V1-R2** | **Blocage immédiat de la délivrance.** Dès que le passage ou l'événement de statut est validé (même unité de travail atomique), la quantité concernée n'est plus allouable : le FEFO ne lit que l'état DISPONIBLE des lots non BLOQUE/RAPPELE/périmés, et la base refuse (a) toute sortie qui entamerait plus que le DISPONIBLE du lot (garde par état), (b) toute vente `sales.sale` sur un lot BLOQUE/RAPPELE. Une vente concurrente est donc soit validée avant, soit refusée atomiquement après ; jamais de vente sur une quantité déjà isolée. | DP1-R5, S-8 |
| **V1-R3** | **Une réservation n'est jamais modifiée ni annulée automatiquement.** Elle reste un fait immuable ; son éventuelle impossibilité est un **état calculé** (« non honorable »), jamais stocké. Elle s'éteint par les événements existants (consommation, libération, expiration). Aucune prolongation. | DP3-R3, immuabilité 0016 |
| **V1-R4** | **Calcul « non honorable ».** Pour un produit, les réservations actives sont classées **par ancienneté de création** (puis identifiant) ; chacune est « honorée » tant que le cumul des quantités réservées, jusqu'à elle comprise, reste inférieur ou égal au **disponible** du produit ; les suivantes sont « non honorables ». Une réservation non honorable porte le code `stock.reservation_unfulfillable` avec quantité disponible et quantité manquante. | Q-3 |
| **V1-R5** | **Validation d'une vente depuis une réservation.** La vente adossée à la réservation R est refusée si le disponible moins les réservations **plus anciennes que R** ne couvre pas sa quantité (au lieu de « toutes les autres réservations » aujourd'hui). Une vente sans réservation déduit toutes les réservations actives. Effet : deux réservations ne se bloquent plus mutuellement après une baisse du disponible. **C'est une modification du comportement existant** ; si le PO la refuse, on conserve la règle actuelle (blocage mutuel possible jusqu'à expiration de 15 min) et V1-R4 reste valable en lecture seule. | Q-3 ; modifie `sales/plan.ts` l. 115 et le déclencheur 0016 l. 323 |
| **V1-R6** | **Signalement et traçabilité** (aucun envoi externe, Q-4). (a) Au moment d'un passage, d'un blocage ou d'une correction, la commande calcule les réservations rendues non honorables et en inscrit **les identifiants, comptoirs, auteurs et quantités dans la charge de l'entrée d'audit** de l'opération (audit chaîné, persistant). (b) La liste des réservations (`sale.create`) affiche l'indicateur « non honorable » et la cause (quantité en contrôle, quarantaine, lot bloqué ou rappelé). (c) L'auteur de la réservation est averti à son prochain accès : la liste et la tentative de validation renvoient un refus nommé `stock.reservation_unfulfillable`, jamais un refus générique. (d) Le tableau d'alertes du stock présente le nombre de réservations non honorables. | DP3-R3, Q-4 |
| **V1-R7** | **Responsabilités.** Voir §4.3. | DP1-R3 |
| **V1-R8** | **Réservation éteinte par la vente d'un autre.** Inchangé : la consommation d'une réservation émet l'événement CONSUMED. Rien n'est ajouté. | existant |
| **V1-R9** | **Sorties manuelles : uniquement depuis DISPONIBLE** (DP4-R4 et DP4-R5 régissent les sorties depuis DISPONIBLE ; les sorties depuis QUARANTAINE et la destruction de PERTE restent reportées, DP1-R7). Une quantité EN_CONTROLE repasse d'abord en DISPONIBLE (passage ordinaire) pour être sortie. Cette lecture est celle du registre §10.3 V-1 ; **elle devient décision si le PO répond Q-1.** | DP4-R4, DP1-R7, C3 |
| **V1-R10** | **Lots bloqués, rappelés ou périmés.** La quantité d'un lot périmé (DISPONIBLE, non vendable) se sort uniquement pour « retrait ou destruction », traduit en motifs normalisés **PEREMPTION, RAPPEL, CASSE** ; pour un lot BLOQUE ou RAPPELE, mêmes motifs. Les motifs USAGE_INTERNE et AUTRE sont refusés sur ces lots. Ce choix traduit « retrait ou destruction » (DP4-R4) en liste de motifs : **il est à confirmer (Q-2).** | DP4-R4, DP4-R5 |

### 4.3 Responsabilités et droits (proposition)

| Acte | Droit requis | Qui l'a par défaut | Traçabilité |
|---|---|---|---|
| Passage vers EN_CONTROLE, QUARANTAINE ou PERTE ; réintégration EN_CONTROLE vers DISPONIBLE | `stock.state.transfer` (métier, non accordé par défaut aux collaborateurs) | ADMIN (automatique) | en-tête de passage immuable + 2 mouvements + audit |
| Blocage ou rappel d'un lot | `stock.lot.block` (métier, non accordé par défaut) | ADMIN | événement de statut append-only + audit |
| **Levée de QUARANTAINE** (QUARANTAINE vers DISPONIBLE) | droit de **gouvernance** `stock.quarantine.release`, non délégable (Q-5) | ADMIN uniquement | passage + audit avec nom de l'autorité |
| **Levée d'un blocage de lot** | même autorité (Q-6) | ADMIN uniquement | événement ACTIF append-only + audit |
| Voir les réservations non honorables | `sale.create` (liste) / `stock.read` (alertes) | selon rôles existants | lecture |

La règle « l'ADMIN conserve ses droits métier automatiques » (décision PO du 17/11/2026) est préservée ; la levée de QUARANTAINE est le seul acte de ce chantier placé en gouvernance.

### 4.4 Scénarios de référence V-1 (servent aux tests, §9 du plan)

| Id | Situation | Résultat attendu |
|---|---|---|
| V1-S1 | Produit à un lot de 10 ; réservation R1 de 6 ; passage de 7 unités en QUARANTAINE | passage **accepté** ; disponible = 3 ; R1 « non honorable » (manque 3) ; entrée d'audit listant R1 ; la vente de 4 est refusée, celle de 3 est refusée tant que R1 est active (3 < 6 réservés par une réservation plus ancienne) |
| V1-S2 | Même produit, deux réservations R1 (6, ancienne) et R2 (3, récente), disponible 10 puis 7 après passage de 3 en EN_CONTROLE | R1 honorée (6 ≤ 7), R2 non honorable (6 + 3 = 9 > 7) ; vente de 6 depuis R1 acceptée (V1-R5) ; vente de 3 depuis R2 refusée avec `stock.reservation_unfulfillable` (7 − 6 réservés par une réservation plus ancienne = 1 < 3) |
| V1-S3 | Lot rappelé alors qu'une réservation porte sur le produit | blocage accepté ; aucune quantité déplacée ; le lot n'est plus alloué ; les autres lots du produit servent la réservation si leur disponible suffit |
| V1-S4 | Passage et vente simultanés sur la dernière quantité | un seul des deux aboutit ; jamais de négatif par état ; jamais de vente de quantité isolée |
| V1-S5 | Sortie manuelle (motif CASSE) sur une quantité réservée | **refusée** (DP4-R1) sauf dérogation ADMIN motivée et tracée ; en revanche le passage de cette même quantité en PERTE est accepté (V1-R1) |
| V1-S6 | Sortie manuelle depuis QUARANTAINE ou PERTE | refusée (code `stock.state_exit_deferred`), message renvoyant au lot ultérieur |
| V1-S7 | Réservation active ; la quantité revient en DISPONIBLE (levée de quarantaine) | l'indicateur « non honorable » disparaît (calculé) |

**Option écartée** : annuler automatiquement les réservations non honorables. Rejetée : elle exigerait un nouveau type d'événement (reconstruction de `sale_reservation_events`), écrirait à la place du comptoir et supprimerait le signal à l'auteur exigé par DP3-R3.

### 4.5 Cohérence avec DP1-R7, DP3, DP4-R4 et DP4-R6

| Décision | Résultat du contrôle |
|---|---|
| DP4-R6 (réservation n'empêche pas QUARANTAINE/PERTE) | Respectée : V1-R1 ; test d'étanchéité sur la source `stock.transfer`. |
| DP3-R3 (sécurité prioritaire, réservation signalée) | Respectée : V1-R3, V1-R4, V1-R6 ; aucune réservation modifiée ; signal à l'auteur. |
| DP3-R1 et DP3-R2 (réservation par produit, lot choisi à la vente, CAT-10) | Respectées : la réservation reste par produit ; le FEFO choisit parmi les lots DISPONIBLE non bloqués, non périmés, **après** déduction des réservations (ancienneté : V1-R5). |
| DP1-R5 (rappel = blocage seul) | Respectée : V1-R2, S-7. |
| DP1-R7 (sorties de QUARANTAINE et destruction de PERTE reportées) | Respectée : V1-R9, S-12 ; aucune sortie hors DISPONIBLE. La destruction **d'un périmé DISPONIBLE** par sortie manuelle relève de DP4-R4, pas de DP1-R7. |
| DP4-R1 (sorties manuelles : réservations bloquantes, dérogation ADMIN) | Respectée : V1-S5 ; sa mise en œuvre étend le déclencheur de réservation aux sorties manuelles (plan §4.4). |
| DP4-R4 (périmés : retrait ou destruction seulement) | Précisée par V1-R10 (liste de motifs), **à confirmer**. |
| C3 | Voir §5. |

**Arbitrages résiduels V-1 pour le PO : Q-1, Q-2, Q-3 (et sa variante « garder la règle actuelle »), Q-4, Q-5, Q-6.**

---

## 5. Résolution de V-2 — corrections et contre-passations

**Rappel du registre.** DP1-R10 : corrections d'inventaire et contre-passations portent sur le **stock physique**. C3 : elles préservent l'état réel des quantités ; une correction négative ne peut pas contourner la procédure de destruction ; une contre-passation tient compte de l'état actuel. DP5-R3 : correction positive justifiée et contrôlée. DP1-R7 : destruction reportée.

### 5.1 Faits relevés dans le code (FAIT VÉRIFIÉ)

| Constat | Source |
|---|---|
| La **correction d'inventaire** n'écrit jamais une quantité : elle ajoute un mouvement `CORRECTION` de `delta = compté − attendu`, motif obligatoire (3 à 300 caractères), avec une garde atomique sur la quantité attendue (`stock.level_changed` si elle a bougé). Elle lit le niveau **du lot** (somme de tous les mouvements), sans notion d'état. | `stock/service.ts` l. 973-1013 |
| Une correction reste permise sur un produit INACTIF ou A_VALIDER (« pour écouler ou régulariser ») et interdite sur un produit ARCHIVE. | `stock/service.ts` l. 239-243 ; déclencheurs 0006 l. 261-277 |
| La **contre-passation de réception** écrit un `CORRECTION` négatif de source `purchasing.reversal` sur le **lot d'origine**, borné par `quantité reçue − déjà reprise`, avec la seule garde « niveau du lot ≥ quantité reprise » (aucune notion d'état). L'annulation intégrale refuse si une opération existe déjà sur la réception. | `purchasing/service.ts` l. 1277-1380 |
| Le journal est immuable (déclencheurs `stock_movements_no_update` et `_no_delete`) ; la contre-passation passe par un mouvement compensatoire lié à la ligne de réception d'origine. | `0005` l. 114-123 ; `0019` l. 130-139 |
| `type = 'CORRECTION'` accepte un `delta` de signe libre ; `reason` est obligatoire pour tout type autre qu'ENTREE. | `0005` l. 96-97 |

### 5.2 Règles proposées (V2-Rn)

| Réf. | Règle | Rattachement |
|---|---|---|
| **V2-R1** | **Une correction d'inventaire constate un écart de comptage sur le stock physique** (DP1-R10). Elle vise un triplet **produit / lot / état** précis. Le comptage s'effectue état par état : l'écran présente la quantité attendue de chaque état et le comptage saisit la quantité trouvée dans l'état visé. La garde « quantité attendue » (`stock.level_changed`) s'applique **à l'état visé**. | DP1-R10 |
| **V2-R2** | **L'état visé est toujours explicite.** Jamais déduit, jamais « débordé » : une correction négative sur DISPONIBLE n'entame jamais une autre quantité du lot ; une correction ne déplace jamais de quantité d'un état à l'autre (c'est le rôle d'un passage). | C3 |
| **V2-R3** | **Conditions d'une correction négative.** (a) **DISPONIBLE** : droit `stock.adjust` ; motif normalisé parmi ECART_COMPTAGE, ERREUR_SAISIE, AUTRE (avec précision) ; garde de quantité attendue ; quantité ≤ disponible de l'état. (b) **EN_CONTROLE ou QUARANTAINE** : mêmes conditions **et** droit restreint `stock.adjust.nondisponible` (habilitation pharmacien, non délégable), signalé dans les rapports. (c) **Lot périmé, BLOQUE ou RAPPELE, ou mise à zéro d'un lot entier** : droit restreint de (b). (d) **PERTE : jamais** (V2-R5). (e) Produit ARCHIVE : refusé (inchangé). | C3, Q-9 |
| **V2-R4** | **Une correction positive n'écrit que dans DISPONIBLE** (INV-4, Q-8) : motif ECART_COMPTAGE, ERREUR_SAISIE ou AUTRE ; garde de quantité attendue ; signalée dans le rapport d'écarts (H-2). Un excédent constaté dans un autre état s'enregistre en deux temps tracés (correction positive en DISPONIBLE, puis passage d'état). Aucune entrée dans EN_CONTROLE, QUARANTAINE ou PERTE. | DP5-R3, DP5-R5 |
| **V2-R5** | **Interdiction de la destruction déguisée.** (a) Une correction **n'accepte pas** les motifs de sortie CASSE, PEREMPTION, RAPPEL, USAGE_INTERNE : ces actes relèvent d'une sortie manuelle (DP4, depuis DISPONIBLE) ou d'un passage en PERTE. (b) Aucune correction négative n'est possible sur l'état PERTE (garde de la base : INV-6). (c) Une correction qui ramène à zéro la quantité d'un lot ou d'un produit demande le droit restreint (V2-R3 c). (d) Les corrections négatives sont **exposées dans un rapport** (cumul par lot, produit, motif et auteur ; H-4 : aucun seuil fixé). (e) Un contrôle de rapprochement signale tout lot dont les corrections négatives cumulées dépassent sa quantité reçue. | C3, DP1-R7 |
| **V2-R6** | **Contre-passation d'une réception tenant compte de l'état actuel.** La quantité reprise est écrite **dans l'état DISPONIBLE du lot d'origine** et ne peut dépasser le minimum de : (i) quantité reçue moins déjà reprise (règle actuelle) ; (ii) **quantité actuellement DISPONIBLE** du lot. Si la quantité demandée excède le DISPONIBLE, le refus est nommé `purchase.reversal_state` avec le détail par état (reprenable, disponible, en contrôle, en quarantaine, en perte). L'**annulation intégrale** est refusée si une seule ligne a une part hors DISPONIBLE (même garde). Une quantité en EN_CONTROLE se reprend après réintégration en DISPONIBLE ; une quantité en QUARANTAINE après levée (DP1-R3) ; une quantité en PERTE **attend le lot ultérieur** (retour fournisseur ou destruction). | C3, Q-10 |
| **V2-R7** | **Réservations.** Une correction ou contre-passation enregistre la réalité physique et n'est **jamais bloquée par une réservation** (DP4-R1 vise les sorties manuelles) ; elle déclenche le calcul et la trace de V1-R6. | Q-7 |
| **V2-R8** | **Un passage en PERTE n'est pas annulable** (DP1-R4). Aucun mouvement, aucune correction ni contre-passation ne rend une quantité PERTE au DISPONIBLE. Garde-fous : confirmation explicite, quantité et irréversibilité affichées, droit non accordé par défaut ; pour un doute, utiliser EN_CONTROLE (réversible). | DP1-R4, Q-11 |
| **V2-R9** | **Aucune modification silencieuse de l'historique.** Toute rectification est un nouveau mouvement lié à son acte (en-tête de passage, ligne d'opération de réception, correction avec motif), horodaté et attribué ; le journal reste immuable (INV-1). Aucune écriture rétroactive de la colonne d'état sur les mouvements existants (ils valent DISPONIBLE par défaut). | C3, INV-1 |
| **V2-R10** | **Traçabilité de rapprochement.** Chaque correction et chaque contre-passation inscrit à l'audit : produit, lot, état visé, quantité attendue, quantité comptée, delta, motif normalisé, auteur, droit utilisé (ordinaire ou restreint), réservations rendues non honorables. | DP1-R2 |

### 5.3 Cas qui exigent une opération métier distincte (aucun contournement)

| # | Cas | Pourquoi impossible par correction | Traitement |
|---|---|---|---|
| 1 | Écart constaté sur une quantité en **PERTE** (moins que le registre) | DP1-R4 ; la destruction est reportée (DP1-R7) | **Lot ultérieur** (constat de destruction) |
| 2 | Contre-passation d'une quantité en **PERTE** | idem | Lot ultérieur (retour fournisseur ou destruction) |
| 3 | Contre-passation d'une quantité en **QUARANTAINE** | sortie de QUARANTAINE reportée (DP1-R7) | Levée par l'ADMIN (puis reprise) ; ou lot ultérieur |
| 4 | Retrait d'une quantité cassée ou périmée sous couvert de « correction » | C3, V2-R5 | Sortie manuelle (DISPONIBLE, motif normalisé) ou passage en PERTE |
| 5 | Annuler un passage en PERTE | DP1-R4 | Aucun (accepté, Q-11) |
| 6 | Excédent constaté en QUARANTAINE ou EN_CONTROLE | toute entrée naît en DISPONIBLE (INV-4) | Correction positive en DISPONIBLE puis passage d'état |
| 7 | Rectifier un mouvement passé | immuabilité | Mouvement compensatoire lié |
| 8 | Médicament existant sans suivi par lot (« à régulariser », DP2-R4) | aucune conversion de son historique | Opération distincte (hors périmètre, DP2) |

**Arbitrages résiduels V-2 pour le PO : Q-7, Q-8, Q-9, Q-10, Q-11.**

---

## 6. Consolidation métier (renvois au registre, sans le répéter)

| Domaine | Règle finale | Source |
|---|---|---|
| **Suivi par lot** | Obligatoire à la création et à l'adoption d'un MÉDICAMENT ; facultatif (défaut non) pour dispositif médical et autres types ; numéro de lot et péremption saisis à la **réception**. Médicaments existants sans suivi : signalés « à régulariser », **aucune conversion** de leur historique. Règle de **code** (aucune colonne nouvelle : `products.tracks_lots` reste immuable). Le signalement est une **requête de lecture** (médicaments avec `tracks_lots = 0`). | DP2-R1 à R4 ; registre §3 |
| **Réservations** | Par produit, logique, lot choisi à la vente (FEFO). Jamais bloquantes pour les passages d'état (V1-R1). Ancienneté pour les non honorables (V1-R4/R5, Q-3). | DP3, DP4-R6, V1 |
| **FEFO** | Un **seul calcul partagé** (aujourd'hui deux implémentations : `stock/service.ts` `fefoFor` l. 940 et `sales/plan.ts` `fefo` l. 154). Entrée : lots DISPONIBLE, non BLOQUE/RAPPELE, non périmés, quantité > 0, triés par péremption puis identifiant. Mode manuel : écart autorisé et tracé (DP4-R3). | DP3-R2, DP4-R3, CAT-10 |
| **Entrées autorisées** | Quatre : réception physique ; régularisation hors commande (justificatif obligatoire, droit distinct `stock.regularize`) ; correction positive justifiée ; retour ou annulation de vente revendable (quantités vendues, lots d'origine). **Toutes écrivent en DISPONIBLE.** Commande, sélection catalogue, adoption, réservation, passage d'état : jamais d'augmentation du physique. | DP5-R1 à R5, INV-4 |
| **Sorties autorisées** | Vente (FEFO, depuis DISPONIBLE) ; sortie manuelle depuis DISPONIBLE (réservations bloquantes, dérogation ADMIN tracée, écart FEFO tracé, périmés pour retrait ou destruction, motifs normalisés) ; correction négative (V2) ; contre-passation de réception (V2-R6). **Aucune sortie depuis EN_CONTROLE, QUARANTAINE ou PERTE.** | DP4, DP1-R7, V1-R9 |
| **Archivage** | Interdit tant que le physique tous états > 0 (C1, S-10). | C1 |
| **Destruction** | Aucune destruction de quantité en PERTE avant le lot ultérieur ; indicateur « en attente de destruction » (C2). Seule exception : la sortie manuelle d'un **périmé DISPONIBLE** (DP4-R4), qui n'a pas transité par PERTE. | DP1-R7, C2, DP4-R4 |
| **Alertes de péremption** | Quantités détenues en tout état (DP1-R11) ; chaque alerte indique l'état de la quantité. | DP1-R11 |
| **Retour client** | Reste direct en DISPONIBLE (comportement actuel). Retour « en contrôle » : REPORTÉ (O-6). | DP5-R6 |
| **Dispositif de motifs** | Table de référence (§8.3). | O-11, Q-12 |

---

## 7. Invariants (à vérifier par la base elle-même, pas seulement par le code)

| Réf. | Invariant | Garde |
|---|---|---|
| **INV-1** | Le journal est immuable : aucun UPDATE ni DELETE sur les mouvements, en-têtes de passage et événements de statut. | déclencheurs existants + nouveaux |
| **INV-2** | Aucun niveau négatif, ni par produit/lot (existant) ni par **produit/lot/état**. | `stock_movements_no_negative` (inchangé) + `stock_movements_no_negative_bucket` (nouveau) |
| **INV-3** | Le physique est la somme du journal : somme des états = ancien `SUM(delta)` ; aucune seconde source (DP1-R6). | vues calculées ; test ST-04-b |
| **INV-4** | Toute `ENTREE` écrit en DISPONIBLE ; une correction positive aussi (V2-R4). Un mouvement **positif** hors DISPONIBLE n'existe que rattaché à un passage valide. Seule exception : la correction **négative** d'inventaire en EN_CONTROLE ou QUARANTAINE (V2-R3 b), sans passage. | `stock_movements_bucket_rules` |
| **INV-5** | Aucune **sortie** (vente, sortie manuelle, contre-passation) hors DISPONIBLE ; seules les corrections négatives en EN_CONTROLE ou QUARANTAINE (droit restreint, V2-R3 b) entament un autre état, jamais PERTE. | garde par état + règles de source |
| **INV-6** | **Aucun mouvement négatif en PERTE** (ni correction, ni sortie) tant que le lot ultérieur n'a pas remplacé cette garde. | `stock_movements_bucket_rules` |
| **INV-7** | Un passage = exactement deux mouvements `CORRECTION` de source `stock.transfer`, de somme nulle, rattachés à un en-tête ; états de départ et d'arrivée conformes à la matrice. | triggers d'en-tête |
| **INV-8** | Un lot BLOQUE ou RAPPELE ne peut pas sortir par `sales.sale`. | `stock_movements_lot_status` |
| **INV-9** | Aucune ligne existante n'est réécrite par la migration (valeur par défaut DISPONIBLE). | contrôles B3-B5 du plan |
| **INV-10** | Une réservation n'empêche jamais un mouvement de source `stock.transfer` ni l'ajout d'un événement de statut de lot. | test d'étanchéité (V1-R1) |

---

## 8. Modèle de mouvements et calcul du stock par état

### 8.1 Modèle (PROPOSITION conforme au plan P81, §3 ; alternatives et rejets : plan P81 §3.1)

- **Dimension d'état portée par le journal** : colonne `stock_movements.bucket` (DISPONIBLE par défaut) ; aucune colonne de quantité ; aucun nouveau type de mouvement (la contrainte `CHECK` des types ne peut pas être modifiée sans reconstruire la table).
- **Passage d'état** = deux `CORRECTION` (source `stock.transfer`, `−q` dans l'état de départ, `+q` dans l'état d'arrivée) + un en-tête immuable `stock_state_transfers` (quantité, motif, auteur, date, états).
- **Statut de lot** = événements append-only `stock_lot_status_events` (ACTIF, BLOQUE, RAPPELE).
- **Vues calculées** : `stock_levels` (produit, lot, état → quantité) ; `stock_lot_received` (quantité reçue par lot).
- **Motifs normalisés** : voir §8.3.

### 8.2 Calcul du stock par état

| Grandeur | Calcul |
|---|---|
| Physique (produit ou lot) | Σ `delta`, tous états |
| Par état | Σ `delta` groupée par état |
| Disponible | Σ `delta` de l'état DISPONIBLE, lots non BLOQUE/RAPPELE, non périmés ; produit sans lot : DISPONIBLE |
| Vendable | Disponible − réservations actives des autres paniers (ancienneté pour une vente adossée : V1-R5) |
| En attente de destruction (C2) | Σ `delta` PERTE ; ancienneté = plus ancien passage en PERTE (H-3) |
| Reçu (CAT-11) | Σ `ENTREE` des sources `purchasing.receipt` et régularisation (`stock.regularization`, et `stock.manual` historique) ; **ni** retours clients **ni** contre-passations |
| Corrections nettes | Σ `delta` des `CORRECTION` hors passages (`source` différente de `stock.transfer`), **contre-passations de réception comprises** (`purchasing.reversal`) |
| Sorties nettes | Σ des sorties de vente et manuelles, moins les retours (`sales.return`, `sales.void`, `sales.correction`) |

**Identité CAT-11** (registre DP1-R9), **par lot et par produit sans lot** :
`reçu + corrections nettes = disponible + en contrôle + quarantaine + perte + sorties nettes`, où « corrections nettes » inclut les contre-passations de réception et « sorties nettes » comptent en positif ce qui sort. **Point de vigilance de calcul (V-3, NOUVEAU)** : la lecture littérale de la fiche P81 place les contre-passations et retours « dans les mouvements nets » sans dire dans quel membre. Le plan fige la convention suivante pour que l'identité soit exacte : les contre-passations (CORRECTION négatives `purchasing.reversal`) sont des **corrections nettes**, les retours clients (ENTREE `sales.*`) viennent en **déduction des sorties nettes**. C'est une convention de calcul, non une règle métier ; le PO peut la lire au passage (aucune décision nouvelle demandée), l'écriture exacte étant verrouillée par le test CAT-11-a.

### 8.3 Motifs normalisés (O-11, Q-12)

| Élément | Proposition |
|---|---|
| Forme | Table de référence `stock_reason_codes (code, kind, label, requires_detail, active)` + colonne **nullable** `stock_movements.reason_code` (clé étrangère) ; la colonne `reason` (texte libre, 3 à 300 caractères) reste et porte la **précision**. |
| Motifs de **sortie manuelle** (DP4-R5) | CASSE, PEREMPTION, RAPPEL, USAGE_INTERNE, AUTRE (avec précision obligatoire). |
| Motifs de **correction** | ECART_COMPTAGE, ERREUR_SAISIE, AUTRE (avec précision). Aucun motif de destruction (V2-R5). |
| Historique | Mouvements existants : `reason_code` nul (non rétroactif, V2-R9). |
| Pourquoi pas un `CHECK` | La liste ne pourrait plus évoluer sans reconstruire `stock_movements` ; une table de référence s'étend par une ligne (migration 0023 ou commande de gouvernance). |
| Risque | Un `ADD COLUMN … REFERENCES` sur une table `STRICT` : même précédent que 0008 (`supplier_id`) ; comportement D1 réel **NON VÉRIFIÉ** (O-7). |

### 8.4 Dérogation ADMIN aux réservations (DP4-R2)

Une sortie manuelle qui entame une quantité réservée est refusée (DP4-R1) sauf **dérogation ADMIN motivée et tracée**. Proposition : un en-tête immuable `stock_issue_overrides` (auteur ADMIN, motif, réservations concernées) ; la sortie dérogatoire **garde la source `stock.manual`** (le regroupement des sorties manuelles en dépend) et référence cet en-tête par son `source_ref` ; le déclencheur de réservation n'exempte une sortie **que** si l'en-tête existe. Les réservations concernées sont alors « non honorables » (V1-R6) et l'auteur est signalé.

---

## 9. Critères de test visés (détail et séquencement : plan P82 §9)

| Critère | Énoncé final | Cas complémentaires V-1/V-2 |
|---|---|---|
| **ST-01** | Une quantité EN_CONTROLE n'est jamais allouée par FEFO ni comptée dans le disponible. | V1-S4, V1-S6 |
| **ST-04** | Réconciliation : niveau par état = Σ des mouvements, recalculable ; calculé **indépendamment** (modèle en mémoire du test). | V2 : corrections par état |
| **CAT-10** | Reformulé (DP3-R2) : seul le lot disponible, non bloqué, non périmé, de péremption la plus proche, est alloué après déduction des réservations du produit. | V1-S1 à S3, V1-S5, S7 |
| **CAT-11** | Identité §8.2 vraie par lot ; côté « reçu » calculé depuis `purchase_receipt_lines` (source indépendante). | V2-R6 : contre-passation limitée au DISPONIBLE |

---

## 10. Ce que ce document n'est pas

Il n'autorise aucun développement ni aucune migration et ne modifie aucune décision du registre. Les règles `V1-Rn`, `V2-Rn`, `S-n`, `INV-n` et les réponses Q-1 à Q-12 deviennent décisions après réponse écrite du PO et addendum du registre. Il ne traite pas O-5, O-6, O-10, O-9 (hors plan), les règles réglementaires de retour (D62, D63, D75), les prépaiements patients, le fuseau horaire de la péremption, ni la réservation pour reliquat d'ordonnance.
