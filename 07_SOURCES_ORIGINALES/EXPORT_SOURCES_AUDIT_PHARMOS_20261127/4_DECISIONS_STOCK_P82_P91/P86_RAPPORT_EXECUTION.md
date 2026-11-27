# PHARM'OS — P86
## Rapport d'exécution — Arbitrage consolidé des 13 points et plan unique de réalisation STOCK

**Date de référence :** 25/11/2026.
**Nature de la mission :** strictement documentaire. **Aucune modification technique.**

---

## 1. Travaux documentaires réalisés

| # | Travail | Résultat |
|---|---|---|
| 1 | Relevé de base (empreintes P81 à P85, agrégats du code, Git, ressources, DEMO, migrations) | fait avant tout travail ; hors dépôt (`/home/user/work/p86/`) |
| 2 | Lecture des sources obligatoires | voir §2 |
| 3 | Inspections légères en lecture seule du code et des tests | voir §3 |
| 4 | Analyse **consolidée** des 13 points du §7 de l'addendum P85 (dépendances croisées traitées dans une seule analyse) | `docs/P86_ARBITRAGE_CONSOLIDE_13_POINTS.md` (381 lignes) |
| 5 | Chantier B : cohérence avec DP-1…DP-6, C1–C4, Q-1…Q-12, H-1…H-6, C-P82-1…3, A-T1/A-T2/A-T3 | arbitrage §9 (tableau de 17 lignes) |
| 6 | Chantier C : six rubriques pour chacun des 13 points ; tableau unique de décisions du PO | arbitrage §3 à §6 et §10 |
| 7 | Chantier D : plan unique de réalisation (séquencement, dépendances, compatibilité, tests, portes, prérequis, retour arrière, arrêts, acceptation, lancement, clôture) | `docs/P86_PLAN_UNIQUE_REALISATION_STOCK.md` (445 lignes) |
| 8 | Rapport | le présent document |

**Livrables (uniquement ces trois) :** `docs/P86_ARBITRAGE_CONSOLIDE_13_POINTS.md`, `docs/P86_PLAN_UNIQUE_REALISATION_STOCK.md`, `docs/P86_RAPPORT_EXECUTION.md`.

---

## 2. Sources consultées

| Source | Lecture |
|---|---|
| `P81_REGISTRE_DECISIONS_PO_STOCK.md` | intégrale (DP1-R1…DP6-R7, C1–C4, O-5/6/9/10/11, V-1/V-2, E-1) |
| `P81_PLAN_MIGRATION_0022.md` | sections lues : §8 (retour arrière, arrêts), §9 (points ouverts), §10 (portes) ; titres du reste (320 lignes) — **lecture intégrale non réalisée pour §3 à §7 en P86** : le contenu repris (inventaire des 20 lectures, contrôles A/B) l'est **par référence** aux documents P82 à P85 |
| `P82_SPECIFICATIONS_FINALES_STOCK.md`, `P82_PLAN_REALISATION_STOCK_0022.md` | intégrale |
| `P83_ADDENDUM_DECISIONS_PO_STOCK.md` | intégrale |
| `P84_ETUDE_FAISABILITE_AT1_AT2.md`, `P84_DOSSIER_FINAL_REALISATION_STOCK_0022.md` | intégrale |
| `P84_MATRICE_TESTS_STOCK_0022.md` | sections §1, §5 à §8 ; §2 à §4 (NEG, REV, PER) **par recherche ciblée seulement** : les identifiants cités (NEG-1…57, REV-1…33, PER-1…35) viennent des totaux de la matrice, les scénarios individuels cités (REV-03, 04, 12, 14, 30…36 ; PER-04, 05, 20…27, 40…47) viennent de P85 et du dossier P84 |
| `P85_ADDENDUM_ARBITRAGES_PO_AT1_AT2_AT3.md` | intégrale (cité par sections) |
| `P84_RAPPORT_EXECUTION.md`, `P83_RAPPORT_EXECUTION.md`, `P82_RAPPORT_EXECUTION.md`, `P85_RAPPORT_EXECUTION.md` | **non relus en P86** (aucun contenu repris d'eux) |
| `PLAN_DIRECTEUR_FINALISATION_PHARMOS_v1.3.2.md` | **une seule ligne** (l. 497, critères des Lots 2 et 3) et le contexte immédiat ; **non lu pour le reste** |

**Conformité à la consigne « consulter intégralement » :** **partielle** pour trois documents (plan de migration P81, matrice P84, rapports P82–P85). Les points concernés sont signalés dans les limites (§6). Ils n'invalident pas les recommandations, qui ne s'appuient sur aucun contenu non lu, mais **un relecteur doit en tenir compte**.

---

## 3. Inspections légères (lecture seule)

Commandes de type `grep`, `sed`, `ls`, `readlink`, `ps`, `curl` sur `/health`. Constats consignés au §2 de l'arbitrage (T-1 à T-15). Aucun fichier de base, de codes ou de la DEMO n'a été ouvert. La lecture du listing de noms de fichiers de la copie de version de la DEMO (T-9) est une **lecture de noms** (CL-2).

---

## 4. Contrôles de cohérence réalisés

| # | Contrôle | Résultat |
|---|---|---|
| 1 | Empreintes P81 à P85 (16 fichiers) avant/après | **16/16 OK** |
| 2 | Agrégats `src`, `migrations`, `tests`, `tools`, `ui/public/on-premise`, `ui/saas-src/src`, `package.json`, `tsconfig.json` avant/après | **8/8 identiques** |
| 3 | `git status` | seuls les documents P86 non suivis |
| 4 | Tags, stashes | 0 tag, 0 stash |
| 5 | HEAD | `35c7a70` (sauvegarde automatique), inchangé |
| 6 | Migrations officielles `pharmacy` | 21 fichiers, dernier `0021_commandes_catalogue_national.sql` |
| 7 | `migrations_candidates` | **absent** |
| 8 | Dump `pharmos/core` | **750 096 384 o**, inchangé |
| 9 | DEMO | PID 5638 actif, `/health` `{"status":"ok"}` |
| 10 | Forme des deux livrables principaux | 0 espace final, 0 tabulation |
| 11 | Scan de motifs sensibles (liste de motifs fixée hors document) dans les 3 livrables P86 | **0 occurrence** (rapport inclus) |
| 12 | Exhaustivité des 13 points | voir §5 |
| 13 | Cohérence des références croisées | voir §5 |

---

## 5. Exhaustivité et cohérence documentaire

**Les 13 points** du §7 de P85 sont tous traités, dans l'ordre de P85, sous les identifiants P86-1 à P86-13 :

| P86-n | Réf. P85 | Traité à |
|---|---|---|
| 1 | A1-L1 | arbitrage §3 |
| 2 | A1-L2 | arbitrage §4 |
| 3 | A1-L3 | arbitrage §3 |
| 4 | A2-L2 | arbitrage §4 |
| 5 | A2-L1 | arbitrage §4 |
| 6 | §3.6 | arbitrage §4 |
| 7 | R7a-L1 | arbitrage §3 |
| 8 | R7a-L2 | arbitrage §3 |
| 9 | A3-L2 | arbitrage §5 |
| 10 | A3-L3 | arbitrage §5 |
| 11 | §6.3 | arbitrage §3 |
| 12 | O-5, O-6, O-9, O-10 | arbitrage §6 (tableau de quatre lignes) |
| 13 | V-3 | arbitrage §6 |

Aucun point n'est omis, aucun n'est inventé. **Une vigilance supplémentaire, P86-V1** (suivi par lot obligatoire du MÉDICAMENT, DP2-R1, écrans non adaptés), est signalée **hors des 13 points**, explicitement non décisionnelle et non bloquante pour la réponse sur 1 à 13.

**Cohérence :** aucune contradiction avec les décisions antérieures (arbitrage §9). Dix tensions entre textes de propositions ou interprétations sont exposées (X-1 à X-10, arbitrage §8.2) ; aucune ne rouvre une décision.

**Ce qui a été distingué :** décision PO existante, PROPOSITION de P84, recommandation de P86. Aucune recommandation n'est présentée comme une décision.

---

## 6. Limites, NON VÉRIFIÉ, NON MESURÉ

| # | Limite | Étiquette |
|---|---|---|
| L-1 | Lecture **partielle** de trois ensembles de documents (§2) | LIMITE |
| L-2 | Aucun test, build ni benchmark : consommation des suites, durée de R5, comportement avec `TMPDIR` redirigé, construction de la V3 | NON TESTÉ / NON MESURÉ |
| L-3 | Absence d'outil de simulation DOM : observation sur une **liste de modules non exhaustive** ; la faisabilité de R7a-S10…S15 reste à confirmer en R1 | OBSERVATION / À CONFIRMER |
| L-4 | Statut réel du Lot 2 et de CAT-14 à 16 (plan directeur) | NON VÉRIFIÉ |
| L-5 | État de la **base** DEMO (O-9) : non lue | NON VÉRIFIÉ |
| L-6 | Compatibilité D1 **réelle** de la migration (O-7) et volumétrie (O-8) | NON VÉRIFIÉ |
| L-7 | Baisse de 8 308 Ko d'espace libre entre P85 et P86 : cause non analysée ; mémoire disponible 632 832 kB en fin de mission contre 644 236 kB au relevé précédent (variations normales de l'hôte, **non analysées**) | HYPOTHÈSE |
| L-8 | Les nombres de lignes de fichiers (ex. `compose.ts` 25 lignes, `schema_parite` l. 21, `p33` l. 95, `p34` l. 48-52) sont ceux du 25/11/2026 ; ils peuvent changer | LIMITE |
| L-9 | Le comptage « 17 fichiers de test utilisent le banc D1 » vient du relevé de lecture légère effectué avant la reprise de cette mission ; il n'a **pas été recompté** en fin de mission | À CONFIRMER en R1 |
| L-10 | Les scénarios NEG, REV, PER, CAT, V6, MIG, A1-T*, R7a-S*, R7a-C* restent **spécifiés et non exécutés** | NON EXÉCUTÉ |
| L-11 | Aucune relecture linguistique arabe | NON VÉRIFIÉ |
| L-12 | Une interruption de la session (limite de crédit) est survenue pendant la rédaction du premier bloc ; il a été **réécrit intégralement**. Aucun fichier partiel n'a été laissé et la forme a été revérifiée | OBSERVATION |

---

## 7. Confirmation d'absence de modification technique

- **Aucun fichier de code, de test, de migration, d'outil ou d'interface modifié** : agrégats 8/8 identiques à la base de P86 (identiques à ceux de P85).
- **`compose.ts` non modifié** ; **aucune migration 0022 créée ni exécutée** ; **aucun répertoire `migrations_candidates`** ; **`TMPDIR` non créé**.
- **Aucun test, aucun build, aucun benchmark** exécuté.
- **Aucune base modifiée ni ouverte** ; DEMO, systemd, services : aucune intervention ; aucun redémarrage ; aucun déploiement.
- **Aucune opération Git manuelle** : pas de commit, push, pull, fetch, merge, rebase, reset, cherry-pick, tag, changement de branche, stash. Les documents P86 apparaissent comme non suivis ; la sauvegarde automatique Genspark reste seule en charge.
- **Aucun nettoyage, suppression, déplacement ni réparation** ; sauvegardes, rapports, preuves, fichiers de codes, dump `pharmos/core` et archives P76/P77/P78 **intouchés**.
- **Aucun secret** lu, écrit ni affiché ; aucun lien créé ; aucune URL signée.
- **P81 à P85 non modifiés** (16/16).
- Fichiers créés **hors dépôt** : `/home/user/work/p86/sha_avant.txt`, `tree_avant.txt`, `tree_apres.txt` (empreintes de contrôle uniquement).

---

## 8. État à la clôture

| Élément | État |
|---|---|
| 13 points analysés et recommandés | oui (propositions, **aucune décision PO fabriquée**) |
| Dépendances | exposées (arbitrage §8) ; point pivot : P86-8 |
| Conditions de promotion R7a/G6 | proposées (arbitrage §7, plan §9) |
| Limites tests et capacité | explicites (arbitrage §4, §11 ; plan §6, §8) |
| Plan cohérent avec P81 à P85 | oui (arbitrage §9) |
| Décisions PO regroupées | oui, tableau unique (arbitrage §10) |
| O-5, O-6, O-9, O-10 | **OUVERTS** (maintien recommandé, aucune clôture proposée) |
| V-3 | **NON APPROUVÉ** (maintien recommandé) |
| V-4, V-5, V-6 | VIGILANCE (inchangées) |
| Migration 0022, réalisation STOCK, P87 | **non lancés** |

**La mission s'arrête ici. Toute suite exige : (1) l'arbitrage du PO sur les 13 points ; (2) une autorisation explicite et distincte de lancer la réalisation (plan §14).**

---
Fin du rapport P86.
