# PHARM'OS — P86
## Arbitrage consolidé des 13 points ouverts après P85 — Préparation de la réalisation STOCK

**Date de référence :** 25/11/2026.
**Nature :** dossier **strictement documentaire**. Aucun code, aucune base, aucune migration, aucun service n'ont été modifiés.
**Statut de ce document :** toutes les recommandations ci-dessous sont des **PROPOSITIONS techniques soumises au PO**. **Aucune n'est une décision.** Aucune décision PO n'est déduite d'une recommandation. Les décisions déjà prises (P81 à P85) ne sont pas rouvertes.

**Documents complémentaires :** `docs/P86_PLAN_UNIQUE_REALISATION_STOCK.md` (plan unique) et `docs/P86_RAPPORT_EXECUTION.md` (rapport).
**Documents non modifiés :** P81 à P85.

---

## 0. Comment lire ce dossier

1. Les **13 points** sont ceux du §7 de l'addendum P85 (liste unique, numérotation conservée : points 1 à 13). Aucun n'est omis, aucun n'est ajouté. Ils sont traités **ensemble** : les dépendances croisées sont exposées au §8.
2. Chaque point est identifié **P86-n** (n = numéro du §7 de P85) et présenté selon six rubriques : **(1)** situation et référence exacte ; **(2)** risque concret si le point reste ouvert ; **(3)** options réellement possibles ; **(4)** recommandation technique par défaut ; **(5)** conséquences sur R7a, R4a, G6, les tests et la migration 0022 ; **(6)** décision PO attendue, en termes métier.
3. Le PO n'a pas à choisir de technologie ni de structure de code. Il décide du **résultat métier** et du **niveau de risque acceptable**.
4. **Façon de répondre (une seule réponse suffit)** : « Je confirme les recommandations P86-1 à P86-13 », ou « Je confirme, sauf P86-n : [option choisie] ». Le tableau récapitulatif unique est au §10.
5. **Confirmer ne ferme rien d'ouvert** : confirmer P86-12 ou P86-13 signifie **maintenir** ces points ouverts (voir §6). Aucune clôture n'est proposée dans ce dossier, sauf mention explicite « PROPOSITION DE CLÔTURE ».

**Étiquettes utilisées :** FAIT VÉRIFIÉ, OBSERVATION, HYPOTHÈSE, PROPOSITION, DÉCIDÉ (décision antérieure), OUVERT, NON VÉRIFIÉ, NON MESURÉ, NON TESTÉ, NON AUTORISÉ, VIGILANCE, CONDITION, LIMITE, SUSPENDU.

---

## 1. Synthèse des 13 points

| P86-n | Réf. P85 §7 | Objet | Groupe | Recommandation par défaut (PROPOSITION) |
|---|---|---|---|---|
| **1** | A1-L1 | Critère de promotion C1 à C8 | A | Adopter C1 à C8 **révisé** : C5 (périmètre de comparaison défini), C7 scindé en C7a/C7b, ajout de C9 (R7a) |
| **2** | A1-L2 | Adaptation préalable de `schema_parite`, `p33`, `p34` | B | Oui : nombres **calculés** au lieu de figés, aucune assertion affaiblie, liste justifiée au rapport |
| **3** | A1-L3 | Nom du manifeste, format des empreintes, constante, nom de l'erreur | A | Fixés **une seule fois** à la fin de R1 par l'équipe technique, consignés au rapport |
| **4** | A2-L2 | Valeur et approbation des seuils de ressources | B | Grille P84 reprise comme **valeurs de départ**, documentées et confrontées à la mesure en R0 avant tout usage |
| **5** | A2-L1 | Phase 2 : mémoire, autre hôte ou report | B | **Report explicite par défaut**, avec une **porte de capacité** (PC-1) avant G6 |
| **6** | §3.6 | Sort de C7 et de la partie D1 local de MIG-T2 | B | Scinder : parité **mémoire/fichiers** vérifiée en phase 1 ; parité **D1 local** vérifiée à PC-1 si possible, sinon **NON VÉRIFIÉE** avec réserve écrite soumise au PO à G6 |
| **7** | R7a-L1 | G6 avec contrôles navigateur/RTL/mobile non vérifiés ? | A | **G6 conditionnel** : contrôles de **sécurité et d'intégrité** obligatoires avant G6 ; contrôles **visuels et linguistiques** reportables sous **verrou d'exposition** |
| **8** | R7a-L2 | Point d'entrée du passage en PERTE | A | **Lecture (2)** : confirmation livrée, passage en PERTE **non exposé** dans V1/V3 tant que la mission d'interface n'a pas fourni le formulaire |
| **9** | A3-L2 | Interfaces V1 et V3 | C | **V1 et V3**, comme P84 ; toute partie non vérifiée est listée NON VÉRIFIÉE (jamais omise) |
| **10** | A3-L3 | Mention MSG-4 dans la confirmation | C | **Liée à P86-8** : sous la lecture (2), **ne pas inclure MSG-4** dans R7a |
| **11** | §6.3 | Séquencement : R7a dans la fenêtre de gel | A | **R7a dans la fenêtre de gel, avant R5** : R4e → R7a → R5 → R6 → G6 |
| **12** | O-5, O-6, O-9, O-10 | Points ouverts du registre | — | **Maintenus ouverts** ; mentionnés au rapport sans être tranchés |
| **13** | V-3 | Formule CAT-11 avec la convention P82 | — | **Maintenu NON APPROUVÉ** ; écriture exacte soumise au PO **avant** G6 |

**Principe directeur (PROPOSITION).** Ce qui devient **irréversible ou coûteux à corriger après G6** (schéma, chaîne de migrations, intégrité des données, protections du service) est vérifié **avant** G6. Ce qui se corrige par une simple modification de code (présentation, mise en page, relecture linguistique) peut être **reporté**, à condition qu'**aucun utilisateur** n'y soit exposé avant exécution du contrôle ou acceptation écrite du PO.

---

## 2. Constats transverses relevés pour P86 (lecture seule)

| Réf. | Constat | Étiquette |
|---|---|---|
| T-1 | Dernier fichier officiel `pharmacy` : **0021** (21 fichiers) ; `control` : 4 fichiers ; aucun répertoire de candidat ; `compose.ts` (25 lignes) : `composeCore(mode, adapters, migrationsDir)` lit les dossiers `control` et `pharmacy` par `readMigrationSources` | FAIT VÉRIFIÉ |
| T-2 | `tests/support/env.ts` : `migrationSets(withFixture)` = sources du produit + fixtures ; **aucun chargeur de candidat** n'existe | FAIT VÉRIFIÉ |
| T-3 | `tests/lot1b/schema_parite.test.ts` l. 21 : `assert.equal(ids.length, 22)` ; ce test construit **aussi un banc D1 local** | FAIT VÉRIFIÉ |
| T-4 | `tests/p33/fleet_inventory.test.ts` l. 95 et `tests/p34/release_unverified.test.ts` l. 48-52 : une ligne d'historique **0022 « inconnue »** simule une base « en avance » ; cette simulation cesse d'être valable dès que 0022 est connue du jeu | FAIT VÉRIFIÉ |
| T-5 | **17 fichiers** de test utilisent le banc D1 local ; D1 n'apparaît dans **aucun point d'entrée de production** (`local.ts` : fichiers SQLite ; `saas.ts` : adaptateurs de référence, aucun adaptateur D1/R2 réel) | FAIT VÉRIFIÉ |
| T-6 | V1 (`purchasing.js`, `purchasing-i18n.js`) et V3 (`PurchaseForms.jsx`, `Achats.jsx`, `purchaseStrings.js`) ne gèrent que le remplacement de `{n}` ; **aucun** message `purchase.reversal_state`, **aucune** confirmation PERTE, **aucun** écran de passage d'état n'existe | FAIT VÉRIFIÉ |
| T-7 | Aucun outil de simulation DOM (jsdom, happy-dom, testing-library) n'apparaît dans `ui/saas-src/node_modules` (seul `react-dom` y figure) : les « tests de composant sans navigateur » R7a-S10 à S15 seraient des tests de **chaînes** ou de **logique extraite**, à confirmer en R1 | OBSERVATION (liste de modules, non exhaustive) |
| T-8 | `ui/saas-src/dist` est **ignoré par Git** (`dist/`) : 0 fichier suivi ; la V3 est livrée en **source** ; sa vérification en navigateur exige une **construction** de consommation **NON MESURÉE** | FAIT VÉRIFIÉ / NON MESURÉ |
| T-9 | La DEMO (PID 5638) s'exécute depuis la **copie de version** `p53ui-fb956a9` ; le listing de noms de son dossier `migrations/pharmacy` s'arrête à **0020** ; `/health` répond `ok` | FAIT VÉRIFIÉ (noms de fichiers uniquement) |
| T-10 | Ressources : `/` 1 139 636 Ko libres (97 %) ; `/tmp` tmpfs plein ; mémoire disponible ≈ 644 Mo sur 2 039 440 Ko ; pas de swap. Baisse de **8 308 Ko** d'espace libre depuis P85 (1 147 944 Ko) | FAIT VÉRIFIÉ ; cause de la baisse : HYPOTHÈSE non analysée |
| T-11 | Aucun fichier de `pharmos/tests` ne contient `CAT-14`, `CAT-15` ni `CAT-16` ; le plan directeur (l. 497) fait de « CAT, CAT-14 à 16 verts » le critère du Lot 2, prérequis du Lot 3 | FAIT VÉRIFIÉ (n'établit pas l'état du Lot 2 : NON VÉRIFIÉ) |
| T-12 | Le critère C7 de P84 §2.5 énonce qu'une condition non vérifiée (D1 non exécuté faute de ressources) **bloque G6** ; R7a-AC6 (P85 §4.5) admet des contrôles **listés NON VÉRIFIÉS** : les deux textes ne se combinent pas sans arbitrage | OBSERVATION |
| T-13 | Le repli « B restreinte » du dossier P84 (§14.6, si A-T3 non arbitré) est **sans objet** : A-T3 est approuvé (option A) | OBSERVATION |
| T-14 | Numérotation : l'étude P84 (CL-4, CL-5) nomme « MIG-T3 » la composition officiel + candidat et « MIG-T4 » le chargeur idempotent ; la matrice P84 et P85 (A1-K1, A1-K3) nomment MIG-T3 le chargeur et MIG-T4 la garde. **La matrice et P85 prévalent** ; une table de correspondance sera fixée en R1 | OBSERVATION |
| T-15 | Collision de noms : `CAT-14` de la matrice P84 (scénario de catalogue) et `CAT-14 à 16` du plan directeur (critères du Lot 2) sont **deux objets différents** | OBSERVATION |

---

## 3. Groupe A — Livraison R7a et G6 (points P86-1, 3, 7, 8, 11)

**Observation d'ensemble (FAIT VÉRIFIÉ, P81 §10 et P84 §2.3) :** G6 est la **promotion du candidat dans le dossier officiel des migrations**, avec le code et les tests adaptés, en un seul ensemble. L'**application** à la DEMO ou à une base réelle est **G7**, décision distincte, **NON AUTORISÉE** (DP6-R7, P85 §8.2). Tant que G7 n'a pas lieu, **aucun utilisateur** n'est exposé à la livraison. Cette distinction fonde la proposition P86-7.

### P86-1 — Critère de promotion C1 à C8 (A1-L1)

1. **Situation et référence.** P84 étude §2.5 : huit conditions (C1 manifeste M2 établi après le dernier changement ; C2 dossier officiel = M0 + un seul `0022_*.sql` ; C3 fichier identique au candidat validé en R5 ; C4 MIG-T1, MIG-T3, MIG-T4 réussis ; C5 non-régression ciblée sans régression ; C6 contrôles d'intégrité A1-A8 et B1-B22 exacts ; C7 parité de schéma mémoire, fichiers **et D1 local** ; C8 acceptation écrite du PO). **PROPOSITION** non adoptée : P85 §2.5 (A1-L1) précise que A1-K5 (« aucune promotion partielle ») est cohérente avec ce critère mais ne l'adopte pas. Deux difficultés : C5 ne définit pas le périmètre exact de comparaison ; C7 est inatteignable tant que la phase 2 est suspendue (T-12).
2. **Risque si ouvert.** Sans critère écrit, la décision G6 devient discrétionnaire ; avec C7 pris à la lettre, G6 est bloquée indéfiniment par un contrôle de banc (D1 local), alors que D1 n'est dans aucun point d'entrée de production (T-5). Inversement, un critère trop souple laisserait passer une livraison partielle (RL-2).
3. **Options.** (a) Adopter C1 à C8 **tels quels** ; (b) adopter C1 à C8 **révisés** (ci-dessous) ; (c) ne rien adopter et juger G6 au cas par cas.
4. **Recommandation (PROPOSITION).** Option **(b)**. Révisions : **C5** : la comparaison porte sur la liste de fichiers de test de la non-régression ciblée fixée en R0 (plan P82 §9.5), avec le **nombre de tests réussis par fichier** ; les fichiers de la phase 2 y figurent comme « NON EXÉCUTÉ », jamais comme réussis. **C7 scindé** : **C7a** parité mémoire et fichiers (obligatoire, phase 1) ; **C7b** parité D1 local (voir P86-6). **C9 ajouté** : R7a livré dans le **même manifeste M2**, ses contrôles obligatoires (P86-7) réussis, le verrou d'exposition consigné (R7a-AC8). C1, C2, C3, C4, C6 et C8 sont inchangés. Un manifeste incomplet, une condition fausse ou non vérifiée **bloque G6**, sauf pour les seuls éléments de la liste « reportable sous verrou » de P86-7, **et seulement par acceptation écrite du PO**.
5. **Conséquences.** *R4a* : le manifeste M0 et la liste de référence de C5 sont établis avant le gel. *R7a* : C9 en fait une condition de G6. *G6* : la grille de décision du §7 s'y appuie. *Tests* : MIG-T1, MIG-T3, MIG-T4 (matrice P84 et P85) obligatoires ; table de correspondance MIG-T3/T4 fixée en R1 (T-14). *Migration 0022* : aucun effet sur son contenu.
6. **Décision PO attendue.** « Acceptez-vous que la livraison STOCK ne soit soumise à votre décision de promotion que si une liste fixe de neuf vérifications est vraie, les seules exceptions possibles étant celles que vous accepterez par écrit au moment de la décision ? »

### P86-3 — Noms, formats et constantes du manifeste (A1-L3)

1. **Situation et référence.** P85 §2.5 (A1-L3) : nom du manifeste, format des empreintes, constante du niveau requis, nom de l'erreur de refus, **à fixer en R1** ; dossier P84 §9.4 (M-1).
2. **Risque si ouvert.** Un nom changé après le début du gel invaliderait le manifeste M0 et les tests A1-T* déjà écrits (condition d'arrêt 12) ; à l'inverse, faire décider ces noms par le PO n'apporte aucune garantie métier.
3. **Options.** (a) Le PO fixe les noms ; (b) l'équipe technique les fixe **une fois**, en fin de R1, et les consigne ; (c) les fixer dans P86.
4. **Recommandation (PROPOSITION).** Option **(b)**, avec trois garde-fous : **un seul** jeu de noms ; consignés au rapport R1 avec leur justification ; **aucun changement** après la fin de R1 sans nouveau calcul du manifeste et mention au rapport. Contrainte de forme (PROPOSITION) : le nom de l'erreur de refus doit être distinct de `migration.unknown_applied` et de `tenant.migrations_pending` (A1-T1c exige que le code existant reste inchangé).
5. **Conséquences.** *R4a* : les noms doivent exister avant le gel, donc **avant R2**. *R7a* : aucune. *G6* : le manifeste M2 les reprend à l'identique. *Tests* : A1-T1a à T5c les utilisent. *Migration 0022* : aucune.
6. **Décision PO attendue.** « Confirmez-vous que ces choix de nommage sont techniques, laissés à l'équipe, et qu'ils vous seront simplement rapportés ? »

### P86-7 — G6 avec contrôles navigateur, RTL et mobile non vérifiés (R7a-L1)

1. **Situation et référence.** P85 §4.8 (R7a-L1) : R7a doit être « intégré avant G6 » avec contrôles FR/EN/AR, RTL, mobile (A-T3, P85 §4.7 : R7a-C01 à C08), mais les tests navigateur sont **SUSPENDUS** (A-T2, phase 2). Statuts P85 : R7a-C01 exécutable en phase 1 ; **C02 à C07** navigateur **SUSPENDU / NON VÉRIFIÉ** ; **C08** (relecture arabe par un arabophone) **non automatisable, jamais réalisée**. Texte antagoniste : P84 §2.5 (une condition non vérifiée bloque G6) et R7a-AC6 (contrôles exécutés **ou** listés NON VÉRIFIÉS).
2. **Risque si ouvert.** (i) Attente indéfinie de G6 si la capacité n'est jamais réévaluée ; (ii) à l'inverse, un message de refus de contre-passation ou une quantité mal affichée en arabe (inversion de chiffres, `D4 C3 Q2 P1` lu de travers) pourrait fausser une décision de stock : **risque d'intégrité d'usage**, non de données.
3. **Options.** (A) **G6 attend** l'exécution de tous les contrôles (phase 2 ou autre hôte) ; (B) **G6 sans condition** avec C02 à C08 NON VÉRIFIÉS ; (C) **G6 conditionnel** : contrôles de sécurité et d'intégrité obligatoires avant G6, contrôles visuels et linguistiques **reportés sous verrou d'exposition**.
4. **Recommandation (PROPOSITION).** Option **(C)**.
   - **Obligatoires avant G6 (exécutables en phase 1, sans navigateur)** : R7a-S01 à S07 (service) ; R7a-S10 à S15 (chaînes et logique de composant, dans la limite de ce que l'outillage permet, T-7) ; R7a-C01 ; V6-18 et test d'égalité des clés (p69) ; R7a-AC1 à AC5, AC7, AC8 ; contrôle **statique** de sécurité d'affichage en arabe (**C03s**, PROPOSITION à confirmer en R1) : les quantités, numéros de lot et sigles d'état sont produits par le texte **comme éléments isolés** (par exemple séparés ou encadrés par des marques d'isolation bidirectionnelle), vérifiable par chaîne sans navigateur ; liste exacte des fichiers d'interface modifiés.
   - **Reportables sous verrou d'exposition** : R7a-C02 (sens de la mise en page), C03 (lisibilité visuelle complète), C04 (débordement horizontal), C05 (boutons PERTE, cible tactile), C06 (messages longs), C07 (non-régression visuelle P65), **C08 (relecture arabe humaine)**. Statut maintenu : **NON VÉRIFIÉ**, jamais PASS.
   - **Verrou d'exposition** : tant que ces contrôles ne sont ni exécutés ni acceptés par écrit, **G7 n'est pas proposée** et **aucune copie de version contenant R7a n'est construite** depuis le dépôt ; le rapport G6 le mentionne en tête.
5. **Conséquences.** *R7a* : R7a-C02 à C08 sont livrés **spécifiés et préparés**, pas exécutés. *R4a* : aucune. *G6* : grille du §7. *Tests* : C03s s'ajoute à R7a ; le reste est NON VÉRIFIÉ. *Migration 0022* : aucune. *Réserve* : cette option s'écarte de la lettre de P84 §2.5 (« bloque G6 ») ; elle n'est valable que **si le PO l'accepte explicitement**.
6. **Décision PO attendue.** « Acceptez-vous que la promotion soit proposée à votre décision avant les contrôles visuels (écran arabe, téléphone, relecture arabe), à condition que rien ne soit mis à la disposition d'utilisateurs avant que ces contrôles aient eu lieu ou que vous les ayez acceptés par écrit ? Sinon : la promotion attend la capacité de les exécuter. »

### P86-8 — Point d'entrée du passage en PERTE (R7a-L2)

1. **Situation et référence.** P85 §4.8 (R7a-L2) ; P85 §4.1 (« exclusivement » deux éléments) ; C-P82-3 (P83) ; R7a-E5 à E9 (P85 §4.4). Constat T-6 : aucun écran de passage d'état n'existe. Le formulaire de passage (UI-2) est **hors R7a** (P85 §4.2).
2. **Risque si ouvert.** Si R7a inclut un accès minimal sans que son périmètre soit borné, il devient de fait l'écran UI-2 (choix du lot, motif, états, droits), en dehors de A-T3. Si la confirmation est livrée sans que ce point soit tranché, le plan de R7a ne peut pas être écrit (P85 §4.8 : « à confirmer avant la rédaction du plan de R7a »).
3. **Options.** **(1)** R7a inclut le **strict minimum d'accès** qui héberge la confirmation (un formulaire de passage réduit, V1 et V3) ; **(2)** la confirmation est livrée, mais le passage en PERTE **n'est pas exposé dans l'interface** tant qu'une mission distincte n'a pas fourni le formulaire ; le **service** refuse tout passage sans confirmation (R7a-E8).
4. **Recommandation (PROPOSITION).** Option **(2)**. Raisons : respecte « exclusivement » ; évite d'introduire dans R7a l'opération **irréversible** (DP1-R4) derrière un écran réduit non couvert par les contrôles visuels reportés (P86-7) ; la protection décisive est **côté service** (R7a-S04, S05, S06). Précision (OBSERVATION) : le service restera atteignable par la **route HTTP** de passage prévue en R4b (plan P82 §3.3, `stockRoutes.ts`) par un détenteur du droit, mais ce droit **n'est pas accordé par défaut** (Q-11) et la confirmation y est exigée. Limite assumée : le scénario d'usage complet (PER-40 à PER-47 au niveau écran) reste **NON VÉRIFIÉ** jusqu'à la mission d'interface, qui devra **réutiliser** le composant de confirmation et rejouer R7a-S12 et S13.
5. **Conséquences.** *R7a* : composant de confirmation (texte et logique) sans point d'entrée ; R7a-E9 vérifié sur chaque écran touché. *R4a* : aucune. *G6* : la grille de P86-7 s'applique ; C-P82-3 est « satisfaite par le service, non encore visible pour l'utilisateur ». *Tests* : S04 à S06 (service), S12, S13 (composant). *Migration 0022* : aucune. Si l'option (1) était choisie : périmètre R7a élargi, droits et écrans à ajouter, contrôles navigateur indispensables avant exposition (VIGILANCE : risque de dérive de périmètre).
6. **Décision PO attendue.** « En attendant la mission d'interface complète, acceptez-vous qu'**aucun écran** ne permette de passer du stock en PERTE, le service exigeant de toute façon la confirmation ? Sinon : acceptez-vous qu'un écran réduit soit livré, avec les contrôles supplémentaires que cela impose ? »

### P86-11 — Séquencement : R7a dans la fenêtre de gel (§6.3 de P85)

1. **Situation et référence.** P85 §6.3 : R0 → R1 → … → R4a → (gel A1-M2) → … → R7a → G6 ; la lecture « R7a dans la fenêtre de gel » découle de A-T1 et A-T3 mais est **À CONFIRMER**. P82 plan : R5 fige l'empreinte du candidat ; R6 produit le manifeste M1.
2. **Risque si ouvert.** R7a hors fenêtre de gel romprait A1-K5 (service et interface livrés ensemble, R7a-AC8) ; R7a après R5 obligerait à refaire la non-régression ; R7a avant que le format de `purchase.reversal_state` soit stable (R4d) obligerait à réécrire les messages.
3. **Options.** (a) R7a dans le gel, **avant R5** ; (b) R7a dans le gel, **après R5** (R5 rejoué) ; (c) R7a hors gel, avant G6 (contredit A1-M2 et A1-K5).
4. **Recommandation (PROPOSITION).** Option **(a)** : **R4a → R4b → R4c → R4d → R4e → R7a → R5 → R6 → G6**. R7a suit R4d (charge utile de refus, paramètre de confirmation) et R4e (clés d'interface du catalogue stabilisées avant le test d'égalité FR/EN/AR). R5 inclut alors les tests d'interface et fige l'empreinte du candidat **avec** R7a présent. R7a ne modifie aucun écran de catalogue, ni les cases de suivi par lot de DP2-R1 (VIGILANCE, §8).
5. **Conséquences.** *R4a* : début du gel (manifeste M0 déjà établi). *R7a* : entre R4e et R5, périmètre exclusif de P85 §4.1. *G6* : M2 inclut les fichiers d'interface de R7a. *Tests* : R5 rejoue la non-régression V1/V3 une seule fois. *Migration 0022* : candidat figé en R5, inchangé par R7a (R7a ne touche pas au schéma ; toute exigence de schéma découverte en R7a est une condition d'arrêt, §7).
6. **Décision PO attendue.** « Confirmez-vous que les deux messages d'interface font partie de la même livraison, figée avec tout le reste, et sont réalisés avant les derniers contrôles de non-régression ? »

---

## 4. Groupe B — Tests, compatibilité, capacité (points P86-2, 4, 5, 6)

**Observation d'ensemble.** La phase 1 (bancs mémoire puis fichiers, sans D1 ni navigateur) est autorisée sous conditions A2-K1 à K4 (P85 §3). La phase 2 (D1 local, navigateur) est SUSPENDUE. Hôte : 2 Go de RAM, mémoire disponible ≈ 644 Mo, `/tmp` plein, disque à 97 % (T-10). Le test D1 le plus lourd a consommé ≈ 1,45 Go en P80 (FAIT VÉRIFIÉ, rapport P80). Rien de ce groupe n'a été mesuré en P86 (NON MESURÉ).

### P86-2 — Adaptation préalable de `schema_parite`, `p33`, `p34` (A1-L2)

1. **Situation et référence.** P85 §2.5 (A1-L2) ; P84 étude §2.3 point 2 ; RL-3 et RL-4 (P84 §2.2). Constats T-3 et T-4 : `schema_parite` fige `ids.length = 22` ; `p33` (l. 95) et `p34` (l. 48-52) simulent une base « en avance » par un identifiant **0022**. Dès que 0022 est un identifiant connu (candidat chargé, puis promotion), ces trois tests échouent sans que le produit soit en défaut. P85 : conséquence technique de A1-M3, **pas une décision PO**.
2. **Risque si ouvert.** Échecs de non-régression au pire moment (G6), ou, pire, **assertion affaiblie à la hâte** pour « faire passer » le test.
3. **Options.** (a) Adapter **avant** G6 en version valable avant et après promotion ; (b) adapter **au moment** de G6 ; (c) ne pas adapter et désactiver ces tests (**exclu** : relâcherait un garde-fou).
4. **Recommandation (PROPOSITION).** Option **(a)**, selon P84 §2.3 : le nombre d'identifiants devient **calculé** (officiels + fixtures) ; la ligne « en avance » utilise un identifiant éloigné non utilisable (par exemple `0099`). Garde-fous : **aucune assertion supprimée ni affaiblie** ; chaque test garde sa sensibilité (un test d'écart doit **échouer** si l'identifiant simulé devenait connu : à démontrer par une variante négative, condition d'arrêt 15) ; modification listée et justifiée au rapport R6 ; ces trois fichiers sont ajoutés au manifeste. Précision de séquence : l'adaptation intervient **en R1** (avant tout chargement du candidat dans les bancs par défaut), donc avant R2, et la « version valable avant et après » est elle-même vérifiée en R0/R1 sur le jeu 0021 (comportement **inchangé**).
5. **Conséquences.** *R4a* : aucun effet (les trois tests sont déjà adaptés). *R7a* : aucun. *G6* : supprime RL-4 ; C5 comparé en nombre de tests réussis par fichier. *Tests* : `schema_parite` reste lié au banc D1 local (T-3) : son **exécution** relève de la phase 2 (voir P86-6) ; son **adaptation** relève de la phase 1 (modification de fichier, sans exécution lourde). `p34` n'utilise pas D1 ; `p33` le mentionne (OBSERVATION, 2 occurrences). *Migration 0022* : aucun effet.
6. **Décision PO attendue.** « Confirmez-vous que trois contrôles existants soient rendus insensibles au simple ajout de la nouvelle migration, sans en affaiblir aucun, et que cette modification vous soit rapportée ? »

### P86-4 — Valeur et approbation des seuils de ressources (A2-L2)

1. **Situation et référence.** P85 §3.4 : grille de P84 §3.4 = **PROPOSITION non approuvée** (disque ≥ 1 000 000 Ko au lancement, arrêt < 800 000 Ko ; mémoire ≥ 500 Mo au lancement, arrêt < 350 Mo ; `TMPDIR` ≤ 300 000 Ko ; phase 2 ≥ 1 800 Mo) ; seuil de 800 Mo du plan P82 §12 : hypothèse non approuvée ; obligation A2-K3 : **documenter** les seuils avant usage. P85 §3.3 (d) : marge **faible** (≈ 48 000 Ko au-dessus du seuil d'arrêt proposé).
2. **Risque si ouvert.** Sans valeur, la mission ne peut ni démarrer ni s'arrêter de façon vérifiable (A2-K4). Avec une valeur trop basse, risque de rejouer l'incident P70 (saturation mémoire, redémarrage, perte de DEMO) ; trop haute, mission impossible dès le départ.
3. **Options.** (a) Le PO **approuve la grille P84 telle quelle** ; (b) le PO **approuve le principe** (seuils documentés, mesurés en R0, arrêt automatique) et laisse la mission **fixer les valeurs** dans le relevé R0 avec justification et **sans les assouplir par rapport à la grille P84** ; (c) le PO fixe d'autres valeurs.
4. **Recommandation (PROPOSITION).** Option **(b)**, avec règle de **non-assouplissement** : les valeurs de P84 sont des **planchers**. La mission peut les **durcir** si la mesure de R0 l'exige (par exemple réduire la taille maximale du `TMPDIR`), jamais les assouplir sans nouvel accord du PO. La taille du `TMPDIR` et le seuil d'arrêt disque sont fixés **avant création** (P85 §3.3 d). Mode opératoire : une suite à la fois ; relevé disque et mémoire avant et après ; arrêt immédiat à la première condition S-1 à S-7. Les valeurs de P84 sont des **hypothèses** de départ : elles ne deviennent des décisions PO que par la confirmation de ce point.
5. **Conséquences.** *R4a* : aucun effet direct (gel logique). *R7a* : les contrôles S10 à S15 restent légers. *G6* : le rapport joint les relevés avant/après. *Tests* : A2-K1 s'applique à toute suite ; la consommation réelle (NON MESURÉ) est établie par R0, **sans benchmark de calibrage** (NON AUTORISÉ, P85 §3.1). *Migration 0022* : aucun effet.
6. **Décision PO attendue.** « Acceptez-vous que les limites de sécurité de l'hôte (espace disque et mémoire à ne jamais franchir pendant les essais) soient celles proposées, que la mission puisse seulement les rendre plus strictes, et qu'elle s'arrête d'elle-même dès qu'une limite est atteinte ? »

### P86-5 — Phase 2 : mémoire, autre hôte ou report (A2-L1)

1. **Situation et référence.** P85 §3.5 et §3.7 (A2-L1) : phase 2 SUSPENDUE ; la réévaluation compare **(1)** augmentation de mémoire, **(2)** autre hôte, **(3)** report des scénarios non vérifiés ; aucune n'est choisie. Constat : ≈ 644 Mo disponibles contre ≈ 1,45 Go observés pour un test D1 en P80 : besoin non couvert.
2. **Risque si ouvert.** Cascade de décisions tardives à G6 ; ou tentation d'exécuter un test lourd sans capacité (P70).
3. **Options.** (1) **Mémoire supplémentaire** : levier matériel, **hors périmètre agent** (NON AUTORISÉ : réglage système) ; (2) **autre hôte** : exécuter les contrôles de phase 2 sur une machine disposant de la capacité, sans déplacer la DEMO ni les bases réelles (la copie de travail, les candidats et les rapports seuls y seraient transférés ; modalités et sécurité des transferts à définir par le PO) ; (3) **report explicite** : les scénarios de la phase 2 sont consignés NON VÉRIFIÉS à G6.
4. **Recommandation (PROPOSITION).** **Report explicite par défaut (3)** : il ne bloque rien et ne prend aucun risque pour la DEMO. Une **porte de capacité PC-1**, placée **après R5 et avant G6**, relève le besoin et la capacité : si, **à ce moment-là**, une des options (1) ou (2) a été **décidée par le PO et mise en œuvre par lui**, la mission exécute les scénarios de la phase 2 (une suite à la fois, arrêts S-1 à S-7) ; sinon elle consigne le report. Aucune capacité n'est présumée.
5. **Conséquences.** *R4a* : aucune. *R7a* : C02 à C07 attendent PC-1 ou restent NON VÉRIFIÉS sous verrou (P86-7). *G6* : le rapport affiche, en tête, la liste des scénarios NON EXÉCUTÉS (§7). *Tests* : V6-10 à V6-17, V6-19, MIG-T2 (D1), C7b. *Migration 0022* : l'essai du candidat (R2) se fait sur **banc mémoire et fichiers**, sans D1 ; O-7 (comportement `ALTER TABLE … ADD COLUMN` sur table `STRICT`) est levé **dans la limite du moteur SQLite du banc** ; son comportement **en D1 réel** reste NON VÉRIFIÉ.
6. **Décision PO attendue.** « Acceptez-vous que, sans moyens supplémentaires, les essais lourds (base D1 locale, navigateur) soient explicitement reportés et marqués non vérifiés, plutôt que de risquer l'hôte ? Vous gardez la possibilité de fournir de la capacité avant la décision de promotion. »

### P86-6 — Sort de C7 et de la partie D1 local de MIG-T2 (§3.6 de P85)

1. **Situation et référence.** P85 §3.6 : C7 (parité sur mémoire, fichiers **et** D1 local) et la partie D1 local de MIG-T2 sont suspendues ; « soit la phase 2 est réévaluée et autorisée, soit C7 est reportée avec mention explicite NON VÉRIFIÉ ». Constat T-5 : D1 n'est dans **aucun** point d'entrée de production ; 17 fichiers de test l'utilisent comme **banc**. Autre constat : O-7 (D1 **réel**) est hors de portée d'un banc local de toute façon.
2. **Risque si ouvert.** Blocage de G6 par une vérification qui ne protège aucun chemin de production actuel ; ou, à l'inverse, perte de la seule alarme qui signalerait une divergence de schéma entre SQLite et D1 avant un futur adaptateur D1.
3. **Options.** (a) Maintenir C7 telle quelle (bloque G6 tant que la phase 2 est suspendue) ; (b) **scinder C7** : C7a (mémoire + fichiers, obligatoire) et C7b (D1 local, à PC-1 ou NON VÉRIFIÉ avec réserve) ; (c) supprimer D1 du critère (**exclu** : relâche un garde-fou sans décision).
4. **Recommandation (PROPOSITION).** Option **(b)**. **Solution réaliste pour MIG-T2 en phase 1** : comparaison **normalisée** du schéma entre banc **mémoire** et banc **fichiers** (tables, index, déclencheurs, vues, historique `_migrations`), exécutable en phase 1 ; **plus** une **comparaison statique** des instructions du candidat (absence de constructions non portables : `hasMultipleStatements`, séparateurs `--> statement-breakpoint`, déjà couvertes par MIG-T8) qui ne remplace **pas** la parité D1 mais en réduit le risque. **Ce qui reste exigé par D1** : l'exécution de `schema_parite` en banc D1 et des tests D1 de stock (P82 plan §3.3 : stock D1, 22 scénarios, ≈ 6 minutes). Statut au rapport G6 : « C7b NON VÉRIFIÉE » avec **réserve écrite** que le PO accepte ou refuse à G6 ; jamais PASS.
5. **Conséquences.** *R4a* : aucune. *R7a* : aucune. *G6* : la réserve C7b est un point de la décision (§7). *Tests* : MIG-T2 mémoire/fichiers PASS possible ; MIG-T2 D1 « NON EXÉCUTÉ » ; `schema_parite` adapté (P86-2) mais non exécuté en D1. *Migration 0022* : écrite pour rester compatible avec les règles de portabilité vérifiables statiquement ; compatibilité D1 réelle NON VÉRIFIÉE.
6. **Décision PO attendue.** « Acceptez-vous que la comparaison avec la base D1 locale soit une vérification à part, non faite tant que l'hôte n'a pas la capacité, et que vous décidiez au moment de la promotion si cette réserve est acceptable ? »

---

## 5. Groupe C — Interfaces, messages, contrôles (points P86-9, 10)

**Observation d'ensemble.** R7a est borné à **deux restitutions** (P85 §4.1) : refus de contre-passation (C-P82-2 : quantités concernées + raison) et confirmation avant PERTE (C-P82-3 : quantité + irréversibilité). Contenus minimaux : P85 §4.3. États : R7a-E1 à E9 (§4.4). Messages P84 : MSG-1 et MSG-2 (refus), MSG-3 (confirmation) dans R7a ; MSG-4 à MSG-8 hors R7a, sauf MSG-4 « À CONFIRMER » ; MSG-8 (aucun message ne suggère une sortie de Q ou P) est une **règle de contenu** applicable aux messages R7a (R7a-E3).

### P86-9 — Interfaces V1 et V3 (A3-L2)

1. **Situation et référence.** P85 §4.9 (A3-L2) : P84 option A visait **V1 et V3** ; le PO n'a pas restreint ; « V1 et V3 sont donc retenues comme dans P84, sous réserve de confirmation ». T-6 : les deux interfaces gèrent les achats et la contre-passation ; V1 (`purchasing.js`, `purchasing-i18n.js`), V3 (`PurchaseForms.jsx`, `Achats.jsx`, `purchaseStrings.js`, `purchaseApi.js`).
2. **Risque si ouvert.** Une seule interface corrigée laisserait l'autre afficher une erreur générique ou technique à la même situation (C-P82-2 non respectée sur ce chemin) ; les tests d'égalité de clés (p69) peuvent aussi diverger.
3. **Options.** (a) V1 **et** V3 ; (b) V1 seule (précise, sans navigateur) ; (c) V3 seule.
4. **Recommandation (PROPOSITION).** Option **(a)**. Les deux interfaces **réutilisent le même contenu fonctionnel** (mêmes champs de la charge utile) ; chaque interface porte ses propres clés FR/EN/AR (p69). Toute vérification impossible dans l'une des deux est listée **NON VÉRIFIÉE** par interface ; elle n'est jamais omise. T-8 : la V3 est livrée en source ; sa construction (dist) ne se fait **pas** dans la mission tant que la capacité n'est pas établie ; ses tests R7a-S10 à S15 portent donc sur la **logique et les chaînes** des sources.
5. **Conséquences.** *R7a* : fichiers V1 et V3 listés au manifeste (R7a-AC7). *R4a* : aucune. *G6* : C9 couvre les deux. *Tests* : S10 à S15 par interface ; V6-18 ; p69. *Migration 0022* : aucune.
6. **Décision PO attendue.** « Confirmez-vous que les deux messages doivent exister dans les deux versions de l'interface (on-premise et SaaS), et qu'une partie non vérifiable dans l'une d'elles soit signalée comme telle ? »

### P86-10 — Mention MSG-4 dans le texte de confirmation (A3-L3)

1. **Situation et référence.** P85 §4.2 et §4.9 (A3-L3) : MSG-4 (« en cas de doute, utiliser EN_CONTROLE, réversible ») « peut figurer **dans le texte** de la confirmation sans écran supplémentaire » ; P82 V2-R8 : garde-fous du passage en PERTE ; P84 dossier §14.6 : MSG-4 listé dans l'option A.
2. **Risque si ouvert.** Sans la mention, l'utilisateur confirme sans connaître l'alternative réversible (risque métier de PERTE par erreur, Q-11). Avec la mention **alors que le passage en EN_CONTROLE n'est pas accessible dans l'interface** (UI-2 hors R7a, P86-8), le texte renvoie vers une action que l'utilisateur ne peut pas faire depuis l'écran : promesse d'une fonction inexistante (cf. R7a-E3, même principe).
3. **Options.** (a) Inclure MSG-4 dans la confirmation ; (b) ne pas l'inclure dans R7a et le **reporter** à la mission d'interface (qui livrera le passage en EN_CONTROLE) ; (c) inclure une formulation **neutre** (« cette opération est définitive ; en cas de doute, ne la confirmez pas »).
4. **Recommandation (PROPOSITION).** **Dépend de P86-8.** Si la lecture (2) est retenue : option **(c)**, qui renforce la prudence **sans** désigner une action inaccessible ; MSG-4 complet est reporté à la mission d'interface, qui disposera du passage en EN_CONTROLE. Si la lecture (1) est retenue : option **(a)**. La formulation neutre (c) est un **ajout de précaution** distinct de MSG-4 : elle n'est pas dans la décision A-T3 ; elle est soumise au PO comme variante (voir tableau §10).
5. **Conséquences.** *R7a* : un texte de plus par langue (clé FR/EN/AR) ; R7a-S12 vérifie la présence de la quantité, de l'irréversibilité et, selon l'option, de la mention. *R4a, G6, 0022* : aucune. *Tests* : R7a-S12, S14.
6. **Décision PO attendue.** « Voulez-vous que l'écran de confirmation recommande la prudence en cas de doute sans renvoyer vers une autre action tant que cette action n'est pas disponible à l'écran ? »

---

## 6. Points maintenus ouverts ou non approuvés (points P86-12 et P86-13)

Ces deux points ne reçoivent **aucune proposition de clôture**. La recommandation est de les **maintenir**, avec la manière dont la réalisation doit les traiter pour ne pas en dépendre à tort.

### P86-12 — O-5, O-6, O-9, O-10 (OUVERT)

| Point | Situation et référence | Risque si ouvert | Options réelles | Recommandation par défaut (PROPOSITION) | Conséquences R7a / R4a / G6 / tests / 0022 | Décision PO attendue |
|---|---|---|---|---|---|---|
| **O-5** Réception « à contrôler » (entrée directe en EN_CONTROLE) | P81 plan §9, P85 §5.1 : OUVERT. DP5-R1 à R5 : les quatre entrées vont **dans DISPONIBLE** | Une réception dont la qualité n'est pas vérifiée entre directement dans le vendable | (a) rester ouvert, entrée en DISPONIBLE seulement (état actuel du plan) ; (b) trancher avant la réalisation | **Maintenir OUVERT.** Le plan ne prévoit aucun chemin d'entrée en EN_CONTROLE ; l'état EN_CONTROLE s'atteint uniquement par passage (R4b). Le rapport le mentionne en limite | *R4b* : aucune entrée directe créée ; *R7a* : aucune ; *G6* : mention au rapport ; *tests* : un scénario de non-création (une réception crée toujours du DISPONIBLE) ; *0022* : le schéma ne l'exclut pas, le service ne l'offre pas | Aucune décision requise pour la réalisation ; à trancher avant un lot ultérieur « réceptions » |
| **O-6** Retour client en contrôle d'abord | P81 plan §9 ; DP5-R6 : **REPORTÉ** ; P85 §5.1 : OUVERT / REPORTÉ | Un article revendable retourné reste vendable sans contrôle (comportement actuel) | (a) rester reporté ; (b) trancher | **Maintenir REPORTÉ.** Le retour client garde son comportement actuel (entrée directe en DISPONIBLE, quantités vendues et lots d'origine) | *R4c, R4d* : aucun changement des retours ; *G6/tests* : non-régression des retours (V6-17/ventes P43) ; *0022* : aucun | Aucune décision pour la réalisation ; à trancher avant le lot « retours » |
| **O-9** État de la migration 0021 en DEMO | P81 plan §9 ; P85 §5.1 : OUVERT, concerne **G7**. OBSERVATION P86 (T-9) : la copie de version exécutée par la DEMO ne liste que jusqu'à **0020** ; l'état de la **base** DEMO n'est pas lu (fichiers de bases et de codes non ouverts) → **NON VÉRIFIÉ** | Une application future de 0022 sur une base DEMO sans 0021 serait refusée ou mal ordonnée | (a) rester ouvert ; (b) mission d'état de la DEMO, hors P86 | **Maintenir OUVERT.** Hors périmètre de la réalisation (G7 non autorisée) ; consigné comme **préalable à toute décision G7** | *Aucun lot* ; condition d'arrêt 7 : aucune lecture de la DEMO ; *G6* : le rapport n'affirme rien sur la DEMO | Aucune décision pour la réalisation ; à instruire avant toute demande de G7 |
| **O-10** Prérequis du Lot 2 (CAT-14 à CAT-16) | P81 plan §9 ; plan directeur v1.3.2 l. 497 : critère du Lot 2 = « CAT, CAT-14 à 16 verts » ; T-11 : **aucun** fichier de test ne contient ces identifiants ; T-15 : collision de noms avec les scénarios CAT-01…CAT-14 de la matrice P84. Statut réel du Lot 2 : **NON VÉRIFIÉ** (documents du plan directeur non analysés en P86) | La clôture du Lot 3 (STOCK) pourrait être déclarée alors que son prérequis (Lot 2) n'est pas établi | (a) rester ouvert, G6 sans claim de clôture du Lot 3 ; (b) mission d'examen du Lot 2 avant G6 | **Maintenir OUVERT.** Le rapport de réalisation emploie « livraison du candidat STOCK » et **jamais** « clôture du Lot 3 » ; il renvoie O-10 | *R6* : formulation ci-dessus ; *R4e* : scénarios CAT-01…CAT-14 de la matrice P84 **non confondus** avec CAT-14 à 16 du plan directeur ; *G6* : rappel de O-10 dans la décision ; *0022* : aucun | Aucune décision pour la réalisation ; décider plus tard s'il faut un examen du Lot 2 avant de déclarer le Lot 3 clos |

**Confirmer P86-12** = confirmer que ces quatre points restent ouverts et qu'aucun ne bloque la réalisation, tout en interdisant au rapport de les présenter comme tranchés.

### P86-13 — V-3 : formule CAT-11 et convention P82 (NON APPROUVÉ)

1. **Situation et référence.** P83 §7 (V-3 : VIGILANCE, NON APPROUVÉ) ; P82 spécifications §8.2 : identité `reçu + corrections nettes = disponible + en contrôle + quarantaine + perte + sorties nettes`, avec la convention de calcul : contre-passations de réception = **corrections nettes** ; retours clients (`ENTREE` de source `sales.*`) = **déduction des sorties nettes** ; « reçu » = réceptions et régularisations (`purchasing.receipt`, `stock.regularization`, `stock.manual` historique), **ni** retours **ni** contre-passations. Seule **DÉCIDÉE** : la formule du registre (DP1-R9), « sous réserve de tests de cohérence préparés avant réalisation » (C4). P82 : « convention de calcul, non règle métier ».
2. **Risque si ouvert.** CAT-11 est un critère du Lot 3 (plan directeur). Si la convention est mal écrite, l'identité peut être vraie par construction (tautologique) ou fausse sur des cas réels (retours, contre-passations) ; le test CAT-11-a la verrouille, mais sur une convention que le PO n'a pas approuvée. Un test vert pourrait donner une assurance qui n'a pas été validée.
3. **Options.** (a) Le PO approuve maintenant la convention de P82 ; (b) **maintenir NON APPROUVÉ**, instruire en R1 sur la photographie 0021 (relevé des 20 lectures `SUM(delta)`), et soumettre au PO l'écriture exacte **avant G6** si elle diffère du registre ; (c) rouvrir DP1-R9 (**exclu** : décision antérieure).
4. **Recommandation (PROPOSITION).** Option **(b)**. Précisions : (i) le **côté « reçu »** est calculé depuis `purchase_receipt_lines` (source indépendante, P82 §9, CAT-11) pour que l'identité ne soit pas tautologique ; (ii) les tests CAT-11 sont écrits **avec** la convention de P82 **et** vérifiés sur des cas simples où la lecture littérale du registre et la convention donnent le même résultat ; (iii) tout écart de lecture est consigné comme **tel** (tableau cas par cas : réception, contre-passation, retour client, régularisation, correction) ; (iv) **aucun PASS de CAT-11 n'est présenté comme approbation de V-3**.
5. **Conséquences.** *R1* : relevé et tableau de cas. *R3/R4e* : tests CAT-11-a et suivants ; *R4a* : aucune ; *R7a* : aucune ; *G6* : décision du PO sur l'écriture exacte (une ligne dans la décision, §7 règle 6) ; *0022* : la vue `stock_lot_received` est définie selon la convention ; si le PO décidait autrement, la vue changerait **avant G6** (le candidat n'étant pas officiel, aucune migration supplémentaire).
6. **Décision PO attendue.** « Souhaitez-vous que la règle de calcul de l'identité entre quantités reçues, sorties et restantes soit soumise à votre lecture, avec des exemples chiffrés, avant la décision de promotion ? » (Une approbation immédiate de la convention de P82 est possible mais **n'est pas recommandée avant les exemples chiffrés**.)

---

## 7. Règle claire de promotion, de report et de blocage de G6 (PROPOSITION)

**Statut.** Ce chapitre **propose** une règle ; il ne la décide pas. Il combine les points P86-1, 5, 6, 7, 8 et 11. Il **ne contredit pas** A-T1 (manifeste, gel R4a → G6, chargeur idempotent, garde de démarrage, A1-K1 à K5) ni A-T2 (phase 2 suspendue) ni A-T3 (R7a exclusivement C-P82-2 et C-P82-3).

### 7.1 Trois catégories de vérifications

| Catégorie | Contenu | Effet si non vrai |
|---|---|---|
| **BLOQUANT (B)** : intégrité et sécurité | C1, C2, C3, C4, C5, C6, C7a, C8 ; C9 (partie obligatoire) ; A1-T1a à T5c ; garde de démarrage ; refus nommés de contre-passation et de passage en PERTE sans confirmation (R7a-S01 à S07) ; aucune promotion partielle (A1-K5) ; aucun niveau négatif ; chaîne d'audit | **G6 non proposée** ; arrêt si la cause est un défaut de la livraison (§7.4) |
| **RÉSERVE (R)** : reportable **avec accord écrit du PO** | C7b (D1 local) ; V6-10 à V6-17, V6-19 ; R7a-C02 à C07 ; scénarios de phase 2 | G6 peut être proposée **avec la réserve inscrite en tête du rapport** ; le PO accepte ou refuse la réserve **dans sa décision** |
| **NON AUTOMATISABLE (H)** | R7a-C08 (relecture arabe humaine) ; V-3 (lecture du PO) ; O-5, O-6, O-9, O-10 | n'est jamais PASS ; inscrit **NON VÉRIFIÉ / OUVERT** ; la **décision du PO** est requise pour C08 si G7 est un jour demandée |

### 7.2 Règle de décision (liste de contrôle pour G6)

1. **Toutes** les vérifications (B) sont PASS, avec preuve (commande, résultat, empreinte).
2. Chaque vérification (R) est **PASS** ou porte une **mention explicite** « NON EXÉCUTÉ — motif — risque résiduel » ; aucune n'est comptée comme réussie sans exécution.
3. Le **verrou d'exposition** est consigné : **aucune copie de version** contenant la livraison n'a été construite depuis le dépôt ; **G7 non proposée** tant que les réserves (R) et (H) concernant l'interface ne sont pas levées ou acceptées par écrit.
4. Le rapport affiche, **en tête**, la liste « NON EXÉCUTÉ / NON VÉRIFIÉ » (scénarios, contrôles, réserves).
5. O-5, O-6, O-9, O-10 : rappelés comme ouverts ; la livraison n'est pas qualifiée de « clôture du Lot 3 ».
6. **V-3** : écriture exacte de l'identité CAT-11 soumise au PO avec exemples chiffrés ; aucune approbation implicite.
7. La décision G6 est **une décision distincte et écrite du PO** (C8). Le rapport de réalisation ne la contient pas.

### 7.3 Conditions de report (G6 non proposée mais mission valide)

- Capacité disponible ultérieurement : les vérifications (R) peuvent être rejouées à PC-1 **sans refaire** R0 à R5, à condition que les empreintes du manifeste M2 restent **identiques** (aucun fichier modifié) ; sinon nouveau manifeste.
- Le PO refuse une réserve (R) : G6 est reportée ; la mission peut proposer d'exécuter la vérification concernée dès que la capacité existe.

### 7.4 Critères de blocage et arrêt

Reprise des **15 conditions d'arrêt** de P84 dossier §13.2 et des **S-1 à S-7** de P84 étude §3.6 (voir plan unique), **plus** les conditions propres à P86 :

| # | Condition ajoutée (PROPOSITION) | Effet |
|---|---|---|
| P86-S1 | R7a exige une **modification de schéma** ou de la migration 0022 après R5 | arrêt ; informer le PO ; R5 rejoué après décision |
| P86-S2 | R7a exige de modifier un écran hors de la liste de P85 §4.2 | arrêt (extension de périmètre, NON AUTORISÉ) |
| P86-S3 | Un test de service R7a-S04 à S06 passe **sans** la confirmation côté service | arrêt (garde-fou inopérant) |
| P86-S4 | La V3 ou la V1 afficherait une clé brute, un `{variable}` non remplacé, ou une quantité **recalculée** côté interface | arrêt (R7a-AC2, AC3) |
| P86-S5 | Un scénario non exécutable serait comptabilisé comme PASS dans un rapport | arrêt (falsification) |

### 7.5 Traitement des scénarios non exécutables

| Statut autorisé | Quand | Interdit |
|---|---|---|
| **PASS** | exécuté, résultat conforme, preuve consignée | — |
| **FAIL** | exécuté, résultat non conforme | masquer l'échec |
| **NON EXÉCUTÉ** | scénario spécifié non lancé (capacité, phase 2, hors périmètre) ; motif et risque résiduel écrits | l'appeler PASS, « supposé PASS » ou « vérifié par lecture » |
| **NON VÉRIFIÉ** | contrôle d'un attribut que ni test ni exécution n'a établi (par exemple relecture arabe) | idem |
| **REPORTÉ** | décision explicite du PO de le traiter plus tard | le perdre de la liste |

Chaque rapport comporte un tableau **un identifiant → un statut → une preuve ou un motif** pour les 167 scénarios de la matrice P84, les A1-T*, R7a-S* et R7a-C*. Un **comptage** global (PASS / FAIL / NON EXÉCUTÉ / NON VÉRIFIÉ) en tête permet de vérifier qu'aucun scénario n'est omis.

---

## 8. Dépendances croisées, contradictions et vigilances

### 8.1 Matrice de dépendances entre les 13 points

| Point | Dépend de | Influence | Nature |
|---|---|---|---|
| P86-1 (critère) | P86-6 (C7), P86-7 (C9), P86-5 | P86-11, §7 | Le critère adopté **est** la liste de contrôle de G6 ; C7a/C7b et C9 n'existent que si P86-6 et P86-7 sont confirmés |
| P86-2 (adaptation des 3 tests) | P86-3 (noms) | P86-1 (C5), P86-6 | Adaptation faite **avant** le chargeur ; `schema_parite` reste lié au banc D1 pour son exécution |
| P86-3 (noms) | — | P86-2, tests A1-T* | À fixer avant le gel : bloque l'écriture des tests A1-T* |
| P86-4 (seuils) | — | P86-5, P86-6, P86-9 | Conditionne toute exécution ; aucune suite sans seuil documenté |
| P86-5 (phase 2) | P86-4 | P86-6, P86-7, P86-9 | Le report par défaut détermine ce que signifie « NON VÉRIFIÉ » ailleurs |
| P86-6 (C7/MIG-T2) | P86-5 | P86-1, §7 | Scinde le critère ; la réserve C7b revient au PO à G6 |
| P86-7 (G6 conditionnel) | P86-5, P86-8, P86-10 | P86-1 (C9), §7 | La liste « obligatoire / reportable » dépend de ce que R7a expose (P86-8) |
| P86-8 (point d'entrée PERTE) | — | P86-7, P86-10, plan R7a | **Point pivot** : lecture (2) réduit les contrôles reportables (aucun écran de passage) et justifie la formulation neutre de P86-10 |
| P86-9 (V1 et V3) | P86-4, P86-5 | P86-7 | Deux interfaces à lister ; V3 non construite sans capacité |
| P86-10 (MSG-4) | P86-8 | R7a-S12 | Sous (2) : formulation neutre ; sous (1) : MSG-4 complet |
| P86-11 (séquencement) | P86-7, P86-9 | plan unique | R7a avant R5 ; fixe la place du verrou |
| P86-12 (O-5/6/9/10) | — | rédaction du rapport | Maintien ouvert ; formulations interdites |
| P86-13 (V-3) | P86-1 | §7 règle 6 | La décision de G6 comporte une ligne distincte pour V-3 |

**Ordre de décision conseillé (aide à la réponse, non contrainte)** : P86-8 → (P86-10, P86-7, P86-9) → (P86-5, P86-6, P86-4) → (P86-1, P86-2, P86-3, P86-11) → (P86-12, P86-13). Une réponse globale « je confirme 1 à 13 » est cohérente ; si le PO change P86-8 vers la lecture (1), les points P86-7 et P86-10 doivent être modifiés **ensemble** (voir §10, colonne « effet de la modification »).

### 8.2 Contradictions identifiées et résolution proposée

| # | Contradiction ou tension | Textes concernés | Résolution proposée (PROPOSITION) |
|---|---|---|---|
| X-1 | « Une condition non vérifiée bloque G6 » vs « contrôles exécutés **ou** listés NON VÉRIFIÉS » | P84 §2.5 (C7) vs P85 R7a-AC6 | Trois catégories B/R/H (§7.1) ; une réserve (R) ne bloque que sans **accord écrit** du PO |
| X-2 | R7a « exclusivement » deux messages vs confirmation PERTE nécessitant un point d'accès | P85 §4.1 vs R7a-L2 | Lecture (2) : composant sans écran d'accès (P86-8) |
| X-3 | MSG-4 dans la confirmation vs passage en EN_CONTROLE absent de l'interface | P85 §4.2 vs UI-2 hors R7a | Formulation neutre (P86-10) |
| X-4 | Gel de livraison R4a→G6 vs adaptation de tests et ajout de R7a dans la fenêtre | P85 §2.1 (A1-M2) | L'adaptation des trois tests et le noms du manifeste sont faits **avant** le gel (R1) ; R7a est **dans** la fenêtre, donc couvert par le manifeste (P86-11) |
| X-5 | MIG-T3/MIG-T4 : numérotation différente selon documents | P84 étude CL-4/CL-5 vs matrice P84 et P85 | Matrice P84 et P85 prévalent ; table de correspondance fixée en R1 (T-14) |
| X-6 | CAT-14 à 16 (plan directeur, Lot 2) vs CAT-01…CAT-14 (matrice P84) | plan directeur l. 497 vs matrice | Deux objets distincts ; préfixes explicites dans les rapports (T-15) ; lien avec O-10 |
| X-7 | Repli « B restreinte » de P84 §14.6 | P84 vs P85 | **Sans objet** : A-T3 approuvé (T-13) |
| X-8 | « Tests de composant sans navigateur » vs absence d'outil de simulation DOM | P85 §4.6 vs T-7 | Tests de chaînes et de logique extraite ; ce qui n'est pas atteignable passe en NON VÉRIFIÉ (jamais PASS) |
| X-9 | Seuil de 800 Mo (P82 §12) vs grille P84 §3.4 vs mémoire disponible ≈ 644 Mo | P82, P84, T-10 | P86-4 : planchers P84, durcissables seulement ; arrêt à 350 Mo ; **toute suite exige ≥ 500 Mo disponibles**, ce qui est tout juste respecté aujourd'hui (OBSERVATION) |
| X-10 | Marge disque très faible (≈ 48 000 Ko au-dessus du seuil d'arrêt avec 300 000 Ko de `TMPDIR`) ; baisse de 8 308 Ko depuis P85 | P85 §3.3 (d), T-10 | Taille du `TMPDIR` fixée **après** mesure en R0, pouvant être inférieure à 300 000 Ko ; si la marge restante est inférieure à zéro, la phase 1 n'est **pas lancée** (BLOQUÉ) |

### 8.3 Vigilances à ne pas perdre (hors des 13 points)

| Réf. | Vigilance | Traitement proposé |
|---|---|---|
| **DP2-R1** | Suivi par lot **obligatoire** pour le type MÉDICAMENT (création et adoption) : touche les écrans de catalogue (V1 `catalogue.js`, V3 `CatalogForms.jsx`, `reference.js`, `ReferenceCatalog.jsx`) où la case de suivi par lot a une valeur par défaut « non » | **Hors R7a** (R7a-AC7). Traitement en R4e **côté service** (rejet de création ou d'adoption d'un MÉDICAMENT sans suivi par lot) ; **côté écrans**, mission d'interface distincte : consigné **NON VÉRIFIÉ** (formulaire non adapté). À signaler au PO comme conséquence : tant que l'écran n'est pas adapté, **un MÉDICAMENT créé par l'écran avec la case par défaut sera refusé par le service** avec un message générique. **À CONFIRMER** par le PO s'il accepte ce comportement transitoire (**P86-V1, nouvelle vigilance, hors des 13 points, non décisionnelle**) |
| **V-5** | C-P82-1 sur ERREUR_SAISIE et AUTRE (précaution sur les corrections négatives) | Inchangée ; tests PER/NEG correspondants spécifiés ; aucune décision |
| **V-6** | Restitution de C-P82-2 et C-P82-3 à l'utilisateur | Traitée par R7a (A-T3) ; reste vigilance jusqu'à vérification de la livraison |
| **V-4** | Couplage code/schéma | Traitée par A-T1 ; levée **seulement** à la vérification de la réalisation |
| **O-7, O-8** | Comportement `ALTER TABLE … ADD COLUMN` sur table `STRICT` ; volumétrie | Levés **dans la limite du banc** en R2 ; D1 réel et volumétrie réelle restent NON VÉRIFIÉS |

---

## 9. Chantier B — Cohérence globale avec les décisions antérieures

Contrôle effectué sur les **textes** (aucune exécution). « Respecté » signifie que les propositions de ce dossier **ne contredisent pas** la décision citée ; le respect effectif du code futur reste à démontrer par les tests listés.

| Décision ou règle | Exigence | Proposition P86 concernée | Verdict documentaire | Preuve future |
|---|---|---|---|---|
| **DP1-R4** irréversibilité de PERTE | aucun retour de PERTE | P86-8 (aucun écran de sortie), R7a-E9 | Respecté | PER-47, R7a-S12/S13 |
| **DP1-R7** sorties de Q et destruction de P reportées | pas de fonction de sortie | P86-10 (aucune suggestion), R7a-E3, MSG-8 | Respecté | REV-36, R7a-S03 |
| **Correction négative vs sortie de QUARANTAINE** | une correction négative n'est **pas** une sortie de Q (C-P82-1, Q-9, V-5) | Aucune proposition n'ouvre une voie de sortie ; R7a-E3 ne renvoie pas vers une correction | Respecté ; V-5 inchangée | NEG-*, E-6 (test de sensibilité) |
| **C-P82-2** (refus de contre-passation expliqué, quantités) | restitution obligatoire | R7a (E1, E2) ; P86-9 (V1 et V3) ; P86-7 (S01, S02, S10, S11 obligatoires) | Respecté | R7a-S01, S02, S10, S11 |
| **C-P82-3** (confirmation avant PERTE) | confirmation explicite, quantité, irréversibilité | R7a (E5 à E8) ; P86-8 : protection **côté service** | Respecté, avec la limite : visible à l'utilisateur seulement quand l'écran de passage existe | R7a-S04 à S06, S12, S13 |
| **Q-11 / V2-R8** droit de passage non accordé par défaut | garde-fou | P86-8 (droit conservé), aucune extension des rôles | Respecté | PER (droits), compteur de droits adapté (plan P82 §3.3) |
| **DP3-R3, DP4-R6, H-5, V1-R1, INV-10** réservations | une réservation n'empêche ni QUARANTAINE, ni PERTE, ni EN_CONTROLE, ni blocage de lot | Aucune proposition P86 ne touche aux réservations ; R4c reste conforme au plan P82 | Respecté | test d'étanchéité `stock.transfer` |
| **DP4-R4 / V1-R10** états et lots (périmés, bloqués) | sorties limitées aux motifs retrait/destruction | Inchangé | Respecté | NEG-* |
| **DP5-R1…R5** entrées | quatre entrées dans DISPONIBLE ; un changement d'état n'augmente pas le physique | P86-12 (O-5, O-6 ouverts) | Respecté | scénario de non-création |
| **DP1-R9 / C4 / V-3** | formule acceptée, tests préparés avant | P86-13 | Respecté ; V-3 NON APPROUVÉ maintenu | CAT-11-a |
| **DP6-R1…R7** candidat hors dossier officiel, aucune application | candidat jamais promu sans G6 ; aucune base réelle | plan unique ; P86-7 (verrou), P86-11 | Respecté ; G6 et G7 distinctes | CL-1, CL-8, CL-10 |
| **Q-1…Q-12, H-1…H-6** | non rouverts | aucune proposition ne les modifie ; P86-8/P86-10 respectent H-5, H-6 | Respecté | — |
| **A-T1** (manifeste, gel, chargeur, garde ; `compose.ts` limité à la garde) | A1-K1 à K5 | P86-1, 2, 3, 11 | Respecté ; aucune extension de l'autorisation sur `compose.ts` | A1-T1a à T5c |
| **A-T2** (phase 1 sous conditions, phase 2 suspendue) | A2-K1 à K4 ; aucune suppression, aucun benchmark | P86-4, 5, 6 | Respecté ; PC-1 n'exécute la phase 2 que si le PO a **déjà** décidé et mis en œuvre la capacité | S-1…S-7 |
| **A-T3** (R7a borné, exclusivement 2 messages) | pas de refonte | P86-8 (2), 9, 10, 11 | Respecté ; option (1) de P86-8 est signalée comme **extension** | R7a-AC7 |
| **Absence de promotion partielle ou incohérente** | A1-K5, C2, C3, C8, CL-1…CL-9 | §7, P86-1 | Respecté ; une réserve (R) n'autorise pas une promotion **partielle de fichiers** : elle ne porte que sur des **contrôles non exécutés**, jamais sur des éléments livrés | manifeste M2 |
| **Traçabilité et droits** | passages tracés (quantité, motif, auteur, date) ; droits distincts | R7a-E6 (la confirmation porte la quantité) | Respecté | PER-04/05, INV-7 |

**Conclusion Chantier B (documentaire).** Aucune contradiction avec DP-1…DP-6, C1–C4, Q-1…Q-12, H-1…H-6, C-P82-1…3, A-T1, A-T2, A-T3. Les seules tensions relevées (§8.2) portent sur des **textes de propositions** (P84) ou sur des **interprétations** que ce dossier soumet au PO ; elles ne rouvrent aucune décision.

---

## 10. Tableau unique des décisions attendues du PO

**Mode de réponse.** « Je confirme les recommandations P86-1 à P86-13. » — ou — « Je confirme, sauf P86-n : [option] ». Chaque ligne est indépendante, sauf les liens indiqués. **Rien n'est décidé tant que le PO n'a pas répondu.**

| P86-n | Question métier posée au PO | Recommandation (PROPOSITION) | Autres options | Lien / effet d'une modification |
|---|---|---|---|---|
| **1** | Acceptez-vous une liste fixe de neuf vérifications comme condition pour proposer la promotion ? | Oui, liste révisée (C1…C9, C7 scindé) | liste initiale à huit (C7 inatteignable) ; jugement au cas par cas | liée à 5, 6, 7 |
| **2** | Trois contrôles existants rendus insensibles au simple ajout de la migration, sans affaiblissement ? | Oui, en R1, rapport des modifications | adapter à G6 ; ne pas adapter (déconseillé) | — |
| **3** | Nommage technique laissé à l'équipe, simplement rapporté ? | Oui, fixé une fois en fin de R1 | le PO fixe les noms | — |
| **4** | Limites de sécurité de l'hôte (disque, mémoire) : celles proposées, durcissables, jamais assouplies ? | Oui | valeurs approuvées telles quelles ; autres valeurs du PO | liée à 5 |
| **5** | Essais lourds (D1 local, navigateur) reportés et marqués non vérifiés si aucune capacité n'est fournie ? | Oui, avec porte de capacité PC-1 avant G6 | mémoire supplémentaire ; autre hôte (à décider et mettre en œuvre par le PO) | liée à 6, 7 |
| **6** | Comparaison avec D1 local traitée à part ; réserve décidée à G6 ? | Oui (C7a obligatoire, C7b réserve) | C7 bloque G6 ; D1 retiré (déconseillé) | liée à 1, 5 |
| **7** | Promotion proposable avant les contrôles visuels et linguistiques, sous condition qu'aucun utilisateur n'y soit exposé avant ? | Oui, conditionnel (verrou d'exposition) | G6 attend tout ; G6 sans condition | liée à 8, 10 ; si « G6 attend », la mission s'arrête à R6 et PC-1 devient bloquant |
| **8** | Aucun écran de passage en PERTE tant que la mission d'interface n'est pas faite ? | Oui (lecture 2) | écran réduit dans R7a (lecture 1) | **si (1) : modifier aussi 7, 10 et 11** (périmètre R7a élargi, contrôles navigateur indispensables avant exposition) |
| **9** | Les deux messages dans les deux versions de l'interface (on-premise, SaaS) ? | Oui | une seule version | liée à 7 |
| **10** | Texte de confirmation : prudence en cas de doute, sans renvoi vers une action indisponible ? | Oui (formulation neutre) si lecture (2) | MSG-4 complet ; aucune mention | liée à 8 |
| **11** | Les deux messages font partie de la même livraison figée, réalisés avant la non-régression finale ? | Oui : R4e → R7a → R5 → R6 → G6 | R7a après R5 (R5 rejoué) ; R7a hors gel (déconseillé) | liée à 7, 9 |
| **12** | O-5, O-6, O-9, O-10 maintenus ouverts, aucun ne bloquant la réalisation ? | Oui | trancher un ou plusieurs avant la réalisation | aucun ne devient décidé par confirmation |
| **13** | V-3 : règle de calcul soumise à votre lecture avec exemples chiffrés avant la promotion ? | Oui | approuver tout de suite la convention de P82 (non recommandé avant exemples) | — |
| *(info)* | **P86-V1 (hors des 13 points)** : acceptez-vous le comportement transitoire du suivi par lot obligatoire pour un MÉDICAMENT (refus par le service, formulaire non adapté jusqu'à la mission d'interface) ? | **À CONFIRMER** ; non couvert par les 13 points | voir §8.3 | **ne bloque pas** la réponse sur 1 à 13 ; à traiter avant R4e |

**Ce qui n'est PAS demandé :** autoriser la réalisation. Cette autorisation est **distincte** (plan unique, §14) et n'est pas présumée par la confirmation des 13 points.

---

## 11. Limites de ce dossier (LIMITE / NON VÉRIFIÉ)

- Analyse **documentaire** et lectures légères ; aucune exécution, aucun test, aucune mesure : **NON TESTÉ / NON MESURÉ** (consommation des suites, durée de R5, comportement avec `TMPDIR` redirigé, capacité de construction de la V3).
- Absence d'outil DOM : T-7 est une observation sur une liste de modules, **non exhaustive**.
- Statut réel du Lot 2 et de CAT-14 à 16 (plan directeur) : **NON VÉRIFIÉ**.
- État de la base DEMO (O-9) : **NON VÉRIFIÉ** (aucune lecture).
- Compatibilité D1 réelle de la migration (O-7) : **NON VÉRIFIÉE**, hors portée des bancs.
- Les références de ligne sont celles des fichiers au 25/11/2026 (HEAD `35c7a70`).
- Aucune recommandation de ce dossier n'est une décision PO.

---
Fin du dossier P86 — arbitrage consolidé.
