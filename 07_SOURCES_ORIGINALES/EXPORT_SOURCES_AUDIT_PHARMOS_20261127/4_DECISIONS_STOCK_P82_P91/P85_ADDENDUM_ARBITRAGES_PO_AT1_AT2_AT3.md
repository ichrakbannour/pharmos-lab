# P85 — Addendum daté du 25/11/2026 : arbitrages PO A-T1, A-T2 et A-T3

**Statut : ARBITRAGES CONFIRMÉS par le Product Owner (confirmation explicite du 25/11/2026) : A-T1 APPROUVÉ ; A-T2 PHASE 1 AUTORISÉE SOUS CONDITIONS, PHASE 2 SUSPENDUE ; A-T3 OPTION A APPROUVÉE (lot R7a borné).**
**Portée : enregistrement documentaire.** Cet addendum n'autorise ni développement, ni migration, ni test, ni création de répertoire, ni déploiement **pendant P85**. Les autorisations qu'il enregistre valent **pour une future mission de réalisation**, elle-même soumise à une **autorisation explicite distincte** du PO.
Données fictives uniquement. Aucun secret ni code.

Cet addendum **complète** les P81 à P84 sans en modifier aucun : les documents historiques restent intacts. En cas de divergence de **statut** (« arbitrage demandé », « sans réponse », « recommandé »), cet addendum fait foi, comme l'addendum P83 l'a fait pour P82.

Sources lues : `docs/P84_ETUDE_FAISABILITE_AT1_AT2.md`, `docs/P84_MATRICE_TESTS_STOCK_0022.md`, `docs/P84_DOSSIER_FINAL_REALISATION_STOCK_0022.md`, `docs/P84_RAPPORT_EXECUTION.md`, `docs/P83_ADDENDUM_DECISIONS_PO_STOCK.md`, `docs/P82_*`, `docs/P81_*`.

Libellés : **ARBITRAGE CONFIRMÉ** · **AUTORISATION FUTURE** (valable seulement dans une mission de réalisation lancée) · **SUSPENDU** · **CONDITION** · **LIMITE** · **PROPOSITION** (non décidée) · **À CONFIRMER** · **OUVERT** · **NON VÉRIFIÉ**.

**Règle de lecture** : seules les mesures **explicitement approuvées** par le PO sont des décisions. Les autres éléments de P84 (grille de seuils, critère C1 à C8, scénarios, noms d'objets, etc.) **restent des propositions techniques**, sauf mention contraire ci-dessous.

---

## 1. Vue d'ensemble

| Arbitrage | Décision PO | Effet immédiat (P85) | Effet futur |
|---|---|---|---|
| **A-T1** — sécurisation des livraisons | **APPROUVÉ** : 4 mesures + autorisation de modifier `compose.ts` | aucun fichier source modifié | modification de `compose.ts` autorisée **dans une mission de réalisation**, sous 5 conditions |
| **A-T2** — capacité et ressources | **Phase 1 autorisée sous conditions ; phase 2 suspendue** | aucun répertoire créé, aucune ressource touchée | `TMPDIR` dédié préparable **après vérification** ; D1 local et navigateur **suspendus** |
| **A-T3** — interface minimale | **Option A approuvée** : lot **R7a borné**, avant G6, **exclusivement** deux messages | aucune interface modifiée | R7a = messages C-P82-2 et C-P82-3 ; pas de refonte de l'interface STOCK |

**Ce qui n'est pas levé** : l'autorisation de **lancer** la réalisation ; O-5, O-6, O-9, O-10 ; les limites matérielles ; la migration 0022 reste **non créée et non exécutée**.

---

## 2. A-T1 — Sécurisation des livraisons : APPROUVÉ

### 2.1 Mesures approuvées (ARBITRAGE CONFIRMÉ)

| Réf. | Mesure approuvée | Rappel P84 (référence technique) |
|---|---|---|
| **A1-M1** | **Manifeste de livraison** contenant les **empreintes des éléments livrés** | étude §2.3, §2.5 (manifestes M0/M1/M2) |
| **A1-M2** | **Gel de livraison** couvrant la séquence **R4a à G6** | étude §2.3 (aucune copie de version ni construction à partir du dépôt pendant le gel) |
| **A1-M3** | **Chargeur de candidat idempotent** : la promotion est une opération **contrôlée et reproductible** | étude §2.3 point 1 ; MIG-T3 |
| **A1-M4** | **Garde de démarrage** vérifiant le **niveau de schéma requis** | étude §2.3 point 3 ; MIG-T4 |

### 2.2 Autorisation explicite relative à `compose.ts` (AUTORISATION FUTURE)

- Le PO **autorise la modification de `compose.ts`** pour implémenter la garde de démarrage (A1-M4), **dans le cadre d'une future mission de réalisation**.
- **Limites** :
  1. **Elle ne permet pas** de modifier `compose.ts` (ni aucun autre fichier source) **pendant P85**.
  2. Elle ne vaut **que pour la garde de démarrage** : toute autre modification de `compose.ts` n'est pas couverte.
  3. Elle ne vaut **qu'une fois la mission de réalisation lancée** par une autorisation explicite distincte (voir §8).
  4. La modification doit être **justifiée au rapport de réalisation** (fichier d'entrée de production), avec son empreinte avant/après.
- Effet sur la condition d'arrêt 9 de P84 (« `compose.ts` à modifier sans arbitrage A-T1 ») : **levée pour la garde de démarrage seulement**, et seulement dans la future mission.

### 2.3 Conditions obligatoires de la future réalisation (CONDITION)

| Réf. | Condition | Lien avec la matrice P84 |
|---|---|---|
| **A1-K1** | Des **tests démontrent le refus d'un schéma incompatible** | MIG-T4 (étendu : voir §2.4) |
| **A1-K2** | Des **tests démontrent le démarrage avec un schéma compatible** | **non couvert explicitement** par P84 : complément A1-T2 |
| **A1-K3** | **Vérification de l'idempotence du chargeur** | MIG-T3 (étendu : voir §2.4) |
| **A1-K4** | **Procédure de retour arrière documentée et vérifiable** | dossier P84 §12 ; MIG-T9 (étendu : voir §2.4) |
| **A1-K5** | **Aucune promotion partielle de livraison** | critère P84 C2, C3, C8 ; conditions d'arrêt 10, 11, 12 ; complément A1-T5 |

### 2.4 Compléments de tests requis par ces conditions (PROPOSITION de spécification)

La matrice P84 n'est pas modifiée ; ces scénarios s'y **ajoutent** et seront intégrés au plan de la future réalisation. Ils sont **spécifiés, non exécutés**.

| Id | Condition | Préconditions | Action | Résultat attendu | PASS / FAIL |
|---|---|---|---|---|---|
| **A1-T1a** | A1-K1 | jeu officiel **sans** l'identifiant requis (0021 seule) | démarrer la composition de production sur une base **jetable** | **refus de démarrer**, erreur **nommée** ; aucune écriture en base | PASS si refus nommé et 0 écriture |
| **A1-T1b** | A1-K1 | base jetable dont l'historique est **en retard** du niveau requis | démarrer | refus nommé indiquant niveau trouvé et niveau requis | PASS si les deux niveaux sont indiqués |
| **A1-T1c** | A1-K1 | base jetable dont l'historique est **en avance** (identifiant inconnu) | démarrer | refus existant (`migration.unknown_applied`) **conservé** ; la garde ne le masque pas | PASS si le code existant est inchangé |
| **A1-T2** | A1-K2 | jeu officiel **contenant** l'identifiant requis ; base jetable migrée | démarrer | démarrage normal ; `/health` du banc répond ; aucun message de refus | PASS si démarrage et 0 refus |
| **A1-T3a** | A1-K3 | candidat présent seul | charger **deux fois** de suite | second chargement = **aucun changement** (même jeu, mêmes sommes) | PASS si empreintes identiques |
| **A1-T3b** | A1-K3 | candidat **et** fichier officiel de même identifiant, sommes **égales** | charger | un seul exemplaire pris en compte | PASS si 1 exemplaire |
| **A1-T3c** | A1-K3 | idem, sommes **différentes** | charger | **arrêt nommé** | PASS si arrêt nommé |
| **A1-T4** | A1-K4 | copie jetable migrée | exécuter la procédure de retour arrière **telle que rédigée** | état d'origine retrouvé ; sommes de contrôle identiques ; le test **échoue** si une étape de la procédure est omise | PASS si retour exact **et** sensibilité démontrée |
| **A1-T5a** | A1-K5 | migration promue **sans** le code correspondant (simulée) | démarrer / vérifier le manifeste | refus ou manifeste non conforme | PASS si détecté |
| **A1-T5b** | A1-K5 | code exigeant le schéma **sans** la migration promue | démarrer | refus nommé (A1-T1a) | PASS |
| **A1-T5c** | A1-K5 | manifeste établi, puis un fichier modifié | recalculer | écart signalé (condition d'arrêt 12) | PASS si écart signalé |

### 2.5 Ce qui n'est PAS décidé par A-T1 (LIMITE, À CONFIRMER)

| # | Point | Statut |
|---|---|---|
| A1-L1 | Le **critère objectif C1 à C8** de P84 §2.5 n'est pas cité dans la décision ; il reste une **PROPOSITION**. La décision A1-K5 (« aucune promotion partielle ») en est cohérente mais ne l'adopte pas. | PROPOSITION / **À CONFIRMER** |
| A1-L2 | L'**adaptation préalable de `schema_parite`, `p33` et `p34`** (P84 §2.3 point 2) n'est pas citée. Elle est la **conséquence technique** de A1-M3 (chargeur idempotent) mais **n'est pas une décision PO**. La future réalisation la **listera et la justifiera** au rapport, sans relâcher d'assertion. | PROPOSITION / **À CONFIRMER** |
| A1-L3 | Le **nom** du manifeste, le **format** des empreintes, la **constante** du niveau requis et le **nom** de l'erreur de refus sont **à fixer en R1** (dossier P84 §9.4, M-1). | PROPOSITION |
| A1-L4 | **Alternatives écartées** de P84 §2.6 : non rouvertes. | inchangé |
| A1-L5 | Aucune **application** à la DEMO ou à une base réelle (G7) n'est autorisée par A-T1. | NON AUTORISÉ |

---

## 3. A-T2 — Capacité et ressources : PHASE 1 AUTORISÉE SOUS CONDITIONS ; PHASE 2 SUSPENDUE

### 3.1 Décision (ARBITRAGE CONFIRMÉ)

| Phase | Décision PO | Statut |
|---|---|---|
| **Phase 1** (légère : bancs mémoire puis fichiers) | **Autorisée sous conditions**, pour une future mission de réalisation | AUTORISATION FUTURE |
| **Phase 2** (D1 local et tests navigateur) | **Suspendue** ; aucune exécution avant **nouvelle évaluation de la capacité** | SUSPENDU |

**Aucune suppression, aucune purge, aucune réparation du système de fichiers, aucune modification de configuration** n'est autorisée par cet arbitrage. Le nettoyage de `/tmp`, la suppression d'archives, le réglage mémoire système et tout benchmark de calibrage restent **NON AUTORISÉS** (alternatives écartées de P84 §3.8, non rouvertes).

### 3.2 Phase 1 — conditions obligatoires (CONDITION)

| Réf. | Condition | Précision |
|---|---|---|
| **A2-K1** | **Exécution légère et séquencée** | une seule suite à la fois ; bancs mémoire puis fichiers ; relevé disque et mémoire avant et après chaque suite (P84 §3.5, repris comme **mode opératoire**, sans valeur de seuil approuvée) |
| **A2-K2** | **Préparation d'un `TMPDIR` dédié sur disque autorisée uniquement après vérification** de quatre points : (a) **espace réellement disponible** ; (b) **permissions** ; (c) **persistance** ; (d) **risques de saturation** | voir §3.3 ; **aucune création en P85** |
| **A2-K3** | **Seuils opérationnels documentés avant leur utilisation** | voir §3.4 ; les seuils de P84 sont des **propositions**, pas des décisions |
| **A2-K4** | **Arrêt de la mission si les ressources deviennent insuffisantes** | conditions d'arrêt S-1 à S-7 de P84 §3.6, reprises comme **modèle** ; les valeurs de seuil restent à documenter (A2-K3) |

### 3.3 Vérifications préalables au `TMPDIR` dédié (A2-K2) — à exécuter dans la future mission

| Point | Vérification attendue (avant toute création) | Constat de P85 (relevé, **aucune création**) |
|---|---|---|
| (a) Espace réellement disponible | espace libre du système de fichiers **cible** (pas de `/tmp`) mesuré **immédiatement avant**, comparé au besoin estimé et à la réserve à conserver | `/` : **1 147 944 Ko** libres (97 %) ; `/tmp` : **0 Ko** (tmpfs plein) |
| (b) Permissions | propriétaire = utilisateur d'exécution ; mode restrictif (`umask 077`) ; hors dépôt ; aucun lien vers un répertoire existant | **NON VÉRIFIÉ** (aucun répertoire créé) |
| (c) Persistance | le répertoire doit se trouver **sur disque** (pas sur un tmpfs, pas dans `/dev/shm`) et **survivre** à un redémarrage du bac à sable ; à contrôler par le type de système de fichiers de la cible | `/` est sur `/dev/root` (disque) ; **persistance NON VÉRIFIÉE** pour le répertoire cible |
| (d) Risques de saturation | marge restante après création et après usage maximal estimé ; DEMO et journaux comptés | **OBSERVATION** : avec la taille maximale proposée par P84 (≤ 300 000 Ko), il resterait environ **847 900 Ko** libres, soit **≈ 48 000 Ko au-dessus** du seuil d'arrêt proposé (800 000 Ko) : marge **faible** |

Résultat de cette lecture : la condition (d) justifie de **fixer la taille maximale du `TMPDIR` et la valeur d'arrêt avant toute création**, et d'arrêter dès que la marge disparaît. **Aucune conclusion n'est tirée** sur la faisabilité : elle dépend des vérifications faites au moment de la mission.

### 3.4 Seuils : statut exact (LIMITE)

| Élément | Statut |
|---|---|
| Grille de P84 §3.4 (disque ≥ 1 000 000 Ko au lancement, arrêt < 800 000 Ko ; mémoire ≥ 500 Mo au lancement, arrêt < 350 Mo ; `TMPDIR` ≤ 300 000 Ko ; phase 2 ≥ 1 800 Mo) | **PROPOSITION non approuvée.** Elle **n'est pas** une décision PO. |
| Seuil de 800 Mo du plan P82 §12 | hypothèse non approuvée (inchangé) |
| Obligation nouvelle | **documenter** les seuils retenus (valeurs, justification, source du relevé) **avant** de les utiliser (A2-K3), dans le relevé de départ R0 |
| Approbation des seuils par le PO | **non précisée** dans l'arbitrage : **À CONFIRMER** (voir §7) |

### 3.5 Phase 2 — suspension (SUSPENDU)

- **D1 local et tests navigateur sont suspendus.** Aucune exécution tant qu'une **nouvelle évaluation de la capacité** n'a pas eu lieu.
- La nouvelle évaluation **compare** trois possibilités (aucune n'est choisie ici) : **(1)** augmentation de la mémoire ; **(2)** utilisation d'un autre hôte ; **(3)** report des scénarios non vérifiés.
- **Constat de départ** (relevé de P85) : mémoire disponible ≈ **630 Mo**, contre ≈ **1,45 Go** observé pour un test D1 en P80 ; `/tmp` plein. **Le besoin de la phase 2 n'est pas couvert aujourd'hui.**

### 3.6 Conséquences sur les scénarios et critères P84 (OBSERVATION)

| Élément P84 | Effet de la suspension de la phase 2 |
|---|---|
| **MIG-T2** (parité de schéma, banc D1 local) et **C7** (parité sur mémoire, fichiers **et** D1 local) | la partie **D1 local** est **suspendue** : elle ne peut être vérifiée tant que la phase 2 est suspendue. Soit la phase 2 est réévaluée et autorisée, soit C7 est **reportée avec mention explicite NON VÉRIFIÉ**. **Le choix n'est pas fait par cet addendum.** |
| **V6-10 à V6-17, V6-19** (navigateur, RTL, mobile) | **suspendus** ; restent **NON VÉRIFIÉS** tant que la phase 2 est suspendue (voir aussi §4.7) |
| **V6-01 à V6-09, V6-18** et scénarios de service (NEG, REV, PER, CAT, MIG-T1, T3 à T9, A1-T*) | **réalisables en phase 1** (bancs mémoire et fichiers, sans navigateur), sous les conditions A2-K1 à A2-K4 |
| **G6** | l'effet de ces reports sur la **décision de promotion** n'est pas tranché par le PO : **À ARBITRER** à la réévaluation de la capacité |

### 3.7 Ce qui n'est PAS décidé par A-T2 (LIMITE)

| # | Point | Statut |
|---|---|---|
| A2-L1 | Choix entre mémoire supplémentaire, autre hôte ou report | **non décidé** (à comparer) |
| A2-L2 | Valeur et approbation des seuils | **À CONFIRMER** |
| A2-L3 | Emplacement exact, nom et taille du `TMPDIR` | à fixer **dans la future mission**, après vérifications |
| A2-L4 | Comportement des bancs avec un `TMPDIR` redirigé | **NON TESTÉ** |
| A2-L5 | Consommation réelle des suites et durée de R5 | **NON MESURÉ** |

---

## 4. A-T3 — Interface minimale R7a : OPTION A APPROUVÉE

### 4.1 Décision (ARBITRAGE CONFIRMÉ)

Le PO approuve l'**option A** : création d'un **lot R7a borné**, à intégrer **avant G6**, couvrant **exclusivement** :

1. le **message explicite en cas de refus de contre-passation**, indiquant les **quantités concernées** et la **raison du refus** (**C-P82-2**) ;
2. la **confirmation explicite avant passage en PERTE**, affichant la **quantité** et le **caractère irréversible** de l'opération (**C-P82-3**).

R7a **ne s'étend pas** à une refonte générale de l'interface STOCK.

### 4.2 Écart avec la recommandation de P84 (OBSERVATION)

P84 §14.6 décrivait l'option A avec les messages **MSG-1 à MSG-6**. La décision du PO est **plus étroite** : seuls les deux messages ci-dessus sont dans R7a. **La décision prévaut.**

| Message P84 | Dans R7a ? | Note |
|---|---|---|
| MSG-1 refus de reprise (quantités, raison) | **OUI** | C-P82-2 |
| MSG-2 refus d'annulation intégrale (lignes concernées) | **OUI**, comme cas du refus de contre-passation | C-P82-2 (annulation intégrale) |
| MSG-3 confirmation PERTE (quantité, irréversibilité) | **OUI** | C-P82-3 |
| MSG-4 orientation « en cas de doute, EN_CONTROLE » | **À CONFIRMER** : non citée ; peut figurer **dans le texte** de la confirmation sans écran supplémentaire | PROPOSITION |
| MSG-5 refus nommés (`stock.state_exit_deferred`, `stock.reservation_unfulfillable`, droit manquant, motif interdit) | **NON** (hors R7a) | message générique d'interface tant qu'une mission distincte ne les couvre pas |
| MSG-6 archivage refusé : répartition par état | **NON** (hors R7a) | idem |
| MSG-7 réservation non honorable (indicateur) | **NON** | idem |
| UI-1 à UI-6, UI-8 à UI-11 (niveaux par état, passage d'état, statut de lot, correction par état, sortie manuelle, alertes, rapport d'écarts) | **NON** | mission d'interface distincte |
| REV-35 (afficher la quantité reprenable **avant** validation) | **NON** : R7a traite le **refus**, pas l'aperçu préalable | PROPOSITION de P84, non reprise |

### 4.3 Contenu minimal exigé de chaque message

**Décision PO** (minimum) : (1) refus de contre-passation : **quantités concernées + raison** ; (2) confirmation PERTE : **quantité + caractère irréversible**.
**PROPOSITION de P84** (contenu complet, à confirmer en R1) : refus de reprise avec demandée, **reprenable**, DISPONIBLE, EN_CONTROLE, QUARANTAINE, PERTE, reçu, déjà repris ; confirmation PERTE avec produit, lot, état d'origine, motif et **deux actions distinctes** (choisir puis confirmer).

### 4.4 États et comportements attendus de R7a (spécification)

| Réf. | Situation | Comportement attendu | Source |
|---|---|---|---|
| **R7a-E1** | Contre-passation **partielle** demandée, quantité > DISPONIBLE du lot | le service refuse (`purchase.reversal_state`) ; l'interface affiche : **raison** (« une partie de la quantité n'est plus disponible »), **quantité demandée**, **quantité disponible/reprenable**, **quantités en EN_CONTROLE, QUARANTAINE, PERTE** ; **aucun mouvement** | C-P82-2 ; V2-R6 ; REV-14, REV-30 |
| **R7a-E2** | **Annulation intégrale** avec au moins une ligne ayant une part hors DISPONIBLE | refus **de toute la réception** ; l'interface nomme **les lignes concernées** et les **quantités** hors DISPONIBLE par état ; **aucune ligne** n'est reprise | C-P82-2 ; V2-R6 ; REV-03, REV-04, REV-34 |
| **R7a-E3** | Refus d'une quantité en **PERTE** ou **QUARANTAINE** | le message **ne suggère jamais** une sortie depuis ces états (REPORTÉ, DP1-R7) ; il renvoie à la situation (« réintégration », « levée », « lot ultérieur ») **sans promettre une fonction inexistante** | DP1-R7 ; REV-36 |
| **R7a-E4** | Refus déjà couvert par le code existant (`purchase.reversal_exceeds`, `stock.insufficient`) | **comportement existant conservé** ; R7a **n'altère pas** ces messages | REV-12 ; non-régression |
| **R7a-E5** | Choix de la destination **PERTE** | **étape de confirmation distincte** avant tout envoi : **quantité exacte** et **texte d'irréversibilité** affichés | C-P82-3 ; PER-40 |
| **R7a-E6** | Confirmation validée | l'appel au service **porte la confirmation et la quantité affichée** ; le passage s'exécute ; résultat affiché | PROPOSITION de P83 §5 (paramètre de confirmation portant la quantité) ; PER-04, PER-05 |
| **R7a-E7** | Confirmation **annulée** ou abandonnée | **aucun mouvement**, aucun en-tête | PER-42 |
| **R7a-E8** | Appel au service **sans** confirmation, ou avec une quantité **différente** de celle confirmée | **refus nommé** côté service ; stock inchangé | PER-04, PER-05 |
| **R7a-E9** | Aucune option « sortir de PERTE » n'est proposée nulle part | vérifié sur chaque écran touché | PER-47 |

### 4.5 Critères d'acceptation de R7a (CONDITION de R7a ; formulation PROPOSITION)

| Réf. | Critère | Preuve |
|---|---|---|
| **R7a-AC1** | Chacun des **états R7a-E1 à E9** est démontré par au moins un scénario PASS | résultats par identifiant |
| **R7a-AC2** | Les **quantités affichées** sont **exactement** celles retournées par le service (aucune recomputation côté interface) | scénarios R7a-S01 à S05 |
| **R7a-AC3** | Aucune **clé brute**, aucun `{variable}` non remplacé, aucun code technique affiché | V6-08 |
| **R7a-AC4** | **Égalité des clés FR/EN/AR** étendue aux nouveaux messages | V6-18 ; `tests/p69` |
| **R7a-AC5** | Les messages **existants** ne sont pas modifiés (seul `{n}` géré aujourd'hui : le remplacement de variables est **étendu sans changer** les cas existants) | V6-17 ; non-régression |
| **R7a-AC6** | Contrôles **FR, EN, AR, RTL et mobile** exécutés **ou** explicitement listés **NON VÉRIFIÉS** (voir §4.7) | rapport |
| **R7a-AC7** | **Aucune refonte** : aucun écran hors périmètre §4.2 modifié ; liste des fichiers d'interface modifiés **justifiée** | revue des empreintes (manifeste) |
| **R7a-AC8** | **Service et interface livrés ensemble** (A1-K5) : aucune promotion de G6 sans R7a | manifeste M2 |

### 4.6 Scénarios de tests associés (spécifiés, **non exécutés**)

Ils **précisent** la matrice P84 (REV-30 à REV-36, PER-04, PER-05, PER-40 à PER-47, V6-01, V6-02, V6-08, V6-18) sans la modifier.

**Service (phase 1, sans navigateur)**

| Id | Préconditions | Action | Résultat attendu | PASS / FAIL |
|---|---|---|---|---|
| **R7a-S01** | FX-I (`D4 C3 Q2 P1`, reçu 10) ; gestionnaire d'achats | reprise de **5** | refus `purchase.reversal_state` ; charge utile contenant demandée, reprenable, D, C, Q, P (+ reçu, déjà repris) ; **0 mouvement** | PASS si toutes les valeurs sont présentes et exactes |
| **R7a-S02** | FX-I | annulation **intégrale** | refus ; charge utile listant **les lignes** et leurs parts hors D | PASS si lignes nommées ; 0 ligne reprise |
| **R7a-S03** | FX-I, part en PERTE seule visée | reprise | refus ; aucune suggestion de sortie de P dans la charge utile | PASS |
| **R7a-S04** | FX-A | passage `D→P` **sans** confirmation | refus nommé ; 0 mouvement | PASS si refus |
| **R7a-S05** | FX-A | passage `D→P` avec confirmation portant **une quantité différente** | refus nommé ; 0 mouvement | PASS si refus |
| **R7a-S06** | FX-A | passage `D→P` avec confirmation exacte | accepté ; deux mouvements de somme nulle + en-tête | PASS |
| **R7a-S07** | FX-H (tout D) | reprise intégrale | **comportement inchangé** (accepté) | PASS si identique à l'existant |

**Interface — composant, sans navigateur (phase 1, **à confirmer en R1** selon l'outillage existant)**

| Id | Contrôle | PASS si |
|---|---|---|
| **R7a-S10** | Le texte produit pour R7a-S01 contient la raison et chaque quantité transmise | chaque valeur présente, 0 `{variable}` |
| **R7a-S11** | Le texte produit pour R7a-S02 nomme chaque ligne concernée | lignes nommées |
| **R7a-S12** | Le texte de confirmation PERTE contient la quantité transmise et la mention d'irréversibilité | présentes |
| **R7a-S13** | La confirmation PERTE exige **deux actions** ; annuler n'envoie aucun appel | 0 appel après annulation |
| **R7a-S14** | Égalité des clés FR/EN/AR des nouveaux messages | test p69 PASS |
| **R7a-S15** | Messages existants inchangés (comparaison avant/après) | identiques |

### 4.7 Contrôles FR, EN, AR, RTL et mobile (exigés par le PO) et leur statut

Le PO exige que R7a inclue les contrôles **FR, EN, AR**, le comportement **RTL** et **mobile**. Ils sont **spécifiés** ici ; leur **exécution dans un navigateur** dépend de la **phase 2, suspendue** (A-T2).

| Id | Contrôle | Langues | Largeurs | Exécutable en phase 1 ? | Statut en P85 |
|---|---|---|---|---|---|
| **R7a-C01** | Texte de chaque message présent, sans clé brute | FR, EN, AR | — | **oui** (test de clés, test de composant) | spécifié, non exécuté |
| **R7a-C02** | `dir="rtl"` en AR, `dir="ltr"` en FR/EN ; mise en page inversée | AR (+ FR/EN) | 1440, 390 | non (navigateur) | **SUSPENDU / NON VÉRIFIÉ** |
| **R7a-C03** | **Quantités, numéros de lot, états** lisibles en RTL sans inversion trompeuse (exemples : `D4 C3 Q2 P1`, quantité confirmée) | AR | 1440, 390 | non (navigateur) | **SUSPENDU / NON VÉRIFIÉ** |
| **R7a-C04** | Aucun débordement horizontal (`scrollWidth ≤ largeur de fenêtre`) | FR, EN, AR | 320, 360, 390, 768 | non (navigateur) | **SUSPENDU / NON VÉRIFIÉ** |
| **R7a-C05** | Boutons de confirmation PERTE : visibles, atteignables au clavier, cible tactile suffisante (≥ 44 px, **PROPOSITION de seuil**) | FR, EN, AR | 390 | non (navigateur) | **SUSPENDU / NON VÉRIFIÉ** |
| **R7a-C06** | Message de refus long (AR et FR, nombreuses lignes) : aucun chevauchement | FR, AR | 360 | non (navigateur) | **SUSPENDU / NON VÉRIFIÉ** |
| **R7a-C07** | Régression : écrans achats et stock **non modifiés** conservent leurs mesures P65 | FR, EN, AR | 390, 768, 1440 | non (navigateur) | **SUSPENDU / NON VÉRIFIÉ** |
| **R7a-C08** | Relecture linguistique arabe par un arabophone | AR | — | **non automatisable** | **NON VÉRIFIÉ** (jamais réalisée) |

**Acquis hérité** (inchangé, périmètre limité) : RTL de la fiche catalogue V3 (P60) ; responsive V1 des écrans existants (P65) ; égalité des clés (P69). **Écrans V3 stock et commandes en RTL : NON VÉRIFIÉS.**

### 4.8 Deux points que cet addendum signale sans les trancher (VIGILANCE, À ARBITRER)

| Réf. | Point | Pourquoi |
|---|---|---|
| **R7a-L1** | **Articulation A-T3 / A-T2** : R7a doit être « intégré avant G6 » **avec** ses contrôles RTL et mobile, mais les tests navigateur sont **suspendus**. Peut-on promouvoir G6 avec R7a-C02 à C08 **NON VÉRIFIÉS**, ou G6 attend-il la réévaluation de la capacité ? | Les deux décisions sont confirmées ; leur combinaison ne l'est pas. **Aucune lecture n'est présumée.** À traiter à la réévaluation de la capacité, avant G6. |
| **R7a-L2** | **Point d'entrée du passage en PERTE** : la confirmation C-P82-3 suppose un endroit où l'utilisateur déclenche le passage. Le formulaire de passage d'état fait partie de l'interface **hors R7a** (UI-2). Deux lectures : **(1)** R7a inclut le **strict minimum d'accès** qui héberge la confirmation ; **(2)** la confirmation est livrée mais le passage en PERTE **n'est pas exposé dans l'interface** tant qu'une mission distincte n'a pas fourni le formulaire, le service continuant à refuser tout passage sans confirmation (R7a-E8). | La décision dit « exclusivement » deux éléments ; elle ne dit pas comment l'utilisateur atteint le passage. **À CONFIRMER** par le PO avant la rédaction du plan de R7a. |

### 4.9 Ce qui n'est PAS décidé par A-T3 (LIMITE)

| # | Point | Statut |
|---|---|---|
| A3-L1 | Format exact de la charge utile `purchase.reversal_state` et des variables de message | à fixer en R1 (dossier P84 §9.4, M-5) |
| A3-L2 | Interfaces concernées : P84 option A visait **V1 et V3**. Le PO n'a pas restreint ce point ; **V1 et V3 sont donc retenues comme dans P84**, sous réserve de confirmation. | **À CONFIRMER** |
| A3-L3 | Mention MSG-4 (orientation EN_CONTROLE) dans le texte de confirmation | **À CONFIRMER** |
| A3-L4 | Refonte générale de l'interface STOCK | **hors périmètre**, mission distincte |
| A3-L5 | Seuil tactile de 44 px | PROPOSITION |

---

## 5. Statut et limites maintenus (ARBITRAGE CONFIRMÉ : maintien explicite)

### 5.1 Points ouverts du registre P83 : inchangés

| Réf. | Objet | Statut après P85 |
|---|---|---|
| **O-5** | Réception « à contrôler » (entrée directe en EN_CONTROLE) | **OUVERT** ; non tranché par A-T1, A-T2 ni A-T3 |
| **O-6** | Retour client en contrôle d'abord | **OUVERT / REPORTÉ** |
| **O-9** | État de la migration 0021 en DEMO | **OUVERT** ; concerne la décision future G7, non autorisée |
| **O-10** | Prérequis du Lot 2 (CAT-14 à CAT-16) | **OUVERT** |
| O-7 | Comportement réel de `ALTER TABLE … ADD COLUMN` sur table `STRICT` | **NON VÉRIFIÉ** (question technique, non décision PO) |
| O-8 | Volumétrie réelle et coût des déclencheurs | **NON VÉRIFIÉ** |

### 5.2 Vigilances du registre P83 : inchangées

| Réf. | Statut après P85 | Remarque |
|---|---|---|
| **V-3** (formule CAT-11 et convention de P82) | **VIGILANCE, NON APPROUVÉ** | aucun des trois arbitrages ne l'approuve |
| **V-4** (couplage code et schéma avant promotion) | **VIGILANCE, traitée par A-T1** | A-T1 approuve les mesures qui la traitent ; la vigilance ne sera levée qu'à la vérification de leur réalisation (NON VÉRIFIÉ à ce jour) |
| **V-5** (C-P82-1 sur ERREUR_SAISIE et AUTRE) | **VIGILANCE** | inchangée |
| **V-6** (restitution de C-P82-2 et C-P82-3 à l'utilisateur) | **VIGILANCE, précisée par A-T3** | A-T3 choisit la voie du lot R7a borné ; l'interface existante reste **NON VÉRIFIÉE** et l'articulation avec G6 reste ouverte (R7a-L1) |

### 5.3 Scénarios, tests et état technique

| Élément | Statut |
|---|---|
| Les **167 scénarios** de la matrice P84 (NEG 57, REV 33, PER 35, V6 19, CAT 14, MIG 9) | **spécifiés, non exécutés** |
| Les scénarios complémentaires A1-T* (§2.4) et R7a-S* (§4.6) | **spécifiés, non exécutés** |
| Contrôles navigateur, FR/EN/AR, RTL, mobile | **NON VÉRIFIÉS** ; phase 2 **SUSPENDUE** |
| Parité de schéma sur D1 local | **NON VÉRIFIÉE** ; phase 2 **SUSPENDUE** |
| Consommation réelle des suites, durée de R5, comportement avec `TMPDIR` redirigé | **NON MESURÉ / NON TESTÉ** |
| **Migration 0022** | **non créée et non exécutée** ; dernier fichier officiel : 0021 ; aucun répertoire de candidat |
| `compose.ts`, tout fichier source, toute base | **non modifiés** par P85 |
| `TMPDIR` dédié | **non créé** par P85 |

### 5.4 Limites matérielles (VIGILANCE)

Relevé de P85 (observations, **non des décisions**) : disque `/` ≈ 1 147 944 Ko libres (97 % utilisé) ; `/tmp` en tmpfs **plein** ; mémoire disponible ≈ 630 Mo sur un hôte de 2 Go et 2 cœurs. Ces limites restent des **points de vigilance** et **conditionnent** la phase 1 (arrêt si ressources insuffisantes, A2-K4). Aucune suppression, purge, réparation de système de fichiers ou modification de configuration n'est autorisée par A-T2.

### 5.5 Décisions et propositions : règle de tri

| Devient une décision PO (ARBITRAGE CONFIRMÉ) | Reste une PROPOSITION de P84 |
|---|---|
| A-T1 : manifeste, gel R4a→G6, chargeur idempotent, garde de démarrage ; autorisation `compose.ts` (future) ; 5 conditions | critère C1 à C8 ; adaptation de `schema_parite`/`p33`/`p34` ; noms, formats et constantes ; MIG-T1…T9 en tant que liste exacte |
| A-T2 : phase 1 autorisée sous conditions ; phase 2 suspendue ; `TMPDIR` après vérifications ; documentation des seuils avant usage ; arrêt si insuffisance | valeurs de la grille de seuils (disque, mémoire, `TMPDIR`, phase 2) ; emplacement et taille du `TMPDIR` |
| A-T3 : lot R7a borné, avant G6, exclusivement deux messages (C-P82-2, C-P82-3) ; états, critères, contrôles FR/EN/AR, RTL, mobile, scénarios | texte exact des messages ; MSG-4 ; formulation des états, critères et scénarios R7a ; seuil tactile ; V1 et V3 |

---

## 6. Cohérence avec P81 à P84 et incidences (OBSERVATION)

### 6.1 Cohérence

| Source | Contrôle | Résultat |
|---|---|---|
| P81 (registre, plan) | Aucune décision antérieure n'est modifiée ; les lots R0…R7 sont conservés ; R7a est une **insertion** | cohérent |
| P82 (modèle) | A-T1, A-T2 et A-T3 ne touchent ni le modèle à 4 états, ni les invariants, ni le journal immuable ; C-P82-2 et C-P82-3 sont les seules précautions reprises par R7a | cohérent |
| P83 (décisions Q-1…Q-12, H-1…H-6, C-P82-1…3) | Aucune décision Q ou H n'est rouverte ; O-5, O-6, O-9, O-10 maintenus ouverts ; V-3 non approuvé | cohérent |
| P84 (étude, matrice, dossier, rapport) | Les statuts « arbitrage demandé » de A-T1, A-T2 et A-T3 sont remplacés par les décisions ci-dessus ; le contenu technique de P84 n'est pas amendé | cohérent, sous les réserves de §7 |

### 6.2 Incidences sur des éléments précis de P84

| Élément | Incidence |
|---|---|
| Condition d'arrêt 9 de P84 (`compose.ts` à modifier sans arbitrage) | levée **pour la garde de démarrage seulement**, dans la future mission (§2.2) |
| MIG-T2 et C7 (parité sur D1 local) | partie D1 local **suspendue** (§3.6) ; choix entre réévaluation et report NON VÉRIFIÉ **non fait** |
| V6-10 à V6-17, V6-19 | **suspendus** ; **NON VÉRIFIÉS** |
| MSG-1 à MSG-8 | seuls MSG-1, MSG-2 et MSG-3 sont repris par R7a ; MSG-4 **À CONFIRMER** ; MSG-5 à MSG-8 et UI-1 à UI-11 hors R7a (§4.2) |
| G6 (promotion) | reste une **décision distincte** ; articulation avec R7a et la phase 2 **À ARBITRER** (R7a-L1) |
| G7 (application DEMO ou base réelle) | **non autorisée** ; O-9 ouvert |

### 6.3 Séquencement retenu (rappel, non modifié)

R0 → R1 → … → R4a → (gel de livraison, A1-M2) → … → R7a (borné) → G6 (décision de promotion) → G7 (hors mission). Le gel couvre R4a→G6 ; R7a se situe **dans** cette fenêtre et son contenu est donc soumis au manifeste et aux empreintes (A1-M1). Cette lecture découle de la combinaison des décisions A-T1 et A-T3 ; elle est **À CONFIRMER** à la rédaction du plan de réalisation (voir §7).

### 6.4 Alternatives non rouvertes

Les alternatives écartées de P84 (§2.6 de l'étude) et les options B et C de A-T3 restent **non rouvertes** par P85.

---

## 7. Points À CONFIRMER ou À ARBITRER (liste unique)

Aucun de ces points n'est décidé. Ils sont à traiter avant ou au début de la future mission de réalisation.

| # | Réf. | Point | Statut |
|---|---|---|---|
| 1 | A1-L1 | Adoption du critère C1 à C8 comme critère de promotion | PROPOSITION / **À CONFIRMER** |
| 2 | A1-L2 | Adaptation préalable de `schema_parite`, `p33`, `p34` (conséquence technique de A1-M3) | PROPOSITION / **À CONFIRMER** |
| 3 | A1-L3 | Nom du manifeste, format des empreintes, constante de niveau, nom de l'erreur | à fixer en R1 |
| 4 | A2-L2 | Valeur et approbation des seuils opérationnels | **À CONFIRMER** |
| 5 | A2-L1 | Choix mémoire supplémentaire / autre hôte / report (phase 2) | **À ARBITRER** à la réévaluation de la capacité |
| 6 | §3.6 | Sort de C7 et de la partie D1 local de MIG-T2 | **À ARBITRER** |
| 7 | R7a-L1 | G6 avec R7a-C02 à C08 NON VÉRIFIÉS, ou attente de la réévaluation de la capacité | **À ARBITRER** |
| 8 | R7a-L2 | Point d'entrée du passage en PERTE (lecture (1) ou (2)) | **À CONFIRMER** avant le plan de R7a |
| 9 | A3-L2 | Interfaces V1 et V3 retenues comme dans P84 | **À CONFIRMER** |
| 10 | A3-L3 | Mention MSG-4 dans le texte de confirmation | **À CONFIRMER** |
| 11 | §6.3 | Lecture du séquencement (R7a dans la fenêtre de gel) | **À CONFIRMER** |
| 12 | O-5, O-6, O-9, O-10 | Points ouverts du registre | **OUVERT** |
| 13 | V-3 | Formule CAT-11 avec la convention P82 | **NON APPROUVÉ** |

---

## 8. Autorisations pour les missions futures et conditions de lancement

### 8.1 Autorisations enregistrées (AUTORISATION FUTURE)

| # | Autorisation | Valable dans | Conditions |
|---|---|---|---|
| F-1 | Concevoir et réaliser le manifeste de livraison avec empreintes, le gel R4a→G6 et le chargeur de candidat idempotent | mission de réalisation lancée | A1-K1 à K5 |
| F-2 | **Modifier `compose.ts`** pour la seule garde de démarrage (niveau de schéma requis) | mission de réalisation lancée | A1-K1 à K5 ; justification et empreintes avant/après au rapport |
| F-3 | Phase 1 : exécution légère et séquencée ; préparer un `TMPDIR` dédié sur disque | mission de réalisation lancée | A2-K1 à K4 ; vérifications préalables (a)-(d) du §3.3 ; seuils documentés avant usage ; arrêt si ressources insuffisantes |
| F-4 | Réaliser le lot R7a borné (deux messages, états, critères, contrôles, scénarios) | mission de réalisation lancée | périmètre exclusif de §4.1 ; points §7 n° 7 à 10 traités ou explicitement reportés |

### 8.2 Non-autorisations (NON AUTORISÉ)

- Application à la DEMO ou à une base réelle (G7) ; décision de promotion G6.
- Phase 2 : D1 local et tests navigateur (**SUSPENDUS**).
- Toute suppression, purge, réparation de système de fichiers, modification de configuration, redémarrage, intervention systemd.
- Toute modification de `compose.ts` au-delà de la garde de démarrage.
- Toute extension de R7a (refonte de l'interface STOCK).
- Toute décision sur O-5, O-6, O-9, O-10 ou V-3.
- Toute opération Git manuelle (la sauvegarde automatique Genspark reste seule en charge).

### 8.3 Conditions de lancement de la future mission

1. **Autorisation explicite distincte** du PO ; cet addendum ne la constitue pas.
2. Relevé de départ (Git, empreintes, ressources, DEMO, migrations) avant toute action.
3. Traitement ou report explicite des points du §7.
4. Rien n'est appliqué à la DEMO ni à une base réelle.

### 8.4 État à la clôture de P85

Aucune action technique n'a été exécutée. Aucune réalisation STOCK, aucune migration 0022 et aucune mission P86 ne sont lancées. **La mission s'arrête ici ; toute suite exige une nouvelle autorisation explicite du PO.**

---
Fin de l'addendum P85.
