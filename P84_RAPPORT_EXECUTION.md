# P84 — Rapport d'exécution : préparation technique consolidée de la réalisation STOCK / migration 0022

**Nature** : étude, faisabilité, sécurisation. **Documentation uniquement.** Aucun code, aucune migration, aucune base, aucun test, aucun benchmark, aucune intervention sur la DEMO ni sur les services.
**Données** : fictives. **Décisions P81/P82/P83** : référence métier, ni rouvertes ni modifiées.

---

## 1. Livrables

| # | Fichier | Lignes | Contenu |
|---|---|---|---|
| 1 | `docs/P84_ETUDE_FAISABILITE_AT1_AT2.md` | 223 | A-T1 (livraison intermédiaire R4→G6) et A-T2 (capacité et ressources) |
| 2 | `docs/P84_MATRICE_TESTS_STOCK_0022.md` | 426 | 167 scénarios : NEG 57, REV 33, PER 35, V6 19, CAT 14, MIG 9 |
| 3 | `docs/P84_DOSSIER_FINAL_REALISATION_STOCK_0022.md` | 564 | dossier technique complet (18 sections) |
| 4 | `docs/P84_RAPPORT_EXECUTION.md` | ce document | synthèse |

---

## 2. Synthèse des chantiers

### 2.1 Chantier A

| Point | Résultat | Statut |
|---|---|---|
| **A-T1** | Risque de livraison partielle analysé (8 risques, 10 contrôles). Recommandation : définition de livraison par **manifeste d'empreintes** (M0/M1/M2), **gel de livraison** de R4a à G6, **chargeur de candidat idempotent** (promotion = déplacement d'un fichier), trois tests de schéma adaptés avant G6, **garde de démarrage « niveau de schéma requis »**. Critère objectif **C1 à C8**. | ANALYSÉ, RECOMMANDÉ ; **arbitrage demandé** |
| **A-T2** | Diagnostic de capacité sans benchmark. **Fait nouveau** : `/tmp` est un tmpfs de 1 Go **plein à 100 %** ; mémoire disponible ≈ 641 Mo contre ≈ 1,45 Go observé pour un test D1 (P80). Recommandation : **deux phases** (phase 1 légère : mémoire puis fichiers, `TMPDIR` dédié sur disque ; phase 2 lourde : D1 local et navigateur, conditionnée à un arbitrage), grille de seuils, 7 conditions d'arrêt. | ANALYSÉ, RECOMMANDÉ ; **non résolu côté matériel** ; arbitrage demandé |
| **A-T3** (nouveau) | Les précautions C-P82-2 et C-P82-3 ne sont respectées côté utilisateur que si une interface minimale (messages de refus de reprise, confirmation PERTE) est livrée avant G6, alors que le plan P82 classe l'interface hors R0-R6. Options A (R7a borné, **recommandée**), B, C. | À ARBITRER |

### 2.2 Chantier B — matrice de tests

Tous les points demandés sont spécifiés, avec préconditions, action, résultat attendu, stock avant/après, trace d'audit et critère PASS/FAIL :
Q-9 et V-5 (NEG-01…82, dont NEG-60…68 pour la distinction correction / sortie de QUARANTAINE) ; Q-10 et C-P82-2 (REV-01…43) ; Q-11 et C-P82-3 (PER-01…47) ; V-6 (V6-01…19, avec séparation « déjà vérifié » / « à tester » : P60, P65, P69) ; V-3 (CAT-01…14, dont CAT-12 qui démontre que la lecture alternative échoue) ; migration (MIG-T1…T9). Des tests de **sensibilité** (rouges avant correctif) sont désignés pour chaque famille.

### 2.3 Chantier C — dossier final

États et transitions, réservations et sorties, corrections, contre-passations, lots, droits (**[PO]** distingués de **[TECH]**), invariants INV-1…10, modèle de données cible, stratégie de migration et conservation de l'historique, compatibilité et non-régression, retour arrière, critères d'acceptation AC-1…10, **15 conditions d'arrêt**, interface et messages, V-3, DP-6 et C-P82-1…3, risques RT-1…12.

---

## 3. Ce qui est prêt / ce qui est bloqué

### 3.1 PRÊT

- Spécifications métier (P82), décisions (P83), plan (P82), **dossier technique, matrice de tests, étude de livraison et de capacité (P84)**.
- Séquencement R0…R7a/G6/G7, critères d'acceptation, conditions d'arrêt, retour arrière.

### 3.2 BLOQUÉ ou NON AUTORISÉ

| # | Blocage | Nature |
|---|---|---|
| B-1 | **Aucune autorisation explicite du PO** de lancer la réalisation | NON AUTORISÉ |
| B-2 | **Arbitrages A-T1, A-T2, A-T3** non rendus | BLOQUÉ |
| B-3 | **Ressources** : mémoire disponible ≈ 641 Mo ; `/tmp` plein ; la phase 2 (D1, navigateur) n'est pas faisable en l'état | BLOQUÉ (phase 2) ; phase 1 conditionnelle |
| B-4 | O-9 (0021 non appliquée à la DEMO) : condition de G7, pas de R0-R6 | OUVERT |
| B-5 | O-5, O-6, O-10 | OUVERT |

---

## 4. Risques

### 4.1 Techniques

RT-1 livraison partielle ; RT-4 `ALTER ADD COLUMN` sur table `STRICT` en D1 réel (O-7, **NON VÉRIFIÉ**) ; RT-5 volumétrie inconnue (O-8) ; RT-6 droit restreint contrôlé en code seulement ; RT-7 ordre des déclencheurs non garanti ; RT-8 complétude d'une paire de passage non garantie par SQLite (parée par unité de travail unique et contrôle B14) ; RT-9 précautions C-P82-2/3 sans interface ; RT-10 retour de code après usage des états (interdit sans restauration) ; RT-11 FEFO dupliqué pendant la transition ; RT-12 messages et RTL.

### 4.2 Opérationnels

| Risque | Détail |
|---|---|
| RT-2 Saturation mémoire ou disque | précédent **P70** : redémarrage du bac à sable et perte de la DEMO ; disque à 97 % |
| RT-3 `/tmp` plein | les bancs fichier et D1 local créent leurs bases sous `/tmp` : échec probable de création (**HYPOTHÈSE, NON TESTÉ**) |
| Non-vérification | écrans V3 stock/commandes non audités en RTL ; lecture arabophone jamais réalisée |

---

## 5. Ressources nécessaires (constat du 25/11/2026)

| Ressource | Constat | Besoin estimé | Écart |
|---|---|---|---|
| Disque `/` | 1 148 048 Ko libres (97 %) | ≥ 1 000 000 Ko au lancement ; arrêt < 800 000 Ko | marge faible |
| `/tmp` | tmpfs 1 019 720 Ko, **100 %** | base temporaire par suite | **insuffisant** sans redirection |
| Mémoire disponible | ≈ 641 Mo | phase 1 : ≥ 500 Mo par suite ; phase 2 : ≥ 1 800 Mo | **phase 2 hors d'atteinte** |
| Répertoire `TMPDIR` dédié | inexistant | ≤ 300 000 Ko sur disque, hors dépôt | décision PO |

Aucune de ces valeurs n'est mesurée par un essai : ce sont des relevés (disque, mémoire) et des **estimations** (besoins). Les seuils sont des **propositions non approuvées**.

---

## 6. Décisions techniques recommandées (propositions, aucune appliquée)

1. **A-T1** : adopter le manifeste M0/M1/M2, le gel R4a→G6, le chargeur idempotent, l'adaptation préalable des 3 tests de schéma, la garde de démarrage (modification de `compose.ts`, **arbitrage point 3**).
2. **A-T2** : phase 1 seule avec `TMPDIR` dédié sur disque ; phase 2 uniquement après apport de mémoire, exécution sur un autre hôte, ou acceptation écrite que C7 (D1) et V6-10…19 restent NON VÉRIFIÉS.
3. **A-T3** : option A (lot R7a borné aux messages MSG-1…6, V1 et V3) avant G6.
4. **R1** : figer noms de droits, actions d'audit, codes d'erreur, format de `purchase.reversal_state` (dossier §9.4, M-1…M-6).
5. **V-3** : ne rien présumer ; vérifier la convention par CAT-01…14 ; arrêt si incohérence avec DP1-R9.

---

## 7. Conditions précises de lancement de la réalisation

La réalisation (R0) ne peut démarrer que si **toutes** les conditions suivantes sont réunies :

| # | Condition | Responsable |
|---|---|---|
| L-1 | **Autorisation explicite du PO** de lancer la réalisation (mission distincte de P84) | PO |
| L-2 | **Arbitrage A-T1** rendu (4 points) | PO |
| L-3 | **Arbitrage A-T2** rendu : grille de seuils, autorisation et création du répertoire `TMPDIR` dédié, politique de phase 2 | PO |
| L-4 | **Arbitrage A-T3** rendu (A, B ou C) | PO |
| L-5 | Relevé de départ conforme : disque libre ≥ 1 000 000 Ko, mémoire ≥ 500 Mo, DEMO saine, 0 tag, 0 stash, dernière migration 0021, `migrations_candidates` absent | exécutant, en R0 |
| L-6 | Manifeste M0 établi (empreintes de `src`, `migrations`, `tests`, `ui`, `tools`, documents P81 à P84) | exécutant, en R0 |
| L-7 | Addendum de réponse du PO aux arbitrages (document daté) | PO |

**Sans réponse** aux arbitrages : la réalisation applique les points 1 et 2 d'A-T1 et le gel, s'arrête avant `compose.ts`, reste en phase 1 sans D1 ni navigateur, et s'arrête à R6 sans G6 si A-T3 n'est pas arbitré (dossier §14.6).

---

## 8. Confirmation d'absence d'action technique

| Contrôle | Résultat |
|---|---|
| Documents P81, P82, P83 (10 fichiers) | `sha256sum -c` : **10/10 OK** |
| `src`, `migrations`, `tests`, `tools`, `ui/public/on-premise`, `ui/saas-src/src`, `package.json`, `tsconfig.json` | **8/8 empreintes identiques** au relevé de début (méthode retrouvée et confirmée) |
| Dernière migration officielle | **0021** ; `migrations_candidates` **absent** ; aucun fichier 0022 |
| Git | 0 tag, 0 stash ; HEAD `e50ff78` « genspark auto-backup » ; aucun commit, push, pull, fetch, merge, rebase, reset, cherry-pick manuel ; non suivis : les seuls documents P84 |
| DEMO | PID 5638 actif ; `/health` : `{"status":"ok"}` |
| Dump `pharmos/core` | 750 096 384 o (inchangé) |
| Disque / mémoire (fin) | 1 148 048 Ko libres (97 %) ; mémoire disponible ≈ 641 Mo ; `/tmp` inchangé |
| Forme des livrables | 0 espace en fin de ligne, 0 tabulation, 0 marqueur de conflit |
| Scan | 0 lien, 0 secret, 0 code, 0 mot de passe dans les livrables P84 |
| Fichiers de codes, `.pharmos-private/*.json`, `/home/user/work/p70`, archives P76/P77/P78 | non ouverts, non modifiés |
| Tests, builds, benchmarks, bases | **aucun** lancé |

**NON TESTÉ / NON MESURÉ en P84** : comportement des bancs avec `TMPDIR` redirigé ; consommation mémoire réelle des suites ; durée de R5 ; D1 réel (O-7) ; volumétrie (O-8) ; écrans nouveaux ; RTL V3 stock/commandes ; lecture arabophone.

---

## 9. Statut final

P84 est **terminée** : A-T1 et A-T2 sont analysés et recommandés, A-T3 est identifié, les tests Q-9, V-5, Q-10, C-P82-2, Q-11, C-P82-3 et V-6 sont spécifiés, le dossier 0022 est complet et cohérent avec P81/P82/P83, les risques, dépendances et conditions d'arrêt sont explicites. **Aucun changement technique.**

**Arrêt de la mission.** Ni P85 ni la réalisation STOCK ne sont lancées sans nouvelle autorisation explicite du PO.
