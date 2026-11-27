# P84 — Matrice de tests STOCK / migration 0022 (spécifiée, non exécutée)

**Statut** : document de préparation. **Aucun test n'a été écrit ni exécuté en P84.** Aucune base ouverte, aucune migration créée. Les décisions P81/P82/P83 sont la référence métier ; ce document ne les modifie pas.
**Étiquettes** : DÉCIDÉ = décision PO confirmée (P83) ; PROPOSITION = choix technique à confirmer ; NON VÉRIFIÉ / NON TESTÉ = pas de preuve à ce jour.
**Données** : toutes fictives.

---

## 1. Conventions

### 1.1 Notation des états

`D / C / Q / P` = quantités en **D**ISPONIBLE, EN_**C**ONTROLE, **Q**UARANTAINE, **P**ERTE. Exemple : `D10 C0 Q0 P0`. « Physique » = D + C + Q + P.

### 1.2 Jeux de données de référence (fixtures fictives)

| Id | Contenu | Usage |
|---|---|---|
| FX-A | Produit P-A (médicament, suivi par lot). Lot A1, péremption **future** (+12 mois), reçu 10 ; état `D10 C0 Q0 P0`. | cas nominaux |
| FX-B | Produit P-B. Lot B1 reçu 10, état `D4 C3 Q2 P1`. | états mixtes |
| FX-C | Produit P-C. Lot C1 **périmé** (−1 mois), reçu 10, `D10`. | lot périmé |
| FX-D | Produit P-D. Lot D1 statut **BLOQUE**, reçu 10, `D6 C0 Q4 P0`. | lot bloqué |
| FX-E | Produit P-E. Lot E1 statut **RAPPELE**, reçu 10, `D10`. | lot rappelé |
| FX-F | Produit P-F **sans suivi par lot** (`lot_id` nul), `D8 C0 Q2 P0`. | produit sans lot |
| FX-G | FX-A + réservation active R1 de 6 sur P-A (comptoir 1). | réservations |
| FX-H | Réception REC-1 de 10 sur P-A lot A1, `D10`, aucune reprise. | contre-passation |
| FX-I | REC-1 puis passages : `D4 C3 Q2 P1` sur le lot d'origine. | contre-passation partielle |
| FX-J | Produit P-J ARCHIVE (physique nul). | refus produit archivé |

### 1.3 Acteurs et droits

| Acteur | Droits |
|---|---|
| U-0 | Collaborateur **sans** droit de stock hors lecture (`stock.read`). |
| U-1 | `stock.adjust` seul. |
| U-2 | `stock.adjust` + `stock.adjust.nondisponible` (droit restreint, non délégable). |
| U-3 | `stock.state.transfer` seul. |
| U-4 | Gestionnaire d'achats : droit de contre-passation des réceptions existant (nom exact **à relever en R1**, NON VÉRIFIÉ ici). |
| ADM | ADMIN : droits métier automatiques + gouvernance (`stock.quarantine.release`, levée de blocage). |

### 1.4 Colonnes obligatoires de chaque scénario

Préconditions · Action · Résultat attendu · **Stock avant → après** · **Trace d'audit** · **PASS si** / **FAIL si**.

### 1.5 Règles communes d'évaluation

| # | Règle | Origine |
|---|---|---|
| E-1 | **Refus** = aucun mouvement écrit, aucun en-tête, stock inchangé **à l'unité près**, et un **code d'erreur nommé** (jamais un refus générique). | INV-1, V1-R6 |
| E-2 | **Succès** = mouvements écrits dans la même unité atomique que l'audit ; le physique = somme du journal. | INV-3 |
| E-3 | Après chaque scénario : `Σ états = SUM(delta)` et aucun niveau négatif (produit/lot, produit/lot/état). | INV-2/3 |
| E-4 | L'entrée d'audit porte (V2-R10) : produit, lot, état visé, quantité attendue, quantité comptée, delta, motif normalisé, auteur, droit utilisé (ordinaire/restreint), réservations rendues non honorables. | V2-R10 |
| E-5 | Les **noms d'actions d'audit** et **codes d'erreur nouveaux** sont des PROPOSITIONS à fixer en R1 et à reporter ici ; les codes déjà existants (`stock.level_changed`, `stock.insufficient`, `purchase.reversal_exceeds`) sont conservés. | — |
| E-6 | Un test d'**échec attendu** doit échouer **sans** le correctif (preuve de sensibilité : « test rouge » en R3). | plan P82 §7 |

---

## 2. Q-9 et V-5 — Corrections négatives (préfixe NEG)

Références : V2-R1 à V2-R5, V2-R10 ; INV-2, INV-5, INV-6 ; Q-9 DÉCIDÉ ; V-5 (distinction correction / sortie de QUARANTAINE, DP1-R7).

### 2.1 Vrai écart de comptage

| Id | Préconditions | Action | Résultat attendu | Stock avant → après | Audit | PASS / FAIL |
|---|---|---|---|---|---|---|
| NEG-01 | FX-A ; U-1 | Correction état **D**, attendu 10, compté 8, motif ECART_COMPTAGE | Accepté ; 1 mouvement CORRECTION `delta −2`, bucket D, source ≠ `stock.transfer` | `D10` → `D8` | E-4 complet, droit « ordinaire » | PASS si D=8 et 1 seul mouvement ; FAIL si autre état touché |
| NEG-02 | FX-B ; U-1 | Correction état D, attendu 4, compté 3 | Accepté ; C, Q, P inchangés | `D4 C3 Q2 P1` → `D3 C3 Q2 P1` | idem | PASS si C/Q/P identiques |
| NEG-03 | FX-A ; U-1 | Correction D, attendu 9 (obsolète), compté 8 | Refus `stock.level_changed` (garde de quantité attendue **à l'état visé**) | `D10` inchangé | refus tracé (pas de mouvement) | PASS si refus et 0 mouvement |
| NEG-04 | FX-B ; U-1 | Correction D, attendu 4, compté **−1** ou non entier | Refus de validation (quantité invalide) | inchangé | — | PASS si refus nommé |
| NEG-05 | FX-A ; U-1 | Correction D qui demanderait `delta −11` (compté < 0 impossible) ou sortie > D | Refus ; jamais de débordement vers un autre état (V2-R2) | `D10` inchangé | — | PASS si 0 mouvement |
| NEG-06 | FX-B ; U-1 | Correction D `delta −5` alors que D=4 et C=3 (total 7 ≥ 5) | Refus : **ne déborde pas** sur C | `D4 C3 …` inchangé | — | PASS si refus ; FAIL si C diminue |
| NEG-07 | FX-F (sans lot) ; U-1 | Correction D, attendu 8, compté 7 | Accepté ; `lot_id` nul conservé ; Q inchangé | `D8 Q2` → `D7 Q2` | idem | PASS si Q=2 |

### 2.2 Refus de dissimulation de destruction ou de sortie interdite (V2-R5)

| Id | Préconditions | Action | Résultat attendu | Stock | Audit | PASS / FAIL |
|---|---|---|---|---|---|---|
| NEG-10 | FX-A ; U-2 | Correction négative avec motif **CASSE** | Refus nommé (motif de sortie interdit en correction) | inchangé | refus tracé | PASS si refus ; FAIL si accepté |
| NEG-11 | idem | motif **PEREMPTION** | Refus | inchangé | — | idem |
| NEG-12 | idem | motif **RAPPEL** | Refus | inchangé | — | idem |
| NEG-13 | idem | motif **USAGE_INTERNE** | Refus | inchangé | — | idem |
| NEG-14 | FX-A ; U-1 | Correction négative **sans motif** ou motif < 3 car. ou > 300 car. | Refus de validation | inchangé | — | PASS si refus |
| NEG-15 | FX-A ; U-1 | Motif **AUTRE** sans précision | Refus (précision obligatoire) | inchangé | — | PASS si refus |
| NEG-16 | FX-A ; U-1 | Motif inconnu (code absent de `stock_reason_codes`) ou inactif | Refus (clé étrangère / validation) | inchangé | — | PASS si refus |
| NEG-17 | FX-B ; U-2 | Correction négative état **P** (PERTE), quelle que soit la quantité | **Refus par la base** (INV-6), y compris par appel direct SQL du test | `P1` inchangé | refus tracé côté service | PASS si refus **service ET base** |
| NEG-18 | FX-A ; U-2 | Séquence « détournement » : correction négative D puis tentative de récupérer en PERTE | La correction n'écrit jamais en P ; aucun mouvement P négatif | inchangé en P | — | PASS si P ne diminue jamais |
| NEG-19 | FX-A ; ADM | Contournement ADMIN : correction avec motif CASSE | Refus aussi pour ADMIN (la règle est métier, pas un droit) | inchangé | — | PASS si refus |

### 2.3 Motifs ERREUR_SAISIE et AUTRE

| Id | Préconditions | Action | Résultat attendu | Stock avant → après | Audit | PASS / FAIL |
|---|---|---|---|---|---|---|
| NEG-20 | FX-A ; U-1 | Correction D `−1`, motif ERREUR_SAISIE, précision « doublon de saisie » | Accepté ; `reason_code` = ERREUR_SAISIE, `reason` = précision | `D10` → `D9` | motif normalisé + précision | PASS si les deux colonnes renseignées |
| NEG-21 | FX-A ; U-1 | Correction D `−1`, motif AUTRE, précision valide (≥ 3 car.) | Accepté ; signalé au rapport d'écarts (V2-R5 d) | `D10` → `D9` | idem | PASS si présent dans le rapport d'écarts |
| NEG-22 | FX-A ; U-1 | Correction positive `+1` ERREUR_SAISIE | Accepté **seulement** en D (V2-R4) ; garde attendue | `D10` → `D11` | idem | PASS si bucket D |
| NEG-23 | FX-B ; U-2 | Correction **positive** visant C ou Q ou P | Refus : aucune entrée hors D (INV-4) | inchangé | — | PASS si refus |

### 2.4 Lots EN_CONTROLE et QUARANTAINE (droit restreint, V2-R3 b)

| Id | Préconditions | Action | Résultat attendu | Stock avant → après | Audit | PASS / FAIL |
|---|---|---|---|---|---|---|
| NEG-30 | FX-B ; U-2 | Correction état **C**, attendu 3, compté 2, ECART_COMPTAGE | Accepté ; droit « restreint » inscrit | `C3` → `C2` ; D, Q, P inchangés | droit restreint tracé ; signalé aux rapports | PASS si droit restreint dans l'audit |
| NEG-31 | FX-B ; **U-1** (sans droit restreint) | Même action sur C | Refus nommé (droit manquant) | inchangé | refus tracé | PASS si refus ; FAIL si accepté |
| NEG-32 | FX-B ; U-2 | Correction état **Q**, attendu 2, compté 1 | Accepté | `Q2` → `Q1` | idem | PASS |
| NEG-33 | FX-B ; U-1 | Correction état Q | Refus (droit) | inchangé | — | PASS si refus |
| NEG-34 | FX-B ; **U-3** (`stock.state.transfer` seul) | Correction état C | Refus : le droit de passage n'autorise pas une correction | inchangé | — | PASS si refus |
| NEG-35 | FX-B ; U-2 | Correction C avec garde attendue obsolète | Refus `stock.level_changed` | inchangé | — | PASS |
| NEG-36 | FX-B ; U-2 | Correction C `−4` (> 3) | Refus (aucun négatif par état, INV-2) ; ne déborde pas | inchangé | — | PASS si C ≥ 0 et D inchangé |
| NEG-37 | FX-B ; U-2 | Un **seul** triplet produit/lot/état par action ; tentative de viser deux états en une requête | Refus de validation (état visé unique, V2-R2) | inchangé | — | PASS si refus |

### 2.5 Lots périmés, bloqués, rappelés, mise à zéro (V2-R3 c)

| Id | Préconditions | Action | Résultat attendu | Stock avant → après | Audit | PASS / FAIL |
|---|---|---|---|---|---|---|
| NEG-40 | FX-C (périmé) ; U-1 | Correction D `−1` | Refus (droit restreint requis) | `D10` inchangé | — | PASS si refus |
| NEG-41 | FX-C ; U-2 | Correction D `−1` ECART_COMPTAGE | Accepté ; droit restreint tracé | `D10` → `D9` | idem | PASS |
| NEG-42 | FX-D (BLOQUE) ; U-1 | Correction D | Refus (droit restreint) | inchangé | — | PASS si refus |
| NEG-43 | FX-D ; U-2 | Correction D `−1` | Accepté | `D6` → `D5` | restreint | PASS |
| NEG-44 | FX-E (RAPPELE) ; U-2 | Correction D `−1` | Accepté ; **le lot reste RAPPELE** | `D10` → `D9` | statut inchangé | PASS si statut identique |
| NEG-45 | FX-A ; U-1 | Correction qui ramène le lot entier à **0** (attendu 10, compté 0) | Refus (droit restreint, V2-R5 c) | `D10` inchangé | — | PASS si refus |
| NEG-46 | FX-A ; U-2 | Même action | Accepté ; signalé « mise à zéro » | `D10` → `D0` | restreint + marque de mise à zéro | PASS si marquée |
| NEG-47 | FX-F ; U-1 | Mise à zéro du **produit** (tous lots/états) | Refus sans droit restreint | inchangé | — | PASS |
| NEG-48 | FX-J (ARCHIVE) ; U-2 | Toute correction | Refus produit archivé (inchangé) | inchangé | — | PASS |
| NEG-49 | Produit INACTIF ou A_VALIDER avec stock ; U-1 | Correction D | **Accepté** (comportement actuel conservé) | variable | idem | PASS si identique à l'existant |

### 2.6 Rapport d'écarts et rapprochement (V2-R5 d/e, H-4)

| Id | Préconditions | Action | Résultat attendu | PASS / FAIL |
|---|---|---|---|---|
| NEG-50 | Plusieurs corrections négatives sur FX-A (NEG-01, 20, 21) | Lire le rapport | Cumul par lot, produit, motif et auteur ; **aucun seuil** appliqué (H-4) | PASS si cumuls exacts |
| NEG-51 | Corrections négatives cumulées > quantité reçue du lot (construit en fixture) | Lancer le contrôle de rapprochement | Le lot est signalé | PASS si signalé ; FAIL si silence |
| NEG-52 | Corrections en C/Q | Lire le rapport | Marquage « droit restreint » visible | PASS |

### 2.7 V-5 — Distinction correction négative vs sortie de QUARANTAINE (DP1-R7)

Objectif : prouver qu'une correction négative en QUARANTAINE **n'est pas** une sortie de QUARANTAINE déguisée.

| Id | Préconditions | Action | Résultat attendu | Stock | Audit | PASS / FAIL |
|---|---|---|---|---|---|---|
| NEG-60 | FX-B ; U-2 | Correction négative Q `−1` **avec un motif de sortie** (CASSE) | Refus (V2-R5 a) | `Q2` inchangé | — | PASS si refus |
| NEG-61 | FX-B ; U-2 | Sortie **manuelle** depuis Q (motif CASSE) | Refus `stock.state_exit_deferred` | inchangé | — | PASS si code exact |
| NEG-62 | FX-B ; U-2 | Correction négative Q `−1` ECART_COMPTAGE (vrai écart) | Accepté ; classée « correction » (source ≠ sortie), **hors** sorties nettes | `Q2` → `Q1` | motif ECART_COMPTAGE | PASS si absente du calcul « sorties nettes » et présente dans « corrections nettes » |
| NEG-63 | FX-B ; U-2 | Correction Q ramenant Q à 0 (`−2`) | Accepté **mais** signalé (mise à zéro d'un état) et cumul visible | `Q2` → `Q0` | marque + restreint | PASS si signalé |
| NEG-64 | FX-B ; U-2 | Séquence : correction Q `−2` puis sortie manuelle D | Aucun lien avec le motif de destruction ; la correction reste au rapport d'écarts | — | — | PASS |
| NEG-65 | FX-B ; ADM | Levée de quarantaine `Q→D` (gouvernance) | Distinct d'une correction : en-tête de passage + 2 mouvements `stock.transfer` | `Q2` → `D+2` | nom de l'autorité | PASS si deux mouvements de somme nulle |
| NEG-66 | FX-B ; U-2 | Tentative de levée de quarantaine avec `stock.adjust.nondisponible` | Refus : seul ADM a la gouvernance (Q-5) | inchangé | refus tracé | PASS si refus |
| NEG-67 | FX-B ; U-3 | Passage `Q→D` avec `stock.state.transfer` seul | Refus (levée = gouvernance, S-5) | inchangé | — | PASS si refus |
| NEG-68 | Contrôle d'architecture | Chercher toute écriture d'un `CORRECTION` négatif en bucket P | Aucune (code et base) | — | — | PASS si 0 occurrence |

### 2.8 Réservations et corrections (V2-R7, Q-7)

| Id | Préconditions | Action | Résultat attendu | Stock | Audit | PASS / FAIL |
|---|---|---|---|---|---|---|
| NEG-70 | FX-G (R1 = 6) ; U-1 | Correction D `−7` (D passe à 3 < 6) | **Accepté** (jamais bloquée par réservation) ; R1 devient « non honorable » | `D10` → `D3` | audit liste R1 (identifiant, comptoir, auteur, quantité) | PASS si accepté et R1 listée |
| NEG-71 | FX-G | Lire la liste des réservations | Indicateur « non honorable » + cause | — | — | PASS |
| NEG-72 | FX-G ; U-1 | Correction positive qui ramène D à 10 | Indicateur disparaît (calculé) | `D3` → `D10` | — | PASS |

### 2.9 Concurrence et atomicité

| Id | Préconditions | Action | Résultat attendu | PASS / FAIL |
|---|---|---|---|---|
| NEG-80 | FX-A | Deux corrections simultanées (attendu 10 chacune) | Une seule réussit, l'autre `stock.level_changed` | PASS si D final cohérent |
| NEG-81 | FX-A | Correction simultanée avec une vente de la dernière quantité | Aucun négatif ; un seul aboutit | PASS |
| NEG-82 | FX-A | Échec d'écriture de l'audit simulé | Aucun mouvement persistant (atomicité) | PASS si 0 mouvement |

**Sensibilité (E-6)** : NEG-06, 10-13, 17, 31, 40, 45, 60-61, 67 doivent être **rouges** avant R4.

---

## 3. Q-10 et C-P82-2 — Contre-passations de réception (préfixe REV)

Références : V2-R6, V2-R7, V2-R9, V2-R10 ; INV-5 ; Q-10 DÉCIDÉ ; C-P82-2 (affichage explicite des quantités et du motif de refus, `purchase.reversal_state`). Code existant : `purchasing/service.ts` l. 1277-1380 (`purchase.reversal_exceeds`, `stock.insufficient`).
Quantité reprenable = min(reçu − déjà repris ; DISPONIBLE du lot). La reprise s'écrit en **D** du lot d'origine.

### 3.1 Contre-passation intégrale

| Id | Préconditions | Action | Résultat attendu | Stock avant → après | Audit | PASS / FAIL |
|---|---|---|---|---|---|---|
| REV-01 | FX-H (REC-1 = 10, `D10`) ; U-4 | Annulation intégrale | Accepté ; 1 `CORRECTION −10` source `purchasing.reversal`, bucket D, lié à la ligne de réception | `D10` → `D0` | E-4 + réception, ligne, quantité reprise | PASS si D=0 et mouvement lié à la ligne |
| REV-02 | FX-H avec **réception sur 2 lignes** (6 + 4, lots distincts, tous D) | Annulation intégrale | Accepté pour les deux lignes, atomique | `D6/D4` → `D0/D0` | 2 lignes d'audit ou 1 avec détail | PASS si tout ou rien |
| REV-03 | FX-I (`D4 C3 Q2 P1`) ; U-4 | Annulation intégrale | **Refus** `purchase.reversal_state` (une part hors D) | inchangé | refus tracé | PASS si refus **et** 0 mouvement |
| REV-04 | REC-2 sur 2 lignes : ligne 1 `D6`, ligne 2 `D2 Q2` | Annulation intégrale | Refus **de toute la réception** (même garde), aucune ligne reprise | inchangé | — | PASS si ligne 1 non reprise (atomicité) |
| REV-05 | FX-H déjà partiellement reprise (3) | Annulation intégrale | Comportement existant conservé : refus (« une opération existe déjà ») | inchangé | — | PASS si identique à l'existant |
| REV-06 | FX-H ; **U-0** | Annulation intégrale | Refus droit | inchangé | refus tracé | PASS |

### 3.2 Contre-passation partielle

| Id | Préconditions | Action | Résultat attendu | Stock avant → après | Audit | PASS / FAIL |
|---|---|---|---|---|---|---|
| REV-10 | FX-H ; U-4 | Reprise de 4 | Accepté | `D10` → `D6` | reprise cumulée = 4 | PASS |
| REV-11 | suite de REV-10 | Reprise de 6 | Accepté ; reçu = repris | `D6` → `D0` | cumul 10 | PASS |
| REV-12 | suite de REV-11 | Reprise de 1 | Refus `purchase.reversal_exceeds` (règle existante) | `D0` | — | PASS si code existant conservé |
| REV-13 | FX-I (`D4 C3 Q2 P1`, reçu 10) ; U-4 | Reprise de 4 | Accepté (≤ D) | `D4` → `D0` ; C, Q, P inchangés | idem | PASS si C/Q/P identiques |
| REV-14 | FX-I | Reprise de **5** (> D=4, ≤ reçu) | Refus `purchase.reversal_state` avec détail | inchangé | refus tracé | PASS si refus |
| REV-15 | FX-I | Reprise de 4 puis 1 | 2e reprise refusée (`D0`) `purchase.reversal_state` | `D0 C3 Q2 P1` | — | PASS |
| REV-16 | FX-I après réintégration de C en D (3) par ADM/U-3 | Reprise de 7 | Accepté (`D7`) | `D7 C0 Q2 P1` → `D0 …` | — | PASS : « réintégrer puis reprendre » fonctionne |
| REV-17 | FX-I après levée de Q (ADM) | Reprise de la part levée | Accepté | idem | — | PASS |
| REV-18 | FX-I, part en P | Reprise visant la quantité en P | Refus ; **aucun** chemin ne reprend P (V2-R8) | `P1` inchangé | — | PASS |
| REV-19 | FX-H, reprise de 0 ou négative | Refus de validation | inchangé | — | PASS |

### 3.3 Réservations et lots particuliers

| Id | Préconditions | Action | Résultat attendu | PASS / FAIL |
|---|---|---|---|---|
| REV-20 | FX-G (R1 = 6) ; U-4 | Reprise de 8 (D → 2 < 6) | **Accepté** (jamais bloquée par réservation, V2-R7) ; R1 non honorable ; audit liste R1 | PASS |
| REV-21 | Lot BLOQUE ou RAPPELE, part D | Reprise dans D | Accepté (un retour au fournisseur d'un lot rappelé reste possible) ; statut inchangé | PASS |
| REV-22 | Lot périmé, part D | Reprise dans D | Accepté | PASS |
| REV-23 | Réception de produit **sans lot** (FX-F) | Reprise dans D | Accepté ; `lot_id` nul | PASS |
| REV-24 | Deux reprises concurrentes de la dernière quantité D | Une seule réussit ; jamais de négatif | PASS |
| REV-25 | Vente concurrente de la dernière quantité D et reprise | Un seul aboutit | PASS |

### 3.4 C-P82-2 — Affichage explicite (service et interface)

| Id | Préconditions | Action | Résultat attendu | PASS / FAIL |
|---|---|---|---|---|
| REV-30 | FX-I ; U-4 | Refus REV-14 : lire la **réponse du service** | `purchase.reversal_state` + détail **par état** : reprenable (min), D, C, Q, P, reçu, déjà repris | PASS si les 7 valeurs présentes et exactes |
| REV-31 | idem | Lire le message **côté interface V1 (on-premise)** | Les quantités et le motif sont **affichés** (aujourd'hui l'interface ne remplace que `{n}` : **à adapter**, NON VÉRIFIÉ) | PASS si chaque valeur visible ; FAIL si message générique |
| REV-32 | idem | Même lecture en **V3 (React)** | idem | idem |
| REV-33 | idem | FR, EN, AR (RTL) | Message traduit ; ordre des nombres lisible en RTL | cf. §5 (V6-xx) |
| REV-34 | FX-I ; refus intégral | Message de refus d'annulation intégrale | Indique **quelles lignes** ont une part hors D, et la marche à suivre (réintégrer, lever, ou attendre le lot ultérieur pour P) | PASS si lignes nommées |
| REV-35 | Aucune part hors D | Écran de reprise | Affiche la quantité reprenable avant validation (pas seulement à l'échec) — **PROPOSITION A-T3** | PASS si visible |
| REV-36 | Refus | Vérifier qu'aucun message ne suggère une sortie de PERTE ou de QUARANTAINE | Texte conforme DP1-R7 | PASS |

### 3.5 Traçabilité et historique

| Id | Action | Résultat attendu | PASS / FAIL |
|---|---|---|---|
| REV-40 | Après REV-01 | Aucune ligne antérieure modifiée ; le mouvement initial intact (INV-1, V2-R9) | PASS si empreinte du journal antérieur identique |
| REV-41 | Après REV-10 | `reason_code` nul **autorisé** pour la reprise si son motif est porté par la ligne d'opération (**à trancher en R1** : PROPOSITION) | PASS selon la règle retenue |
| REV-42 | Identité CAT-11 après reprise | La reprise compte en **corrections nettes** (V-3), jamais en sorties nettes | PASS (voir §6) |
| REV-43 | Après refus | 0 mouvement, 0 en-tête, audit de refus présent | PASS |

**Sensibilité (E-6)** : REV-03, 04, 14, 15, 18, 30 doivent être rouges avant R4 (aujourd'hui seule la garde « niveau du lot » existe).

---

## 4. Q-11 et C-P82-3 — Passage en PERTE (préfixe PER)

Références : DP1-R4, V2-R8, INV-6, S-5, S-11, S-12 ; Q-11 DÉCIDÉ ; C-P82-3 (confirmation explicite avant PERTE ; quantité et conséquences irréversibles affichées).

### 4.1 Passage en PERTE

| Id | Préconditions | Action | Résultat attendu | Stock avant → après | Audit | PASS / FAIL |
|---|---|---|---|---|---|---|
| PER-01 | FX-A ; U-3 | Passage `D→P` de 2, motif valide, **confirmation explicite** | Accepté ; en-tête immuable + 2 mouvements `stock.transfer` de somme nulle | `D10` → `D8 P2` | passage : quantité, états, motif, auteur, **marque « irréversible »** | PASS si 2 mouvements et en-tête |
| PER-02 | FX-B ; U-3 | `C→P` de 1 | Accepté | `C3` → `C2`, `P1` → `P2` | idem | PASS |
| PER-03 | FX-B ; U-3 | `Q→P` de 1 | Accepté | `Q2` → `Q1`, `P` +1 | idem | PASS |
| PER-04 | FX-A ; U-3 | `D→P` **sans** confirmation | Refus nommé (confirmation requise) ; 0 mouvement | inchangé | — | PASS si refus ; FAIL si accepté |
| PER-05 | FX-A ; U-3 | Confirmation présente mais **quantité différente** de celle affichée (écart entre aperçu et requête) | Refus (la confirmation porte la quantité) — **PROPOSITION** | inchangé | — | PASS si refus |
| PER-06 | FX-A ; U-3 | Quantité > D | Refus (aucun négatif par état) | inchangé | — | PASS |
| PER-07 | FX-A ; **U-1** (sans droit de passage) | `D→P` | Refus droit | inchangé | refus tracé | PASS |
| PER-08 | FX-A ; U-0 | `D→P` | Refus | inchangé | — | PASS |
| PER-09 | FX-A ; U-3 | Motif < 3 ou > 300 car. | Refus validation | inchangé | — | PASS |
| PER-10 | FX-G (R1 = 6) ; U-3 | `D→P` de 8 | **Accepté** (V1-R1) ; R1 non honorable ; audit liste R1 | `D10` → `D2 P8` | idem | PASS |
| PER-11 | FX-C (périmé) ; U-3 | `D→P` | Accepté (voie de retrait compatible DP1-R7) | `D10` → `D0 P10` | idem | PASS |
| PER-12 | FX-D (BLOQUE) ; U-3 | `Q→P` | Accepté ; statut inchangé | variable | — | PASS |
| PER-13 | FX-F (sans lot) ; U-3 | `D→P` | Accepté ; `lot_id` nul | `D8` → … | — | PASS |
| PER-14 | FX-A ; U-3 | Passage depuis un état **identique** (`D→D`) | Refus (matrice) | inchangé | — | PASS |

### 4.2 Refus de retour depuis PERTE (irréversibilité)

| Id | Préconditions | Action | Résultat attendu | Stock | PASS / FAIL |
|---|---|---|---|---|---|
| PER-20 | FX-B (`P1`) ; ADM | Passage `P→D` | **Refus** (matrice : PERTE vers rien), y compris pour ADM | `P1` inchangé | PASS si refus nommé |
| PER-21 | idem | `P→C`, `P→Q` | Refus | inchangé | PASS |
| PER-22 | idem | **Insertion directe** en base d'un `CORRECTION +1` en D et `−1` en P rattachés à un faux en-tête `P→D` | Refus **par la base** (`transfers_matrix`, INV-6) | inchangé | PASS si refus base |
| PER-23 | idem | Correction négative en P | Refus (cf. NEG-17) | inchangé | PASS |
| PER-24 | idem | Contre-passation visant P | Refus (cf. REV-18) | inchangé | PASS |
| PER-25 | idem | Sortie manuelle depuis P | Refus `stock.state_exit_deferred` | inchangé | PASS |
| PER-26 | idem | Correction positive ciblant P | Refus (INV-4) | inchangé | PASS |
| PER-27 | Contrôle d'architecture | Chercher tout mouvement négatif en P ou tout passage `P→x` dans le code | 0 occurrence | — | PASS |

### 4.3 Indicateur « en attente de destruction » (C2, H-3)

| Id | Action | Résultat attendu | PASS / FAIL |
|---|---|---|---|
| PER-30 | Après PER-01 | Indicateur par produit et par lot = Σ P ; ancienneté = plus ancien passage en P | PASS si exact |
| PER-31 | Plusieurs passages à des dates différentes (horloge de test) | Ancienneté = la plus ancienne | PASS |
| PER-32 | Aucune destruction automatique après N jours simulés | P inchangé | PASS |
| PER-33 | Archivage d'un produit avec `P>0` | Refus (C1, S-10) ; message détaillant la répartition par état | PASS |
| PER-34 | Alertes de péremption | Les quantités en P apparaissent avec leur **état** (DP1-R11) | PASS |

### 4.4 C-P82-3 — Confirmation et informations obligatoires (service + interface)

| Id | Préconditions | Action | Résultat attendu | PASS / FAIL |
|---|---|---|---|---|
| PER-40 | Écran de passage vers P (V1 puis V3) | Choisir « Perte » | Avant validation : **quantité exacte**, produit, lot, état d'origine, **texte d'irréversibilité** (« ne peut pas être annulé ; aucune remise en vente ; destruction réalisée plus tard »), motif | PASS si les 5 éléments sont affichés ; FAIL si l'un manque |
| PER-41 | idem | Bouton de validation | **Deux actions distinctes** (choix puis confirmation explicite) ; pas de validation par Entrée seule — PROPOSITION | PASS |
| PER-42 | idem | Annuler à l'étape de confirmation | Aucun mouvement | PASS |
| PER-43 | idem | Pour un doute : message orientant vers **EN_CONTROLE** (réversible) | Texte présent | PASS |
| PER-44 | idem | Confirmation affichée en FR, EN, AR (RTL) et mobile | cf. §5 | cf. §5 |
| PER-45 | Après validation | Résultat affiché : quantités avant/après par état | PASS |
| PER-46 | Audit | Contient la marque d'irréversibilité, la quantité confirmée et l'auteur | PASS |
| PER-47 | Écran de levée/réintégration | **Aucune** option « depuis Perte » proposée | PASS |

**Sensibilité (E-6)** : PER-04, 05, 20, 21, 22, 25 doivent être rouges avant R4.

---

## 5. V-6 — Écrans, messages, langues, RTL, mobile (préfixe V6)

Références : C-P82-2, C-P82-3, V1-R6, S-10, DP1-R11 ; rapports P60 (RTL V3, fiche catalogue) et P65 (responsive V1).
**Principe** : ces vérifications sont **NON EXÉCUTÉES** en P84. Elles relèvent de la phase 2 (navigateur, arbitrage A-T2). Sans phase 2, elles restent **NON VÉRIFIÉES** à G6 et le rapport de réalisation le dit explicitement (conformément à C7).

### 5.1 Déjà vérifié (acquis hérité, ne pas refaire à l'identique)

| Acquis | Source | Portée exacte | Limite |
|---|---|---|---|
| RTL V3 : règle limitée à `html[dir="rtl"]`, FR/EN inchangés, AR sans débordement horizontal | P60 | **fiche catalogue** (4 fiches, 13 mesures, bureau 1440 et mobile 390) | Chromium unique ; point final d'un texte latin en RTL non corrigé ; pas de relecture arabophone |
| Responsive V1 : 918 mesures, 135 combinaisons (3 langues, 6 rubriques, largeurs 320 à 1440) ; Stock bureau : écart de rendu d'amplitude ≤ 9 (bruit) | P65 | écrans **V1 existants** (dont Stock) | lisibilité des champs et boutons à 320/360/370/390 non revue visuellement |
| Clés i18n : test d'égalité des clés entre langues | `tests/p69/i18n_keys.test.ts` | **clés présentes**, pas leur rendu | ne vérifie pas le contenu des phrases |

### 5.2 Non vérifié à ce jour (à tester)

| Domaine | Statut |
|---|---|
| Écrans V3 stock, ventes, commandes en RTL | **NON VÉRIFIÉ** (P60 §limites) |
| Tout écran **nouveau ou modifié** par 0022 (passage d'état, confirmation PERTE, détail de contre-passation, rapport d'écarts, indicateurs, motifs) | **NON TESTÉ** (n'existe pas) |
| Lisibilité arabophone native | **NON VÉRIFIÉ** (jamais réalisée) |
| Navigateur autre que Chromium | **NON VÉRIFIÉ** |
| Remplacement de variables dans les messages d'achats (seul `{n}` géré aujourd'hui) | **OBSERVATION** : à adapter (REV-31) |

### 5.3 Écrans et opérations à couvrir

| Réf. | Écran / opération | V1 | V3 | Nouveauté 0022 |
|---|---|---|---|---|
| UI-1 | Stock : niveaux **par état** (D/C/Q/P) par produit et lot | oui | oui | oui |
| UI-2 | Passage d'état (formulaire, quantité, motif, confirmation) | oui | oui | oui |
| UI-3 | Confirmation de passage en PERTE (C-P82-3) | oui | oui | oui |
| UI-4 | Statut de lot (blocage, rappel, levée ADMIN) | oui | oui | oui |
| UI-5 | Correction d'inventaire par état + motifs normalisés + droit restreint | oui | oui | modifié |
| UI-6 | Sortie manuelle : motifs normalisés, dérogation ADMIN, refus depuis Q/P | oui | oui | modifié |
| UI-7 | Contre-passation de réception : détail par état (C-P82-2) | oui | oui | modifié |
| UI-8 | Réservations : indicateur « non honorable » + cause | oui | oui | oui |
| UI-9 | Alertes : péremption avec état ; réservations non honorables ; « en attente de destruction » | oui | oui | modifié |
| UI-10 | Rapport d'écarts (corrections négatives) | oui | oui | oui |
| UI-11 | Archivage refusé : répartition par état | oui | oui | modifié (message) |

### 5.4 Messages et informations obligatoires

| Id | Écran | Contenu obligatoire | PASS si |
|---|---|---|---|
| V6-01 | UI-3 | quantité, produit, lot, état d'origine, texte d'irréversibilité, motif, 2 actions (choisir puis confirmer) | les 6 éléments visibles **avant** validation |
| V6-02 | UI-7 | reprenable, D, C, Q, P, reçu, déjà repris ; lignes concernées (annulation intégrale) ; marche à suivre | 7 valeurs + lignes affichées |
| V6-03 | UI-2 | matrice des passages proposés **limitée** aux transitions valides ; aucune option depuis P ; levée de Q visible seulement pour ADM | options conformes à la matrice et aux droits |
| V6-04 | UI-5 | état visé explicite ; quantité attendue de **chaque** état ; motif normalisé ; mention « droit restreint » quand requis | tous présents |
| V6-05 | UI-6 | refus `stock.state_exit_deferred` expliqué (lot ultérieur) ; refus réservation expliqué ; dérogation ADMIN distincte | messages nommés, pas génériques |
| V6-06 | UI-8 | indicateur et cause (contrôle, quarantaine, lot bloqué/rappelé) ; auteur concerné | présents ; disparaît après retour en D |
| V6-07 | UI-11 | refus d'archivage : D/C/Q/P | répartition chiffrée |
| V6-08 | Tous | aucune clé brute ni code technique affiché à l'utilisateur ; aucun `{variable}` non remplacé | 0 occurrence |
| V6-09 | Tous | cohérence du vocabulaire des quatre états entre V1, V3, rapports | libellés identiques à la table de traduction |

### 5.5 Vérifications de langue, RTL et mobile (à exécuter en phase 2)

| Id | Contrôle | Langues | Largeurs | PASS si |
|---|---|---|---|---|
| V6-10 | Pour **chaque** écran UI-1 à UI-11 : aucun texte manquant, aucune clé brute | FR, EN, AR | 1440 et 390 | 0 clé manquante |
| V6-11 | Direction : `dir="rtl"` en AR, `dir="ltr"` en FR/EN ; mise en page inversée | AR | 1440, 390 | éléments alignés à droite, ordre logique |
| V6-12 | **Nombres, quantités et codes de lot** lisibles en RTL (chiffres, `D4 C3 Q2 P1`, dates) sans inversion trompeuse | AR | 1440, 390 | lecture correcte d'au moins 5 cas de référence |
| V6-13 | Débordement horizontal : `scrollWidth ≤ largeur de fenêtre` | FR, EN, AR | 320, 360, 390, 768 | 0 débordement |
| V6-14 | Boutons de confirmation PERTE et de passage : taille tactile suffisante, non masqués, atteignables au clavier | FR, EN, AR | 390 | cible ≥ 44 px (PROPOSITION de seuil) |
| V6-15 | Tableaux d'états par lot : lisibles sur mobile (défilement interne ou empilement), colonnes D/C/Q/P identifiables | FR, EN, AR | 360, 390 | aucune colonne tronquée |
| V6-16 | Chaînes longues (AR et FR) dans les messages de refus : pas de chevauchement | FR, AR | 360 | 0 chevauchement |
| V6-17 | Régression : écrans existants non modifiés par 0022 (liste P65) | FR, EN, AR | 390, 768, 1440 | mêmes mesures qu'avant |
| V6-18 | Test de clés : égalité des clés FR/EN/AR **étendue** aux nouveaux messages (`tests/p69`) | — | — | PASS automatique |
| V6-19 | Relecture linguistique arabe par un arabophone | AR | — | **NON AUTOMATISABLE** : restera NON VÉRIFIÉ sauf décision PO |

### 5.6 Sans phase 2

Si la phase 2 n'est pas autorisée : V6-18 (test automatique, léger) et V6-01 à V6-09 en **test de composant sans navigateur** (lecture du texte produit) restent possibles ; V6-10 à V6-17 et V6-19 restent **NON VÉRIFIÉS** et listés comme tels au rapport.

---

## 6. V-3 — Identité CAT-11 et formules (préfixe CAT)

Référence : spécifications P82 §8.2 ; DP1-R9 ; **V-3 NON APPROUVÉ** : la formule de la fiche P81 (DP1-R9) est la référence ; la convention du plan P82 (contre-passations = corrections nettes ; retours = déduction des sorties nettes) n'est qu'une proposition de calcul **vérifiée par ces tests, pas décidée**.

| Id | Scénario | Résultat attendu | PASS / FAIL |
|---|---|---|---|
| CAT-01 | Réception 10 seule | reçu 10 = D10 | égalité exacte |
| CAT-02 | Réception 10 puis vente 3 | 10 = D7 + sorties 3 | égalité |
| CAT-03 | Vente 3 puis retour revendable 1 | sorties nettes = 2 ; D8 ; 10 = 8 + 2 | égalité (retour en déduction des sorties) |
| CAT-04 | Contre-passation 4 | corrections nettes = −4 ; 10 − 4 = D6 | égalité (contre-passation en corrections) |
| CAT-05 | Correction négative −2 D | corrections nettes −2 | égalité |
| CAT-06 | Passage D→Q 3 (somme nulle) | aucune incidence sur reçu, corrections nettes ni sorties nettes ; Σ états inchangée | égalité |
| CAT-07 | Régularisation hors commande 5 | comptée dans **reçu** (`stock.regularization`) | égalité |
| CAT-08 | Entrée `stock.manual` historique | comptée dans reçu (compatibilité) | égalité |
| CAT-09 | Produit sans lot | même identité par produit | égalité |
| CAT-10 | Jeu **mixte** (tous cas ci-dessus, états variés) | identité vraie par lot et par produit | égalité |
| CAT-11 | Calcul du **reçu** depuis `purchase_receipt_lines` (source indépendante) vs somme des ENTREE | égaux pour les réceptions | PASS ; écart = signalement |
| CAT-12 | **Lecture alternative** de V-3 (contre-passation en sorties nettes) | l'identité **échoue** sur CAT-04 : preuve que l'écriture choisie est forcée par les données | FAIL attendu documenté |
| CAT-13 | Modèle indépendant en mémoire du test (somme recalculée sans la vue) | égal à la vue | PASS |
| CAT-14 | Lots **antérieurs à 0022** (bucket par défaut D) | identité vraie après migration | PASS |

---

## 7. Migration et livraison (préfixe MIG)

Références : plan P82 §9 (MIG-T1/T2), P84 étude A-T1 (MIG-T3/T4, C1 à C8).

| Id | Objet | Procédure | PASS si |
|---|---|---|---|
| MIG-T1 | Application du candidat sur le jeu 0001-0021 (copie jetable) | migrer, puis contrôles A1-A8 et B1-B22 | égalité exacte des contrôles ; 0 ligne réécrite (INV-9) |
| MIG-T2 | Parité de schéma entre bancs | mémoire, fichiers, D1 local (phase 2) | schémas identiques (comparaison normalisée) |
| MIG-T3 | Chargeur de candidat idempotent | présence simultanée du candidat et du fichier officiel de même identifiant | sommes de contrôle égales sinon arrêt nommé |
| MIG-T4 | Garde de démarrage « niveau de schéma requis » | jeu officiel sans 0022 | refus de démarrer avec erreur nommée |
| MIG-T5 | Base de 0021 avec données (fixtures FX-*) | migrer | mouvements existants valent D ; vues = ancien niveau ; aucun niveau négatif |
| MIG-T6 | Journal antérieur inchangé | empreinte des lignes avant/après (hors colonnes ajoutées) | identique |
| MIG-T7 | Rejeu de la migration | second passage | refus ou no-op documenté (checksum `verifyHistory`) |
| MIG-T8 | Instruction multiple / séparateurs | `hasMultipleStatements`, `--> statement-breakpoint` | conforme |
| MIG-T9 | Retour arrière (copie jetable) | restaurer la copie d'avant | état d'origine, checksums identiques |

---

## 8. Renvoi aux tests du plan P82 (non dupliqués)

| Famille | Contenu | Où |
|---|---|---|
| V2-T1…T10 | corrections, contre-passations, états | plan P82 §9 — **cette matrice les précise** (NEG, REV, PER) |
| V1-S1…S7 | réservations (non honorable, ancienneté, passage, sortie manuelle) | spécifications P82 §4.4 |
| DP2-T1/T2, DP5-T1/T2, C1-T1 | suivi par lot, entrées, archivage | plan P82 §9 |
| Non-régression §9.5 | suites ciblées existantes | plan P82 §9.5 |

**Dénombrement de la matrice** (lignes d'identifiant comptées par commande en P84) : NEG 57, REV 33, PER 35, V6 19, CAT 14, MIG 9, soit **167 scénarios**. Les familles V1-S, V2-T, DP2/DP5/C1 du plan P82 s'y ajoutent sans doublon.

---
