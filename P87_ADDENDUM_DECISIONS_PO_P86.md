# PHARM'OS — P87
## Addendum officiel des arbitrages PO P86 (P86-1 à P86-13, précautions A et B)

**Date des décisions :** 25/11/2026.
**Autorisation :** confirmation explicite du Product Owner (PO).
**Nature :** enregistrement **strictement documentaire**. Aucun code, aucune base, aucune migration, aucun service n'ont été modifiés.
**Statut de ce document :** **référence officielle** des arbitrages P86 confirmés par le PO. Il transforme en **DÉCISION PO CONFIRMÉE** les recommandations P86-1 à P86-13 du dossier `docs/P86_ARBITRAGE_CONSOLIDE_13_POINTS.md`, et enregistre deux précautions supplémentaires (A et B).
**Documents non modifiés :** P81 à P86 (addenda seulement). Le détail technique des 13 points reste dans le dossier P86 ; cet addendum en reprend la portée, les conditions, les limites et les références sans les reformuler.
**Ce document n'autorise aucune réalisation** : voir §5.

---

## 0. Comment lire cet addendum

1. **Portée des décisions.** Le PO a confirmé « intégralement » les recommandations P86-1 à P86-13 « telles que présentées dans le dossier d'arbitrage P86 ». Chaque recommandation devient une **décision métier**. Les passages que le dossier P86 présentait comme **OBSERVATION**, **HYPOTHÈSE**, **LIMITE** ou **VIGILANCE** gardent leur étiquette : confirmer une recommandation ne transforme pas une observation en décision.
2. **Trois niveaux à ne pas confondre** (voir §5) : **décision métier** (ce que le PO a confirmé), **proposition technique** (la manière de réaliser, laissée à la mission de réalisation dans les limites fixées), **autorisation d'exécution** (distincte, **non accordée** ici).
3. **Intitulés.** Les identifiants P86-n et leurs intitulés sont repris tels qu'au dossier P86 (§3 à §6). Les références P81 à P86 sont conservées (critères C1 à C9, lots R0 à R7a, PC-1, G6, G7, décisions DP, Q, H, C-P82, A-T1 à A-T3).
4. **Étiquettes utilisées :** DÉCISION PO CONFIRMÉE, PRÉCAUTION CONFIRMÉE, DÉCIDÉ (décision antérieure), OUVERT, NON APPROUVÉ, NON VÉRIFIÉ, NON AUTORISÉ, AUTORISATION FUTURE (A-T1 à A-T3, inchangées), SUSPENDU, CONDITION, LIMITE, OBSERVATION, VIGILANCE, À CONFIRMER, RÉSERVE (nouvelle, §8).
5. **Rien n'est rouvert.** Les décisions P81 à P85 (DP-1 à DP-6, C1 à C4, Q-1 à Q-12, H-1 à H-6, C-P82-1 à C-P82-3, A-T1, A-T2, A-T3) restent en vigueur telles qu'écrites.

---

## 1. Synthèse des décisions enregistrées

| P86-n | Objet | Groupe | Décision PO confirmée (recommandation retenue) |
|---|---|---|---|
| **1** | Critère de promotion C1 à C8 (A1-L1) | A | **C1 à C8 révisé** : C5 (périmètre de comparaison défini), C7 scindé en C7a/C7b, ajout de C9 (R7a) |
| **2** | Adaptation préalable de `schema_parite`, `p33`, `p34` (A1-L2) | B | **Oui, en R1** : nombres calculés, aucune assertion affaiblie, liste justifiée au rapport |
| **3** | Noms, formats et constantes du manifeste (A1-L3) | A | Fixés **une seule fois, à la fin de R1**, par l'équipe technique, consignés au rapport |
| **4** | Valeur et approbation des seuils de ressources (A2-L2) | B | Grille P84 = **planchers de départ**, documentés et confrontés à la mesure en R0 ; **durcissables, jamais assouplis** |
| **5** | Phase 2 : mémoire, autre hôte ou report (A2-L1) | B | **Report explicite par défaut**, avec **porte de capacité PC-1** avant G6 |
| **6** | Sort de C7 et de la partie D1 local de MIG-T2 (§3.6 de P85) | B | **Scinder** : C7a (mémoire/fichiers, obligatoire) ; C7b (D1 local) à PC-1 si possible, sinon **NON VÉRIFIÉE** avec réserve écrite soumise au PO à G6 |
| **7** | G6 avec contrôles navigateur, RTL et mobile non vérifiés (R7a-L1) | A | **G6 conditionnel** : contrôles de sécurité et d'intégrité obligatoires ; contrôles visuels et linguistiques reportables **sous verrou d'exposition** |
| **8** | Point d'entrée du passage en PERTE (R7a-L2) | A | **Lecture (2)** : confirmation livrée ; passage en PERTE **non exposé** dans V1/V3 tant que la mission d'interface n'a pas fourni le formulaire (complétée par la **précaution A**, §4.1) |
| **9** | Interfaces V1 et V3 (A3-L2) | C | **V1 et V3** ; toute partie non vérifiée est listée NON VÉRIFIÉE, jamais omise |
| **10** | Mention MSG-4 dans le texte de confirmation (A3-L3) | C | **Formulation neutre** (option c), liée à P86-8 ; MSG-4 complet reporté à la mission d'interface |
| **11** | Séquencement : R7a dans la fenêtre de gel (§6.3 de P85) | A | **R4e → R7a → R5 → R6 → G6** (R7a dans le gel, avant R5) |
| **12** | O-5, O-6, O-9, O-10 | — | **Maintenus ouverts** ; mentionnés au rapport sans être tranchés |
| **13** | V-3 : formule CAT-11 et convention P82 | — | **Maintenu NON APPROUVÉ** ; écriture exacte soumise au PO **avant** G6 |
| **A** | Précaution : opération PERTE et API | — | **PRÉCAUTION CONFIRMÉE** (§4.1) |
| **B** | Précaution : P86-V1, suivi obligatoire par lot | — | **Choix du PO** : refus explicite et compréhensible, correction possible, traitement avant R4e (§4.2) |

**Principe directeur confirmé (P86 §1).** Ce qui devient irréversible ou coûteux à corriger après G6 (schéma, chaîne de migrations, intégrité des données, protections du service) est vérifié **avant** G6. Ce qui se corrige par une simple modification de code (présentation, mise en page, relecture linguistique) peut être reporté, à condition qu'**aucun utilisateur** n'y soit exposé avant exécution du contrôle ou acceptation écrite du PO.

---

## 2. Rappel des constats sur lesquels reposent les décisions (OBSERVATIONS du dossier P86 §2)

Ces constats sont des **faits relevés en lecture seule** par P86, **pas des décisions**. Ils sont cités parce que plusieurs décisions s'y appuient.

| Réf. | Constat | Étiquette (P86) |
|---|---|---|
| T-1, T-2 | Dernier fichier officiel `pharmacy` : 0021 ; aucun chargeur de candidat n'existe ; `compose.ts` lit `control` et `pharmacy` | FAIT VÉRIFIÉ |
| T-3, T-4 | `schema_parite` fige `ids.length = 22` ; `p33` et `p34` simulent une base « en avance » par l'identifiant 0022 | FAIT VÉRIFIÉ |
| T-5 | D1 n'apparaît dans aucun point d'entrée de production ; 17 fichiers de test l'utilisent comme banc | FAIT VÉRIFIÉ (comptage non recompté) |
| T-6 | V1 et V3 ne gèrent ni `purchase.reversal_state`, ni confirmation PERTE, ni passage d'état | FAIT VÉRIFIÉ |
| T-7 | Aucun outil de simulation DOM relevé dans `ui/saas-src/node_modules` | OBSERVATION (non exhaustive) |
| T-8 | `ui/saas-src/dist` ignoré par Git ; construction de la V3 NON MESURÉE | FAIT VÉRIFIÉ / NON MESURÉ |
| T-10 | Ressources : disque 97 %, `/tmp` plein, mémoire disponible ≈ 644 Mo, pas de swap | FAIT VÉRIFIÉ |
| T-11, T-15 | Aucun test ne contient CAT-14 à CAT-16 ; collision de noms avec CAT-14 de la matrice P84 | FAIT VÉRIFIÉ / OBSERVATION |

---

## 3. ENREGISTREMENT DES 13 ARBITRAGES

Pour chaque point : **DÉCISION PO CONFIRMÉE** = la recommandation du dossier P86, retenue telle que présentée. La **réponse à la question métier** posée au PO (rubrique 6 du dossier) est : **conforme à la recommandation**.

### 3.1 Groupe A — Livraison R7a et G6

#### P86-1 — Critère de promotion C1 à C8 (A1-L1) — **DÉCISION PO CONFIRMÉE**

- **Référence.** P84 étude §2.5 ; P85 §2.5 (A1-L1) ; dossier P86 §3 (P86-1), §7.
- **Décision (option b).** Adopter C1 à C8 **révisés**, avec les révisions suivantes :
  - **C5** : la comparaison porte sur la **liste de fichiers de test de la non-régression ciblée fixée en R0** (plan P82 §9.5), avec le **nombre de tests réussis par fichier** ; les fichiers de la phase 2 y figurent comme « NON EXÉCUTÉ », **jamais comme réussis**.
  - **C7 scindé** : **C7a** = parité mémoire et fichiers (**obligatoire**, phase 1) ; **C7b** = parité D1 local (voir P86-6).
  - **C9 ajouté** : R7a livré dans le **même manifeste M2**, ses contrôles obligatoires (P86-7) réussis, le verrou d'exposition consigné (R7a-AC8).
  - **C1, C2, C3, C4, C6 et C8 sont inchangés.**
- **Rappel des critères (P84 §2.5, repris par P86-1).** C1 manifeste M2 établi après le dernier changement ; C2 dossier officiel = M0 + un seul `0022_*.sql` ; C3 fichier identique au candidat validé en R5 ; C4 MIG-T1, MIG-T3, MIG-T4 réussis ; C5 non-régression ciblée sans régression (révisé) ; C6 contrôles d'intégrité A1-A8 et B1-B22 exacts ; C7a/C7b ; C8 acceptation écrite du PO ; C9 (nouveau).
- **Règle de blocage.** Un manifeste incomplet, une condition fausse ou non vérifiée **bloque G6**, **sauf** pour les seuls éléments de la liste « reportable sous verrou » de P86-7, **et seulement par acceptation écrite du PO**.
- **Conditions et dépendances.** Liée à P86-5, P86-6, P86-7 : C7a/C7b et C9 n'existent que par la confirmation de P86-6 et P86-7 (confirmés). Le critère adopté **est** la liste de contrôle de G6 (§7 du dossier P86, repris au §6 ci-dessous).
- **Conséquences.** *R4a* : le manifeste M0 et la liste de référence de C5 sont établis avant le gel. *R7a* : C9 en fait une condition de G6. *Tests* : MIG-T1, MIG-T3, MIG-T4 obligatoires ; table de correspondance MIG-T3/MIG-T4 fixée en R1 (T-14 : la matrice P84 et P85 prévalent). *Migration 0022* : aucun effet sur son contenu.
- **Limite.** « Liste fixe de neuf vérifications » désigne la numérotation C1 à C9 ; C7b relève de la catégorie « réserve » (§6), les autres sont bloquantes (OBSERVATION terminologique, §8).

#### P86-3 — Noms, formats et constantes du manifeste (A1-L3) — **DÉCISION PO CONFIRMÉE**

- **Référence.** P85 §2.5 (A1-L3) ; dossier P84 §9.4 (M-1) ; dossier P86 §3 (P86-3).
- **Décision (option b).** Le nom du manifeste, le format des empreintes, la constante du niveau requis et le nom de l'erreur de refus sont **fixés une seule fois, en fin de R1, par l'équipe technique**, et **consignés au rapport R1**. Ce choix est **technique**, laissé à l'équipe ; il est **simplement rapporté** au PO.
- **Garde-fous confirmés.** **Un seul** jeu de noms ; consignés au rapport R1 avec leur justification ; **aucun changement** après la fin de R1 sans nouveau calcul du manifeste et mention au rapport.
- **Contrainte de forme (PROPOSITION du dossier, conservée comme telle).** Le nom de l'erreur de refus doit être distinct de `migration.unknown_applied` et de `tenant.migrations_pending` (A1-T1c exige que le code existant reste inchangé).
- **Conséquences.** *R4a* : les noms doivent exister avant le gel, donc **avant R2**. *G6* : le manifeste M2 les reprend à l'identique. *Tests* : A1-T1a à A1-T5c les utilisent. *Migration 0022* : aucune.

#### P86-7 — G6 avec contrôles navigateur, RTL et mobile non vérifiés (R7a-L1) — **DÉCISION PO CONFIRMÉE**

- **Référence.** P85 §4.8 (R7a-L1), §4.7 (R7a-C01 à C08) ; P84 §2.5 ; R7a-AC6 ; dossier P86 §3 (P86-7), §7.
- **Décision (option C).** **G6 conditionnel.**
  - **Obligatoires avant G6 (exécutables en phase 1, sans navigateur)** : R7a-S01 à S07 (service) ; R7a-S10 à S15 (chaînes et logique de composant, **dans la limite de ce que l'outillage permet**, T-7) ; R7a-C01 ; V6-18 et test d'égalité des clés (p69) ; R7a-AC1 à AC5, AC7, AC8 ; **contrôle statique de sécurité d'affichage en arabe (C03s)** ; liste exacte des fichiers d'interface modifiés. Précision du dossier : C03s est une **PROPOSITION à confirmer en R1** (les quantités, numéros de lot et sigles d'état sont produits **comme éléments isolés**, vérifiable par chaîne sans navigateur) ; sa confirmation de détail relève de R1, pas du PO.
  - **Reportables sous verrou d'exposition** : R7a-C02 (sens de la mise en page), C03 (lisibilité visuelle complète), C04 (débordement horizontal), C05 (boutons PERTE, cible tactile), C06 (messages longs), C07 (non-régression visuelle P65), **C08 (relecture arabe humaine)**. Statut maintenu : **NON VÉRIFIÉ**, **jamais PASS**.
  - **Verrou d'exposition** : tant que ces contrôles ne sont ni exécutés ni acceptés par écrit, **G7 n'est pas proposée** et **aucune copie de version contenant R7a n'est construite** depuis le dépôt ; le rapport G6 le mentionne **en tête**.
- **Condition du PO (question métier confirmée).** La promotion peut être proposée avant les contrôles visuels et linguistiques, **à condition que rien ne soit mis à la disposition d'utilisateurs avant que ces contrôles aient eu lieu ou que le PO les ait acceptés par écrit**.
- **Limite assumée (reprise du dossier).** Cette option s'écarte de la lettre de P84 §2.5 (« bloque G6 ») : elle n'est valable **que parce que le PO l'a explicitement acceptée** ici.
- **Conséquences.** *R7a* : R7a-C02 à C08 sont livrés **spécifiés et préparés**, **pas exécutés**. *G6* : grille du §6. *Tests* : C03s s'ajoute à R7a ; le reste est NON VÉRIFIÉ. *Migration 0022* : aucune. **Si le PO choisissait plus tard « G6 attend »** : la mission s'arrêterait à R6 et PC-1 deviendrait bloquant (effet décrit au plan P86 §13 ; **non décidé**).

#### P86-8 — Point d'entrée du passage en PERTE (R7a-L2) — **DÉCISION PO CONFIRMÉE** (complétée par la précaution A, §4.1)

- **Référence.** P85 §4.8 (R7a-L2), §4.1 (« exclusivement »), §4.4 (R7a-E5 à E9) ; C-P82-3 (P83) ; dossier P86 §3 (P86-8).
- **Décision (option 2, « lecture (2) »).** La **confirmation est livrée**, mais le passage en PERTE **n'est pas exposé dans l'interface** (V1/V3) tant qu'une **mission d'interface distincte** n'a pas fourni le formulaire de passage. **Aucun écran** ne permet de passer du stock en PERTE en attendant ; le **service** refuse tout passage sans confirmation (R7a-E8).
- **Raisons retenues (dossier).** Respect de « exclusivement » (A-T3) ; ne pas introduire dans R7a l'opération **irréversible** (DP1-R4) derrière un écran réduit non couvert par les contrôles visuels reportés (P86-7) ; la protection décisive est **côté service** (R7a-S04, S05, S06).
- **Limite assumée (reprise).** Le scénario d'usage complet (PER-40 à PER-47 au niveau écran) reste **NON VÉRIFIÉ** jusqu'à la mission d'interface, qui devra **réutiliser** le composant de confirmation et rejouer R7a-S12 et S13. C-P82-3 est « satisfaite par le service, non encore visible pour l'utilisateur ».
- **OBSERVATION du dossier, conservée comme observation et non comme décision.** « Le service restera atteignable par la route HTTP de passage prévue en R4b (plan P82 §3.3, `stockRoutes.ts`) par un détenteur du droit, mais ce droit n'est pas accordé par défaut (Q-11) et la confirmation y est exigée. » **Cette observation est à relire à la lumière de la précaution A** : voir **RÉSERVE RES-1** (§8).
- **Conséquences.** *R7a* : composant de confirmation (texte et logique) **sans point d'entrée** ; R7a-E9 vérifié sur chaque écran touché. *G6* : grille du §6. *Tests* : S04 à S06 (service), S12, S13 (composant). *Migration 0022* : aucune.
- **Effet d'une modification (non décidé, rappel).** Si le PO choisissait un jour la lecture (1) (écran réduit dans R7a), P86-7, P86-10 et P86-11 devraient être modifiés **ensemble** ; ce serait une **extension de A-T3**, donc une décision distincte. **Option (1) NON retenue.**

#### P86-11 — Séquencement : R7a dans la fenêtre de gel (§6.3 de P85) — **DÉCISION PO CONFIRMÉE**

- **Référence.** P85 §6.3 ; plan P82 (R5 fige l'empreinte du candidat, R6 produit M1) ; dossier P86 §3 (P86-11), plan P86 §2.
- **Décision (option a).** **R4a → R4b → R4c → R4d → R4e → R7a → R5 → R6 → G6**, avec **PC-1** entre R5 et R6 (P86-5). La lecture « R7a dans la fenêtre de gel » de P85 §6.3, qui était **À CONFIRMER**, est **confirmée**.
- **Justification (dossier).** R7a suit R4d (charge utile de refus, paramètre de confirmation) et R4e (clés d'interface du catalogue stabilisées avant le test d'égalité FR/EN/AR). R5 inclut alors les tests d'interface et fige l'empreinte du candidat **avec** R7a présent.
- **Question métier confirmée.** Les deux messages d'interface font partie de la **même livraison**, figée avec tout le reste, et sont réalisés **avant les derniers contrôles de non-régression**.
- **Conditions.** R7a **ne modifie aucun écran de catalogue**, ni les cases de suivi par lot de DP2-R1 (voir précaution B, §4.2, qui traite ce point **hors R7a**). R7a ne touche pas au schéma : toute exigence de schéma découverte en R7a est une **condition d'arrêt** (P86-S1).
- **Conséquences.** *R4a* : début du gel (manifeste M0 déjà établi). *G6* : M2 inclut les fichiers d'interface de R7a. *Tests* : R5 rejoue la non-régression V1/V3 **une seule fois**. *Migration 0022* : candidat figé en R5, inchangé par R7a.
- **Alternatives non retenues.** R7a après R5 (R5 rejoué) ; R7a hors gel (contredit A1-M2 et A1-K5, déconseillé).

### 3.2 Groupe B — Tests, compatibilité, capacité

#### P86-2 — Adaptation préalable de `schema_parite`, `p33`, `p34` (A1-L2) — **DÉCISION PO CONFIRMÉE**

- **Référence.** P85 §2.5 (A1-L2) ; P84 étude §2.3 point 2 ; RL-3, RL-4 (P84 §2.2) ; constats T-3 et T-4 ; dossier P86 §4 (P86-2).
- **Décision (option a).** Trois contrôles existants sont rendus **insensibles au simple ajout de la nouvelle migration**, **sans en affaiblir aucun**, et cette modification est **rapportée au PO** : le nombre d'identifiants devient **calculé** (officiels + fixtures) ; la ligne « en avance » utilise un **identifiant éloigné non utilisable** (le dossier cite `0099` **à titre d'exemple**).
- **Garde-fous confirmés.** **Aucune assertion supprimée ni affaiblie** ; chaque test garde sa sensibilité (un test d'écart doit **échouer** si l'identifiant simulé devenait connu : à démontrer par une **variante négative**, condition d'arrêt 15 de P84) ; modification listée et justifiée au **rapport R6** ; ces trois fichiers sont ajoutés au manifeste. Désactiver ces tests est **exclu**.
- **Séquence.** L'adaptation intervient **en R1** (avant tout chargement du candidat dans les bancs par défaut, donc avant R2) ; la « version valable avant et après » est elle-même vérifiée en R0/R1 sur le jeu 0021 (comportement **inchangé**).
- **Conséquences.** *G6* : supprime RL-4 ; C5 comparé en nombre de tests réussis par fichier. *Tests* : `schema_parite` reste lié au banc D1 local (T-3) ; son **exécution** relève de la phase 2 (voir P86-6), son **adaptation** relève de la phase 1 (modification de fichier). **Cette décision confirme le principe métier ; l'adaptation elle-même n'est pas autorisée par P87** (§5).

#### P86-4 — Valeur et approbation des seuils de ressources (A2-L2) — **DÉCISION PO CONFIRMÉE**

- **Référence.** P85 §3.4 ; P84 étude §3.4 ; plan P82 §12 ; dossier P86 §4 (P86-4).
- **Décision (option b).** Le PO approuve le **principe** : seuils **documentés**, **mesurés en R0**, **arrêt automatique**. Les valeurs de la grille P84 sont des **planchers de départ** que la mission peut **durcir** si la mesure de R0 l'exige, **jamais assouplir sans nouvel accord du PO**.
- **Valeurs de départ (P84 §3.4, rappel).** Disque ≥ 1 000 000 Ko au lancement, arrêt < 800 000 Ko ; mémoire ≥ 500 Mo au lancement, arrêt < 350 Mo ; `TMPDIR` ≤ 300 000 Ko ; phase 2 ≥ 1 800 Mo. Le seuil de 800 Mo du plan P82 §12 reste une **hypothèse non approuvée**.
- **Statut précisé.** Ces valeurs deviennent des **décisions PO** par la confirmation de ce point, **en tant que planchers** ; le dossier P86 les qualifiait d'**hypothèses** de départ jusque-là.
- **Mode opératoire confirmé.** Une suite à la fois ; relevé disque et mémoire avant et après ; arrêt immédiat à la première condition S-1 à S-7 ; taille du `TMPDIR` et seuil d'arrêt disque **fixés avant création** (P85 §3.3 d) ; **aucun benchmark de calibrage** (NON AUTORISÉ, P85 §3.1).
- **Vigilance conservée (X-9, X-10 du dossier).** La marge disque est très faible (≈ 48 000 Ko au-dessus du seuil d'arrêt avec 300 000 Ko de `TMPDIR`) ; si la marge restante est inférieure à zéro, la phase 1 n'est **pas lancée** (BLOQUÉ). La mémoire disponible relevée (≈ 616 à 644 Mo selon le relevé) respecte tout juste le plancher de 500 Mo. Ces valeurs sont des **relevés instantanés**, non des garanties.
- **Conséquences.** *G6* : le rapport joint les relevés avant/après. *Migration 0022* : aucun effet.

#### P86-5 — Phase 2 : mémoire, autre hôte ou report (A2-L1) — **DÉCISION PO CONFIRMÉE**

- **Référence.** P85 §3.5 et §3.7 (A2-L1) ; dossier P86 §4 (P86-5), plan P86 §8.3.
- **Décision (option 3 par défaut).** **Report explicite** des essais lourds (D1 local, navigateur), marqués **NON VÉRIFIÉS** si aucune capacité n'est fournie. Le PO **garde la possibilité de fournir de la capacité avant la décision de promotion**.
- **Porte de capacité PC-1.** Placée **après R5 et avant G6** (plan P86 : après R5, avant R6). Si, **à ce moment-là**, une des options (1) mémoire supplémentaire ou (2) autre hôte a été **décidée par le PO et mise en œuvre par lui**, la mission exécute les scénarios de la phase 2 (une suite à la fois, arrêts S-1 à S-7) ; **sinon** elle consigne le report. **Aucune capacité n'est présumée.**
- **Limites.** Une augmentation de mémoire est un levier matériel **hors périmètre agent** (NON AUTORISÉ : réglage système). Les modalités et la sécurité des transferts vers un autre hôte restent **à définir par le PO**. O-7 (comportement `ALTER TABLE … ADD COLUMN` sur table `STRICT`) est levé **dans la limite du moteur SQLite du banc** (R2) ; son comportement **en D1 réel** reste **NON VÉRIFIÉ**.
- **Statut de A-T2 (inchangé).** La **phase 2 reste SUSPENDUE** (P85 §3.5). La décision P86-5 enregistre le **principe du report** et la **porte PC-1** ; elle **ne lève pas** la suspension. Le plan P86 §14 point 4 maintient « phase 2 sauf PC-1 dans les conditions du §8.3 » parmi les non-autorisations.
- **Conséquences.** *R7a* : C02 à C07 attendent PC-1 ou restent NON VÉRIFIÉS sous verrou (P86-7). *G6* : le rapport affiche, **en tête**, la liste des scénarios NON EXÉCUTÉS. *Tests* : V6-10 à V6-17, V6-19, MIG-T2 (D1), C7b. *Migration 0022* : l'essai du candidat (R2) se fait sur **banc mémoire et fichiers**, sans D1.

#### P86-6 — Sort de C7 et de la partie D1 local de MIG-T2 (§3.6 de P85) — **DÉCISION PO CONFIRMÉE**

- **Référence.** P85 §3.6 ; constat T-5 ; dossier P86 §4 (P86-6).
- **Décision (option b).** **C7 est scindé** : **C7a** (mémoire + fichiers, **obligatoire**) et **C7b** (D1 local, à PC-1 **ou NON VÉRIFIÉE avec réserve écrite**).
- **Solution confirmée pour MIG-T2 en phase 1.** Comparaison **normalisée** du schéma entre banc **mémoire** et banc **fichiers** (tables, index, déclencheurs, vues, historique `_migrations`) **plus** comparaison **statique** des instructions du candidat (absence de constructions non portables : `hasMultipleStatements`, séparateurs `--> statement-breakpoint`, déjà couvertes par MIG-T8). Cette comparaison statique **ne remplace pas** la parité D1 : elle en réduit le risque.
- **Ce qui reste exigé par D1.** L'exécution de `schema_parite` en banc D1 et des tests D1 de stock (plan P82 §3.3 : stock D1, 22 scénarios, ≈ 6 minutes).
- **Statut au rapport G6.** « **C7b NON VÉRIFIÉE** » avec **réserve écrite** que le PO accepte ou refuse **à G6** ; **jamais PASS**. Le PO décide **au moment de la promotion** si la réserve est acceptable.
- **Exclu.** Supprimer D1 du critère (relâcherait un garde-fou sans décision).
- **Conséquences.** *Tests* : MIG-T2 mémoire/fichiers PASS possible ; MIG-T2 D1 « NON EXÉCUTÉ » ; `schema_parite` adapté (P86-2) mais non exécuté en D1. *Migration 0022* : écrite pour rester compatible avec les règles de portabilité vérifiables statiquement ; **compatibilité D1 réelle NON VÉRIFIÉE**.

### 3.3 Groupe C — Interfaces, messages, contrôles

#### P86-9 — Interfaces V1 et V3 (A3-L2) — **DÉCISION PO CONFIRMÉE**

- **Référence.** P85 §4.9 (A3-L2) ; constats T-6 et T-8 ; dossier P86 §5 (P86-9).
- **Décision (option a).** **V1 et V3.** Les deux messages doivent exister **dans les deux versions de l'interface** (on-premise et SaaS), et une partie non vérifiable dans l'une d'elles est **signalée comme telle**.
- **Conditions confirmées.** Les deux interfaces **réutilisent le même contenu fonctionnel** (mêmes champs de la charge utile) ; chaque interface porte ses **propres clés FR/EN/AR** (test d'égalité p69). Toute vérification impossible dans l'une des deux est listée **NON VÉRIFIÉE par interface** ; elle **n'est jamais omise**.
- **V3 livrée en source (T-8).** Sa construction (`dist`) **ne se fait pas** dans la mission de réalisation tant que la capacité n'est pas établie (sauf PC-1, dans les conditions du plan P86 §8.3) ; les tests R7a-S10 à S15 portent donc sur la **logique et les chaînes** des sources.
- **Fichiers cités par le dossier (T-6, constat).** V1 : `purchasing.js`, `purchasing-i18n.js` ; V3 : `PurchaseForms.jsx`, `Achats.jsx`, `purchaseStrings.js`, `purchaseApi.js`. La **liste exacte** des fichiers modifiés sera **justifiée au manifeste** (R7a-AC7) ; elle n'est pas fixée ici.
- **Conséquences.** *G6* : C9 couvre les deux interfaces. *Tests* : S10 à S15 par interface ; V6-18 ; p69. *Migration 0022* : aucune.

#### P86-10 — Mention MSG-4 dans le texte de confirmation (A3-L3) — **DÉCISION PO CONFIRMÉE**

- **Référence.** P85 §4.2 et §4.9 (A3-L3) ; P82 V2-R8 ; P84 dossier §14.6 ; dossier P86 §5 (P86-10).
- **Décision (option c, liée à P86-8).** La lecture (2) de P86-8 étant retenue, le texte de confirmation comporte une **formulation neutre** qui **renforce la prudence sans désigner une action inaccessible**. **MSG-4 complet** (« en cas de doute, utiliser EN_CONTROLE, réversible ») est **reporté à la mission d'interface**, qui disposera du passage en EN_CONTROLE.
- **Statut précisé.** La formulation neutre est un **ajout de précaution distinct de MSG-4** ; elle n'était pas dans la décision A-T3 ; le PO la confirme ici comme **variante retenue**. MSG-4 (P85 §4.2 : **À CONFIRMER**) reste **non inclus dans R7a**.
- **Limite.** Le **texte exact** n'est **pas fixé** par P87 (l'exemple du dossier, « cette opération est définitive ; en cas de doute, ne la confirmez pas », est **illustratif**). Il sera défini dans la mission de réalisation, un texte de plus par langue (clé FR/EN/AR), et rapporté. La décision PO porte sur le **principe** : prudence en cas de doute, **sans renvoi vers une action indisponible**.
- **Conséquences.** *R7a* : R7a-S12 vérifie la présence de la quantité, de l'irréversibilité et de la formulation neutre ; R7a-S14. *R4a, G6, 0022* : aucune.

### 3.4 Points maintenus ouverts ou non approuvés

#### P86-12 — O-5, O-6, O-9, O-10 (OUVERT) — **DÉCISION PO CONFIRMÉE : MAINTENIR OUVERTS**

- **Référence.** P81 plan §9 ; P85 §5.1 ; dossier P86 §6 (tableau de quatre lignes).
- **Décision.** Ces quatre points **restent ouverts** ; **aucun ne bloque la réalisation** ; **aucun ne devient décidé** par la confirmation ; le rapport de réalisation **ne peut pas** les présenter comme tranchés.

| Point | Statut conservé | Traitement imposé par la décision (dossier P86 §6) | Étiquette de limite |
|---|---|---|---|
| **O-5** Réception « à contrôler » (entrée directe en EN_CONTROLE) | **OUVERT** | Le plan ne prévoit aucun chemin d'entrée en EN_CONTROLE ; les quatre entrées vont **dans DISPONIBLE** (DP5-R1 à R5) ; l'état EN_CONTROLE s'atteint **uniquement par passage** (R4b) ; un scénario de **non-création** vérifie qu'une réception crée toujours du DISPONIBLE ; mention au rapport en limite | à trancher avant un lot ultérieur « réceptions » |
| **O-6** Retour client en contrôle d'abord | **OUVERT / REPORTÉ** (DP5-R6) | Le retour client **garde son comportement actuel** (entrée directe en DISPONIBLE, quantités vendues et lots d'origine) ; non-régression des retours (V6-17, ventes P43) | à trancher avant le lot « retours » |
| **O-9** État de la migration 0021 en DEMO | **OUVERT** (concerne G7) ; état de la **base** DEMO **NON VÉRIFIÉ** | Hors périmètre de la réalisation (G7 non autorisée) ; **préalable à toute décision G7** ; aucune lecture de la DEMO ; le rapport G6 **n'affirme rien** sur la DEMO | à instruire avant toute demande de G7 |
| **O-10** Prérequis du Lot 2 (CAT-14 à CAT-16 du plan directeur) | **OUVERT** ; statut réel du Lot 2 **NON VÉRIFIÉ** | Le rapport emploie « **livraison du candidat STOCK** » et **jamais** « clôture du Lot 3 » ; les scénarios CAT-01…CAT-14 de la matrice P84 ne sont **pas confondus** avec CAT-14 à 16 du plan directeur (T-15) ; rappel de O-10 dans la décision G6 | décider plus tard s'il faut un examen du Lot 2 avant de déclarer le Lot 3 clos |

#### P86-13 — V-3 : formule CAT-11 et convention P82 (NON APPROUVÉ) — **DÉCISION PO CONFIRMÉE : MAINTENIR NON APPROUVÉ**

- **Référence.** P83 §7 (V-3 : VIGILANCE, NON APPROUVÉ) ; P82 spécifications §8.2 ; DP1-R9 ; C4 ; dossier P86 §6 (P86-13).
- **Décision (option b).** **V-3 reste NON APPROUVÉ.** La convention de calcul de P82 n'est **pas approuvée** par cet addendum. Le PO demande que l'**écriture exacte** de l'identité CAT-11 lui soit **soumise, avec exemples chiffrés, avant G6**. Une approbation immédiate de la convention de P82 **n'a pas été donnée**.
- **Précisions confirmées (dossier).** (i) Le côté « reçu » est calculé depuis `purchase_receipt_lines` (source indépendante) pour que l'identité ne soit pas tautologique ; (ii) les tests CAT-11 sont écrits **avec** la convention de P82 **et** vérifiés sur des cas simples où la lecture littérale du registre et la convention donnent le même résultat ; (iii) tout écart de lecture est consigné **comme tel** (tableau cas par cas : réception, contre-passation, retour client, régularisation, correction) ; (iv) **aucun PASS de CAT-11 n'est présenté comme approbation de V-3**.
- **Seule formule DÉCIDÉE.** Celle du registre (DP1-R9), « sous réserve de tests de cohérence préparés avant réalisation » (C4). La convention de P82 est une **convention de calcul, non une règle métier**.
- **Conséquences.** *R1* : relevé de lectures et tableau de cas. *R3/R4e* : tests CAT-11. *G6* : une ligne distincte dans la décision (règle 6 du §6). *0022* : la vue `stock_lot_received` est définie selon la convention ; si le PO décidait autrement, elle changerait **avant G6** (le candidat n'étant pas officiel).

---

## 4. ENREGISTREMENT DES DEUX PRÉCAUTIONS SUPPLÉMENTAIRES

Ces deux précautions ne font **pas** partie des 13 points. Elles sont enregistrées **comme formulées par le PO** et **ne sont pas reformulées** de façon à modifier leur portée.

### 4.1 PRÉCAUTION A — Opération PERTE et API — **PRÉCAUTION CONFIRMÉE**

- **Références.** Q-11 (P83 §4) ; C-P82-3 (P83 §5) ; V2-R8 (P82) ; DP1-R4 (P81) ; P86-8 ; P85 §4.1 et §4.4 (R7a-E5 à E9).
- **Texte de la précaution (PO).**
  1. **Aucun passage en PERTE ne doit être possible par API** tant que le **parcours utilisateur complet** et sa **confirmation explicite** ne sont pas disponibles.
  2. La confirmation doit présenter la **quantité concernée** et les **conséquences irréversibles**.
  3. Le **droit d'effectuer cette opération reste non accordé par défaut**.
  4. **L'absence d'écran ne doit jamais permettre de contourner la confirmation utilisateur.**
- **Statut.** Cette précaution **complète C-P82-3 et Q-11**. Elle **ne vaut pas autorisation de développer le parcours** (§5).
- **Rapport avec les décisions existantes (OBSERVATION).** Q-11 confirme déjà : confirmation explicite, quantité et irréversibilité affichées, droit non accordé par défaut ; C-P82-3 : confirmation avant validation. La précaution A **ajoute** deux exigences : (a) **l'API elle-même ne permet pas** le passage en PERTE tant que le parcours complet n'existe pas ; (b) la confirmation est celle de **l'utilisateur** et ne peut être contournée faute d'écran. Elle **va plus loin que la lecture (2) de P86-8**, qui bornait l'absence d'écran mais laissait le service atteignable par une route HTTP (voir **RES-1**, §8).
- **Limite (À CONFIRMER).** Le PO n'a pas listé les éléments du « parcours utilisateur complet ». Sont nommés : la confirmation explicite, la quantité, les conséquences irréversibles. Que le **formulaire de passage** (UI-2, hors R7a) en fasse partie est une **HYPOTHÈSE** raisonnable, **non décidée**.
- **Conséquence documentaire (non décisionnelle).** Tant que la précaution A n'est pas levée par une décision PO, le plan de réalisation ne peut pas livrer un chemin d'API de passage en PERTE ; la conception technique correspondante (plan P86 §3, R4b) est à réviser (RES-1).

### 4.2 PRÉCAUTION B — P86-V1, suivi obligatoire par lot, traitement par lot — **CHOIX EXPLICITE DU PO**

- **Références.** DP2-R1 (P81 : suivi par lot **obligatoire** pour le type MÉDICAMENT, création et adoption — DÉCIDÉ) ; DP2-R2, R3, R4 (inchangées) ; P86-V1 (dossier P86 §8.3, hors des 13 points) ; plan P86 §3 (R4e), §8.4 (PRH-3), §13.
- **Choix du PO.**
  1. **Refus explicite et compréhensible** lorsqu'une création ou une adoption est **incompatible avec le suivi obligatoire par lot**.
  2. **Explication du motif** du refus.
  3. **Possibilité de corriger les informations dans le parcours utilisateur.**
  4. Traitement **à intégrer avant R4e**.
  5. **Aucune suspension générale** des créations et adoptions lorsque leur conformité **peut être corrigée**.
- **Interdits confirmés.** Ne pas remplacer ce choix par un **simple message technique générique** ni par une **suspension globale**.
- **Effet sur P86-V1.** Le comportement transitoire que le dossier P86 proposait de faire confirmer (refus par le service avec message générique, formulaire non adapté jusqu'à une mission d'interface) **n'est pas retenu**. P86-V1 cesse d'être une simple vigilance : le **traitement est décidé** (ci-dessus) ; sa **réalisation n'est pas autorisée** (§5). DP2-R1 à R4 restent **inchangées**.
- **Écrans cités par le dossier P86 (§8.3, constat).** V1 : `catalogue.js` ; V3 : `CatalogForms.jsx`, `reference.js`, `ReferenceCatalog.jsx`, où la case de suivi par lot a une valeur par défaut « non ». Leur modification **n'est pas dans R7a** (R7a-AC7, P86-S2, A-T3 « exclusivement ») : voir **RES-2** (§8).
- **Plan P86 §13 (rappel).** « Si refusé : une mission d'interface sur les cases de suivi par lot précède R4e (extension hors R7a) ; le plan est alors **à réviser**. » Le choix du PO relève de ce cas ; la révision du plan **n'est pas faite par P87**.

---

## 5. STATUT DES AUTORISATIONS

### 5.1 Décisions métier confirmées par le PO (25/11/2026)

| Objet | Statut |
|---|---|
| **P86-1 à P86-13** (§3) | **DÉCISIONS PO CONFIRMÉES** (P86-12 : maintien ouvert ; P86-13 : maintien NON APPROUVÉ) |
| **Précaution A** — opération PERTE et API (§4.1) | **PRÉCAUTION CONFIRMÉE** |
| **Précaution B** — P86-V1, suivi obligatoire par lot (§4.2) | **CHOIX EXPLICITE DU PO** |

### 5.2 Autorisations techniques NON ACCORDÉES par P87 (NON AUTORISÉ)

| # | Ce qui n'est pas autorisé | Remarque |
|---|---|---|
| N-1 | **Réalisation STOCK** (lots R0 à R6) | autorisation explicite et distincte requise (plan P86 §14, point 2 ; P85 §8.3) |
| N-2 | **Création ou exécution de la migration 0022** (y compris le candidat `0022_stock_etats.sql`) | aucun candidat n'existe ; dernier fichier officiel : 0021 |
| N-3 | **Modification de `compose.ts`** | même la garde de démarrage (F-2) n'est valable que dans une mission de réalisation lancée |
| N-4 | **Développement de R7a** | décision de périmètre confirmée ; aucune écriture de code autorisée |
| N-5 | **Développement du traitement de P86-V1** (précaution B) | décision du résultat attendu confirmée ; aucune interface, aucun service modifiés |
| N-6 | **Développement du parcours utilisateur complet de passage en PERTE** (précaution A) | la précaution ne vaut pas autorisation de développer le parcours |
| N-7 | **Adaptation de `schema_parite`, `p33` et `p34`** | principe confirmé (P86-2) ; aucune modification de fichier de test autorisée (le terme « schema_parity » de la demande P87 désigne le test `tests/lot1b/schema_parite.test.ts` du dépôt) |
| N-8 | **Promotion G6** | décision écrite et distincte du PO (C8) ; ne peut même pas être proposée avant la réalisation |
| N-9 | **G7** (application à la DEMO ou à une base réelle) | NON AUTORISÉE (DP6-R2, DP6-R3, P85 A1-L5) ; O-9 préalable |
| N-10 | **Phase 2** (D1 local, navigateur) et **exécution de PC-1** | phase 2 **SUSPENDUE** (A-T2) ; PC-1 n'exécute la phase 2 que si le PO a décidé **et mis en œuvre** une capacité |
| N-11 | **Tests, builds, benchmarks, déploiement, copies de version** | aucun |
| N-12 | **Mission d'interface** (formulaire de passage d'état, écrans de catalogue, MSG-4 complet, MSG-5 à MSG-8, UI-1 à UI-11) | hors A-T3 ; aucune autorisation |
| N-13 | Suppression, purge, réparation de système de fichiers, redémarrage, intervention systemd, opération Git manuelle | inchangé (P85 §8.2) |

### 5.3 Autorisations antérieures : portée inchangée

| Autorisation | Portée (P85) | Effet de P87 |
|---|---|---|
| **A-T1** (manifeste, gel R4a → G6, chargeur de candidat idempotent, garde de démarrage ; `compose.ts` pour la garde seule ; A1-K1 à K5) | **AUTORISATION FUTURE** F-1, F-2, valable **dans une mission de réalisation lancée** | **non élargie** ; aucune modification de `compose.ts` au-delà de la garde |
| **A-T2** (phase 1 sous conditions A2-K1 à K4 ; phase 2 SUSPENDUE ; `TMPDIR` après vérifications) | **AUTORISATION FUTURE** F-3 | **non élargie** ; P86-4 fixe les seuils comme **planchers** ; P86-5 et P86-6 **ne lèvent pas** la suspension |
| **A-T3** (lot R7a borné, avant G6, **exclusivement** C-P82-2 et C-P82-3) | **AUTORISATION FUTURE** F-4 | **non élargie** ; la précaution B et la précaution A **n'étendent pas** R7a |

Le tableau F-1 à F-4 de P85 §8.1 est **inchangé** ; les non-autorisations de P85 §8.2 sont **maintenues** (G6, G7, phase 2, suppression, redémarrage, systemd, `compose.ts` au-delà de la garde, extension de R7a, décision sur O-5/O-6/O-9/O-10/V-3, Git manuel).

### 5.4 Points ouverts, non approuvés, vigilances (état après P87)

| Réf. | Statut après P87 |
|---|---|
| O-5 | **OUVERT** (P86-12) |
| O-6 | **OUVERT / REPORTÉ** (P86-12) |
| O-9 | **OUVERT** (P86-12) ; état de la base DEMO **NON VÉRIFIÉ** |
| O-10 | **OUVERT** (P86-12) ; statut du Lot 2 **NON VÉRIFIÉ** |
| O-7, O-8 | **NON VÉRIFIÉS** ; levés seulement dans la limite du banc en R2 (P86-5) |
| V-3 | **NON APPROUVÉ** (P86-13) |
| V-4 | VIGILANCE, traitée par A-T1 ; levée seulement à la vérification de la réalisation |
| V-5 | VIGILANCE inchangée (C-P82-1 sur ERREUR_SAISIE et AUTRE) |
| V-6 | VIGILANCE inchangée : restitution de C-P82-2 et C-P82-3 ; R7a la traite, mais **tant que la livraison n'est pas vérifiée, elle reste ouverte** (voir RES-1 pour C-P82-3) |
| P86-V1 | **traitement décidé** (précaution B) ; réalisation **NON AUTORISÉE** ; **à intégrer avant R4e** (voir RES-2) |

**Aucun de ces points n'est clos par P87.**

---

## 6. RÈGLE DE G6 ENREGISTRÉE (conséquence des points confirmés)

Les points P86-1, 5, 6, 7 et 11 étant confirmés, la règle suivante en **découle**. Elle reprend le §7 du dossier P86 ; seule la **portée décidée** est enregistrée ici. La décision G6 elle-même **n'est pas prise** (N-8).

### 6.1 Catégories de vérifications (P86 §7.1)

| Catégorie | Contenu | Effet si non vrai |
|---|---|---|
| **BLOQUANT (B)** | C1, C2, C3, C4, C5, C6, **C7a**, C8 ; **C9** (partie obligatoire, P86-7) ; A1-T1a à A1-T5c ; garde de démarrage ; refus nommés de contre-passation et de passage en PERTE sans confirmation (R7a-S01 à S07) ; aucune promotion partielle (A1-K5) ; aucun niveau négatif ; chaîne d'audit | **G6 non proposée** ; arrêt si la cause est un défaut de la livraison |
| **RÉSERVE (R)** — reportable **avec accord écrit du PO** | **C7b** (D1 local) ; V6-10 à V6-17, V6-19 ; R7a-C02 à C07 ; scénarios de phase 2 | G6 peut être proposée **avec la réserve inscrite en tête du rapport** ; le PO l'accepte ou la refuse **dans sa décision** |
| **NON AUTOMATISABLE (H)** | R7a-C08 (relecture arabe) ; V-3 (lecture du PO) ; O-5, O-6, O-9, O-10 | jamais PASS ; inscrit NON VÉRIFIÉ / OUVERT |

### 6.2 Règle de décision (conséquence de P86-1, 5, 6, 7)

1. Toutes les vérifications **(B)** sont PASS, avec preuve (commande, résultat, empreinte).
2. Chaque vérification **(R)** est PASS ou porte « NON EXÉCUTÉ — motif — risque résiduel » ; aucune n'est comptée comme réussie sans exécution.
3. **Verrou d'exposition** consigné : aucune copie de version contenant la livraison n'est construite ; G7 non proposée tant que les réserves d'interface ne sont pas levées ou acceptées par écrit.
4. La liste « NON EXÉCUTÉ / NON VÉRIFIÉ » figure **en tête** du rapport.
5. O-5, O-6, O-9, O-10 sont rappelés ouverts ; la livraison n'est pas qualifiée de « clôture du Lot 3 ».
6. **V-3** : écriture exacte de l'identité CAT-11 soumise au PO avec exemples chiffrés ; aucune approbation implicite.
7. La décision G6 est **une décision distincte et écrite du PO** ; le rapport de réalisation ne la contient pas.

**Statut des conditions d'arrêt P86-S1 à P86-S5 et des statuts autorisés** (PASS / FAIL / NON EXÉCUTÉ / NON VÉRIFIÉ / REPORTÉ) : ils figurent au dossier P86 §7.4 et §7.5 et au plan P86 §11.3 comme **PROPOSITIONS techniques** liées aux points confirmés. P87 **ne les décide pas séparément** : leur reprise dans la mission de réalisation sera **vérifiée à l'autorisation de lancement** (À CONFIRMER). Les 15 conditions d'arrêt de P84 §13.2 et S-1 à S-7 restent en vigueur comme documentées.

---

## 7. SÉQUENCEMENT ENREGISTRÉ

```
R0 ─► R1 ─► R2 ─► R3 ─► R4a ══(gel)══► R4b ─► R4c ─► R4d ─► R4e ─► R7a ─► R5 ─► PC-1 ─► R6 ─► [G6 : décision PO distincte] ─► (G7 hors mission)
```

| Élément | Statut |
|---|---|
| Ordre R0 → R1 → R2 → R3 → R4a → gel → R4b → R4c → R4d → R4e → R7a → R5 → PC-1 → R6 → G6 | **DÉCISION PO CONFIRMÉE** (P86-11 pour R7a avant R5 ; P86-5 pour PC-1 ; plan P86 §2 pour l'ensemble) |
| Gel de livraison R4a → G6 (A1-M2) | **DÉCIDÉ** (A-T1), inchangé |
| Adaptation des trois tests et noms du manifeste **en R1**, donc avant le gel | confirmée (P86-2, P86-3) |
| R7a **dans** la fenêtre de gel, couvert par le manifeste M2 | confirmée (P86-11) |
| PC-1 après R5 et avant G6 (placée avant R6 dans le plan) | confirmée (P86-5) ; **les deux formulations du dossier et du plan sont compatibles** (voir §9) |
| **Traitement de P86-V1** | **« à intégrer avant R4e »** (précaution B) ; **sa place exacte et son lot ne sont pas fixés** (RES-2) |
| Précaution A | s'applique à **R4b** (passages d'état) et au parcours futur de passage en PERTE (RES-1) |

---

## 8. RÉSERVES ET POINTS SIGNALÉS (sans décision PO inventée)

Ces points sont **signalés**. Aucun n'est tranché par P87. Ils n'invalident aucune décision confirmée.

### RES-1 — Précaution A et conception de R4b (CONTRADICTION APPARENTE avec le plan P86, non avec les décisions PO)

- **Constat (FAIT VÉRIFIÉ sur les textes).** Le plan P86 (R4b, §3) prévoit un passage d'état **par service et par route HTTP** (`stockRoutes.ts`), la **confirmation de PERTE exigée par le service** (paramètre de confirmation portant la quantité) et un droit non accordé par défaut. Le dossier P86 (P86-8, point 4) note que le service « restera atteignable par la route HTTP » par un détenteur du droit. Les scénarios PER-04, PER-05 et R7a-E6 supposent qu'un passage **confirmé** puisse s'exécuter.
- **Précaution A.** « Aucun passage en PERTE ne doit être possible par API tant que le parcours utilisateur complet et sa confirmation explicite ne sont pas disponibles. »
- **Tension.** Lue **à la lettre**, la précaution A est **plus restrictive** que le plan : **aucun passage en PERTE par API**, même avec un paramètre de confirmation, tant que le parcours complet n'existe pas. Lue comme « pas de passage sans confirmation », elle serait **déjà satisfaite** par le plan. **Le PO n'a pas précisé laquelle.**
- **Effets possibles si la lecture littérale est retenue (HYPOTHÈSE, non décidée).** Les scénarios de **succès** d'un passage en PERTE (PER-04, PER-05, R7a-E6) ne pourraient plus être exécutés par l'API ; seuls les **refus** (R7a-S04 à S06) resteraient testables ; l'état PERTE ne serait **atteignable par aucune voie** (y compris pour les indicateurs, rapports et tests qui le supposent) jusqu'à la mission d'interface.
- **Traitement.** **À CONFIRMER par le PO** avant R4b : la lecture retenue et, si c'est la lecture littérale, la conséquence sur les tests PER-* et R7a-E6. **La révision du plan P86 §3 (R4b) n'est pas faite par P87.** Aucune autorisation d'implémenter l'une ou l'autre lecture n'est donnée.
- **Compatibilité avec Q-11 et C-P82-3 (vérifiée).** La précaution A **ne contredit** ni Q-11 ni C-P82-3 ; elle les **renforce** (§9, point 2).

### RES-2 — Précaution B : périmètre, place et autorisation du traitement

- **Constat.** Le choix du PO inclut « la possibilité de corriger les informations dans le parcours utilisateur ». Cela suppose une **modification des écrans** de création et d'adoption (case de suivi par lot, message de refus lisible, explication du motif), donc des fichiers d'interface **hors de R7a** (R7a-AC7 ; P86-S2 ; A-T3 « exclusivement »).
- **Tensions documentaires (OBSERVATION).** (a) **Aucune autorisation** existante (F-1 à F-4) ne couvre ce traitement : il n'est pas dans A-T3. (b) **Sa place** « avant R4e » n'est pas située par rapport au gel R4a → G6 : avant R4a (donc avant le gel), ou entre R4d et R4e (donc dans la fenêtre de gel et couvert par le manifeste M2). (c) Sa **nature** (lot nouveau, extension de R7a, mission d'interface distincte) n'est pas fixée ; le plan P86 §13 le nomme « mission d'interface sur les cases de suivi par lot précédant R4e (extension hors R7a) » et précise que « le plan est alors **à réviser** ».
- **Ce qui est décidé.** Le **résultat** attendu (refus explicite, motif, correction possible, avant R4e, pas de suspension générale).
- **Ce qui n'est pas décidé.** Le **lot**, sa **position**, ses **fichiers**, son **autorisation** et son **effet sur le manifeste, R7a-AC7 et les contrôles visuels** (RTL, mobile) pour les écrans de catalogue. Les vérifications navigateur correspondantes seraient **NON VÉRIFIÉES** tant que la phase 2 reste suspendue (cohérent avec P86-7 et P86-9).
- **Traitement.** **À arbitrer par le PO** à l'autorisation de lancement ou dans la révision du plan. DP2-R1 à R4 **inchangées**.

### RES-3 — Précisions de vocabulaire (OBSERVATIONS, sans effet sur les décisions)

1. **« Neuf vérifications »** (P86-1) désigne C1 à C9 ; C7b est en catégorie **réserve** (§6.1) : toutes les neuf ne sont donc pas **bloquantes**. La règle de blocage de P86-1 (« sauf éléments reportables sous verrou, et seulement par acceptation écrite du PO ») fait foi.
2. **Place de PC-1** : le dossier P86 écrit « après R5 et avant G6 », le plan « après R5, avant R6 ». Les deux sont compatibles : R6 précède G6.
3. **Décision de principe sur O-5/O-6/O-9/O-10** : « maintenir ouverts » n'est pas une décision de fond ; elle ne clôt ni ne tranche rien.
4. **Texte de la formulation neutre** (P86-10) : exemple illustratif seulement ; texte exact à définir en réalisation.
5. **Statut du plan P86 §14 point 1** (« arbitrage sur les 13 points consigné par écrit ») : **rempli par cet addendum**. Le point 2 (autorisation de lancement) **n'est pas rempli**.

### RES-4 — Documents historiques non modifiés

Le dossier P86, le plan P86 et le rapport P86 contiennent encore les mentions « PROPOSITION », « EN ATTENTE » (PRH-1) et « À CONFIRMER » (PRH-3 : P86-V1). **Ils ne sont pas modifiés.** Pour les points enregistrés ici, **le présent addendum prévaut** sur ces mentions : PRH-1 est **levé** (confirmation du 25/11/2026) ; PRH-3 est **traité** par la précaution B ; PRH-2 (autorisation de lancement) reste **EN ATTENTE** ; PRH-4 (relecture arabe) reste **NON PLANIFIÉE** ; PRH-5 (lecture du PO sur CAT-11) reste **À PLANIFIER**.

---

## 9. COHÉRENCE DOCUMENTAIRE (contrôle sur les textes)

| # | Contrôle | Résultat |
|---|---|---|
| 1 | Compatibilité des 13 décisions avec P81 à P85 | **Aucune contradiction** relevée avec DP-1 à DP-6, C1 à C4, Q-1 à Q-12, H-1 à H-6, C-P82-1 à C-P82-3, A-T1, A-T2, A-T3 (dossier P86 §9, **recontrôlé par lecture** pour les points cités ; les tensions X-1 à X-10 du dossier P86 §8.2 portent sur des **textes de propositions**, pas sur des décisions) |
| 2 | **Q-11, C-P82-3 et précaution A** | **Pas de contradiction.** Q-11 : PERTE non annulable, confirmation, quantité, irréversibilité, droit non accordé par défaut. C-P82-3 : confirmation explicite avant validation avec quantité et conséquences. Précaution A : mêmes exigences **plus** l'absence de passage par API tant que le parcours complet manque et l'interdiction de contourner la confirmation. **Elle durcit, ne modifie aucun terme.** Seule la **conception technique** du plan P86 est à réviser : **RES-1** |
| 3 | P86-V1 intégré **avant R4e** | **Enregistré** (précaution B). Sa position exacte dans la séquence n'est **pas fixée** : **RES-2** |
| 4 | Séquencement R0 → R1 → R2 → R3 → R4a → gel → R4b–R4e → R7a → R5 → PC-1 → R6 → G6 | **Cohérent** avec P86-11, P86-5, P86-2, P86-3 et le gel A1-M2. La précaution B s'y insère **avant R4e** sans le contredire |
| 5 | Décision métier / proposition technique / autorisation d'exécution | **Distinguées** (§5). Les propositions techniques du dossier P86 (C03s, nom de l'erreur de refus, valeur `0099`, formulation neutre exacte, P86-S1 à S5) restent des **propositions** |
| 6 | O-5, O-6, O-9, O-10 ouverts ; V-3 NON APPROUVÉ | **Conservés** (§3.4, §5.4) |
| 7 | A-T1, A-T2, A-T3 non élargies | **Vérifié** (§5.3) ; P86-5 ne lève pas la suspension de la phase 2 ; les précautions A et B n'étendent pas R7a |
| 8 | P86-8 (lecture (2)) et P86-10 (formulation neutre) cohérents entre eux | **Oui** : liés (dossier P86 §8.1) ; la précaution A ne les contredit pas, elle les **renforce** |
| 9 | Distinction G6 / G7 | **Conservée** ; G6 et G7 non autorisées |

**Contradiction réelle découverte :** **aucune contradiction entre décisions PO.** Deux **tensions entre une précaution nouvelle et le plan technique P86** sont **signalées** (RES-1, RES-2). Elles **n'ont pas été résolues** par P87.

---

## 10. TABLEAU RÉCAPITULATIF DES DÉCISIONS ENREGISTRÉES

| Réf. | Intitulé | Statut | Effet immédiat |
|---|---|---|---|
| P86-1 | Critère de promotion C1 à C8 révisé (C5, C7a/C7b, C9) | DÉCISION PO CONFIRMÉE | liste de contrôle de G6 |
| P86-2 | Adaptation de `schema_parite`, `p33`, `p34` en R1 | DÉCISION PO CONFIRMÉE | aucune modification (N-7) |
| P86-3 | Noms du manifeste fixés une fois, fin de R1 | DÉCISION PO CONFIRMÉE | aucun |
| P86-4 | Seuils = planchers durcissables | DÉCISION PO CONFIRMÉE | applicables à la mission future |
| P86-5 | Report de la phase 2 + PC-1 | DÉCISION PO CONFIRMÉE | phase 2 **SUSPENDUE** inchangée |
| P86-6 | C7 scindé (C7a obligatoire, C7b réserve) | DÉCISION PO CONFIRMÉE | aucun |
| P86-7 | G6 conditionnel sous verrou d'exposition | DÉCISION PO CONFIRMÉE | G6 non décidée |
| P86-8 | Lecture (2) : pas d'écran de passage en PERTE | DÉCISION PO CONFIRMÉE (complétée par précaution A ; **RES-1**) | aucun |
| P86-9 | V1 et V3 | DÉCISION PO CONFIRMÉE | aucun |
| P86-10 | Formulation neutre (MSG-4 complet reporté) | DÉCISION PO CONFIRMÉE | aucun |
| P86-11 | R4e → R7a → R5 → R6 → G6 | DÉCISION PO CONFIRMÉE | aucun |
| P86-12 | O-5, O-6, O-9, O-10 maintenus ouverts | DÉCISION PO CONFIRMÉE (maintien) | aucun point clos |
| P86-13 | V-3 maintenu NON APPROUVÉ | DÉCISION PO CONFIRMÉE (maintien) | écriture exacte soumise avant G6 |
| Précaution A | PERTE et API | PRÉCAUTION CONFIRMÉE | **RES-1** à lever |
| Précaution B | P86-V1, refus explicite, correction possible, avant R4e | CHOIX EXPLICITE DU PO | **RES-2** à lever |

---

## 11. LIMITES ET CLÔTURE

- **Enregistrement documentaire** : aucune exécution, aucun test, aucune mesure ; **NON TESTÉ / NON MESURÉ** pour tout ce qui est technique.
- Les **textes de la confirmation du PO** sont ceux de la demande P87 ; leur reformulation dans cet addendum **reprend la portée** sans la modifier. En cas d'écart de lecture, **la formulation du PO prévaut**.
- La lecture des **sources antérieures** a été faite en lecture seule ; certaines sections de P81, P82 et P84 n'ont été relues que **partiellement** pour cet addendum (voir rapport P87).
- **Aucune autorisation technique** n'est accordée (§5.2). **La réalisation STOCK, la migration 0022 et la promotion G6 restent soumises à une autorisation technique distincte du PO.**

---
Fin de l'addendum P87.
