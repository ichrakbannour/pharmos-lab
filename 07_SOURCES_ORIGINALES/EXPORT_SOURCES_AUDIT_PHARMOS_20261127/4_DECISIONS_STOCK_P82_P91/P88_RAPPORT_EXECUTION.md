# PHARM'OS — P88 — Rapport d'exécution consolidé
## Réalisation du chantier STOCK — GO conditionnel du PO (25/11/2026)

**Statut global : BLOQUÉ AU PRÉFLIGHT (R0) — PRQ-3 (mémoire disponible sous le plancher de lancement).**
**Aucune modification technique n'a été effectuée. Aucune suite de test n'a été lancée. G6 : NON PROPOSÉE, NON PROMUE.**

---

## 1. Décisions PO enregistrées (DÉCIDÉ)

### GO conditionnel (25/11/2026)
- DÉCIDÉ : lancement de la réalisation consolidée du plan P86, après arbitrage de RES-1 et RES-2.
- DÉCIDÉ : cette autorisation ne permet pas de contourner contrôles, gels, critères d'acceptation ni limites antérieures.
- DÉCIDÉ : condition d'arrêt — si disque, mémoire, sauvegardes, intégrité ou retour arrière ne permettent pas une réalisation sûre, aucune modification, rapport précis, arrêt. Aucun nettoyage, purge, réparation ni suppression de temporaires.

### RES-1 — PERTE / API
- DÉCIDÉ : aucune opération PERTE n'est déclenchée directement par une API sans parcours utilisateur complet.
- DÉCIDÉ : l'API appelée par l'interface n'est autorisée qu'après authentification et confirmation explicite dans le parcours ; la confirmation est liée à l'opération, à la quantité et aux conséquences irréversibles ; le serveur vérifie les droits et la validité du contexte de confirmation ; un booléen client n'est pas une preuve ; sans parcours complet l'opération reste indisponible.
- À ADAPTER (non réalisé, bloqué) : PER-04, PER-05, R7a-E6. Aucun test de succès ne doit contourner la protection.
- PROPOSITION (non appliquée) : en R4b, aucune route HTTP de passage en PERTE utilisable sans contexte de confirmation émis et vérifié côté serveur, lié à l'opération et à la quantité.

### RES-2 — P86-V1 (suivi obligatoire par lot)
- DÉCIDÉ : lot de réalisation identifié, distinct de R7a, non couvert par A-T3, intégré avant R4e.
- Périmètre : écrans de création et d'adoption des médicaments ; obligation de suivi par lot affichée ; refus expliqués ; correction possible dans le parcours ; contrôle serveur maintenu ; créations/adoptions conformes non suspendues ; vérification FR/EN/AR, RTL, mobile.
- État : NON RÉALISÉ (bloqué en amont).

### Décisions antérieures rappelées (P87)
P86-1…13 confirmées ; C1–C9 (C7a obligatoire, C7b réserve, C9 = R7a dans M2) ; seuils P84 = planchers durcissables, jamais abaissés ; O-5/O-6/O-9/O-10 OUVERTS ; V-3 NON APPROUVÉ ; A-T1/A-T2/A-T3 inchangées ; phase 2 SUSPENDUE.

---

## 2. Lecture préalable (déclaration honnête)
- FAIT VÉRIFIÉ : lus pour le plan : P86 (lots, séquencement, portes, CP, PRQ, arrêts), P85 §2–§4/§8, P82 §9.5/§12, P83 Q-11/C-P82-1…3, P81 DP2-R1, `tests/support/env.ts`, `package.json`.
- LIMITE : P81–P84 et les rapports d'exécution n'ont été lus que partiellement, pas « intégralement » comme demandé par la mission. Non vérifié ligne à ligne.

---

## 3. Préflight R0 — résultats

| Contrôle | Résultat | Étiquette |
|---|---|---|
| Git | `main`, HEAD `d88461a` (auto-backup), statut propre, 0 tag, 0 stash ; aucune opération Git manuelle | FAIT VÉRIFIÉ |
| Disque `/` | 1 129 200 Ko libres (97 %) ≥ plancher de lancement 1 000 000 Ko ; > arrêt 800 000 Ko | FAIT VÉRIFIÉ |
| TMPDIR | `/home/user/work/p88/tmp` (700, vide, ext sur `/dev/root`) ; marge +181 092 Ko avec TMPDIR max 150 000 Ko | FAIT VÉRIFIÉ |
| `/tmp` | tmpfs plein (≈ 1 020 544 kB) — non touché | OBSERVATION |
| **Mémoire** | **MemAvailable : 489 648 kB (dernier relevé de la phase précédente), puis 497 432–497 900 kB au relevé final** — **< plancher de lancement 500 000 kB (PRQ-3)**, > arrêt 350 000 kB. Valeur initiale en début de mission : 619 840 kB | **BLOQUÉ** |
| Sauvegardes | archive sources M0 : 589 entrées, 1 902 285 o + empreinte ; auto-backup Git | FAIT VÉRIFIÉ |
| Retour arrière | copies jetables abandonnées + archive M0 hors dépôt ; restauration de l'archive : NON TESTÉ | NON TESTÉ |
| Intégrité | voir §7 | FAIT VÉRIFIÉ |
| DEMO | PID 5638 active, `/health` → `{"status":"ok"}` ; aucune intervention | FAIT VÉRIFIÉ |
| Migrations | 21 officielles (dernière 0021) ; `migrations_candidates` absent | FAIT VÉRIFIÉ |
| `core` | 750 096 384 o, inchangé | FAIT VÉRIFIÉ |

### Cause du blocage
- FAIT VÉRIFIÉ : la mémoire disponible est restée sous 500 000 kB sur plusieurs relevés espacés (≈ 489–499 Mo), alors que le plancher P84/PRQ-3 est de 500 Mo au lancement de chaque suite.
- HYPOTHÈSE : la baisse depuis 619 840 kB (≈ 120 Mo) est liée au cache/Shmem du tmpfs `/tmp` plein. Non confirmée. Non réductible sans nettoyage, interdit par la mission.
- Le plancher ne peut pas être abaissé sans décision du PO ; aucune dérogation implicite n'a été appliquée.
- Les relevés fluctuent de quelques Mo ; aucun n'a atteint 500 000 kB de façon stable.

---

## 4. Résultats par étape

| Étape | Statut |
|---|---|
| R0 préflight | Partiellement réalisé : sauvegarde, empreintes, seuils, TMPDIR OK ; go/no-go = **BLOQUÉ (PRQ-3)** |
| R0d ligne de base de non-régression | NON EXÉCUTÉ |
| R1 (adaptation schema_parite/p33/p34, noms du manifeste) | NON EXÉCUTÉ |
| R2 (candidat 0022 + chargeur) | NON EXÉCUTÉ |
| R3 (tests rouges) | NON EXÉCUTÉ |
| R4a + gel + empreintes | NON EXÉCUTÉ |
| P86-V1 | NON EXÉCUTÉ |
| R4b–R4e | NON EXÉCUTÉ |
| R7a | NON EXÉCUTÉ |
| R5, PC-1, R6 | NON EXÉCUTÉ |
| G6 | **NON PROPOSÉE, NON PROMUE** (aucune preuve d'intégrité, aucun contrôle visuel) |

## 5. Fichiers et composants modifiés
- Dépôt (`pharmos/`, `docs/` existants, `lot0a`) : **aucune modification**.
- Créé : ce rapport `docs/P88_RAPPORT_EXECUTION.md`.
- Créés hors dépôt dans `/home/user/work/p88/` (mode 700) : `tmp/`, `seuils.txt`, `backup_M0_sources.tar.gz` + `.sha`, `M0_docs_P81_P86.sha`, `M0_docs_P87.sha`, `M0_tree.txt`, `M0_migrations_pharmacy.agg`, `M0_compose.sha`.
- Fichier temporaire propre à la mission, créé puis supprimé par moi : `/home/user/work/p88_list_tmp.txt` (liste de 70 chemins de tests du §9.5). Aucun fichier préexistant touché ; à signaler par honnêteté.

## 6. Migration candidate, tests, FR/EN/AR/RTL/mobile, PERTE/API, P86-V1
- Migration 0022 candidate : NON CRÉÉE. Validation : NON EXÉCUTÉ.
- Tests : aucun lancé (suites de phase 1 non lancées : mémoire sous plancher). Un test non exécuté n'est pas un succès.
- Contrôles FR/EN/AR, RTL, mobile : NON EXÉCUTÉ.
- Vérification P86-V1 : NON EXÉCUTÉ.
- Vérification PERTE/API (RES-1) : NON EXÉCUTÉ. Cadrage enregistré en §1.
- C1–C9 : NON ÉVALUÉS (aucun statut PASS déclaré).

## 7. Empreintes avant / après (identiques)
- Docs P81–P86 : 19/19 OK ; docs P87 : 2/2 OK (contre `M0_docs_*.sha`).
- Agrégats M0 vs maintenant : `src` 14ec8cc6… ; `migrations` 7f65a800… ; `tests` 9d9bffc5… ; `tools` 865e10c9… ; `ui/public/on-premise` d431b61c… ; `ui/saas-src/src` c56a4a53… ; `package.json` de12f154… ; `tsconfig.json` 36759dd8… — tous identiques (8/8).
- `src/entry/compose.ts` : empreinte 5584701d… inchangée.
- Précision : une première vérification des docs lancée depuis le mauvais répertoire a signalé à tort des écarts ; refaite depuis `/home/user/webapp`, 21/21 OK. Aucun écart réel.

## 8. Sauvegardes et retour arrière
- Rien à annuler : aucune modification n'a été faite.
- Pour une reprise : archive M0 hors dépôt (non restaurée, NON TESTÉ) ; auto-backup Git ; `core` intact.

## 9. DEMO
- Intacte : PID 5638, `/health` ok. Aucune donnée, aucun service, aucun déploiement touché.

## 10. Anomalies, réserves, non exécutés
- BLOQUÉ : PRQ-3 (mémoire ≈ 497 Mo < 500 Mo).
- OBSERVATION : disque 97 %, `/tmp` tmpfs plein ; marge de ressources très faible.
- LIMITE : lecture incomplète de P81–P84 et des rapports.
- RES-1, RES-2 : enregistrées, non réalisées.
- O-5/O-6/O-9/O-10 : OUVERTS, non levés. V-3 : NON APPROUVÉ.
- Phase 2 (D1 local, navigateur) : SUSPENDUE ; sans elle, les contrôles visuels FR/EN/AR/RTL/mobile de G6 ne sont pas possibles dans l'état actuel (à reconfirmer à la reprise).

## 11. Éléments de reprise (décision du PO)
Aucune mission suivante créée. Pour reprendre, le PO décide entre :
1. libérer de la mémoire de façon autorisée (par exemple en traitant le tmpfs `/tmp` plein) puis relancer P88 ; MemAvailable ≥ 500 000 kB stable requis ;
2. renforcer/ajuster le plancher par décision explicite (jamais abaissé implicitement) ;
3. différer.
À la reprise : refaire le préflight (R0), vérifier les empreintes M0, puis R0d et la séquence R1 → R6 avec P86-V1 avant R4e et RES-1 intégrée à PER-04/PER-05/R7a-E6. G6 reste une décision distincte du PO.

---

# MISE À JOUR P90 — Reprise conditionnelle de P88 (Option A, GO du PO du 25/11/2026)

**Statut : ARRÊTÉ à l'étape R0d, 8 secondes après le lancement du premier fichier de test, sur franchissement du seuil mémoire. Aucune étape de P88 n'est validée. Migration 0022 : NON CRÉÉE. G6 : NON PROPOSÉE, NON PROMUE.**

## P90-1. Autorisation et périmètre
- DÉCIDÉ (PO, 25/11/2026) : reprise de P88 en Option A, phase 1 et contrôles légers uniquement. Phase 2 SUSPENDUE (contrôles visuels FR/EN/AR, RTL, mobile, scénarios à forte consommation mémoire).
- DÉCIDÉ : arrêt immédiat de l'opération en cours si `MemAvailable` < 500 000 kB ; seuil jamais abaissé ; aucune opération simultanée ; aucun nettoyage de `/tmp` ; archives P76/P77/P78 préservées ; aucun arrêt de processus ; aucune modification système ; aucune intervention sur DEMO ; aucune migration 0022 officielle ; aucune promotion G6 sans tous les contrôles et autorisations.

## P90-2. Conditions préalables (toutes satisfaites)

### Cinq relevés consécutifs de `MemAvailable` (script `releves.sh`, pas de 60 s, remise à zéro si < 500 000 kB)
| # | Heure (UTC) | MemAvailable (kB) | Statut | Compteur |
|---|---|---|---|---|
| 1 | 14:45:39 | 590 872 | OK | 1 |
| 2 | 14:46:39 | 587 496 | OK | 2 |
| 3 | 14:47:39 | 587 496 | OK | 3 |
| 4 | 14:48:39 | 587 244 | OK | 4 |
| 5 | 14:49:39 | 587 244 | OK | 5 |

FAIT VÉRIFIÉ : cinq relevés consécutifs ≥ 500 000 kB, aucune remise à zéro. Condition atteinte à 14:49:39.

### Autres conditions
| Contrôle | Résultat |
|---|---|
| DEMO | PID 5638, `/health` → `{"status":"ok"}` (14:45:47, 14:50:18, 14:53:47) ; `oom_score` 694 (14:45, 14:50), 695 (14:52:58), 694 (14:53:47) ; `oom_score_adj` 0 ; aucune intervention |
| Disque `/` | 1 129 080 Ko libres (14:45:47) ≥ 1 000 000 Ko (plancher de lancement) |
| Sauvegarde M0 | archive 589 entrées, 1 902 285 o ; empreinte actuelle `9a18c194…` identique à celle enregistrée (le fichier `.sha` ne contient que l'empreinte, sans nom de fichier : comparaison directe, `sha256sum -c` inapplicable) ; restauration : NON TESTÉ |
| Archives P76/P77/P78 | non touchées ; pack P78 `work/p78/p78_historique_v3.pack` : `0391879e…` conforme à P78 ; archive P77 `/tmp/p77_pkg/…v2.tar` : `70370586…` conforme à P77 ; P76 : taille vérifiée, empreinte non recalculée |
| `/tmp` | 2 035 fichiers, 1 039 507 493 o, inchangé ; tmpfs toujours à 100 % |

## P90-3. Étapes réellement exécutées

| Étape | Statut |
|---|---|
| Relevés préalables et contrôles P90-2 | EXÉCUTÉ, satisfaits |
| Établissement de la liste de non-régression (§9.5 P82) et de son classement | EXÉCUTÉ (voir limites) |
| R0d — ligne de base de non-régression | **INTERROMPU** : 1 fichier lancé sur 58, **0 fichier terminé, 0 résultat** |
| R1, R2, R3, R4a–R4e, P86-V1, R7a, R5, PC-1, R6 | **NON EXÉCUTÉ** |
| Adaptations RES-1 (PER-04, PER-05, R7a-E6) et RES-2 (P86-V1) | **NON EXÉCUTÉ** |
| G6 | **NON PROPOSÉE, NON PROMUE** |

### Liste de non-régression (fichiers de travail, hors dépôt)
- 67 fichiers `*.test.ts` retenus à partir de §9.5 (fichiers nommés + répertoires listés).
- Classement par recherche textuelle (`d1Env`, `makeD1Env`, `D1Provider`, `wrangler`, `miniflare`) : **58 fichiers hors D1** (phase 1) et **9 fichiers D1 différés** (phase 2 suspendue) : `lot1b/gouvernance_d1`, `lot1b/schema_parite`, `p19/suppliers_d1`, `p20/adoption_d1`, `p20/reference_d1`, `p20/suppliers_d1`, `p54/commandes_catalogue_p54_d1`, `p66/parcours_p66_d1`, `ui4/catalog_d1`.
- LIMITE : ce classement est heuristique, non vérifié fichier par fichier. La distinction « banc mémoire puis banc fichiers » de P84 n'a pas été faite : l'ordre suivi est alphabétique (`sort -u`), donc le premier fichier est `tests/demo/migration_0007.test.ts`.
- LIMITE : la liste de P88 (nettoyée depuis) comptait 70 chemins ; celle de P90 en compte 67. L'écart de 3 n'est pas analysé.
- LIMITE : `tests/lot1b/schema_parite.test.ts` est classé D1 par heuristique ; son statut réel (mémoire, fichier, D1) n'est pas établi.

## P90-4. Surveillance et arrêt

Paramètres du chien de garde (`r0d.sh`, échantillonnage toutes les 2 s, un seul test à la fois, `TMPDIR=/home/user/work/p88/tmp`) :
- arrêt si `MemAvailable` < 500 000 kB (seuil appliqué en cours d'exécution, comme demandé par P90 §3) ;
- arrêt si disque < 800 000 Ko ; arrêt si TMPDIR > 150 000 Ko ; arrêt si un fichier dépasse 600 s (valeur fixée par P90 comme substitut de la règle S-5, la durée de référence R0 n'existant pas encore : **hypothèse de ma part, non fixée par le plan**) ;
- avant chaque fichier : mémoire ≥ 500 000 kB, disque, `/health` DEMO, 0 tag et 0 stash Git.

| Heure (UTC) | Événement | MemAvailable (kB) | Disque (Ko) | TMPDIR (kB) |
|---|---|---|---|---|
| 14:51:36 | DÉBUT n=1 `tests/demo/migration_0007.test.ts` | 588 124 | 1 129 052 | 4 |
| **14:51:44** | **ARRÊT_OPÉRATION (raison MEM)** | **470 860** | 1 122 828 | 6 220 |
| 14:51:44 | ARRÊT_SÉQUENCE | — | — | — |
| 14:52:58 → 14:53:23 (6 relevés) | après arrêt | 601 136 → 600 920 | 1 122 828 | — |
| 14:53:48 → 14:53:54 | après arrêt | 602 036 | 1 122 828 | — |

- FAIT VÉRIFIÉ : le chien de garde a coupé l'opération (arrêt du groupe de processus), puis la séquence. Aucun processus de test ni watchdog résiduel (contrôle 14:53:47). DEMO (PID 5638, `/health` ok), PM2 God (613) non touchés.
- FAIT VÉRIFIÉ : la mémoire disponible est repassée à ≈ 602 Mo après l'arrêt. OBSERVATION (déduction, non isolée par une mesure contrôlée) : la baisse coïncide avec l'exécution du test ; une autre cause simultanée n'est pas exclue.
- OBSERVATION : la baisse mesurée entre le départ et l'arrêt est de 117 264 kB en 8 s, pour **un seul fichier de test hors D1**, avant que celui-ci n'ait produit un résultat. Le journal du test ne contient que « TAP version 13 ». Résultat du test : **NON EXÉCUTÉ jusqu'au bout, aucun verdict**.
- LIMITE : l'échantillonnage à 2 s ne donne que la valeur déclenchante (470 860 kB) ; le minimum réel entre deux relevés est **NON MESURÉ**. La part de la baisse due à la mémoire anonyme du test, au cache de pages sale et au slab n'est pas décomposée (HYPOTHÈSE : combinaison du processus de test, de ses lectures et des écritures SQLite, 6 220 kB dans TMPDIR).
- **Conséquence factuelle** : avec une marge de départ d'environ 588 Mo et un plancher de 500 Mo, la marge exploitable est d'environ 88 Mo. Le premier test tenté en a consommé davantage. Dans l'état actuel du système, **l'Option A ne permet pas d'exécuter ce test sans franchir le plancher**. Je n'ai pas testé d'autre fichier : il n'est pas établi que tous consomment autant.

## P90-5. Résidus et effets de bord (aucun nettoyage effectué)
- Résidu dans le TMPDIR dédié : `/home/user/work/p88/tmp/pharmos-s2-w950Bg` (6 220 kB), créé par le test interrompu ; il contient notamment un `codes.json` créé par le test (**non ouvert**). Non supprimé (aucun nettoyage autorisé).
- Fichiers de travail P90, hors dépôt (`/home/user/work/p90/`, mode 700) : `releves.sh`, `releves.log`, `liste_brute.txt`, `liste_classee.txt`, `liste_phase1.txt`, `liste_d1_differee.txt`, `r0d.sh`, `r0d_mesures.log`, `r0d_stdout.log`, `r0d_statut.txt`, `journaux_r0d/` (1 journal de 15 o).
- Disque : −6 224 Ko pendant le test ; reste 1 122 828 Ko : au-dessus du seuil d'arrêt (800 000 Ko) et au-dessus du plancher de lancement (1 000 000 Ko).
- Un contrôle `pgrep -f r0d.sh` à 14:52:58 a renvoyé deux PID : ils correspondaient à la commande d'observation elle-même (le motif figurait dans sa propre ligne de commande) ; la liste de processus de 14:53:47 ne montre plus aucun `r0d.sh`.

## P90-6. Intégrité après arrêt (identique à M0)
- Agrégats `src`, `tests`, `tools`, `migrations`, `ui/public/on-premise`, `ui/saas-src/src` : 6/6 identiques à M0 ; `package.json`, `tsconfig.json`, `src/entry/compose.ts` identiques.
- Docs P81–P86 : 19/19 OK ; docs P87 : 2/2 OK ; rapport P89 : 24 578 o inchangé.
- Migrations officielles : 21 ; `migrations_candidates` absent ; `core` : 750 096 384 o.
- Git : 0 tag, 0 stash, aucune opération manuelle ; HEAD `2107b37` (auto-backup).
- `/tmp` : 2 035 fichiers, 1 039 507 493 o, identique au relevé P89.
- Seul changement du dépôt : cet addendum au rapport P88.

## P90-7. Statut exact
- **Migration 0022** : NON CRÉÉE ; aucune migration officielle, aucun candidat.
- **G6** : NON PROPOSÉE, NON PROMUE.
- **C1–C9** : NON ÉVALUÉS. **CP, PR-0** : porte PR-0 NON FRANCHIE (ligne de base non relevée).
- **RES-1, RES-2** : enregistrées en P88, non réalisées.
- **Phase 2** : toujours SUSPENDUE.
- **DEMO** : intacte.

## P90-8. Éléments de reprise (décision du PO ; aucune mission suivante créée)
1. Il est établi que, pour le fichier testé, l'Option A telle que définie (plancher 500 Mo appliqué en cours d'exécution, marge ≈ 88 Mo) ne suffit pas. Reprendre exige une marge de mémoire supplémentaire : voir l'option B de P89 (libération par paliers, après vérification d'une copie durable et décision PO), qui reste NON AUTORISÉE.
2. Ne pas abaisser le plancher : la décision reste interdite sans le PO.
3. Avant toute reprise : décider du sort du résidu `pharmos-s2-w950Bg`, relancer cinq relevés, puis relever la ligne de base R0d d'abord sur un fichier unique, en mesurant son minimum de mémoire avec un échantillonnage plus fin.
