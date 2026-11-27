# P85 — Rapport d'exécution : enregistrement des arbitrages PO A-T1, A-T2 et A-T3

**Date :** 25/11/2026 · **Nature :** mission strictement documentaire · **Statut :** TERMINÉE, mission arrêtée.
Données fictives uniquement. Aucun secret, code, jeton ni lien créé.

## 1. Livrables

| Livrable | Contenu |
|---|---|
| `docs/P85_ADDENDUM_ARBITRAGES_PO_AT1_AT2_AT3.md` (415 lignes) | §1 vue d'ensemble ; §2 A-T1 ; §3 A-T2 ; §4 A-T3 (R7a) ; §5 statut et limites maintenus ; §6 cohérence et incidences ; §7 points À CONFIRMER / À ARBITRER ; §8 autorisations futures, non-autorisations, conditions de lancement |
| `docs/P85_RAPPORT_EXECUTION.md` | le présent rapport |

## 2. Documents consultés (lecture seule)

- P84 : `P84_ETUDE_FAISABILITE_AT1_AT2.md`, `P84_MATRICE_TESTS_STOCK_0022.md`, `P84_DOSSIER_FINAL_REALISATION_STOCK_0022.md`, `P84_RAPPORT_EXECUTION.md`.
- P83 : `P83_ADDENDUM_DECISIONS_PO_STOCK.md` (décisions Q, précautions C-P82-1 à 3, points O-n, vigilances V-n).
- P82 (3 documents) et P81 (5 documents, dont P81-DOC) : consultés pour la cohérence.

## 3. Arbitrages enregistrés (exactitude vérifiée contre la demande du PO)

| Arbitrage | Décision enregistrée | Écart avec la demande |
|---|---|---|
| **A-T1** | APPROUVÉ : manifeste avec empreintes, gel R4a→G6, chargeur idempotent, garde de démarrage ; autorisation de modifier `compose.ts` **dans une future mission de réalisation** ; 5 conditions (refus de schéma incompatible, démarrage compatible, idempotence, retour arrière documenté et vérifiable, aucune promotion partielle) | aucun |
| **A-T2** | Phase 1 autorisée sous conditions (légère, séquencée, `TMPDIR` dédié seulement après vérification d'espace, permissions, persistance, saturation ; seuils documentés avant usage ; arrêt si insuffisance). Phase 2 **suspendue** (D1 local, navigateur) ; comparaison mémoire / autre hôte / report ; seuils P84 = non décisions ; aucune suppression, purge, réparation, modification de configuration | aucun |
| **A-T3** | Option A : lot **R7a borné**, avant G6, exclusivement (1) message de refus de contre-passation (C-P82-2), (2) confirmation avant PERTE (C-P82-3) ; états, critères, contrôles FR/EN/AR, RTL, mobile, scénarios ; pas de refonte de l'interface STOCK | aucun |

## 4. Contrôles de cohérence

- **Avec P81–P84** : aucune décision antérieure rouverte ; Q-1…Q-12, H-1…H-6, C-P82-1…3 inchangées ; O-5, O-6, O-9, O-10 maintenus **OUVERTS** ; O-7, O-8 NON VÉRIFIÉS ; V-3 NON APPROUVÉ ; V-4, V-5, V-6 maintenues (addendum §5, §6).
- **Décisions vs propositions** : tableau de tri en §5.5 ; critère C1–C8, seuils, noms/formats, MSG-4, V1/V3 restent des **propositions** ou **À CONFIRMER**.
- **Tensions signalées sans être tranchées** (addendum §4.8 et §7) : R7a-L1 (R7a avant G6 avec contrôles RTL/mobile alors que la phase 2 est suspendue) ; R7a-L2 (point d'entrée du passage en PERTE) ; sort de C7 et de la partie D1 local de MIG-T2.
- **Références croisées** : contrôlées par recherche textuelle : A1-M1…M4, A1-K1…K5, A2-K1…K4, R7a-E1…E9, R7a-AC1…AC8, R7a-S01…S15, R7a-C01…C08, R7a-L1/L2, A1-L1…L5, A2-L1…L5, A3-L1…L5 : toutes définies ; renvois « §7 » et « §8 » résolus.

## 5. Vérifications finales (relevés de fin de mission)

| Contrôle | Résultat |
|---|---|
| Empreintes SHA-256 des 14 documents P81–P84 (`sha_avant.txt`) | **14/14 identiques**, 0 écart |
| Agrégats de code (src, migrations, tests, tools, ui/public/on-premise, ui/saas-src/src, `package.json`, `tsconfig.json`) | **8/8 identiques** à la ligne de base P85 (donc à P84) |
| `compose.ts` et tout fichier source | non modifiés (agrégat `src` identique) |
| Dernière migration officielle / `migrations_candidates` | 0021 (dossiers `control`, `pharmacy`) / absent : **0022 non créée, non exécutée** |
| Forme de l'addendum (espaces de fin de ligne, tabulations) | 0 occurrence |
| Scan de l'addendum (URL, variables de jeton, mots de passe, PIN, TOTP) | 0 occurrence |
| Git | branche `main`, HEAD `ae24dda` « genspark auto-backup » (inchangé durant la mission) ; 0 tag ; 0 stash ; seul fichier non suivi au contrôle : l'addendum P85 |
| DEMO | PID 5638 actif ; `/health` : `{"status":"ok"}` |
| `pharmos/core` | 750 096 384 o (inchangé) |
| Ressources | disque `/` 1 147 900 Ko libres (97 %) ; mémoire disponible ≈ 630 Mo (relevé de départ : 1 147 944 Ko et ≈ 630 612 kB) |

## 6. Confirmation : aucune action technique exécutée

Aucun fichier source, test, benchmark, build, migration, base de données, `TMPDIR`, service, DEMO, systemd, déploiement, redémarrage, suppression, purge, réparation, nettoyage ni opération Git manuelle. Seuls ont été écrits : les deux livrables dans `docs/` et deux fichiers de relevé hors dépôt dans `/home/user/work/p85/` (`sha_avant.txt`, `tree_avant.txt`, `tree_apres.txt`). Sauvegardes, rapports, preuves, fichiers de codes, archives P76/P77/P78 et dump `pharmos/core` non touchés. Aucun lien créé ; les liens hérités ne sont pas reconduits.

## 7. Limites et suite

- Les 167 scénarios P84, les scénarios A1-T* et R7a-S*, les contrôles navigateur/FR/EN/AR/RTL/mobile restent **spécifiés, non exécutés / NON VÉRIFIÉS** ; limites matérielles = **VIGILANCE**.
- **Aucune réalisation STOCK, migration 0022 ou mission P86 n'est lancée.** Toute suite exige une **nouvelle autorisation explicite du PO** (addendum §8.3). Mission arrêtée.
