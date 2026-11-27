# PHARM'OS — P86
## Plan unique de réalisation STOCK (migration 0022, R7a, G6)

**Date de référence :** 25/11/2026.
**Nature :** plan **documentaire**. Rien n'est lancé, créé ni exécuté. Aucune migration 0022 n'existe ; `compose.ts` n'est pas modifié.
**Statut :** PROPOSITION de plan, **sous réserve de l'arbitrage du PO** sur `docs/P86_ARBITRAGE_CONSOLIDE_13_POINTS.md`. Les recommandations de ce dossier (P86-1 à P86-13) sont prises ici **comme hypothèse de travail** ; le §13 indique ce qui change si le PO en modifie certaines. **Aucune autorisation de réaliser n'est donnée par ce document** (§14).
**Sources :** P81 (registre, plan 0022), P82 (spécifications, plan), P83 (addendum), P84 (étude, matrice, dossier final), P85 (addendum), P86 (arbitrage). P81 à P85 ne sont pas modifiés.

---

## 1. Principes directeurs

1. **Un seul ensemble livrable** (A1-K5) : candidat 0022, code, tests adaptés, R7a, manifeste. Aucune version intermédiaire promouvable.
2. **Le dossier officiel `migrations/pharmacy/` reste inchangé jusqu'à G6** (CL-1, condition d'arrêt 10). Le candidat vit hors du dossier officiel (DP6-R1).
3. **Gel de livraison de R4a à G6** (A1-M2) : aucune copie de version ni construction depuis le dépôt (CL-2, condition d'arrêt 11).
4. **Rien n'est appliqué** à la DEMO ni à une base réelle (G7 hors mission, DP6-R2/R3) ; aucune lecture de la DEMO, de ses copies de version, de ses bases ni de fichiers de codes.
5. **Phase 1 seule** par défaut (bancs mémoire et fichiers) ; phase 2 SUSPENDUE sauf porte PC-1 (§8.3).
6. **Aucun PASS sans exécution** (arbitrage §7.5).
7. **Arrêt au premier écart** : on consigne, on ne répare pas, on informe le PO (DP6-R5, R6).
8. **Aucune opération Git manuelle** ; la sauvegarde automatique n'est jamais une preuve de livraison (P84 §2.5).
9. **Une exécution lourde à la fois**, avec relevé disque/mémoire avant et après (A2-K1).

---

## 2. Séquencement complet

```
R0 ─► R1 ─► R2 ─► R3 ─► R4a ══(gel)══► R4b ─► R4c ─► R4d ─► R4e ─► R7a ─► R5 ─► PC-1 ─► R6 ─► [G6 : décision PO distincte] ─► (G7 hors mission)
```

| Lot | Contenu | Phase de test | Avant le gel ? | Porte de sortie |
|---|---|---|---|---|
| **R0** | Relevé de départ, ligne de base de non-régression, seuils documentés (P86-4), manifeste M0 | 1 | oui | PR-0 |
| **R1** | Adaptation des 3 tests (P86-2), noms du manifeste (P86-3), relevé des 20 lectures `SUM(delta)`, photographie 0021, tableau CAT-11 (P86-13), format de la charge utile de refus, fixture, table MIG-T3/T4 | 1 | oui | PR-1 |
| **R2** | Candidat `0022_stock_etats.sql` hors dossier officiel, chargeur de candidat idempotent dans les bancs de test, premier essai sur copie jetable (levée de O-7/O-8 dans la limite du banc) | 1 | oui | PR-2 |
| **R3** | Tests rouges : MIG-T1/T3/T4 (A1-T*), ST-01, ST-04, CAT-10, CAT-11, NEG/REV/PER en échec attendu | 1 | oui | PR-3 |
| **R4a** | Calcul partagé des niveaux et FEFO unique **sans changement de comportement** ; **début du gel** | 1 | **dernier lot avant gel** | PR-4a |
| R4b | Passages d'état, statut de lot, droits, audits, routes | 1 | non | PR-4b |
| R4c | Sorties manuelles, réservations par ancienneté | 1 | non | PR-4c |
| R4d | Corrections par état, contre-passation limitée à DISPONIBLE (`purchase.reversal_state`), régularisation | 1 | non | PR-4d |
| R4e | Catalogue (DP2-R1 côté service), C1/C2, alertes de péremption (DP1-R11), garde de démarrage (`compose.ts`, garde seule) | 1 | non | PR-4e |
| **R7a** | Deux messages d'interface, V1 et V3 (P86-9, P86-8 lecture 2), clés FR/EN/AR, tests R7a-S* | 1 | non (dans le gel) | PR-7a |
| **R5** | Non-régression ciblée, contrôles d'intégrité, **empreinte du candidat figée** (C3/CL-6) | 1 | non | PR-5 |
| **PC-1** | Porte de capacité : exécution de la phase 2 **seulement** si la capacité a été décidée et fournie par le PO ; sinon report consigné | 2 ou report | non | PR-C |
| **R6** | Rapport de réalisation, manifeste M1 puis M2, liste « NON EXÉCUTÉ / NON VÉRIFIÉ » en tête | 1 | non | PR-6 |
| **G6** | **Décision PO distincte** : promotion (un déplacement de fichier + code + tests) | — | — | décision écrite |
| G7 | Application DEMO/base réelle | hors mission | — | NON AUTORISÉ |

**Lecture du gel.** Le gel couvre R4a → G6. Les modifications de **tests** de R1 et le **chargeur** de R2 interviennent **avant** le gel ; R7a est **dans** la fenêtre (P86-11) et couvert par le manifeste.

---

## 3. Détail des lots

### R0 — Relevé de départ et ligne de base

- **Entrées :** autorisation explicite distincte (§14). Aucune action avant le relevé.
- **Travaux :**
  1. Relevé Git (HEAD, `git status`, 0 tag, 0 stash), empreintes de `src`, `migrations`, `tests`, `tools`, `ui/public/on-premise`, `ui/saas-src/src`, `package.json`, `tsconfig.json`, des documents P81 à P86 (méthode d'empreinte agrégée de P85).
  2. Relevé ressources : disque, mémoire disponible, tmpfs, DEMO (PID et `/health` seulement).
  3. **Documentation des seuils** (A2-K3) : valeurs, justification, source du relevé ; planchers P84 (disque d'arrêt 800 000 Ko ; mémoire d'arrêt 350 Mo ; lancement d'une suite ≥ 500 Mo) non assouplis (P86-4).
  4. Vérifications (a) à (d) du `TMPDIR` dédié (A2-K2) : espace disponible, permissions, persistance, risque de saturation ; **fixer la taille maximale et la valeur d'arrêt avant création** ; création avec `test ! -e`, hors dépôt, mode restrictif ; si la marge est négative, **phase 1 non lancée (BLOQUÉ)**.
  5. **Ligne de base de non-régression** : liste des fichiers de test de la non-régression ciblée (P82 plan §9.5) et **nombre de tests réussis par fichier**, suite par suite, séquentiellement, avec journaux hors dépôt.
  6. **Manifeste M0** (empreintes de l'état de départ).
- **Livrables :** relevé R0, seuils documentés, ligne de base chiffrée, M0.
- **Porte PR-0 :** relevé complet ; seuils documentés ; ligne de base **verte** (une base rouge n'est pas masquée : elle est consignée et soumise au PO, condition 6).

### R1 — Préparation (tests, noms, relevés)

- **Travaux :**
  1. Adaptation de `schema_parite`, `p33`, `p34` (nombre calculé ; identifiant « en avance » éloigné), **version valable avant et après promotion** ; **variante négative** démontrant que le test échoue si l'identifiant simulé devient connu ; vérification que les trois tests donnent les **mêmes résultats** que la ligne de base sur le jeu 0021 (aucune assertion relâchée).
  2. Fixation des noms (P86-3) : manifeste, format d'empreinte, constante de niveau requis, nom de l'erreur de refus (distincte de `migration.unknown_applied` et de `tenant.migrations_pending`) ; table de correspondance MIG-T3/MIG-T4 (T-14).
  3. Relevé des **20 lectures** `SUM(delta)` (P81 plan §3.5) et photographie des sorties sur le jeu 0021 (valeurs de référence pour R4a).
  4. **Tableau CAT-11 cas par cas** (réception, contre-passation, retour client, régularisation, correction) (P86-13) ; calcul du côté « reçu » depuis `purchase_receipt_lines`.
  5. Format de la charge utile `purchase.reversal_state` (A3-L1) : champs minimaux (demandée, reprenable, DISPONIBLE, EN_CONTROLE, QUARANTAINE, PERTE, reçu, déjà repris ; lignes concernées pour l'annulation intégrale) ; paramètre de confirmation de PERTE portant la quantité.
  6. Outillage de test d'interface : confirmation de ce qui est **réellement atteignable sans navigateur** (T-7) ; liste ferme de ce qui passe en NON VÉRIFIÉ.
  7. Fixtures FX-A…FX-J (matrice P84) et acteurs U-0…U-4, ADM.
  8. Confirmation de la table des constats : écarts avec P86 consignés (aucun relâchement).
- **Livrables :** fichiers de test adaptés, rapport R1 (modifications listées et justifiées), noms et formats, tableaux.
- **Porte PR-1 :** ligne de base inchangée après adaptation ; noms figés ; `migrations/` identique à M0.

### R2 — Candidat 0022 et chargeur

- **Travaux :**
  1. Écriture de `pharmos/migrations_candidates/pharmacy/0022_stock_etats.sql` (répertoire créé avec `test ! -e`), **additive et progressive** (aucun `DROP`, aucune reconstruction de table, aucun `UPDATE`), conforme à P82 : colonne `bucket`, `stock_state_transfers`, `stock_reason_codes`, `stock_lot_status_events`, `stock_issue_overrides`, vues `stock_levels`, `stock_lot_received`, `stock_reservations_state`, déclencheurs (INV-1…INV-10), droits (+5 : 4 métier + 1 gouvernance).
  2. **Chargeur de candidat idempotent** dans `tests/support/env.ts` : un candidat dont l'identifiant existe déjà dans le dossier officiel n'est pris qu'**une fois**, avec **vérification d'égalité de somme de contrôle** ; somme différente → arrêt nommé (A1-T3a/b/c).
  3. **Premier essai** sur copie jetable (banc mémoire puis fichiers, jamais D1) : migration sur 0001-0021, contrôles A1-A8, B1-B22, rejouabilité ; **levée de O-7 et O-8 dans la limite du moteur du banc** (D1 réel NON VÉRIFIÉ).
  4. Contrôle statique de portabilité (MIG-T8 : instructions multiples, séparateurs).
- **Porte PR-2 :** contrôles verts ; `migrations/pharmacy/` identique à M0 ; chargeur : A1-T3a/b/c PASS ; aucune base réelle ouverte.

### R3 — Tests rouges d'abord

- **Travaux :** écrire les tests **avant** le code : MIG-T1, MIG-T3, MIG-T4 (garde), A1-T1a…T5c, ST-01, ST-04, CAT-10, CAT-11-a, scénarios NEG/REV/PER/CAT prioritaires ; démontrer qu'ils **échouent pour la bonne raison** (test de sensibilité E-6 ; condition d'arrêt 15 : un test qui passe avant correction ne prouve rien).
- **Porte PR-3 :** liste des rouges attendus et de leur cause ; aucun rouge inattendu dans la base ; empreintes de P81 à P86 inchangées (CL-7).

### R4a — Calcul partagé et FEFO unique (début du gel)

- **Travaux :** unifier les 20 lectures et les deux FEFO derrière un calcul partagé **sans changement de comportement** : toutes les valeurs de référence de R1 sont **retrouvées à l'identique** (sorties photographiées). Aucun changement de schéma exigé par ce lot au-delà du candidat.
- **Début du gel :** juste après PR-4a, M0 est complété par l'empreinte du candidat (premier relevé ; il sera figé en R5).
- **Porte PR-4a :** non-régression des 20 lectures ; MIG-T3 vert ; `migrations/` = M0 ; CL-2 (liste des copies de version inchangée, lecture de noms seulement).

### R4b — Passages d'état, statut de lot, droits

- Passages = deux `CORRECTION` `stock.transfer` de somme nulle + en-tête immuable (INV-7) ; matrice d'états (DP1-R1, R2) ; **PERTE définitive** (DP1-R4) ; **levée de QUARANTAINE** réservée à l'ADMIN (droit de gouvernance, Q-5, H-6) ; blocage ou rappel de lot sans transfert automatique (DP1-R5) ; une réservation n'empêche aucun de ces actes (V1-R1, INV-10, H-5) ; **confirmation de PERTE exigée par le service** (R7a-E8) : le paramètre de confirmation porte la quantité ; refus nommé sans confirmation ou avec quantité différente ; droit de passage **non accordé par défaut** (Q-11) ; compteur de droits adapté (50 → 55) justifié au rapport ; routes (`stockRoutes.ts`).
- **Porte PR-4b :** ST-01, ST-04, CAT-10 verts ; R7a-S04, S05, S06 verts (service) ; tests PER de droit.

### R4c — Sorties manuelles et réservations

- Réservations bloquantes avec dérogation ADMIN motivée et tracée (DP4-R1, R2) ; écart FEFO manuel toujours tracé (DP4-R3) ; périmés et lots bloqués : motifs retrait/destruction (DP4-R4, V1-R10) ; motifs normalisés (DP4-R5, Q-12) ; réservations **par ancienneté** : refus nommé `stock.reservation_unfulfillable` ; sécurité sanitaire prioritaire (DP3-R3).
- **Porte PR-4c :** scénarios NEG correspondants verts ; test d'étanchéité (aucune réservation n'empêche `stock.transfer`).

### R4d — Corrections par état, contre-passation, régularisation

- Corrections d'inventaire par état (DP1-R10, C3) ; **C-P82-1** : une correction négative ne sert pas à faire disparaître un produit périmé, cassé ou destiné à la destruction (ERREUR_SAISIE et AUTRE : V-5 inchangée) ; contre-passation limitée à DISPONIBLE, refus **`purchase.reversal_state`** avec détail par état, y compris annulation intégrale (**toute la réception** refusée si une ligne est concernée, aucune ligne reprise : R7a-E2) ; **comportement existant conservé** pour `purchase.reversal_exceeds` et `stock.insufficient` (R7a-E4) ; régularisation d'entrée hors commande avec justificatif et droit distincts (DP5-R2).
- **Porte PR-4d :** REV-* verts ; R7a-S01 à S03, S07 verts (service) ; charge utile conforme au format de R1.

### R4e — Catalogue, alertes, garde de démarrage

- DP2-R1 **côté service** (P86-V1 : écran non adapté, NON VÉRIFIÉ ; comportement transitoire à faire confirmer) ; DP2-R2/R3 ; DP2-R4 (médicaments existants signalés « à régulariser », aucune conversion automatique) ; C1 (archivage interdit si physique non nul), C2 (indicateur « en attente de destruction », H-3), alertes de péremption sur la quantité physiquement détenue (DP1-R11) ; **garde de démarrage dans `compose.ts` — seule modification autorisée de ce fichier** (F-2, P85 §2.2), avec justification et **empreintes avant/après** au rapport ; MIG-T4 et A1-T1a/b/c/T2 verts.
- **Porte PR-4e :** CAT-* verts ; garde : refus nommé sur jeu sans 0022, démarrage sur jeu avec 0022 ; refus existant `migration.unknown_applied` **inchangé** (A1-T1c) ; diff de `compose.ts` limité à la garde.

### R7a — Restitution minimale (V1 et V3)

- **Périmètre exclusif** (P85 §4.1) : message de refus de contre-passation (MSG-1, MSG-2 : quantités concernées + raison) et confirmation avant PERTE (MSG-3 : quantité + caractère irréversible ; formulation de prudence neutre selon P86-10) ; clés FR/EN/AR ; extension du remplacement de variables **sans changer** les cas existants (R7a-AC5).
- **Hors périmètre, à ne pas toucher :** écrans de catalogue et cases de suivi par lot ; niveaux par état ; formulaire de passage (**aucun point d'entrée vers PERTE**, P86-8 lecture 2) ; MSG-5 à MSG-8 (hors R7a) ; aucun écran de sortie de PERTE (R7a-E9).
- **Contenu :** quantités **exactement** celles de la réponse du service (R7a-AC2, aucun recalcul côté interface) ; aucune clé brute ni `{variable}` non remplacé (AC3) ; aucun message ne suggère une sortie depuis QUARANTAINE ou PERTE (R7a-E3).
- **Tests obligatoires :** R7a-S01 à S07, S10 à S15 (dans la limite de l'outillage de R1), R7a-C01, C03s, V6-18, p69.
- **Contrôles reportables sous verrou :** R7a-C02 à C08 (spécifiés, préparés, **NON VÉRIFIÉS**).
- **Porte PR-7a :** liste des fichiers d'interface modifiés **justifiée** (AC7) ; aucun écran hors périmètre modifié (P86-S2) ; messages existants identiques (S15).

### R5 — Non-régression et empreinte du candidat

- Non-régression ciblée (jamais la suite complète) : mêmes fichiers que la ligne de base R0, mêmes **nombres de tests réussis par fichier**, plus les nouveaux, **0 régression** (C5) ; contrôles d'intégrité A1-A8 et B1-B22 après les scénarios de référence, sur copie jetable (C6) ; **empreinte SHA-256 du candidat consignée** (CL-6) : identique à R6 et à G6 ; parité de schéma mémoire/fichiers (C7a, MIG-T2 phase 1).
- **Porte PR-5 :** tous les contrôles bloquants (B) vrais ; aucune condition d'arrêt déclenchée.

### PC-1 — Porte de capacité (après R5, avant R6)

- Relevé des ressources ; **si et seulement si** le PO a décidé et mis en œuvre une capacité (mémoire supplémentaire, autre hôte) : exécution de la phase 2 (D1 local : MIG-T2 D1, C7b ; navigateur : V6-10 à V6-17, V6-19, R7a-C02 à C07), **une suite à la fois**, arrêts S-1 à S-7, DEMO surveillée. **Sinon** : report consigné (statut NON EXÉCUTÉ avec motif et risque résiduel). Aucune capacité n'est présumée ; aucun benchmark de calibrage.
- **Porte PR-C :** rapport de la porte (exécuté ou reporté), sans effet sur les empreintes.

### R6 — Rapport et manifestes

- Manifeste **M1** (jeu candidat à promouvoir) puis **M2** (empreintes de `src`, `migrations`, `tests`, `ui`, `tools`, documents) établi **après le dernier changement de fichier** (C1) ; contrôles CL-1 à CL-9 ; tableau **un identifiant → un statut → une preuve** pour les 167 scénarios, A1-T*, R7a-S*, R7a-C* ; **comptage** PASS / FAIL / NON EXÉCUTÉ / NON VÉRIFIÉ en tête ; réserves (C7b, R7a-C02 à C08, V6 phase 2) ; verrou d'exposition ; O-5, O-6, O-9, O-10 rappelés ouverts (formulation « livraison du candidat STOCK », jamais « clôture du Lot 3 ») ; V-3 : écriture exacte soumise au PO avec exemples chiffrés ; procédure de **retour arrière** (§10) testée (A1-T4) ; justification de la modification de `compose.ts`.
- **Porte PR-6 :** M2 conforme ; le rapport ne contient **pas** de décision G6.

### G6 — Promotion (décision PO distincte, hors mission de réalisation)

Voir §9 et arbitrage §7. Contenu mécanique **si** le PO décide : déplacement du candidat vers `migrations/pharmacy/` (un fichier, somme égale à CL-6), le code et les tests étant déjà prêts (chargeur idempotent : aucun changement de test) ; contrôle CL-8 (dossier officiel = M0 + exactement un fichier `0022_*.sql`) et CL-9 (non-régression complète identique). **Cette opération elle-même n'est pas autorisée par ce plan.**

---

## 4. Dépendances R7a / R4a / G6 / 0022

| Élément | Dépend de | Est requis par | Remarque |
|---|---|---|---|
| Noms du manifeste, constante, erreur (P86-3) | R1 | R2 (chargeur), R3 (A1-T*), R4e (garde), R6 (M2) | À figer **avant** le gel ; tout changement ultérieur invalide M0 |
| Adaptation de `schema_parite`, `p33`, `p34` (P86-2) | R1 | R2 (le chargeur change le nombre d'identifiants) | Doit précéder le premier chargement du candidat dans les bancs par défaut |
| Chargeur de candidat idempotent | R1 (noms), R2 | R3, tous les lots R4, R5, G6 | Rend G6 sans modification de test (supprime RL-3) |
| Candidat `0022` | R1 (relevés, CAT-11), P82 | R3 à R5 ; G6 | **Figé en R5** (empreinte CL-6). Toute modification après R5 impose de rejouer R5 (P86-S1) |
| Calcul partagé (R4a) | R1 (photographie des 20 lectures) | R4b à R4e | Aucun changement de comportement ; référence = relevé R1 |
| Format de `purchase.reversal_state` | R1 | R4d, R7a | Formats et variables figés en R1 (A3-L1) |
| Confirmation de PERTE côté service | R4b | R7a (composant), R7a-S04 à S06 | La protection existe **avant** toute interface |
| Garde de démarrage (`compose.ts`) | R1 (constante, erreur) ; R2 (candidat chargé) | R4e, G6 | **Seule** modification autorisée de `compose.ts` (F-2) |
| **R7a** | R4d (charge utile), R4e (clés d'interface stabilisées), R4b (confirmation service) | R5, R6, G6 (C9) | Dans la fenêtre de gel ; ne touche **ni** le schéma **ni** `compose.ts` |
| **R5** | tous les lots précédents y compris R7a | PC-1, R6 | Fige l'empreinte du candidat avec R7a présent |
| **G6** | R6, décision écrite du PO | G7 | Mécaniquement : un déplacement de fichier ; aucune modification de code ni de test |
| **0022 promue** | G6 | G7 | G7 : sauvegarde vérifiée, service arrêté, restauration en cas d'échec (P81 plan §8.1) — hors mission |

**Règle de non-contournement.** Aucun lot n'est livrable seul. Aucun lot ultérieur à R4a ne peut être « promu » partiellement (A1-K5).

---

## 5. Contrôles de compatibilité

| Réf. | Contrôle | Quand | Échec = |
|---|---|---|---|
| CP-1 | `migrations/pharmacy/` : même empreinte et même liste de fichiers que M0 (21 fichiers) ; `migrations/control/` inchangé | à chaque porte jusqu'à G6 (CL-1) | arrêt (condition 10) |
| CP-2 | Aucun fichier ne figure sous `~/.pharmos-private/releases/` en plus (lecture de **noms** seulement) ; aucune copie de version construite depuis le dépôt pendant le gel | à chaque porte de R4a à R6 (CL-2) | arrêt (condition 11) |
| CP-3 | Jeu officiel de 21 migrations (construit **dans le test** par filtrage des identifiants < 0022) + code R4 : **refus net nommé** | R3 (rouge), R4e (vert), puis toujours (MIG-T4 / A1-T1a) | arrêt |
| CP-4 | Officiel + candidat : démarre et passe A1-A8, B1-B22 | R2, puis à chaque lot (MIG-T1, MIG-T3) | arrêt |
| CP-5 | Chargeur : candidat identique à l'officiel = un seul exemplaire ; sommes différentes = arrêt nommé | R2, R3, G6 (A1-T3a/b/c) | arrêt |
| CP-6 | Base « en retard » : refus avec niveaux trouvé et requis (A1-T1b) ; base « en avance » : `migration.unknown_applied` **inchangé** (A1-T1c) | R4e, R5 | arrêt |
| CP-7 | Empreinte du candidat figée en R5, identique à R6 et à G6 (CL-6) | R5, R6, G6 | arrêt (RL-5) |
| CP-8 | Documents P81 à P86 : empreintes identiques à M0 pendant R3 à R6 (CL-7) | à chaque porte | arrêt (RL-8) |
| CP-9 | À G6 : dossier officiel = M0 + **exactement un** fichier `0022_*.sql` de somme égale à CP-7 ; 21 précédents inchangés (CL-8) | G6 | refus de G6 |
| CP-10 | Non-régression ciblée : mêmes nombres de tests réussis par fichier que R0, plus les nouveaux, 0 régression (CL-9, C5) | R5 ; G6 | refus de G6 |
| CP-11 | Aucune base existante (DEMO, `.pharmos-private`) ouverte ni migrée ; aucun fichier de codes lu (CL-10) | toujours | arrêt (condition 7) |
| CP-12 | `compose.ts` : diff limité à la garde de démarrage ; empreintes avant/après au rapport | R4e, R6 | arrêt (condition 9) |
| CP-13 | R7a : liste des fichiers d'interface modifiés = liste justifiée ; aucun écran hors périmètre (R7a-AC7) | R7a, R6 | arrêt (P86-S2) |
| CP-14 | Messages existants **identiques** avant/après (R7a-S15) ; cas `purchase.reversal_exceeds` et `stock.insufficient` inchangés (R7a-E4) | R7a, R5 | arrêt |
| CP-15 | Aucun tag, aucun stash, aucun commit manuel | toujours | arrêt (condition 14) |

---

## 6. Matrice des tests obligatoires

**Source des identifiants :** matrice P84 (167 scénarios : NEG 57, REV 33, PER 35, V6 19, CAT 14, MIG 9), A1-T* (P85 §2.4), R7a-S*, R7a-C* (P85 §4.6, §4.7). **Tous spécifiés, non exécutés à ce jour.** Statuts à produire en R6 : PASS / FAIL / NON EXÉCUTÉ / NON VÉRIFIÉ (jamais PASS sans exécution).

| Famille | Identifiants | Phase | Lot de mise en rouge → vert | Obligatoire avant G6 ? |
|---|---|---|---|---|
| Migration, schéma | MIG-T1, T3 (chargeur), T4 (garde), T5, T6, T7, T8, T9 | 1 | R2/R3 → R4e | **Oui (B)** |
| Migration, parité | MIG-T2 mémoire/fichiers (C7a) | 1 | R3 → R5 | **Oui (B)** |
| Migration, parité D1 | MIG-T2 partie D1 local (C7b) | 2 | PC-1 | **Réserve (R)** : NON EXÉCUTÉ si PC-1 reporte |
| Livraison | A1-T1a, T1b, T1c, T2, T3a, T3b, T3c, T4, T5a, T5b, T5c | 1 | R3 → R4e/R6 | **Oui (B)** |
| Niveaux, FEFO, réservations | ST-01, ST-04, CAT-10 | 1 | R3 → R4b/R4c | **Oui (B)** |
| Identité CAT-11 | CAT-11-a et suivants (convention P82, V-3 maintenu) | 1 | R3 → R4e | **Oui (B)** ; **PASS ≠ approbation de V-3** |
| Catalogue | CAT-01…CAT-14 (matrice P84) | 1 | R3 → R4e | **Oui (B)** |
| Sorties et corrections négatives | NEG-1…NEG-57 (dont E-1…E-6, tests de sensibilité) | 1 | R3 → R4b à R4d | **Oui (B)** |
| Contre-passations | REV-1…REV-33 (dont REV-03, 04, 12, 14, 30…36) | 1 | R3 → R4d | **Oui (B)** |
| Passages en PERTE | PER-1…PER-35 (dont PER-04, 05, 20…27 ; PER-40 à PER-47 selon la matrice) | 1 | R3 → R4b | **Oui (B)** pour les scénarios de **service** ; les scénarios au niveau écran (PER-40 à PER-47) : voir R7a ci-dessous |
| Interface, service | R7a-S01…S07 | 1 | R3 → R4b/R4d | **Oui (B)** |
| Interface, composant (chaînes, logique) | R7a-S10…S15 | 1 | R7a | **Oui (B)** dans la limite de l'outillage de R1 ; au-delà : NON VÉRIFIÉ |
| Interface, contrôles | R7a-C01 (clés, textes), C03s (isolation des quantités en arabe, statique) | 1 | R7a | **Oui (B)** |
| Interface, contrôles navigateur | R7a-C02, C03, C04, C05, C06, C07 | 2 | PC-1 | **Réserve (R)** |
| Interface, relecture humaine | R7a-C08 | — | — | **(H)** NON VÉRIFIÉ |
| V6 (restitution) | V6-01…V6-09, V6-18 | 1 | R7a | **Oui (B)** |
| V6 (navigateur, RTL, mobile) | V6-10…V6-17, V6-19 | 2 | PC-1 | **Réserve (R)** |
| Clés FR/EN/AR | p69 (égalité des clés étendue) | 1 | R7a | **Oui (B)** |
| Non-régression ciblée | liste fixée en R0 (plan P82 §9.5) : stock (`ui3`), ventes (`p43`), achats, catalogue (`ui4`, `p19`, `p20`, `p21`), gouvernance (`lot1b`, `ui5`), flotte (`p33`, `p34`) | 1 (D1 : 2) | R0 (base) → R5 | **Oui (B)** hors fichiers D1 ; fichiers D1 = NON EXÉCUTÉ si PC-1 reporte |
| Tests de sensibilité | variante négative des 3 tests adaptés ; E-6 (un test qui passe avant correction = arrêt 15) | 1 | R1, R3 | **Oui (B)** |

**Règles d'exécution (A2-K1).** Une seule suite à la fois ; bancs mémoire puis fichiers ; journaux hors dépôt ; relevé disque et mémoire avant et après ; `setsid -f` pour les suites longues ; arrêt aux conditions S-1…S-7. Les tests D1 (≈ 1,45 Go observés en P80, durée de l'ordre de 6 minutes par fichier) ne sont **pas** lancés en phase 1 (T-5, P86-5).

**Preuve de non-omission.** Le rapport R6 produit un fichier de statuts dont le nombre de lignes égale le total attendu (167 + A1-T* + R7a-S* + R7a-C*), avec un comptage par statut ; un écart est une condition d'arrêt (P86-S5).

---

## 7. Conditions de passage entre phases et portes

| Porte | Condition de franchissement (toutes vraies) | En cas d'échec |
|---|---|---|
| **PR-0** | relevé complet ; seuils documentés ; ligne de base verte ; M0 établi ; marge disque positive pour le `TMPDIR` | ne pas poursuivre ; consigner ; informer le PO |
| **PR-1** | 3 tests adaptés, mêmes résultats que la base, variante négative PASS ; noms figés ; relevés et tableaux produits ; CP-1 | arrêt |
| **PR-2** | candidat essayé sur copie jetable ; A1-A8, B1-B22 exacts ; chargeur A1-T3a/b/c PASS ; CP-1, CP-11 | arrêt (copie défaillante **abandonnée**, jamais réparée) |
| **PR-3** | rouges attendus, causes expliquées, sensibilité démontrée ; CP-8 | arrêt |
| **PR-4a** | 20 lectures retrouvées à l'identique ; CP-1, CP-2 ; **gel déclaré** | arrêt |
| **PR-4b … PR-4e** | tests du lot verts ; CP-1, CP-2, CP-8, CP-11 ; pour R4e : CP-3, CP-6, CP-12 | arrêt |
| **PR-7a** | S01…S07, S10…S15, C01, C03s PASS (ou NON VÉRIFIÉ listé pour la part inatteignable) ; CP-13, CP-14 | arrêt |
| **PR-5** | CP-4, CP-5, CP-7, CP-10 ; A1-A8, B1-B22 ; parité C7a ; aucune condition d'arrêt | arrêt |
| **PR-C** | relevé de capacité consigné ; phase 2 exécutée **ou** report écrit | report (aucun effet sur les empreintes) |
| **PR-6** | M1, M2 établis ; CP-9 vérifiable ; tableau de statuts complet ; réserves et verrou consignés ; rapport sans décision G6 | arrêt |
| **G6** | décision écrite du PO selon l'arbitrage §7.2 | G6 non proposée, ou reportée |

**Passage de la phase 1 à la phase 2.** Uniquement par PC-1, et seulement si : (i) le PO a décidé la capacité (mémoire supplémentaire ou autre hôte) et l'a mise en œuvre ; (ii) la mémoire disponible relevée atteint le seuil de phase 2 documenté en R0 (valeur de départ : ≥ 1 800 Mo) ; (iii) la DEMO répond (`/health`). Sinon : **pas de phase 2**.

---

## 8. Prérequis matériels et opérationnels

### 8.1 Hôte (relevé P86, FAIT VÉRIFIÉ)

`/` : 1 139 636 Ko libres (97 %) ; `/tmp` : tmpfs plein ; mémoire disponible ≈ 644 Mo / 2 039 440 Ko ; pas de swap ; 2 cœurs. Cause de la baisse de 8 308 Ko depuis P85 : non analysée (HYPOTHÈSE).

### 8.2 Prérequis de lancement de la phase 1

| # | Prérequis | Vérification |
|---|---|---|
| PRQ-1 | Seuils documentés avant usage (A2-K3) | R0 |
| PRQ-2 | `TMPDIR` dédié sur disque, créé **après** vérifications (a)-(d) (A2-K2), `test ! -e`, hors dépôt, `umask 077`, taille maximale et valeur d'arrêt fixées avant création | R0 |
| PRQ-3 | Mémoire disponible ≥ 500 Mo au lancement de **chaque** suite (plancher de départ) ; arrêt < 350 Mo | avant/après chaque suite |
| PRQ-4 | Disque libre ≥ 1 000 000 Ko au lancement ; arrêt < 800 000 Ko | avant/après chaque suite |
| PRQ-5 | DEMO active : `/health` `ok` avant et après chaque suite (lecture seule, aucune action) | avant/après |
| PRQ-6 | Journaux hors dépôt, nom unique (`test ! -e`) | chaque suite |
| PRQ-7 | `ulimit -c 0` ; `umask 077` ; aucune commande au premier plan > ~120 s (`setsid -f`) | permanent |
| PRQ-8 | Aucun nettoyage, suppression, déplacement ni réparation de fichier existant (S-6) | permanent |

**Si un prérequis n'est pas satisfait** : la phase 1 **n'est pas lancée** (BLOQUÉ) ; le PO est informé ; rien n'est supprimé pour « faire de la place ».

### 8.3 Porte de capacité PC-1 (détail)

- **Placement :** après R5, avant R6.
- **Déclenchement :** relevé de ressources ; lecture de l'état de décision du PO (capacité décidée et fournie, ou non).
- **Si capacité fournie :** phase 2 par suites séparées (MIG-T2 D1 local, tests D1 de stock, navigateur pour R7a-C02 à C07 et V6-10 à V6-17, V6-19). La V3 doit être **construite** pour être vérifiée en navigateur : cette construction (consommation NON MESURÉE) n'est autorisée qu'**à PC-1**, après relevé, **hors dépôt** (sortie ignorée), et **sans création de copie de version** (CP-2). Arrêts S-1…S-7 ; une suite lourde à la fois ; DEMO surveillée.
- **Sinon :** report écrit, statut NON EXÉCUTÉ, réserve inscrite au rapport.
- **Aucun benchmark de calibrage** n'est autorisé ; aucun réglage système.

### 8.4 Prérequis humains et décisionnels

| # | Prérequis | Statut |
|---|---|---|
| PRH-1 | Arbitrage PO sur les 13 points de l'arbitrage P86 | **EN ATTENTE** |
| PRH-2 | Autorisation explicite distincte de lancer la réalisation (§14) | **EN ATTENTE** |
| PRH-3 | Confirmation (ou non) de P86-V1 (comportement transitoire DP2-R1) avant R4e | **À CONFIRMER** |
| PRH-4 | Relecture arabe (R7a-C08) : arabophone à identifier par le PO | **NON PLANIFIÉE** |
| PRH-5 | Lecture du PO sur l'écriture exacte de CAT-11 (V-3) avant G6 | **À PLANIFIER** |

---

## 9. Conditions de la décision G6 (PROPOSITION, rappel de l'arbitrage §7)

La décision G6 est une **décision écrite et distincte du PO**, jamais comprise dans le rapport de réalisation. Elle ne peut être **proposée** que si :

1. toutes les vérifications **bloquantes (B)** sont PASS avec preuve (commande, résultat, empreinte) ;
2. chaque vérification **réserve (R)** est PASS ou porte « NON EXÉCUTÉ — motif — risque résiduel » ;
3. le **verrou d'exposition** est consigné (aucune copie de version contenant la livraison construite ; G7 non proposée tant que les réserves d'interface ne sont pas levées ou acceptées par écrit) ;
4. la liste « NON EXÉCUTÉ / NON VÉRIFIÉ » figure **en tête** du rapport ;
5. O-5, O-6, O-9, O-10 sont rappelés ouverts ; le texte n'emploie pas « clôture du Lot 3 » ;
6. l'écriture exacte de l'identité CAT-11 (V-3) est soumise au PO avec exemples chiffrés ;
7. M2 est conforme (C1, C2, C3) et le rapport ne contient aucune décision G6.

**Si le PO refuse une réserve** : G6 est reportée ; les vérifications concernées sont rejouées à PC-1 dès que la capacité existe, **sans refaire R0 à R5** tant que les empreintes de M2 restent identiques ; sinon nouveau manifeste.

---

## 10. Retour arrière (A1-K4, P81 plan §8.1)

La migration est **additive et progressive** ; aucune migration descendante n'est livrée. En cas d'anomalie, la copie défaillante est **abandonnée, jamais réparée**.

| Situation | Retour arrière | Vérification |
|---|---|---|
| Essai sur copie jetable (R2, R5) | abandon de la copie ; nouvelle copie après correction du **candidat** (non officiel) ; aucun fichier existant supprimé | A1-T4 : état d'origine retrouvé, sommes de contrôle identiques ; **sensibilité démontrée** (le test échoue si une étape est omise) |
| Défaut du candidat avant G6 | candidat corrigé hors dossier officiel ; **nouvelle empreinte** (CL-6) ; R5 rejoué ; M2 recalculé | CP-7 |
| Défaut du code avant G6 | correction dans le lot concerné ; reprise depuis la porte de ce lot ; empreintes recalculées | portes PR-* |
| G6 refusée ou reportée | rien n'est promu ; le dossier officiel reste à M0 (21 fichiers) | CP-1, CP-9 |
| Après G6 (promotion décidée) | correction par **migration 0023** ; la 0022 n'est **jamais** éditée | décision distincte |
| Application à une base réelle ou à la DEMO (G7, **hors mission**) | sauvegarde vérifiée avant ; service arrêté ; restauration de la sauvegarde en cas d'échec de contrôle ; pas de réparation sur place | hors P86 |
| Retour du **code** après migration | sans danger tant qu'aucun passage d'état n'a été enregistré ; **interdit après** le premier passage ou blocage de lot sans restauration | P81 plan §8.1 |

**Limite (NON TESTÉ).** La procédure ne sera testée (A1-T4) qu'en réalisation, sur copie jetable ; aucune restauration de base réelle n'est envisagée.

---

## 11. Conditions d'arrêt (une seule suffit)

**Action commune :** consigner l'état exact, **ne rien réparer, ne rien supprimer**, informer le PO, ne pas poursuivre sans arbitrage.

### 11.1 Conditions de livraison et d'intégrité (reprise de P84 dossier §13.2)

| # | Condition |
|---|---|
| 1 | Contrôle A ou B1-B22 hors tolérance (égalité exacte) |
| 2 | Ligne perdue, modifiée ou ajoutée dans une table existante par la migration |
| 3 | Somme de contrôle d'une migration déjà appliquée qui diverge |
| 4 | Niveau négatif (produit, lot ou état) |
| 5 | Chaîne d'audit rompue |
| 6 | Régression par rapport à la ligne de base R0 |
| 7 | Toute lecture ou écriture visant la DEMO, un répertoire réel, la copie de version de la DEMO ou un fichier de codes |
| 8 | Disque ou mémoire sous les seuils, ou durée anormale |
| 9 | Réponse du PO manquante ou ambiguë qui change un résultat attendu ; **ou** `compose.ts` à modifier au-delà de la garde |
| 10 | Écriture dans `migrations/pharmacy/` avant G6 |
| 11 | Construction d'une version livrable ou copie de version depuis le dépôt pendant le gel (R4a → G6) |
| 12 | Manifeste M2 non conforme au candidat validé (C3) ou au dossier officiel (C2) |
| 13 | Échec de création de base temporaire (`ENOSPC`, tmpfs plein) sans `TMPDIR` autorisé |
| 14 | Apparition d'un tag, d'un stash ou d'un commit manuel |
| 15 | Test de sensibilité (E-6) qui **passe** avant correction |

### 11.2 Conditions de ressources (reprise de P84 étude §3.6)

| # | Condition |
|---|---|
| S-1 | Disque libre < 800 000 Ko (plancher de départ) |
| S-2 | Mémoire disponible < 350 Mo (plancher de départ) |
| S-3 | `/health` de la DEMO ne répond plus |
| S-4 | Échec de création de base temporaire (`ENOSPC`) |
| S-5 | Une suite dépasse deux fois la durée de R0 pour le même périmètre |
| S-6 | Besoin de supprimer, déplacer ou réparer un fichier existant |
| S-7 | Apparition de tags, de stashes ou d'un commit manuel |

Valeurs de seuil : **documentées en R0** (P86-4), jamais assouplies sans accord du PO.

### 11.3 Conditions ajoutées par P86 (PROPOSITION)

| # | Condition |
|---|---|
| P86-S1 | R7a exige une modification de schéma ou du candidat après R5 |
| P86-S2 | R7a exige de modifier un écran hors périmètre (P85 §4.2) |
| P86-S3 | Un test R7a-S04 à S06 passe **sans** la confirmation côté service |
| P86-S4 | Une interface affiche une clé brute, un `{variable}` non remplacé, ou une quantité recalculée côté interface |
| P86-S5 | Un scénario non exécuté serait compté comme PASS, ou le tableau de statuts est incomplet |
| P86-S6 | Un lot du plan serait livrable séparément (promotion partielle) |
| P86-S7 | Le chargeur de candidat accepte deux sommes de contrôle différentes pour un même identifiant |

---

## 12. Critères d'acceptation de la réalisation

La **mission de réalisation** (hors G6) est acceptable si **toutes** les conditions suivantes sont vraies :

| # | Critère | Preuve |
|---|---|---|
| AC-1 | Les lots R0 à R7a, R5, PC-1, R6 sont exécutés dans l'ordre du §2, chaque porte franchie avec sa preuve | rapport par porte |
| AC-2 | Le dossier officiel est inchangé (M0, 21 fichiers) ; le candidat est figé (empreinte CL-6) | CP-1, CP-7 |
| AC-3 | Les vérifications bloquantes (B) sont PASS ; aucune non-régression | CP-3 à CP-6, CP-10 |
| AC-4 | Le tableau de statuts couvre **tous** les scénarios attendus ; comptage en tête ; aucun PASS sans exécution | fichier de statuts |
| AC-5 | R7a : AC1 à AC5, AC7, AC8 de P85 §4.5 vrais ; R7a-AC6 : contrôles exécutés ou listés NON VÉRIFIÉS | rapport R7a |
| AC-6 | `compose.ts` : diff limité à la garde ; empreintes avant/après | CP-12 |
| AC-7 | Aucune base réelle, DEMO, copie de version ni fichier de codes touché ; aucune opération Git manuelle ; 0 tag, 0 stash | CP-2, CP-11, CP-15 |
| AC-8 | Aucun nettoyage, suppression ni réparation ; ressources respectées pendant toute la mission | relevés avant/après |
| AC-9 | Réserves, verrou d'exposition, points ouverts et V-3 inscrits en tête du rapport | rapport R6 |
| AC-10 | Procédure de retour arrière testée (A1-T4) | résultat A1-T4 |
| AC-11 | Le rapport ne contient **aucune** décision G6 ni G7 | revue |

**Acceptation ≠ promotion.** L'acceptation de la réalisation est un constat ; G6 reste une décision distincte du PO.

---

## 13. Effet d'une modification de l'arbitrage par le PO

| Si le PO modifie… | Effet sur ce plan |
|---|---|
| **P86-1** (critère) | La liste de contrôle de G6 (§9) et les critères AC-3, AC-4 changent ; le manifeste M2 inclut la liste choisie |
| **P86-2** (adaptation) | Si « à G6 » : l'adaptation sort de R1 et revient dans la fenêtre de gel (incompatible avec « G6 = déplacement de fichier sans modification de test ») : **signaler au PO** |
| **P86-3** (noms) | Si le PO fixe les noms : ils sont intégrés au plan avant R0 |
| **P86-4** (seuils) | Les valeurs approuvées remplacent les planchers de départ en R0 |
| **P86-5 / P86-6** (phase 2, C7) | Si capacité fournie par le PO : PC-1 exécute la phase 2 ; si C7 maintenu bloquant : G6 non proposable sans PC-1 exécutée |
| **P86-7** (G6 conditionnel) | Si « G6 attend tout » : la mission s'arrête à R6 ; PC-1 devient **bloquant** ; si « sans condition » : le verrou d'exposition disparaît du plan, **les contrôles (R) deviennent informatifs** (risque accru, signalé) |
| **P86-8** (point d'entrée PERTE) | Si lecture (1) : R7a ajoute un formulaire de passage réduit (V1 et V3), droits et écrans ; **P86-7 et P86-10 modifiés avec lui** ; contrôles navigateur indispensables avant exposition ; **périmètre R7a élargi** : à confirmer comme extension de A-T3 |
| **P86-9** (V1/V3) | Si une seule interface : fichiers du manifeste et tests S10 à S15 réduits ; l'autre interface listée NON VÉRIFIÉE avec écart assumé |
| **P86-10** (MSG-4) | Texte de confirmation adapté ; R7a-S12 en conséquence |
| **P86-11** (séquencement) | Si R7a après R5 : R5 rejoué après R7a ; si R7a hors gel : A1-M2 et A1-K5 sont contredits : **non recommandé** |
| **P86-12** (O-5/6/9/10) | Tout point tranché devient une décision à intégrer avant R0 ; effet à instruire au cas par cas |
| **P86-13** (V-3) | Si approuvé immédiatement : la convention de P82 devient décision ; sinon inchangé |
| **P86-V1** (DP2-R1 transitoire) | Si refusé : une mission d'interface sur les cases de suivi par lot précède R4e (extension hors R7a) ; le plan est alors **à réviser** |

---

## 14. Conditions de lancement de la réalisation

La réalisation ne démarre que si **toutes** les conditions suivantes sont vraies :

1. **Arbitrage du PO** sur les 13 points (confirmation globale ou modifications ciblées), consigné par écrit ;
2. **Autorisation explicite et distincte** de lancer la réalisation, nommant le plan P86 ; ni l'arbitrage ni ce plan ne la constituent ;
3. relevé de départ (R0) fait **avant toute action** (Git, empreintes, ressources, DEMO, migrations) ;
4. autorisations futures de P85 §8.1 (F-1 à F-4) applicables **dans leurs conditions** ; **non-autorisations** de P85 §8.2 maintenues : G6, G7, phase 2 sauf PC-1 dans les conditions du §8.3, suppression, redémarrage, intervention systemd, extension de `compose.ts` au-delà de la garde, extension de R7a, décision sur O-5, O-6, O-9, O-10, V-3, opération Git manuelle ;
5. prérequis PRQ-1 à PRQ-8 satisfaits (§8.2) ; sinon **phase 1 non lancée** ;
6. rien n'est appliqué à la DEMO ni à une base réelle.

**Découpage de la mission de réalisation (PROPOSITION, pour éviter la fragmentation).** Une seule mission substantielle couvre **R0 → R6** avec arrêts aux portes et sauvegarde de l'état à chaque porte (rapports intermédiaires hors dépôt). Elle peut être **suspendue** à toute condition d'arrêt et **reprise** à la dernière porte franchie, sans refaire les lots précédents tant que les empreintes de CP-1, CP-7 et CP-8 sont identiques. G6 est une décision **séparée** ; G7 est hors mission.

---

## 15. Conditions de clôture du plan

Le plan est clos lorsque :

- le rapport R6 est remis, avec M2, le tableau de statuts complet et le comptage ;
- toutes les portes PR-* sont franchies **ou** l'arrêt est consigné ;
- le PO a décidé G6 (promotion, report ou refus) **ou** a pris acte du report ;
- aucun élément NON EXÉCUTÉ / NON VÉRIFIÉ n'a été présenté comme PASS ;
- G7 n'a pas été exécutée sans décision distincte.

**Ce plan ne clôt ni O-5, ni O-6, ni O-9, ni O-10, ni V-3.**

---
Fin du plan unique P86.
