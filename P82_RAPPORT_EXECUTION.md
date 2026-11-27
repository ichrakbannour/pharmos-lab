# P82 — Rapport d'exécution : finalisation métier et préparation de réalisation du STOCK

Date : 2026-11-25. Nature : analyse, arbitrage technique préparatoire, documentation. **Aucune action technique n'a été exécutée** (§7). Données : fictives uniquement.

## 1. Livrables

| Réf. | Fichier | Contenu |
|---|---|---|
| 1 | `docs/P82_SPECIFICATIONS_FINALES_STOCK.md` | Fiche de décision PO (Q-1 à Q-12, H-1 à H-6), états, résolution de V-1 et de V-2, invariants INV-1 à INV-10, modèle et calcul par état, critères de test |
| 2 | `docs/P82_PLAN_REALISATION_STOCK_0022.md` | Dépendances et fichiers, conception du candidat 0022, adaptation des données, risques des migrations automatiques (RM-1 à RM-10), lots R0 à R7, points de contrôle, plan de tests, contrôles B14 à B22, retour arrière, huit conditions de DP-6, ressources |
| 3 | `docs/P82_RAPPORT_EXECUTION.md` | Ce rapport |

Les documents historiques P81 (fiche d'arbitrages, plan de migration, registre, rapports P81 et P81-DOC) n'ont **pas été modifiés** : leur SHA-256 a été relevé avant et revérifié après (§8). Les réponses du PO aux questions Q-1 à Q-12 seront inscrites dans un **addendum daté du registre**, rédigé après sa décision (rien n'est inscrit au registre par P82).

## 2. Décisions acquises (par renvoi au registre P81, sans les répéter)

DP1-R1 à DP6-R7 et C1 à C4 du `P81_REGISTRE_DECISIONS_PO_STOCK.md` restent la source unique. P82 n'en change aucune. Les spécifications les appliquent et signalent trois écarts de précision :
- **Erratum E-1** (registre) : conservé tel quel.
- **Vocabulaire** : `sales/plan.ts` nomme `sellable` la quantité disponible avant réservations et `available` la quantité vendable après réservations, à l'inverse du vocabulaire P81. **OBSERVATION**, sans effet sur le comportement ; renommage prévu comme tâche R-A1 en R4a.
- **DP4-R6** lu comme couvrant aussi EN_CONTROLE et le blocage/rappel de lot (H-5, à confirmer).

## 3. Recommandations (à valider par le PO)

Q-1 à Q-12 et H-1 à H-6 : voir fiche §1 des spécifications. Résumé des recommandations :

| Réf. | Recommandation |
|---|---|
| V-1 (Q-1 à Q-4) | Sorties manuelles depuis DISPONIBLE seulement ; motif de retrait obligatoire pour un lot bloqué, rappelé ou périmé ; réservation honorée par **ancienneté** ; signalement interne avec trace, sans envoi externe. Une réservation ne bloque jamais un passage en QUARANTAINE ou PERTE ; l'option « annulation automatique des réservations » est écartée. |
| V-2 (Q-7 à Q-11) | Corrections et contre-passations jamais bloquées par une réservation ; toute entrée, correction positive comprise, naît en DISPONIBLE ; correction négative restreinte hors DISPONIBLE, jamais en PERTE ; contre-passation limitée à la quantité en DISPONIBLE ; passage en PERTE non annulable (conséquence de DP1-R4). |
| Droits (Q-5, Q-6) | Levée de QUARANTAINE et levée de blocage de lot : droit de gouvernance, ADMIN uniquement (= pharmacien titulaire), authentification forte hors PIN. Quatre droits métier distincts : `stock.state.transfer`, `stock.lot.block`, `stock.adjust.nondisponible`, `stock.regularize`. |
| Forme (Q-12) | Motifs normalisés en table de référence plutôt qu'en `CHECK`. |

## 4. Arbitrages encore nécessaires

**Décisions nouvelles** : Q-3, Q-5, Q-6, Q-9, Q-10, Q-11, Q-12. **Confirmations** : Q-1, Q-2, Q-4, Q-7, Q-8.

**Arbitrages techniques** (plan P82, §4.4 et §12) :
- **A-T1** — Couplage code/schéma (V-4) : aucune version livrable construite depuis le dépôt entre R4 et G6 ; promotion (G6) et livraison du code en un seul ensemble ; test de couplage écrit en R3 (la composition de production avec le dossier officiel seul doit refuser de démarrer avec le nouveau code). Alternative « double chemin de code » écartée.
- **A-T2** — Mémoire (RR-1) : si la mémoire disponible est insuffisante au lancement des fichiers D1 (R2/R5), la mission s'arrête et le PO arbitre ; aucun arrêt de la DEMO ni réparation ne sont prévus. Seuil d'arrêt disque proposé : 800 Mo libres (HYPOTHÈSE à confirmer).

**Points ouverts sans effet sur le plan** : O-5, O-6, O-10. **O-9** (0021 non appliquée à la DEMO) conditionne seulement la décision future G7. **O-7** (comportement D1 réel de `ALTER TABLE … STRICT`) et **O-8** (volumétrie réelle) restent **NON VÉRIFIÉS** : traités par l'essai sur copie jetable en R2.

**Décisions de portes distinctes, hors P82** : autorisation de réaliser (G0), promotion de la 0022 dans le dossier officiel (G6), application à la DEMO (G7), interface (R7, optionnelle).

## 5. Risques et vigilances

| Réf. | Risque / vigilance | Traitement dans le plan |
|---|---|---|
| RM-1 | Un fichier posé dans `migrations/pharmacy/` est appliqué automatiquement | Candidat dans `pharmos/migrations_candidates/pharmacy/`, chargé par les tests seulement |
| RM-2, RM-3 | Historique « en avance » refusé ; deux tests simulent une ligne 0022 inconnue | Adaptation **à la promotion** |
| RM-4 | Somme de contrôle d'une migration promue figée | Candidat relu avant G6 ; erreur ultérieure = 0023 |
| RM-5, RM-6 | `ALTER … STRICT` en D1 réel ; coût des déclencheurs | O-7, O-8 : essai en R2 |
| RM-7 | 0022 derrière une 0021 absente en DEMO | Condition de G7 |
| RM-8 | Reconstruction de `stock_movements` | Évitée (colonnes et tables additives) |
| RM-9 | Retour arrière du code après usage des états | Interdit sans restauration de sauvegarde |
| RM-10 = V-4 | Couplage code et schéma | A-T1 |
| RR-1 | Mémoire disponible ≈ 650 Mo contre ≈ 1,45 Go observés pour le test D1 du stock | A-T2 |
| **V-3** | Convention CAT-11 : les contre-passations de réception sont des corrections nettes ; les retours clients sont déduits des sorties nettes ; l'identité de rapprochement s'écrit avec ces définitions (spécifications §8.2) | Test B-rapprochement ; à confirmer en R1 sur la photographie 0021 |
| **V-4** | Couplage code/schéma avant promotion | = RM-10 / A-T1 |

Ordre de réalisation proposé : R0 → R1 → R2 → R3 → R4a → R4b → R4c → R4d → R4e → R5 → R6, puis R7 (distinct) ; G6 et G7 sont des décisions séparées.

## 6. Contrôles effectivement réalisés

Voir les résultats de la vérification finale en §8. Revues faites en **lecture seule** : `src/stock/service.ts`, `src/purchasing/service.ts`, `src/sales/plan.ts`, `src/sales/service.ts`, `src/catalog/service.ts`, `src/governance/catalog.ts`, `src/governance/authorize.ts`, `src/platform/tenants.ts`, `src/platform/migrations.ts`, `src/kernel/sqlText.ts`, `src/entry/compose.ts`, `src/entry/stockRoutes.ts`, `src/adapters/file/migrationFiles.ts`, migrations 0005, 0006, 0008, 0016, 0019, et les fichiers de tests cités dans le plan §3.3. Aucune recherche n'a ouvert de fichier de secrets ou de codes.

## 7. Confirmation : aucune action technique

Aucun changement de code source, aucune migration (ni 0022 dans le dossier officiel, ni dossier `migrations_candidates`), aucune modification de base, aucun test lancé, aucune action sur la DEMO ou systemd, aucun déploiement, aucun commit, push, pull, fetch, merge, rebase, reset, cherry-pick, tag, changement de branche ni stash manuels, aucun nettoyage ni réparation du serveur, aucun lien de fichier créé, aucun secret lu ni affiché. Le dump `pharmos/core`, les sauvegardes, rapports, preuves et archives P76/P77/P78 sont intacts.

## 8. Vérification finale

Relevé du 2026-11-25, en fin de mission (FAIT VÉRIFIÉ) :

| Contrôle | Résultat |
|---|---|
| SHA-256 des 5 documents P81 (relevé avant, revérifié après) | 5 sur 5 identiques |
| Défaut de forme (`git diff --no-index --check`) sur les 3 documents P82 | aucun (0 espace en fin de ligne) |
| `git status --short` | uniquement les 3 documents P82 non suivis |
| Dépôt | branche `main`, HEAD « genspark auto-backup » (e9e377c) ; 0 tag ; 0 stash ; aucun commit manuel |
| `git diff` et `git status` sur `pharmos` et `lot0a` | vides |
| Dernière migration officielle | `0021_commandes_catalogue_national.sql` ; dossier `migrations_candidates` inexistant |
| DEMO | processus 5638 actif, `/health` = `{"status":"ok"}` |
| Dump `pharmos/core` | 750 096 384 o (inchangé) |
| Disque | 1 164 732 Ko libres (97 %) |
| Mémoire | ≈ 624 Mo disponibles (confirme RR-1 / A-T2) |
| Recherche de liens, adresses, jetons, mots de passe dans les 3 documents | aucun lien ni adresse ; occurrences du mot « secret » ou « token » limitées à des mentions de règle (« ne pas lire ») |

## 9. Limites

- Les numéros de ligne cités dans les documents correspondent au relevé du 2026-11-25 ; ils sont à relire en R1.
- Les 20 lectures de `stock_movements` à classer, le comportement D1 réel (O-7) et la volumétrie (O-8) ne sont pas vérifiés ici.
- Les recommandations ne sont pas des décisions tant que le PO n'a pas répondu.

## 10. Critères de clôture de P82

Dossier de trois documents présent et sans défaut de forme ; documents P81 inchangés (SHA-256) ; zéro modification de `pharmos` et `lot0a` ; zéro tag, zéro stash ; DEMO inchangée ; aucune réalisation lancée. **P83 et la réalisation ne démarrent que sur autorisation explicite du PO**, après ses réponses à Q-1 à Q-12 et A-T1/A-T2.
