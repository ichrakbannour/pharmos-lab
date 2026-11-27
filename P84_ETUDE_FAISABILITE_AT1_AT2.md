# P84 — Étude de faisabilité A-T1 (cohérence livraison / version) et A-T2 (capacité et ressources)

Date : 25/11/2026. Nature : étude documentaire, **aucune exécution**. Données fictives uniquement.
Sources : `docs/P81_REGISTRE_DECISIONS_PO_STOCK.md`, `docs/P81_PLAN_MIGRATION_0022.md`, `docs/P82_SPECIFICATIONS_FINALES_STOCK.md`, `docs/P82_PLAN_REALISATION_STOCK_0022.md`, `docs/P83_ADDENDUM_DECISIONS_PO_STOCK.md`, `docs/P83_RAPPORT_EXECUTION.md`, `docs/P80_RAPPORT_EXECUTION.md`, `docs/P70_RAPPORT_EXECUTION.md`, et relevés de code et d'hôte en lecture seule du 25/11/2026 (11:00 à 11:10 UTC).
Les décisions PO (P81, P82, P83) ne sont ni rouvertes ni modifiées. A-T1 et A-T2 sont des **arbitrages techniques** : leurs recommandations sont des **PROPOSITIONS**.
Libellés : FAIT VÉRIFIÉ · OBSERVATION · HYPOTHÈSE · PROPOSITION · NON MESURÉ · NON TESTÉ · ARBITRAGE (décision demandée).

---

## 1. Synthèse

| Point | Conclusion | Statut |
|---|---|---|
| A-T1 | Le risque de livraison intermédiaire est réel : le dépôt reçoit des sauvegardes automatiques à des moments arbitraires, donc **un commit n'est pas une livraison**. Une version construite entre R4 et G6 démarrerait contre des migrations officielles sans 0022. | FAIT VÉRIFIÉ + PROPOSITION |
| A-T1 — recommandation | **Livraison = manifeste d'empreintes validé**, jamais un état du dépôt. Gel de livraison de R4 à G6. Garde de démarrage « niveau de schéma requis ». Chargeur de candidat **idempotent** (la promotion devient un simple déplacement de fichier). | PROPOSITION (arbitrage A-T1) |
| A-T2 | Le plan P82 sous-estimait un fait : **`/tmp` est un tmpfs de 1 Go saturé à 100 %** et la mémoire disponible est d'environ 630 Mo. Les bancs « fichiers » et « D1 local » écrivent sous `/tmp` (voir §3.2). | FAIT VÉRIFIÉ ; conséquence NON TESTÉE |
| A-T2 — recommandation | **Réalisation en deux phases** : phase 1 sur bancs mémoire (puis fichiers avec dossier temporaire redirigé sur disque, sous budget) ; **phase 2 (D1 local et navigateur) seulement après arbitrage du PO sur les ressources**. G6 exige la parité D1 (phase 2). Aucun nettoyage ni réglage de l'hôte n'est entrepris par la mission. | PROPOSITION (arbitrage A-T2) |
| Seuil de 800 Mo | Hypothèse conservée comme seuil d'**arrêt disque**, complétée d'un seuil de **lancement** (1 000 Mo) et de seuils mémoire. Tous restent des **HYPOTHÈSES** à mesurer en R0. | HYPOTHÈSE |

---

## 2. A-T1 — Cohérence livraison / version

### 2.1 Faits relevés (FAIT VÉRIFIÉ, lecture seule)

| # | Fait | Source |
|---|---|---|
| F-1 | Le code charge les migrations du dossier **officiel** `pharmos/migrations/` (`DEFAULT_MIGRATIONS_DIR`), pour les plans `control` et `pharmacy`. Entrées de production : `local.ts` l. 74 et `saas.ts` l. 19 appellent `composeCore`. | `src/adapters/file/migrationFiles.ts` l. 16 ; `src/entry/compose.ts` l. 19-24 |
| F-2 | À l'ouverture d'une base, si des migrations du jeu chargé ne sont **pas** appliquées, l'ouverture est refusée (`tenant.migrations_pending`, UNAVAILABLE). Une base **en avance** sur le jeu chargé est refusée par `verifyHistory` (`migration.unknown_applied`). | `src/platform/tenants.ts` l. 222-226 ; `src/platform/migrations.ts` l. 53-60 ; plan P82 §6 (RM-2) |
| F-3 | `defineMigrationSet` trie par identifiant et **ne déduplique pas**. Deux sources de même identifiant produiraient deux migrations « 0022 » ; la table `_migrations` a `id UNIQUE` : l'application échouerait. | `src/platform/schema.ts` l. 11-16 ; `src/platform/migrations.ts` (`CREATE_HISTORY`) |
| F-4 | Les tests obtiennent leurs jeux via `migrationSets()` (`tests/support/env.ts` l. 27-33) : migrations officielles, puis fixture `tests/fixtures/9001_fixture_vente.sql`. Le banc D1 appelle `migrationSets(true)` (`tests/lot0c/d1Env.ts`). Trois fichiers de test lisent aussi le dossier officiel directement (`env.ts`, `demo/migration_0007.test.ts`, `p32/added_columns.test.ts`). | relevé `grep` |
| F-5 | `tests/lot1b/schema_parite.test.ts` affirme **22** identifiants (21 migrations officielles + la fixture 9001) ; deux tests simulent une ligne `0022` « en avance » (`tests/p33/fleet_inventory.test.ts` l. 95 ; `tests/p34/release_unverified.test.ts` l. 52). | lecture de fichiers |
| F-6 | La DEMO s'exécute depuis une **copie de version** `~/.pharmos-private/releases/p53ui-fb956a9/pharmos`, qui ne contient que les migrations jusqu'à **0020**. Elle ne lit pas le dépôt. | plan P82 §3.2 ; relevé `ls` (fichiers 0019 et 0020 en fin de liste) |
| F-7 | Le dépôt reçoit des commits « genspark auto-backup » à des heures non maîtrisées : 01:14, 01:33, 03:26 puis 09:01 le 25/11/2026. Aucune opération Git manuelle n'est autorisée (pas de tag, pas de branche). | `git log` |
| F-8 | Un « candidat » placé hors du dossier officiel n'est lu par aucun chemin de production (F-1). | plan P82 §4.3 |

### 2.2 Risques de livraison partielle ou incohérente

| Réf. | Risque | Conséquence | Gravité |
|---|---|---|---|
| RL-1 | Une copie de version est construite depuis le dépôt entre R4 et G6 (le code lit `bucket`, le dossier officiel n'a pas 0022). | Erreur SQL « no such column » **tardive** (à la première lecture de stock), non à l'ouverture. | Élevée |
| RL-2 | Promotion de 0022 **sans** le code R4 (ou l'inverse) dans une même livraison. | Code et schéma de niveaux différents : bases refusées (F-2) ou fonctions en échec. | Élevée |
| RL-3 | Promotion alors que le chargeur de candidat des tests charge encore le dossier candidat. | Identifiant 0022 en double (F-3) : tous les bancs échouent. Échec **bruyant** donc sûr, mais bloquant. | Moyenne |
| RL-4 | Les tests `schema_parite`, `p33`, `p34` ne sont pas adaptés à la promotion. | Échecs de non-régression au moment de G6, au pire moment. | Moyenne |
| RL-5 | Candidat modifié **après** sa validation (R5) puis promu. | Migration promue différente de la migration essayée. | Élevée |
| RL-6 | Une sauvegarde automatique capture un état intermédiaire (par exemple entre le déplacement du fichier et l'adaptation des tests) et il est pris pour une version. | Livraison incohérente par confusion avec un commit. | Moyenne |
| RL-7 | Application de la migration à une base existante (DEMO ou autre) avant décision G7. | Violation de DP6-R2. | Critique |
| RL-8 | La documentation de référence (spécifications) change entre R3 (tests rouges) et R6. | Tests et code ne vérifient plus la même règle. | Moyenne |

### 2.3 Séquencement recommandé (PROPOSITION)

Principe : **trois « paquets » qui n'existent comme livraison qu'ensemble**, à un seul instant (G6).

| Étape | Contenu | Livrable ? | Remarque |
|---|---|---|---|
| R0 | Ligne de base : empreintes, tests de référence, ressources | non | **Manifeste M0** (empreintes de `src`, `migrations`, `tests`, `ui`, documents P81 à P84) |
| R1 à R3 | Fixture 0021, candidat hors dossier officiel, tests rouges | non | aucun fichier dans `migrations/pharmacy/` |
| **Gel de livraison** | **Commence avec R4a** : plus aucune copie de version ni construction à partir du dépôt jusqu'à G6 | — | porté par le rapport de lot, pas par Git (F-7) |
| R4a à R4e | Code | non | le code est exécuté **uniquement par les tests** |
| R5 | Non-régression ciblée et contrôles d'intégrité | non | fige l'empreinte du candidat (**C3**/CL-6) et établit la non-régression (**C5**, §2.5) |
| R6 | Rapport de réalisation | non | **Manifeste M1** = jeu candidat à promouvoir |
| R7a (si arbitrage **A-T3**, défini au dossier final §14.6 et au rapport) | Messages d'interface obligatoires pour C-P82-2 et C-P82-3 (quantités par état, motif, confirmation PERTE) | non | avant G6 |
| **G6** | Décision PO distincte : promotion + code + tests adaptés | **oui, un seul ensemble** | **Manifeste M2** vérifié (§2.5) |
| G7 | Application à la DEMO ou à une base réelle | hors mission | décision distincte (DP6-R7) |

**Préparer G6 pour qu'il soit minimal** (PROPOSITION, à confirmer en R1) :
1. **Chargeur de candidat idempotent** : `migrationSets()` ajoute les sources candidates **sauf** celles dont l'identifiant existe déjà dans le dossier officiel, et vérifie alors que les sommes de contrôle sont égales. La promotion devient un **déplacement de fichier** sans modification du code de test (supprime RL-3).
2. **Adapter `schema_parite`, `p33`, `p34` avant G6**, en version valable **avant et après** promotion : nombre d'identifiants calculé (officiels + fixtures) et ligne « en avance » simulée avec un identifiant éloigné (par exemple `0099`) au lieu de `0022`. Ces trois adaptations sont listées et justifiées au rapport (supprime RL-4). Elles **ne relâchent aucune assertion** : elles remplacent un nombre figé par un nombre calculé.
3. **Garde de démarrage « niveau de schéma requis »** : la composition de production vérifie que le jeu officiel contient l'identifiant requis par le code (constante unique, par exemple `0022`) et **refuse de démarrer** avec une erreur nommée sinon. Effet : RL-1 devient un refus net à l'ouverture. **Point d'attention** : cela modifie `compose.ts` (fichier d'entrée de production) ; c'est une modification de code de R4 à justifier au rapport.

### 2.4 Contrôles empêchant une livraison partielle ou incohérente

| Réf. | Contrôle | Quand | Échec = |
|---|---|---|---|
| CL-1 | Somme de contrôle de `pharmos/migrations/` identique à M0 | chaque lot jusqu'à G6 (B21) | arrêt (condition 10 du plan P82) |
| CL-2 | Aucun fichier de version créé depuis le dépôt pendant le gel : liste de `~/.pharmos-private/releases/` inchangée (lecture seule) | chaque lot R4 à R6 | arrêt |
| CL-3 | Test **MIG-T1** : composition de production avec le jeu officiel **de 21 migrations** (construit dans le test par filtrage des identifiants `< 0022`) et le code R4 : **refus net** nommé | R3 (rouge), R4b (vert), puis **à G6 (toujours vrai)** | arrêt |
| CL-4 | Test **MIG-T3 (nouveau)** : composition avec « officiel + candidat » démarre et passe les contrôles B1 à B22 | R2 puis chaque lot | arrêt |
| CL-5 | Test **MIG-T4 (nouveau)** : chargeur idempotent — candidat déjà présent à l'officiel avec somme égale : accepté, **un seul** 0022 ; somme différente : refus nommé | R3 | arrêt |
| CL-6 | Empreinte du candidat (SHA-256) consignée à la fin de R5 ; **identique** à R6 et à G6 | R5, R6, G6 | arrêt (RL-5) |
| CL-7 | Les documents de référence (P81 à P84) : empreintes identiques à M0 pendant R3 à R6 | chaque lot | arrêt (RL-8) |
| CL-8 | À G6, le dossier officiel = M0 **plus exactement un fichier** `0022_*.sql` de somme égale à CL-6 ; les 21 fichiers précédents inchangés | G6 | refus de G6 |
| CL-9 | Après G6, non-régression ciblée complète : **mêmes nombres de tests réussis** que la ligne de base R0, plus les tests nouveaux | G6 | refus de G6 |
| CL-10 | Aucune base existante (DEMO, `.pharmos-private`) ouverte ni migrée ; lecture seule des listes de répertoires seulement | toujours | arrêt (condition 7) |

### 2.5 Critère objectif de validation (PROPOSITION)

**Une livraison STOCK/0022 est valide si et seulement si les huit conditions suivantes sont vraies simultanément**, vérifiées par commande et consignées dans le rapport :

| # | Condition | Preuve |
|---|---|---|
| C1 | Le manifeste M2 (empreintes de `src`, `migrations`, `tests`, `ui`, `tools`, documents) est établi **après** le dernier changement de fichier. | liste des empreintes |
| C2 | Le dossier officiel = M0 + un seul fichier `0022_*.sql`. | CL-8 |
| C3 | Ce fichier est identique (SHA-256) au candidat validé en R5. | CL-6 |
| C4 | MIG-T1, MIG-T3 et MIG-T4 réussissent. | résultats |
| C5 | La non-régression ciblée (plan P82 §9.5) donne les mêmes nombres de tests réussis qu'en R0, plus les tests nouveaux, **0 régression**. | CL-9 |
| C6 | Contrôles d'intégrité A1-A8 et B1-B22 vrais (égalité exacte) sur une copie jetable après les scénarios de référence. | rapport |
| C7 | Parité de schéma sur les bancs disponibles : **mémoire, fichiers et D1 local** (D1 : phase 2, A-T2). | MIG-T2 |
| C8 | Acceptation écrite du PO sur le rapport (G6). | décision |

Un manifeste incomplet, une condition fausse ou une condition non vérifiée (par exemple D1 non exécuté faute de ressources) **bloque G6** et se consigne comme tel. **Un commit de sauvegarde automatique n'est jamais une preuve de livraison.**

### 2.6 Alternatives écartées

| Alternative | Raison du rejet |
|---|---|
| Détection du schéma à l'exécution avec double chemin de code (avec ou sans `bucket`) | Double surface de test et de défauts ; reporte l'erreur au lieu de l'empêcher (plan P82 §4.4 e). |
| Promouvoir 0022 **avant** le code (migration seule d'abord) | Le jeu officiel avec 0022 est refusé par toute base non migrée (F-2) et le code actuel ignore `bucket` : états intermédiaires non testés ; viole le principe « un seul ensemble ». |
| Marquer la livraison par un tag ou une branche Git | **Interdit** (aucune opération Git manuelle). |
| Candidat dans le dossier officiel sous une extension ignorée par le chargeur | `readMigrationSources` filtre par motif de nom ; un renommage est une forme de promotion déguisée, difficile à auditer. |

### 2.7 Recommandation et arbitrage demandé

**ARBITRAGE A-T1** — Le PO confirme : (1) la définition de livraison par **manifeste** (§2.5) ; (2) le **gel de livraison** de R4a à G6 ; (3) l'autorisation de modifier `compose.ts` pour la garde de démarrage (point 3 du §2.3) ; (4) l'adaptation **avant G6** des trois tests de schéma et le chargeur idempotent (points 1 et 2). Sans réponse, la réalisation applique les points 1, 2 et le gel, et **s'arrête avant** la modification de `compose.ts` (condition d'arrêt 9 du plan P82).

---

## 3. A-T2 — Capacité et ressources (diagnostic, sans benchmark)

### 3.1 Faits relevés (lecture seule, FAIT VÉRIFIÉ)

| # | Fait | Source |
|---|---|---|
| K-1 | Disque `/` : 29 788 200 Ko au total, **1 148 140 Ko libres** (97 % utilisé). | `df -k` |
| K-2 | Mémoire disponible : **≈ 641 Mo** (`MemAvailable`), machine à 2 Go et 2 cœurs. | `/proc/meminfo` |
| K-3 | Mémoire partagée déjà occupée : `Shmem` ≈ **1 020 544 Ko** (le tmpfs `/tmp` en est la cause principale). | `/proc/meminfo` |
| K-4 | Un test D1 local précédent (P80) a consommé **≈ 1,45 Go** de mémoire. | rapport P80 (OBSERVATION héritée, non rejouée) |
| K-5 | Un incident de saturation mémoire en P70 a provoqué un redémarrage du bac à sable et la perte de la DEMO. | rapport P70 |
| K-6 | La DEMO est active (PID 5638) ; elle consomme une partie de la mémoire et ne doit pas être perturbée. | relevé de processus |
| K-7 | Le seuil de 800 Mo de mémoire libre cité au plan P82 §12 est une **hypothèse non approuvée**. | plan P82 §12 |

### 3.2 Fait nouveau : `/tmp` est un tmpfs plein

- `/tmp` est un système de fichiers en mémoire de **1 019 720 Ko, utilisé à 100 %** (0 Ko libre). **FAIT VÉRIFIÉ.**
- Le banc de test `tests/support/env.ts` et le banc D1 local `tests/lot0c/d1Env.ts` créent des répertoires temporaires par `mkdtemp` sous `os.tmpdir()`, donc sous `/tmp` par défaut. **OBSERVATION** (lecture du code).
- **HYPOTHÈSE** : les bancs « fichiers » et « D1 local » **échoueront** à créer leurs bases tant que `TMPDIR` pointe vers `/tmp`. **NON TESTÉ** (aucune écriture n'a été tentée, conformément à l'interdiction de benchmark).
- Libérer `/tmp` est un **nettoyage** : **NON AUTORISÉ** dans P84. Ce n'est pas proposé comme première option.
- Rediriger `TMPDIR` vers `/` (disque, 1,1 Go libre) déplace la charge du tmpfs vers le disque : cela évite la mémoire mais consomme le disque (voir §3.4).
- `/dev/shm` est un autre tmpfs ; il n'offre **pas de marge** réelle puisque `Shmem` est déjà à ≈ 1 Go : ne pas l'utiliser.

### 3.3 Besoins par étape (ESTIMATIONS, NON MESURÉ)

Ces ordres de grandeur viennent de la lecture du code et des rapports P66/P80. **Aucune mesure nouvelle n'a été faite en P84.** Ils servent à décider des précautions, pas à prédire une durée.

| Étape | Nature | Mémoire | Disque | Durée (ordre de grandeur) | Banc |
|---|---|---|---|---|---|
| R0 | Empreintes, relevés, tests de référence ciblés | faible (< 300 Mo) | négligeable | minutes | mémoire |
| R1–R3 | Fixture 0021, candidat, tests rouges | faible | quelques Mo (fichiers) | minutes | mémoire |
| R4a–R4e | Code (modifications, relectures) | négligeable | négligeable | hors machine (édition) | aucun |
| R5 | Non-régression ciblée (plan P82 §9.5) | **moyenne** (une suite à la fois) | quelques dizaines de Mo (bases jetables) | dizaines de minutes | mémoire puis fichiers |
| R5-D1 | Parité sur D1 local (MIG-T2, C7) | **élevée** (≈ 1,45 Go observé en P80) | centaines de Mo (état local `.wrangler`) | à mesurer | D1 local — **phase 2** |
| R5-UI | Vérifications navigateur FR/EN/AR, RTL, mobile | **élevée** (navigateur) | centaines de Mo (profil) | dizaines de minutes | navigateur — **phase 2** |
| R6 | Rapport, manifeste M1 | négligeable | négligeable | minutes | aucun |
| R7a | Messages d'interface (si A-T3) | négligeable | négligeable | hors machine | aucun |
| G6 | Promotion (déplacement d'un fichier), manifeste M2 | négligeable | négligeable | minutes | aucun |

### 3.4 Budget et seuils proposés (PROPOSITION, non approuvée)

| Seuil | Valeur proposée | Justification |
|---|---|---|
| Disque libre au **lancement** de la phase 1 | **≥ 1 000 000 Ko** | marge de 150 Mo au-dessus de l'état actuel : aucun besoin supplémentaire ne doit absorber plus que cela. |
| Disque libre **minimal** (arrêt) | **< 800 000 Ko** → arrêt | réserve pour le journal de la DEMO et le noyau ; à approuver. |
| Mémoire disponible au **lancement** d'une suite | **≥ 500 Mo** | actuellement ≈ 641 Mo ; une suite « mémoire » doit rester sous ce plafond. |
| Mémoire disponible **minimale** (arrêt) | **< 350 Mo** → arrêt immédiat de la suite | éviter de rejouer P70. |
| Mémoire pour la **phase 2** (D1 local, navigateur) | **≥ 1 800 Mo** disponibles **ou** arbitrage PO | cohérent avec K-4 ; **non atteint** aujourd'hui (K-2). |
| Taille de `TMPDIR` de remplacement | **≤ 300 000 Ko** | évite de consommer le disque restant. |

Le seuil de 800 Mo du plan P82 (K-7) est **remplacé par cette grille** à titre de proposition ; la grille n'est pas approuvée.

### 3.5 Séquencement léger en deux phases (PROPOSITION)

**Phase 1 — légère (autorisable avec les ressources actuelles, sous conditions)**
1. Redirection de `TMPDIR` vers un répertoire dédié sur le disque (`/home/user/work/p85-tmp`, hors dépôt, créé avec `test ! -e` ; **à créer uniquement dans la réalisation, pas en P84**), taille surveillée.
2. Une seule suite à la fois, en banc **mémoire** puis banc **fichiers**, avec `setsid -f` et un journal ; relevé disque/mémoire avant et après chaque suite.
3. Aucune suite D1 ni navigateur.
4. Aucune interférence avec la DEMO ; vérification de `/health` entre deux suites.
5. Arrêt à la première condition du §3.6.

**Phase 2 — lourde (NON autorisée par défaut ; arbitrage A-T2)**
1. Condition préalable : mémoire disponible ≥ 1 800 Mo **ou** acceptation écrite du risque par le PO.
2. Options : libérer de la mémoire (**décision PO**, hors P84) ; exécuter ces vérifications sur un autre hôte ; ou accepter que C7 (D1) et la partie navigateur de V-6 restent **NON VÉRIFIÉES** à G6 avec mention explicite au rapport.
3. Une seule suite lourde à la fois ; DEMO surveillée ; arrêt à 350 Mo.

### 3.6 Conditions d'arrêt (ressources)

| # | Condition | Action |
|---|---|---|
| S-1 | Disque libre < 800 000 Ko | arrêter la suite en cours ; ne rien supprimer ; rapporter |
| S-2 | Mémoire disponible < 350 Mo | arrêter immédiatement ; ne pas relancer sans arbitrage |
| S-3 | `/health` de la DEMO ne répond plus | arrêter ; ne pas redémarrer ; rapporter |
| S-4 | Échec de création de base temporaire (tmpfs plein, `ENOSPC`) | arrêter ; ne pas nettoyer `/tmp` ; demander arbitrage |
| S-5 | Une suite dépasse deux fois la durée de R0 pour le même périmètre | arrêter ; analyser avant de poursuivre |
| S-6 | Tout besoin de supprimer, déplacer ou réparer un fichier existant | arrêter (NON AUTORISÉ) |
| S-7 | Apparition de tags ou de stashes Git, ou d'un commit manuel | arrêter ; rapporter |

### 3.7 Ce qui n'est pas mesuré

- La consommation réelle de chaque suite sur cet hôte dans l'état actuel : **NON MESURÉ**.
- Le comportement des bancs fichiers et D1 avec un `TMPDIR` redirigé : **NON TESTÉ**.
- La durée de R5 : **NON MESURÉ** ; ordre de grandeur seulement.
- L'effet du navigateur sur la mémoire de l'hôte : **NON MESURÉ**.

### 3.8 Alternatives écartées

| Alternative | Raison du rejet |
|---|---|
| Vider ou nettoyer `/tmp` | **NON AUTORISÉ** ; destructif. |
| Supprimer d'anciennes archives pour libérer du disque | **NON AUTORISÉ** ; préservation P76/P77/P78. |
| Lancer un benchmark pour calibrer les seuils | **NON AUTORISÉ** en P84 (risque P70). |
| Augmenter la mémoire par réglage système | Changement de configuration : **NON AUTORISÉ**. |
| Exécuter toutes les suites d'un seul bloc | Pic mémoire inconnu ; risque de saturation (K-5). |

### 3.9 Recommandation et arbitrage demandé

**ARBITRAGE A-T2** — Le PO décide : (1) la grille de seuils du §3.4 (ou ses valeurs) ; (2) l'autorisation de **rediriger `TMPDIR`** vers un répertoire dédié sur le disque pendant la réalisation (phase 1) ; (3) la politique de **phase 2** : fournir plus de mémoire, exécuter ailleurs, ou accepter C7 et V-6 (navigateur) comme NON VÉRIFIÉS. Sans réponse : seule la phase 1 est envisageable, **après** création du répertoire dédié autorisée par le PO, et la réalisation **s'arrête** si `TMPDIR` ne peut être redirigé.

### 3.10 Conclusion et limites

- A-T2 est **analysé et recommandé** ; il n'est **pas résolu** sur le plan matériel : la mémoire disponible (≈ 641 Mo) est inférieure au besoin observé pour D1 (≈ 1,45 Go).
- La phase 1 est faisable sous conditions strictes ; la phase 2 dépend d'un arbitrage.
- Toutes les valeurs de §3.3 et §3.4 sont des estimations ou des propositions ; aucune n'a été mesurée en P84.

---
