# PHARM'OS — Dossier de conception consolidé v1.2.1

**Produit** : PHARM'OS — Intelligent Pharmacy Management System · **Éditeur** : APEX AI DEV
**Version** : **1.2.1 (correction documentaire et consolidation finale)** — 2026-11-04 · remplace pour usage la v1.2 du 2026-11-03, conservée sans modification
**Historique** : v1.0 → v1.1 (EXT02) → v1.2 (consolidation, 2026-11-03) → **v1.2.1 : D3 arbitrée (une base de données physiquement distincte par pharmacie), moteur de base maintenu ouvert (ARB-01), harmonisation isolation / sauvegarde / restauration / migration / sécurité / administration, critères responsive transverses (§14.4), inventaire corrigé (`shift_coverage_rules` / `payer_coverage_rules`), statistiques recalculées (§18).** Détail ligne à ligne : `JOURNAL_MODIFICATIONS_v1.2.1.md` ; contrôles : `RAPPORT_VALIDATION_CROISEE_v1.2.1.md`.
**Statut** : **CONSOLIDATION SOUMISE À VALIDATION HUMAINE — NON VALIDÉE DANS SON ENSEMBLE.**
Ce document regroupe la conception existante et les arbitrages connus. Il **ne commence pas le développement**, ne crée
aucune table et ne lance aucune migration. Les décisions non arbitrées restent signalées comme telles (§18).

| Livrable | Fichier | Rôle |
|---|---|---|
| 1 | `CONCEPTION_PHARMOS_v1.2.1.md` | Dossier maître (ce document) |
| 2 | `MATRICE_DECISIONS_D1_D86_v1.2.1.md` | Traçabilité des 86 décisions |
| 3 | `RAPPORT_AUDIT_COHERENCE_v1.2.1.md` | Contradictions INC-01 à INC-41, doublons, risques, corrections proposées |
| 4 | `MATRICE_DEPLOIEMENT_SAAS_ONPREMISE_v1.2.1.md` | Double mode de déploiement, adaptations, tests DEP / LOC / LIC / BKP / UPD |
| 5 | `REGISTRE_POINTS_OUVERTS_PHARMOS_v1.2.1.md` | Points réglementaires (R1–R55), documentaires (PD), arbitrages (ARB), techniques (PT) |

**Versions antérieures conservées sans modification** : `CONCEPTION_PHARMOS_v1.md` (v1.1), EXT02, EXT03 v1.0, EXT04,
EXT05, EXT06 v1.0 et v1.1, rapport de revue EXT06 v1.1, copie conforme de l'audit EXT03 v1.1
(`archives/AUDIT_EXT03_v1.1/`), ainsi que les **cinq livrables v1.2** (2026-11-03). Empreintes SHA-256 vérifiées avant et après la consolidation et la correction v1.2.1 (§20).

---

## Sommaire

0. Légendes
1. Inventaire documentaire et méthode
2. Vision, périmètre et acteurs
3. Principes directeurs
4. Cartographie des modules et dépendances
5. Exigences transverses (double déploiement, responsive, langues, performance)
6. Gouvernance, comptes, permissions et délégations
7. Modèle de données consolidé
8. Flux (stock, dispensation, finance, comptabilité, corrections, audit)
9. Règles métier consolidées par domaine
10. Données de santé et confidentialité
11. Architecture IA
12. Sécurité
13. Architecture technique portable (SaaS et local)
14. Responsive et expérience utilisateur
15. Stratégie de tests
16. Plan de développement révisé
17. Points réglementaires et nature des énoncés
18. Décisions
19. Risques
20. Traçabilité des modifications

---

## 0. Légendes

### 0.1 Fiabilité de la provenance

| Code | Signification |
|---|---|
| **CD** | **Confirmé documentairement** : lu dans un document source accessible |
| **AP** | **Arbitré par le propriétaire** (revue d'EXT06 ou prompt de consolidation v1.2) |
| **RC** | **Reconstitué** à partir des éléments disponibles |
| **AV** | **À vérifier** : interprétation ou portée incertaine |
| **ND** | **Non documenté** |

### 0.2 Nature des énoncés

| Marque | Signification |
|---|---|
| **[DÉCIDÉ]** | Règle métier arbitrée par le propriétaire |
| **[PROPOSÉ]** | Règle métier documentée dans une extension mais non arbitrée |
| **[TECH]** | Choix technique proposé par la conception (réversible, à valider) |
| **[RECO]** | Recommandation de la consolidation v1.2 |
| **[JURIDIQUE Rxx]** | Obligation juridique ou réglementaire **à confirmer** ; aucune valeur n'est présumée |
| **[ARB-xx]** | Question d'arbitrage nouvelle, sans numéro de décision (registre §3) |
| **[INC-xx]** | Contradiction documentée dans le rapport d'audit |

---

## 1. Inventaire documentaire et méthode

### 1.1 Documents retrouvés et exploités

| Document | Emplacement | Lignes | Statut | Lu intégralement | Fiab. |
|---|---|---|---|---|---|
| Dossier principal v1.1 | `docs/CONCEPTION_PHARMOS_v1.md` | 904 | Référence en vigueur jusqu'à validation de la v1.2 | Oui | CD |
| Dossier principal v1.0 | archive utilisateur `02_HISTORIQUE_VERSIONS/` | 850 | Historique | Consulté | CD |
| EXT02 — RH & Team Management | `docs/EXT02_RH_TEAM_MANAGEMENT.md` | 798 | Intégrée au dossier v1.1 ; D17–D26 non arbitrées | Oui | CD |
| EXT03 v1.0 — Ordonnances | `docs/EXT03_ORDONNANCES_PRESCRIPTEURS.md` | 744 | Remplacée par la v1.1 | Oui | CD |
| EXT03 v1.1 + audit (7 fichiers) | `docs/archives/AUDIT_EXT03_v1.1/` | 1 915 | Proposition ; D27–D43 non arbitrées | Oui | CD |
| EXT04 — CNAM, conventions, assurances | `docs/EXT04_CNAM_CONVENTIONS_ASSURANCES.md` | 847 | Proposition ; D44–D58 non arbitrées | Oui | CD |
| EXT05 — Annulations, retours, délégations | `docs/EXT05_ANNULATIONS_RETOURS_DELEGATIONS.md` | 825 | Proposition ; D59–D71 non arbitrées | Oui | CD |
| EXT06 v1.0 | `docs/EXT06_CATALOGUE_GOUVERNANCE_FINANCE.md` | 1 019 | Historique | Oui | CD |
| EXT06 v1.1 | `docs/EXT06_CATALOGUE_GOUVERNANCE_FINANCE_v1.1.md` | 1 490 | Validée dans son principe ; D73, D75 arbitrées | Oui | CD |
| Rapport de revue EXT06 v1.1 | `docs/EXT06_v1.1_RAPPORT_DE_REVUE.md` | 341 | Revue | Oui | CD |
| Journal du projet | archive `00_JOURNAL_DU_PROJET.md` | 63 | Arrêté avant EXT04 (obsolète, PD-02) | Oui | CD |
| Arbitrages D72, D74, D76–D86, double mode, responsive | Prompt de consolidation v1.2 | — | Arbitrages et exigences du propriétaire | — | AP |

**Écart avec la mission.** Le prompt indiquait que les fichiers complets d'EXT02 à EXT05 n'étaient pas accessibles. Ils le
sont et ont été **lus intégralement**. Aucun élément de ce dossier n'a donc été **reconstitué sans source** (aucun code RC
ou ND). Les passages marqués AV portent sur l'**interprétation** de certains arbitrages (D72 « base de données propre »,
D74 « second collaborateur », D78 « nom du client ») — rapport d'audit INC-10, INC-17, INC-31. **v1.2.1** : l'interprétation de D72
« base de données propre » est désormais **tranchée** par l'arbitrage de D3 (base physiquement distincte par pharmacie) ; INC-17 est résolue.

**Copie d'archive.** Le dossier d'audit EXT03 v1.1 n'existait que dans une archive temporaire. Une **copie conforme**
(empreintes vérifiées) a été placée dans `docs/archives/AUDIT_EXT03_v1.1/` afin que toutes les références soient
résolubles dans le dépôt. Aucun fichier n'y a été modifié.

**Code.** Le dépôt ne contient que le squelette initial (Hono / Vite / Cloudflare). Aucun code métier n'existe ; le code
n'a pas été modifié.

### 1.2 Méthode

1. Extraction des décisions (D1–D86), exigences réglementaires (R1–R51), tables, permissions et tests de chaque document,
   avec numéro de ligne.
2. Comparaison des définitions de tables entre documents : **243 noms de tables distincts**, dont **9** définis dans deux documents ;
   l'un d'eux (`coverage_rules`) désigne deux tables différentes, soit **244 tables** (correction v1.2.1, §7.4).
3. Confrontation des arbitrages D72 à D86 aux propositions d'EXT06 v1.1, et des extensions entre elles.
4. Documentation de chaque contradiction (INC-01 à INC-41) **sans résolution silencieuse** : chaque résolution indique si
   elle découle d'un arbitrage (RÉSOLUE) ou si elle reste une proposition (À VALIDER).
5. Intégration des exigences transverses nouvelles : double mode de déploiement (§5.1, §13), responsive (§5.2, §14).

### 1.3 Règle de priorité entre documents

**Arbitrage du propriétaire (AP)** > extension **validée dans son principe** (EXT06 v1.1) > proposition d'extension la
plus récente > dossier principal v1.1. Une proposition non arbitrée ne prévaut jamais sur une décision arbitrée. Lorsqu'une
extension remplace une table du dossier, la v1.2 retient la définition de l'extension **sous réserve de sa validation**.

---

## 2. Vision, périmètre et acteurs

### 2.1 Vision

PHARM'OS est un logiciel de gestion d'officine (ERP) destiné au marché tunisien, en **français, arabe (RTL) et anglais**,
exploitable **en SaaS** ou **installé localement** dans la pharmacie, avec un assistant d'aide à la décision (PHARM'OS AI)
qui ne prend **jamais** de décision sensible.

### 2.2 Périmètre fonctionnel consolidé

| Domaine | Modules | Origine |
|---|---|---|
| Plateforme, gouvernance, comptes, permissions, délégations, dérogations | M00 | Dossier ; EXT02 ; EXT05 ; EXT06 ; D72–D74 |
| Tableau de bord, accueil adapté | M01, `/home` | Dossier ; EXT06 ; D77 |
| Vente, caisse nominative | M02, M02b | Dossier ; EXT02 |
| Achats, fournisseurs, commandes | M03, M06, M11 | Dossier |
| Catalogue (produits, classification, fiscalité, substitution, services) | M04 | Dossier ; EXT06 ; D78, D84 |
| Stock et lots (registre, FEFO, états, réservations logiques) | M05 | Dossier ; EXT05 ; EXT06 ; D76 |
| Clients et patients | M07 | Dossier ; EXT03 v1.1 |
| Finance (tiers, paiements, banque, charges, emprunts, immobilisations, paie importée) | M08 | Dossier ; EXT06 ; D81, D85, D86 |
| Médicaments réglementés | M09 | Dossier ; EXT03 v1.1 |
| Péremptions et pertes | M10 | Dossier |
| Rapports, couche métriques, rentabilité | M12 | Dossier ; EXT03 v1.1 ; EXT06 ; D79 |
| PHARM'OS AI et avatar | M13 | Dossier |
| Hors-ligne du poste | M14 | Dossier |
| Ressources humaines | M15 | EXT02 |
| Laboratoire (préparations) | M16 | EXT02 |
| Ordonnances et prescripteurs | M17 | EXT03 v1.1 |
| Tiers payant (CNAM, conventions, assurances) | M18 | EXT04 |
| Opérations correctives (annulations, retours, remboursements) | M19 | EXT05 ; EXT06 |
| Comptabilité (partie double, « à titre indicatif ») | M20 | EXT06 v1.1 ; D80–D83 |

### 2.3 Acteurs

| Acteur | Rôle | Source |
|---|---|---|
| **Super Admin APEX** | Compte unique et immuable de l'éditeur (`apexproib@gmail.com`) ; crée, bloque, supprime et recrée les **accès** des administrateurs des pharmacies | AP (D72) |
| **Pharmacien administrateur** | Un par officine ; autorité de gouvernance (droits, délégations, dérogations, paramètres) | CD (EXT06 GV1) + AP (D72) |
| Titulaire | Responsable légal de l'officine ; administrateur par défaut, distinct en droit si R48 le permet (INC-18) | CD (dossier §2.1) |
| Pharmacien adjoint, préparateur, caissier, responsable achats et stock, comptable, auditeur | Rôles par défaut personnalisables | CD (dossier §9.1) |
| Responsable d'équipe, gestionnaire RH, collaborateur (libre-service) | Rôles RH | CD (EXT02 §11) |
| Responsable tiers payant | Rôle cumulable | CD (EXT04, D57 proposée) |
| Patient / client | Données minimisées, consentement | CD (dossier §9.4, EXT03 v1.1) |
| Expert-comptable | Destinataire des exports ; responsable des comptes officiels | CD (EXT06) ; D80 |
| PHARM'OS AI | Lecture des agrégats, brouillons, recommandations ; aucune décision sensible | CD (dossier §7) |

---

## 3. Principes directeurs

### 3.1 Principes confirmés

| # | Principe | Source | Nature |
|---|---|---|---|
| P1 | **Une seule source de vérité par donnée** ; les écrans d'accès direct sont des vues | Dossier §3.2 ; EXT06 §1.2 | CD |
| P2 | **Stock = registre immuable de mouvements** ; les niveaux sont des projections recalculables | Dossier §4.4 | CD |
| P3 | **Lots et péremptions, allocation FEFO** ; un lot périmé, rappelé ou bloqué n'est jamais alloué | Dossier §2.2 ; EXT06 §5.4 | CD |
| P4 | **Aucune suppression physique** d'une opération métier validée ; correction par opération inverse tracée (`reverses_id`, `correlation_id`) | Dossier §9.3 ; EXT05 ; EXT06 CP1–CP2 | CD |
| P5 | **Transactions atomiques** pour toute opération sensible, avec gardes conditionnelles et idempotence | Dossier §6.3 ; EXT03 v1.1 §19 ; EXT06 CP8 | CD |
| P6 | **Identité du collaborateur** (`employee_id`) associée à chaque opération | Dossier v1.1 ; EXT02 | CD |
| P7 | **Droits vérifiés côté serveur à chaque requête** ; l'interface masque mais n'est jamais la seule barrière | Dossier §9.1 ; EXT06 GV12 | CD |
| P8 | **Séparation stricte des données entre pharmacies** | Dossier §2.3 ; EXT06 MO1–MO10 | CD |
| P9 | **Montants en millimes entiers**, identifiants ULID, horodatage serveur dans le fuseau de l'officine | Dossier §2.3, §4.1 | CD |
| P10 | **Aucune valeur réglementaire ou fiscale présumée** (taux, durées, plafonds, autorisations) | Toutes les extensions | CD |
| P11 | **L'IA ne prend jamais de décision sensible** ; lecture seule et brouillons | Dossier §7 | CD |
| P12 | **Aucune donnée fictive en production** ; jeux de référence réservés aux tests | EXT02 §12.3 | CD |
| P13 | **Pré-comptabilité dérivée des événements**, jamais saisie en double ; écritures validées immuables | EXT06 v1.1 ; D80 | [DÉCIDÉ] |

### 3.2 Principes ajoutés en v1.2

| # | Principe | Source | Nature |
|---|---|---|---|
| P14 | **Une seule base de code pour deux modes de déploiement** (SaaS et local) : même cœur, mêmes règles, mêmes écrans, même sécurité | Exigence du propriétaire | [DÉCIDÉ] |
| P15 | **Le cœur métier ne dépend d'aucun service d'infrastructure** : ports et adaptateurs | Consolidation | [TECH] |
| P16 | **Les services distants sont optionnels** : aucune opération métier n'exige une connexion à APEX | Exigence du propriétaire | [DÉCIDÉ] |
| P17 | **Suppression d'un accès ≠ suppression des données** ; aucune destruction automatique de l'historique métier ou comptable | D72 | [DÉCIDÉ] |
| P18 | **Comptabilité affichée « Comptabilité à titre indicatif »** | D80 | [DÉCIDÉ] |
| P19 | **Modèle standard modifiable ≠ donnée officielle** : un modèle de gestion livré (plan comptable, classification thérapeutique) est utilisable et adaptable ; il ne remplace jamais une donnée réglementaire officielle. Les taux, durées, plafonds et autorisations restent sans valeur par défaut (P10) | D83, D84 ; reformule EXT06 §1.2 principe 7 (INC-40) | [DÉCIDÉ] |
| P20 | **Responsive par usage**, pas seulement par taille d'écran | Exigence du propriétaire ; dossier §2.3 | [DÉCIDÉ] |

---

## 4. Cartographie des modules et dépendances

### 4.1 Vue d'ensemble

```
            ┌──────────────────── NIVEAU PLATEFORME (mode A ; mode B : ARB-05) ────────────────────┐
            │ Super Admin APEX (D72) · accès des administrateurs · abonnements / licences ·          │
            │ paquets de référentiels APEX · journal plateforme · demandes de suppression de données │
            └───────────────────────────────────────┬───────────────────────────────────────────────┘
                                                    │ aucune lecture des données métier sans
                                                    │ ouverture temporaire par l'officine (INC-13)
┌─────────────────────────── NIVEAU OFFICINE (une base physiquement distincte par pharmacie — D3) ───────────────┐
│ M00 GOUVERNANCE : administrateur · droits effectifs · délégations · dérogations · paramètres · audit chaîné      │
│ M15 PERSONNES & DROITS (collaborateurs, qualifications, habilitations, présence) → acteur et droits de chaque    │
│     requête                                                                                                      │
├──────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ M04 CATALOGUE ──► M05 STOCK (registre, lots, FEFO, états, réservations logiques)                                 │
│                    ▲          ▲              ▲              ▲               ▲               ▲                    │
│ M03 ACHATS · M11 COMMANDES   M02 VENTE ── M17 ORDONNANCES  M10 PÉREMPTIONS  M09 RÉGLEMENTÉS  M16 LABORATOIRE      │
│                               │  ├── M18 TIERS PAYANT (parts, créances)                                          │
│                               │  └── M19 CORRECTIONS (annulations, retours, remboursements)                      │
│                               ▼                                                                                  │
│ M02b CAISSE NOMINATIVE ──► M08 FINANCE (tiers, paiements, banque, charges, emprunts, immobilisations, paie)       │
│                               ▼  (événements)                                                                    │
│ M20 COMPTABILITÉ — « Comptabilité à titre indicatif » (écritures générées, immuables, exceptions)                │
├──────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ COUCHE MÉTRIQUES (metric_definitions, metrics_daily, financial_snapshots, rx_facts) ──► M01 · M12 · G23–G25      │
│ M13 PHARM'OS AI (agrégats, brouillons) · M14 HORS-LIGNE (poste) · ACCUEIL /home (D77)                            │
└──────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
```

### 4.2 Catalogue des modules

| Code | Module | Données dont il est propriétaire | Référence | Fiab. |
|---|---|---|---|---|
| M00 | Plateforme et gouvernance | Comptes, rôles, permissions, délégations, dérogations, paramètres, audit, notifications, licences, conformité, conservation | Dossier §4.2 ; EXT02 §2.2 ; EXT05 §3.5 ; EXT06 §4.1 ; D72 | CD + AP |
| M01 | Tableau de bord | Aucune (lecture de la couche métriques) | Dossier | CD |
| M02 | Vente | Ventes, lignes, paiements de vente | Dossier §4.5 | CD |
| M02b | Caisse nominative | Caisses, tiroirs, sessions, comptages, mouvements de caisse, transferts | EXT02 §2.5 | CD |
| M03 | Achats | Réceptions, factures, retours fournisseurs | Dossier §4.6 ; EXT06 §4.8 | CD |
| M04 | Catalogue | Produits, unités, prix, classification, fiscalité, substitution, services | Dossier §4.3 ; EXT06 §4.2–4.7 ; D78, D84 | CD + AP |
| M05 | Stock | Mouvements, lots, niveaux par état, réservations, inventaires | Dossier §4.4 ; EXT05 §3.3 ; EXT06 §4.6 ; D76 | CD + AP |
| M06 | Fournisseurs | Fiches, conditions | Dossier §4.7 | CD |
| M07 | Clients et patients | Clients, volet patient cloisonné, fidélité, consentements | Dossier §4.7 ; EXT03 v1.1 | CD |
| M08 | Finance | Grand livre des tiers, paiements, instruments, banque, charges, emprunts, immobilisations, paie importée | Dossier §4.8 ; EXT06 §4.8 | CD + AP |
| M09 | Réglementés | Classes, registre généré, rectifications | Dossier §4.9 ; EXT03 v1.1 §16 | CD |
| M10 | Péremptions et pertes | Règles d'alerte, pertes, destructions | Dossier §4.10 | CD |
| M11 | Commandes | Bons de commande, suggestions | Dossier §4.6 | CD |
| M12 | Rapports et métriques | Définitions, agrégats, clichés, journaux de requêtes et d'exports | Dossier ; EXT03 v1.1 ; EXT06 §4.10 | CD |
| M13 | PHARM'OS AI | Exécutions, recommandations, conversations | Dossier §4.11, §7 | CD |
| M14 | Hors-ligne (poste) | File locale du poste | Dossier §6.7 | CD |
| M15 | RH et équipes | Collaborateurs, organisation, planning, présence, congés, KPI, formations | EXT02 | CD |
| M16 | Laboratoire | Préparations, contrôles | EXT02 §2.6 | CD |
| M17 | Ordonnances et prescripteurs | Prescripteurs, ordonnances, délivrances, documents, OCR | EXT03 v1.1 | CD (proposée) |
| M18 | Tiers payant | Organismes, conventions, règles, couvertures, créances, bordereaux, règlements, litiges | EXT04 | CD (proposée) |
| M19 | Opérations correctives | Opérations, demandes, contrôle de réintégration, remboursements, avoirs | EXT05 ; EXT06 | CD (proposée, amendée) |
| M20 | Comptabilité | Plan de comptes, journaux, règles de passation, écritures, périodes, exceptions, exports | EXT06 v1.1 §4.9 ; D80–D83 | CD + AP |

**Aucun module nouveau en v1.2.** Le niveau plateforme (Super Admin) est un sous-ensemble de M00. Les délégations
(décrites par EXT05 comme sous-module M19.5) sont rattachées à M00, qui les gouverne depuis EXT06 [RECO, INC-41].

### 4.3 Matrice des dépendances

Lecture : la ligne **dépend de** la colonne (● forte, ○ faible). Une écriture dans un autre module passe toujours par le
service du module propriétaire (dossier §3.2) ; les effets asynchrones passent par l'outbox.

| Module | M00 | M04 | M05 | M02 | M02b | M08 | M15 | M17 | M18 | M19 | M20 | Métriques |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| M02 Vente | ● | ● | ● | — | ● | ● | ● | ○ | ○ | — | — | — |
| M03 Achats | ● | ● | ● | — | — | ● | ● | — | — | — | — | — |
| M05 Stock | ● | ● | — | — | — | — | ● | — | — | — | — | — |
| M08 Finance | ● | — | — | ○ | ● | — | ● | — | ○ | — | — | — |
| M09 Réglementés | ● | ● | ● | ● | — | — | ● | ● | — | ○ | — | — |
| M16 Laboratoire | ● | ● | ● | — | — | — | ● | ○ | — | — | — | — |
| M17 Ordonnances | ● | ● | ● | ● | — | — | ● | — | — | — | — | — |
| M18 Tiers payant | ● | ● | — | ● | ● | ● | ● | ● | — | — | — | — |
| M19 Corrections | ● | ● | ● | ● | ● | ● | ● | ● | ● | — | — | — |
| M20 Comptabilité | ● | ○ | ○ | ○ | ○ | ● | ○ | — | ○ | ○ | — | — |
| Couche métriques | ● | ● | ● | ● | ● | ● | ○ | ● | ● | ● | ○ | — |
| M01 / M12 / M13 | ● | ○ | ○ | — | — | — | ○ | — | — | — | ○ | ● |

M20 et la couche métriques lisent des **événements** et des registres ; ils n'écrivent jamais dans les modules
opérationnels (EXT06 v1.1 CP6, MT7, EX6).

---

## 5. Exigences transverses

### 5.1 Double mode de déploiement [DÉCIDÉ — exigence du propriétaire]

| Mode | Définition | Points clés |
|---|---|---|
| **A — SaaS** | Hébergement centralisé APEX | Multi-pharmacies, abonnements et licences, sauvegardes et mises à jour centralisées, console Super Admin |
| **B — Local** | Installation sur le poste ou le serveur de la pharmacie | Base et fichiers locaux, sauvegardes locales, administration locale, mises à jour contrôlées, utilisateurs locaux, **aucune dépendance permanente à APEX** ; IA, notifications et intégrations optionnelles |

Règle : **une seule base de code**, même cœur, mêmes règles, mêmes écrans, mêmes mécanismes de sécurité, même modèle
fonctionnel, et **une base de données physiquement distincte par pharmacie dans les deux modes** [DÉCIDÉ D3, v1.2.1] ; le moteur
de base reste à choisir (ARB-01). L'architecture Cloudflare de la v1.1 n'est **pas** compatible en l'état avec le mode B [INC-39].
Architecture cible : §13 ; analyse détaillée, risques et tests : `MATRICE_DEPLOIEMENT_SAAS_ONPREMISE_v1.2.1.md`.

### 5.2 Responsive et ergonomie [DÉCIDÉ]

Ordinateur de bureau, portable, tablette et smartphone, avec une **adaptation par usage** (caisse sur ordinateur,
réception et inventaire sur tablette, consultation, alertes et validations sur smartphone). Critères par module : §14.

### 5.3 Langues

FR et AR (RTL) dès le Lot 0 ; **EN** prévue dans le dossier v1.1 (« clés i18n, aucun texte en dur ») et **exigée** par la
v1.2 pour l'ensemble des écrans [DÉCIDÉ]. Chiffres latins par défaut en arabe (D16, proposée). Les libellés des
référentiels prévoient `libelle_fr`, `libelle_ar` et `libelle_en` (déjà le cas pour les spécialités, EXT03 v1.1).

### 5.4 Exigences non fonctionnelles (reprises du dossier §2.3, complétées)

| Domaine | Exigence | Source |
|---|---|---|
| Performance | Recherche produit < 150 ms serveur ; ajout de ligne < 100 ms (cache local) ; tableau de bord < 1,5 s ; **dans les deux modes, sur le matériel minimal** (ARB-08) | Dossier ; v1.2 |
| Volumétrie | 30 000 références, 300 ventes / jour, 5 ans d'historique par officine | Dossier |
| Précision monétaire | Millimes entiers, aucun flottant | Dossier |
| Disponibilité | Vente possible poste déconnecté (Lot 14) ; mode B indépendant d'Internet | Dossier ; v1.2 |
| Traçabilité | Toute écriture sensible produit un événement d'audit immuable | Dossier |
| Isolation | Aucune requête sans contexte d'officine ; **une base physiquement distincte par pharmacie** (D3) ; tests automatisés | Dossier ; EXT06 MO10 ; D3 |
| Accessibilité | Contrastes, cibles tactiles ≥ 48 px, navigation au clavier, lecteur de codes-barres sans souris | EXT06 §2.1 ; v1.2 §14 |
| Portabilité | Même jeu de migrations, appliqué **séparément à chaque base** avec contrôle de version ; mêmes tests dans les deux modes | v1.2 ; D3 |

---

## 6. Gouvernance, comptes, permissions et délégations

### 6.1 Deux niveaux de comptes [DÉCIDÉ D72]

| Niveau | Compte | Pouvoirs | Ce qu'il ne peut **pas** faire |
|---|---|---|---|
| **Instance / plateforme** | **Super Admin APEX** — compte unique, identifiant `apexproib@gmail.com` | Créer une pharmacie et son accès administrateur ; bloquer, débloquer, supprimer et recréer cet accès ; générer un code d'accès initial ; consulter le journal plateforme | Lire ou modifier les données métier d'une officine ; modifier sa comptabilité ; supprimer ses données hors de la procédure §6.3 ; être lui-même modifié, désactivé ou supprimé [RECO, INC-13] |
| **Officine** | Pharmacien administrateur (un seul actif, EXT06 GV1) | Gouvernance de l'officine : droits, délégations, dérogations, paramètres, périodes comptables, services, plan comptable | Agir dans une autre officine ; se déléguer à lui-même ; s'accorder une dérogation (EXT06 GV8) |
| Officine | Collaborateurs | Selon les droits effectifs (§6.5) | — |

**Immuabilité du Super Admin [DÉCIDÉ]** : aucune route d'API, aucun écran et aucun rôle ne permet de modifier son identifiant,
son rôle, de le désactiver ou de le supprimer ; un contrôle en base (déclencheur) refuse ces opérations [TECH]. Seuls ses
**facteurs d'authentification** peuvent être renouvelés, par la procédure de récupération (§6.4).

**Accès aux données métier.** Le dossier v1.1 (§9.4) prévoit que l'accès du support APEX est « désactivé par défaut,
ouvert temporairement par le titulaire, journalisé », et EXT06 (W1) que « le support APEX n'obtient jamais d'accès aux
données ». La v1.2 **concilie** ces règles avec D72 : le Super Admin gère des **accès**, pas des **données** ; toute
consultation de données métier exige une ouverture temporaire par l'administrateur de l'officine, tracée dans les deux
journaux [RECO, INC-13 — à confirmer, R52].

### 6.2 Cycle de vie d'un accès administrateur [DÉCIDÉ D72, modalités TECH]

```
          (Super Admin) créer
                │  code d'accès initial généré : aléatoire, usage unique, durée de validité paramétrée,
                │  conservé uniquement haché, transmis hors de l'application
                ▼
            [CRÉÉ] ──(première connexion : code initial)──► changement OBLIGATOIRE du mot de passe
                │                                           + enrôlement MFA (administrateur, dossier §9.2)
                ▼
            [ACTIF] ◄──(débloquer)── [BLOQUÉ] ◄──(bloquer : sessions révoquées)── [ACTIF]
                │
                ├─(régénérer le mot de passe) → nouveau code initial, retour en [CRÉÉ] ; sessions révoquées ;
                │                               données de l'officine INCHANGÉES
                ▼
          [SUPPRIMÉ] : identifiants et sessions révoqués ; le compte reste en base (suppression logique) pour
                       l'audit ; AUCUNE donnée de l'officine n'est supprimée
                │
                └─(recréer) → nouvel accès [CRÉÉ], rattaché à la MÊME base de l'officine
```

- **Lien avec l'administrateur désigné** : l'accès est rattaché à un collaborateur désigné dans `tenant_administrators`.
  Recréer l'accès pour **une autre personne** équivaut à une désignation (mode `DESIGNATION_SUPER_ADMIN`) : la
  qualification de pharmacien vérifiée reste exigée (EXT06 GV1), un justificatif est joint, et l'ancien administrateur
  perd immédiatement ses droits de gouvernance [RECO, INC-15].
- **Chaque pharmacie a ses propres identifiants d'administration** [DÉCIDÉ] ; un même identifiant ne peut pas
  administrer deux officines sans désignation explicite dans chacune (EXT06 MO6).
- Toutes les actions sont écrites dans `platform_audit_log` (chaîné) et notifiées à l'administrateur concerné lorsqu'il
  existe.

### 6.3 Suppression des données d'une pharmacie — procédure distincte [DÉCIDÉ principe D72 ; modalités RECO]

| Étape | Règle |
|---|---|
| 1. Demande | `tenant_data_deletion_requests` : demandeur (administrateur de l'officine ou fin de contrat), motif, pièce |
| 2. Réversibilité | Export complet proposé et tracé (dossier §6.6) |
| 3. Contrôle de conservation | Vérification des durées légales minimales (ordonnances, registres, comptabilité, fiscalité) — **aucune valeur présumée** [JURIDIQUE R5, R25, R41, R53] ; les données soumises à conservation ne sont pas détruites |
| 4. Double validation | Super Admin **et** administrateur de l'officine (ou décision documentée si l'officine n'existe plus) |
| 5. Délai de grâce | Paramétrable, annulable |
| 6. Exécution | Destruction de la base et des fichiers de l'officine, y compris dans les sauvegardes selon leur cycle ; certificat de destruction ; trace conservée dans le journal plateforme |

**Jamais automatique** : ni la suppression d'un accès, ni le blocage, ni l'expiration d'un abonnement ou d'une licence
n'entraînent de suppression de données [DÉCIDÉ P17]. En **mode B**, les données sont sur le matériel de l'officine :
seule l'officine peut les supprimer ; APEX n'a aucun moyen technique de le faire [TECH].

### 6.4 Récupération du compte Super Admin [DÉCIDÉ principe D72 ; modalités ARB-04]

Proposition (à arbitrer) :
1. **Codes de récupération** générés à l'initialisation, scellés, conservés hors ligne dans deux lieux distincts.
2. **Double contrôle** : la récupération exige deux personnes habilitées d'APEX AI DEV, identifiées nommément.
3. La récupération **renouvelle seulement les facteurs** (mot de passe, MFA) ; l'identifiant et le rôle restent inchangés.
4. **Aucune réinitialisation par e-mail** pour ce compte : la boîte `apexproib@gmail.com` sert d'identifiant, pas de canal
   de récupération (une compromission de la messagerie ne doit pas donner la plateforme) [RECO, risque RG3].
5. Chaque usage est journalisé, alerte immédiate, codes régénérés après usage.

### 6.5 Droits effectifs (consolidés)

Ordre d'évaluation, côté serveur, à chaque requête (EXT02 §1.2, EXT05 §5.3, EXT06 §5.1, complétés) :

```
0. Session valide, officine active dans la session, accès non bloqué ni supprimé (D72)
1. Module couvert par l'abonnement / la licence ?                    non → lecture seule ou refus (ARB-06)
2. Module coupé pour ce collaborateur (module_disabled) ?            oui → refus
3. Permission de gouvernance ? → évaluée UNIQUEMENT depuis tenant_administrators (jamais un rôle)
4. Action accordée : rôle ∪ réglages individuels ∪ délégation active − retraits
5. Habilitation, qualification valide, statut, présence pointée (EXT02)
6. Périmètre (soi / équipe / tous ; exclusions de délégation ; réglementés)
7. Plafonds (montant, nombre, délai)                                 dépassement → dérogation (§6.7)
→ AUTORISÉ | REFUSÉ (motif lisible) | DÉROGATION REQUISE
```

### 6.6 Délégations [PROPOSÉ EXT05, amendé EXT06]

Seul l'administrateur délègue (D66 **caduque**, INC-08) ; délégation nominative, datée (début et fin obligatoires), plafonnée,
limitée à une liste de couples module × action ; pas de redélégation ; opérations réglementées et gestion des droits non
délégables ; durée maximale proposée 30 jours (D67) ; révocation immédiate ; historique complet (`delegation_usage`).
**[JURIDIQUE R37, R48]**.

### 6.7 Dérogations et validation à distance [DÉCIDÉ D73]

Dépassement de plafond ou dérogation de délai : accordé par l'administrateur seul, opération par opération. Validation à
distance : MFA au moment de la décision, confirmation explicite, motif, opération identifiée par empreinte, autorisation à
usage unique et expirante, journalisation complète, aucune substitution ; **exclue pour les produits réglementés** et
**sans effet sur l'exigence de présence d'un pharmacien** (D24, INC-19). Détail : EXT06 v1.1 W4-D, GV13.
**Mode B** : disponible sur le réseau local ; hors de l'officine, seulement si l'officine fournit un accès sécurisé [ARB-07, INC-16].

### 6.8 Réintégration corrective [DÉCIDÉ D74]

« Un second collaborateur peut effectuer la réintégration d'une opération corrective uniquement si le pharmacien
administrateur lui a explicitement accordé cette autorisation. »

Traduction proposée [RECO, interprétation AV, INC-10] :

| Point | Règle proposée |
|---|---|
| Permission | Nouvelle permission **`stock.return.reintegrate`** : décision `REINTEGRE` (retour en stock vendable) d'un article `EN_CONTROLE` |
| Attribution | **Jamais par un rôle par défaut** ; seulement par réglage individuel ou délégation **de l'administrateur**, nominativement |
| « Second collaborateur » | La personne qui réintègre est différente de celle qui a exécuté l'opération corrective |
| Administrateur | Peut réintégrer lui-même (il détient le droit) ; ses réintégrations restent tracées |
| Autres décisions | `stock.return.decide` (EXT05) reste la permission des décisions `QUARANTAINE` et `PERTE` |
| Réglementés | `stock.return.decide.controlled` inchangé ; aucune réintégration d'un réglementé (EXT05, R35) |
| Immédiateté | La décision peut être immédiate (check-list réduite, EXT06 v1.1 §5.2) — aucune réintégration **automatique** (EXT06 AN6) |

Conséquence : la matrice par défaut d'EXT05 §5.2 (`stock.return.decide` ✔ adjoint, ✔ achats/stock) est **amendée** pour
la seule décision de réintégration.

### 6.9 Accueil adapté [DÉCIDÉ D77]

La page affichée après connexion et les tuiles de l'accueil (`/home`, EXT06 G1) sont calculées à partir des **permissions
effectives** du collaborateur (§6.5), pas seulement de son rôle : un module non autorisé n'apparaît pas, une tuile n'est
affichée que si l'action principale est permise. Page par défaut selon le rôle, modifiable par l'utilisateur dans la limite
de ses droits. L'accueil n'affiche aucun indicateur financier (EXT06 §2.1). Tests : NAV-01, NAV-05 à NAV-07.

### 6.10 Rôles par défaut — corrections de la v1.2

Les matrices par défaut restent celles des documents sources (dossier §9.1, EXT02 §11.2, EXT03 v1.1 §5.2, EXT04 §5.2,
EXT05 §5.2, EXT06 v1.1 §6.3), avec trois corrections :
1. Dans EXT02 à EXT05, la colonne **« Titulaire »** se lit **« Administrateur »** pour toute permission de gouvernance
   (droits, délégations, dérogations, exceptions accordées) [RECO, INC-18].
2. `stock.return.reintegrate` n'est attribuée à aucun rôle par défaut (D74).
3. Les permissions `sales.void.own`, `sales.void.any`, `sales.refund` d'EXT02 sont remplacées par le catalogue `sale.*`
   d'EXT05 [PROPOSÉ, INC-25].

---

## 7. Modèle de données consolidé

### 7.1 Conventions (dossier §4.1, extensions)

ULID ; `tenant_id` sur toute table métier ; montants en millimes entiers ; horodatages UTC + fuseau de l'officine ;
aucune suppression physique d'une opération validée ; suppression logique (`archived_at`) pour les référentiels sans
mouvement. Catégories d'intégrité :

| Catégorie | Règle | Mise en œuvre |
|---|---|---|
| **Immuable** | Insertion seule | Déclencheurs refusant UPDATE / DELETE |
| **Transitions contrôlées** | Changements de statut d'une liste fermée ; états finaux figés | Déclencheur de transition + audit |
| **Projection** | Recalculable depuis les registres ; jamais source de vérité | Reconstruction testée |
| **Référentiel** | Données de référence versionnées ; lecture seule pour l'officine si d'origine APEX | Paquets signés (§7.2) |
| **Modifiable** | Données de gestion courantes | Verrou optimiste, audit des champs sensibles |

### 7.2 Répartition des données entre bases [DÉCIDÉ D3 (isolation) ; moteur : ARB-01]

| Base | Contenu | Mode A | Mode B |
|---|---|---|---|
| **Base d'instance** | `tenants`, `users`, `memberships`, `sessions`, `subscriptions` (licences), `platform_audit_log`, `tenant_data_deletion_requests`, `backup_runs`, `instance_versions` | Base plateforme centrale | Petite base locale de l'installation |
| **Base d'officine** (une par pharmacie) | Toutes les autres tables métier, y compris `devices`, `settings`, `audit_log`, `domain_events` | **Une base physiquement distincte par pharmacie** ; plusieurs bases peuvent être hébergées sur un même serveur ou une même infrastructure | Une base locale par pharmacie installée |
| **Référentiels APEX** | `ref_*`, `app_modules`, `product_type_fields`, `metric_definitions`, lignes « APEX » des référentiels mixtes | Paquets versionnés importés dans chaque base d'officine | Paquets signés importés |

Avec une base par pharmacie, la colonne `tenant_id` est **conservée** (défense en profondeur, migration entre modes,
consolidation future). Les référentiels « mixtes » (spécialités, marques, axes et nœuds de classification, catégories
fiscales, catégories de charges, groupes de substitution…) distinguent les lignes d'origine APEX (lecture seule) et les
ajouts locaux [RECO, INC-27].

**Règles d'exploitation par base** [DÉCIDÉ D3 pour le principe ; modalités TECH, à confirmer par PT-05, PT-06, PT-09, PT-13] :

| Domaine | Règle | Tests |
|---|---|---|
| Isolation | Aucune table de données métier partagée entre pharmacies ; aucune requête ne lit ou n'écrit dans deux bases de pharmacie à la fois | MOF-01 à MOF-07, DEP-03 |
| Hébergement | Plusieurs bases de pharmacie sur un même serveur ou une même infrastructure autorisées ; aucun serveur dédié obligatoire ; la séparation porte sur les **bases**, pas sur les serveurs | DEP-13 |
| Routage | La base est déterminée côté serveur à partir de la pharmacie de la **session authentifiée**, jamais à partir d'un paramètre fourni par le client ; refus si la pharmacie est inconnue, suspendue ou non autorisée | DEP-03, DEP-13, PT-05 |
| Sauvegarde | Sauvegarde **indépendante par base**, planifiée et manuelle, tracée dans `backup_runs` | BKP-01, BKP-07 |
| Restauration | Restauration d'une base sans effet sur les autres bases du même serveur ; vérification de la chaîne de hachage après restauration | BKP-02, BKP-07 |
| Export et archivage | Export complet et archivage par pharmacie, dans un format documenté | BKP-07, LIC-04 |
| Migrations | Exécutées **séparément sur chaque base**, avec numéro de version par base (`instance_versions`), sauvegarde préalable, reprise idempotente ; une base en échec n'empêche pas les autres et reste fermée en écriture jusqu'à résolution | PT-06, UPD-07 |
| Administration | Création, suspension, export et suppression (procédure §6.3) d'une base par pharmacie ; le Super Admin gère l'accès à la base, pas son contenu (INC-13, ARB-09) | ADM-01 à ADM-10 |
| Mode local | Même règle : une base par pharmacie installée ; une installation multi-pharmacies reste possible avec des bases distinctes | DEP-03, LOC-01 |
| Moteur | **Non tranché** (ARB-01) : D1, SQLite, PostgreSQL ou autre, après comparatif PT-13 | PT-13 |

### 7.3 Tables créées ou modifiées par la v1.2

Les tables des extensions ne sont pas reprises ici (voir l'inventaire §7.4 et les documents sources). Seules les
**créations et modifications propres à la v1.2** sont détaillées.

#### 7.3.1 Nouvelles tables (4)

| Table | Raison d'être | Colonnes principales | Relations | Propriétaire | Contraintes d'intégrité | Conservation | Accès |
|---|---|---|---|---|---|---|---|
| `platform_audit_log` | Journal des actions du Super Admin et des opérations d'instance (D72) | id, actor_user_id, action (`TENANT_CREATE`, `ADMIN_ACCESS_CREATE`, `ACCESS_BLOCK`, `ACCESS_UNBLOCK`, `ACCESS_DELETE`, `ACCESS_RECREATE`, `INITIAL_CODE_REGENERATE`, `DATA_ACCESS_OPENED`, `DELETION_REQUEST_*`, `SUPER_ADMIN_RECOVERY`, `LICENSE_*`, `UPDATE_APPLIED`), tenant_id, cible, motif, ip, device, at, hash_prev, hash | n─1 `users`, n─0..1 `tenants` | M00 (instance) | **Immuable**, chaîné par hachage comme `audit_log` | Au moins la durée de vie de l'instance + durée légale [JURIDIQUE R52] | Super Admin (lecture) ; l'administrateur d'une officine lit les lignes de son officine |
| `tenant_data_deletion_requests` | Séparer la suppression des **données** de la suppression des **accès** (D72, P17) | id, tenant_id, demande_par, motif, piece_key, export_reversibilite_at, controle_conservation (JSON), valide_super_admin_par / le, valide_officine_par / le, fin_delai_grace, statut (`DEMANDEE`, `EN_DELAI`, `ANNULEE`, `EXECUTEE`, `REFUSEE`), certificat_key, execute_le | n─1 `tenants` | M00 (instance) | **Transitions contrôlées** ; CHECK(deux validations distinctes avant `EXECUTEE`) ; exécution impossible avant la fin du délai | Conservée après exécution (preuve) | Super Admin, administrateur de l'officine |
| `backup_runs` | Suivi des sauvegardes et restaurations (BKP, alerte de second support) | id, tenant_id (ou instance), type (`PLANIFIEE`, `MANUELLE`, `AVANT_MISE_A_JOUR`, `RESTAURATION`), cible (`LOCAL`, `SECOND_SUPPORT`, `CENTRAL`), debut, fin, taille, sha256, schema_version, statut, verifie_le, erreur | n─0..1 `tenants` | M00 (instance) | Insertion + statut final ; empreinte obligatoire si réussie | Paramétrable | Administrateur (mode B) ; Super Admin et administrateur (mode A) |
| `instance_versions` | Historique des versions logicielles et des migrations appliquées par base (UPD) | id, base (`INSTANCE` ou tenant_id), version_logicielle, migration, applique_le, duree_ms, statut, paquet_sha256, signature_verifiee (bool), sauvegarde_prealable_id | 0..1─1 `backup_runs` | M00 (instance) | **Immuable** | Durée de vie de l'instance | Administrateur, Super Admin (mode A) |

#### 7.3.2 Tables modifiées

| Table | Origine | Modification v1.2 | Raison | Contraintes ajoutées | Accès |
|---|---|---|---|---|---|
| `users` | Dossier §4.2 | + `platform_role` (`AUCUN`, `SUPER_ADMIN`), + `is_immutable`, + `access_status` (`CREE`, `ACTIF`, `BLOQUE`, `SUPPRIME`), + `must_change_password`, + `initial_code_hash`, + `initial_code_expires_at`, + `deleted_at` | D72 | Un seul `SUPER_ADMIN` ; déclencheur refusant toute modification ou suppression d'un compte `is_immutable` (hors renouvellement des facteurs) ; connexion par code initial seulement si `must_change_password` | Super Admin (accès administrateurs) ; chaque utilisateur (son profil) |
| `tenant_administrators` | EXT06 v1.1 §4.1 | + mode `DESIGNATION_SUPER_ADMIN` (remplace la proposition `SECOURS_APEX` d'EXT06, D72) | D72 | Qualification de pharmacien vérifiée toujours exigée ; justificatif obligatoire | Administrateur, Super Admin |
| `subscriptions` | Dossier §4.2 | + `mode_deploiement` (`SAAS`, `LOCAL`), + `licence_payload`, + `licence_signature`, + `licence_expires_at`, + `max_postes` | Licences du mode B | Signature vérifiée par clé publique APEX ; aucune suppression de données à l'expiration | Super Admin, administrateur (lecture) |
| `coverage_rules` (EXT02) | EXT02 §2.3 | **Renommage proposé : `shift_coverage_rules`** (INC-01, à valider) | Collision de nom avec EXT04 (INC-01) | — | Inchangé |
| `coverage_rules` (EXT04) | EXT04 §3.2 | **Renommage proposé : `payer_coverage_rules`** (INC-01, à valider) ; références `sale_line_payer_shares.coverage_rule_id` → `payer_coverage_rule_id` | INC-01 | — | Inchangé |
| `stock_buckets` | EXT05 §3.3 | Valeur `RESERVE` **supprimée** | D76 | La réservation reste dans `stock_reservations` (engagement logique) | Inchangé |
| `service_definitions` | EXT06 v1.1 §4.7 | **Simplifiée** : product_id, libellé FR / AR / EN, description, tarif (via `product_prices`) ou gratuit, actif, **reference_autorisation (facultative, informative)** ; retirés : habilitation obligatoire, patient requis, consentement, `donnees_saisies`, autorisation réglementaire **bloquante**, modèles livrés | D78 (INC-11, INC-32) | Aucun service livré par défaut ; seul l'administrateur crée et active | Administrateur (gestion) ; collaborateurs (lecture) |
| `service_acts` | EXT06 v1.1 §4.7 | **Simplifiée** : id, tenant_id, numero, product_id (type d'opération), **nom du client** (texte) ou `customer_id`, date et heure, collaborateur, `sale_line_id` si facturé, statut, `reverses_id` ; retirés : `mesures`, `observations`, `consent_id`, `duree_reelle_min` | D78 | Immuable ; correction par acte d'annulation | Collaborateurs habilités par la matrice ; lecture journalisée (le type de service peut révéler une information de santé, §10) |
| `chart_of_accounts` | EXT06 v1.1 §4.9 | `source` : `STANDARD` (modèle livré) / `LOCAL` ; le modèle standard est **actif et utilisable dès l'installation** ; toute modification est historisée (`audit_log`) | D83 (INC-12) | Un compte utilisé par une règle de passation active ne peut pas être désactivé sans remplacement ; contrôle de cohérence comptes ↔ règles avant activation d'une règle | Administrateur ; comptable habilité |
| `classification_nodes` | EXT06 v1.1 §4.4 | `source` : `STANDARD` (classification initiale livrée), `LOCAL`, `OFFICIEL` (si un référentiel officiel est confirmé, R43) ; nœuds `STANDARD` modifiables par l'administrateur, nœuds `OFFICIEL` en lecture seule | D84 | La classification interne n'alimente aucun champ réglementaire (`prescription_policy`, classe réglementaire, statut AMM) | Administrateur |
| `permissions` | EXT02 §2.2, EXT06 | + `stock.return.reintegrate` (attribuable uniquement par l'administrateur, par réglage individuel ou délégation nominative ; jamais par un rôle par défaut, D74) ; + `services.manage` réservé à l'administrateur (D78) | D74, D78 | Aucune attribution par rôle par défaut pour `stock.return.reintegrate` | — |
| `return_control_items` | EXT05 §3.3 | + `reintegre_par_employee_id`, + `autorisation_ref` (réglage individuel ou délégation) | D74 | CHECK(réintégré par ≠ exécutant de l'opération corrective) | — |
| `app_modules` | EXT06 v1.1 §4.1 | « Module non souscrit » : **données consultables et exportables**, nouvelles opérations refusées (au lieu de « inaccessible à tous ») | ARB-06, INC-38 | — | — |
| `settings` | Dossier §4.2 | Clés : `cancellation.reference_day` (EXT06), `remote_approval_ttl` (EXT06), `expense.validation_threshold` (D86), `backup.schedule`, `backup.second_target`, `deployment.connectors` (connecteurs distants activés) | D86, mode B | Clés de gouvernance modifiables par l'administrateur seul | Administrateur |

### 7.4 Inventaire consolidé des tables

Inventaire **généré à partir des documents sources** (extraction des tableaux de définition, numéros de ligne conservés
dans les fichiers de travail). Chaque table a **un seul module propriétaire**. La définition détaillée (colonnes,
contraintes, index) reste celle du document source indiqué ; la v1.2 ne la recopie pas, pour ne pas créer une seconde
source. Légende de la colonne « Base » : **I** = base d'instance, **O** = base d'officine, **R** = référentiel APEX importé,
**O+R** = référentiel mixte (lignes APEX en lecture seule + ajouts locaux).

| Module | Nb | Tables (document source) |
|---|---|---|
| M00 Plateforme et gouvernance | 26 | **DOSSIER v1.1** : `tenants` (I), `subscriptions` (I), `sites`, `users` (I), `memberships` (I), `roles` (O+R), `role_permissions`, `sessions` (I), `devices`, `settings`, `document_sequences`, `audit_log`, `domain_events`, `notifications`, `consents` · **EXT02** : `permissions` · **EXT03 v1.1** : `sensitive_access_log`, `retention_policies`, `compliance_profiles` (R) · **EXT05** : `delegable_permissions`, `delegations`, `delegation_usage`, `permission_overrides` · **EXT06 v1.1** : `tenant_administrators`, `app_modules` (R), `permission_exceptions` |
| M02 Vente | 7 | **DOSSIER v1.1** : `sales`, `sale_lines`, `sale_returns`, `sale_return_lines`, `payments`, `payment_allocations`, `prescriptions` |
| M02b Caisse nominative | 8 | **DOSSIER v1.1** : `cash_registers`, `cash_drawers`, `cash_sessions`, `cash_counts`, `cash_count_lines`, `cash_movements`, `cash_transfers`, `drawer_events` |
| M03 / M11 Achats et commandes | 7 | **DOSSIER v1.1** : `purchase_orders`, `purchase_order_lines`, `po_suggestions`, `goods_receipts`, `goods_receipt_lines`, `supplier_invoices`, `supplier_returns` |
| M04 Catalogue | 24 | **DOSSIER v1.1** : `ref_products` (R), `ref_dci` (R), `ref_laboratories` (R), `ref_forms` (R), `ref_barcodes` (R), `ref_updates` (R), `products`, `product_barcodes`, `product_prices_history`, `categories` · **EXT06 v1.1** : `ref_product_substances` (R), `product_substances`, `brands` (O+R), `product_units`, `product_variant_attributes`, `product_type_fields` (R), `product_prices`, `substitution_groups` (O+R), `substitution_group_members` (O+R), `classification_axes` (O+R), `classification_nodes` (O+R), `product_classifications`, `tax_categories` (O+R), `tax_rates` (O+R) |
| M04 (services) Catalogue — services | 3 | **EXT06 v1.1** : `service_definitions`, `service_acts`, `service_act_consumables` |
| M05 Stock | 7 | **DOSSIER v1.1** : `lots`, `stock_movements`, `stock_levels`, `stock_reservations`, `inventory_sessions`, `inventory_counts` · **EXT05** : `stock_buckets` (O+R) |
| M06 / M07 Fournisseurs, clients | 6 | **DOSSIER v1.1** : `suppliers`, `supplier_products`, `customers`, `customer_treatments`, `loyalty_ledger`, `party_ledger` |
| M08 Finance | 18 | **DOSSIER v1.1** : `bank_accounts`, `payment_instruments`, `instrument_status_history`, `bank_deposits`, `due_dates`, `expenses` · **EXT06 v1.1** : `supplier_credit_notes`, `supplier_advances`, `expense_categories` (O+R), `recurring_charge_templates`, `loans`, `loan_schedule_lines`, `fixed_assets`, `bank_statements`, `bank_statement_lines`, `bank_reconciliations`, `payroll_imports`, `payroll_import_lines` |
| M09 Réglementés | 4 | **DOSSIER v1.1** : `controlled_classes`, `controlled_register`, `controlled_rectifications`, `controlled_periodic_reports` |
| M10 Péremptions et pertes | 3 | **DOSSIER v1.1** : `expiry_alert_rules`, `loss_records`, `destruction_records` |
| M12 Rapports et métriques | 8 | **DOSSIER v1.1** : `metrics_daily`, `product_demand_stats` · **EXT03 v1.1** : `rx_facts`, `rx_metrics_monthly`, `report_query_log`, `export_requests` · **EXT06 v1.1** : `metric_definitions` (R), `financial_snapshots` |
| M13 PHARM'OS AI | 5 | **DOSSIER v1.1** : `ai_runs`, `ai_recommendations`, `ai_conversations`, `ai_messages`, `ai_feedback` |
| M15 RH et équipes | 42 | **EXT02** : `employees`, `employee_private`, `employee_compensation`, `contracts`, `employee_documents`, `teams`, `team_leads`, `positions`, `employee_assignments`, `qualifications` (O+R), `employee_qualifications`, `skills` (O+R), `employee_skills`, `habilitations` (O+R), `employee_habilitations`, `opening_hours`, `public_holidays`, `duty_periods`, `shift_templates`, `rotation_patterns`, `shift_coverage_rules` (nom proposé ; `coverage_rules` dans EXT02 §2.3), `schedules`, `shifts`, `shift_swap_requests`, `attendance_records`, `attendance_days`, `overtime_records`, `labor_rules`, `leave_types` (O+R), `leave_requests`, `leave_approvals`, `leave_ledger`, `leave_balances`, `quality_events`, `kpi_definitions` (O+R), `kpi_profiles`, `kpi_profile_items`, `performance_indicators`, `performance_reviews`, `employee_comments`, `training_catalog`, `training_records` |
| M16 Laboratoire | 3 | **EXT02** : `lab_preparations`, `lab_preparation_inputs`, `lab_controls` |
| M17 Ordonnances et prescripteurs | 20 | **EXT03 v1.1** : `medical_specialties` (O+R), `prescribers`, `prescriber_specialties`, `prescriber_history`, `prescriber_duplicate_candidates`, `prescriber_merges`, `patient_profiles`, `patient_duplicate_candidates`, `prescription_lines`, `prescription_dispensations`, `prescription_substitutions`, `prescription_documents`, `prescription_versions`, `prescription_demand`, `prescription_rules`, `prescription_adjustments`, `prescription_line_balances`, `prescription_completeness_items`, `ocr_jobs`, `ocr_extractions` |
| M18 Tiers payant | 32 | **EXT04** : `payer_coverage_rules` (nom proposé ; `coverage_rules` dans EXT04 §3.2), `ref_payers` (R), `payers`, `payer_pharmacy_ids`, `source_documents`, `payer_agreements`, `agreement_suspensions`, `agreement_required_documents`, `reference_lists` (O+R), `reference_list_items` (O+R), `rejection_reasons` (O+R), `document_templates` (O+R), `patient_coverages`, `coverage_status_history`, `coverage_authorizations`, `sale_payer_selections`, `sale_line_payer_shares`, `reimbursement_documents_issued`, `claims`, `claim_lines`, `claim_events`, `claim_documents`, `claim_adjustments`, `claim_batches`, `claim_batch_items`, `payer_responses`, `payer_response_lines`, `payer_remittances`, `remittance_allocations`, `payer_deductions`, `claim_disputes`, `dispute_events` |
| M19 Opérations correctives | 12 | **EXT05** : `corrective_operations`, `corrective_operation_lines`, `sale_line_correction_totals`, `correction_reasons` (O+R), `correction_requests`, `correction_request_events`, `return_control_items`, `return_control_checklists`, `refunds`, `store_credit_vouchers`, `store_credit_movements`, `payer_regularizations` |
| M20 Comptabilité | 9 | **EXT06 v1.1** : `chart_of_accounts`, `journals`, `posting_rules`, `accounting_periods`, `journal_entry_drafts`, `journal_entries`, `journal_entry_lines`, `accounting_exceptions`, `accounting_exports` |
| **Total sources** | **244** | 243 noms distincts dans les sources ; `coverage_rules` désigne deux tables (EXT02, EXT04), comptées séparément (INC-01) |

**Tables à statut particulier** (toutes les autres sont « conservées » telles que définies dans leur source) :

| Table | Module | Source | Statut v1.2 |
|---|---|---|---|
| `subscriptions` | M00 | DOSSIER v1.1 §4.2 | MODIFIÉE v1.2 : licence signée (mode B) |
| `users` | M00 | DOSSIER v1.1 §4.2 | MODIFIÉE v1.2 : cycle de vie des accès (D72) |
| `product_prices_history` | M04 | DOSSIER v1.1 §4.3 | REMPLACÉE → `product_prices` (EXT06) |
| `categories` | M04 | DOSSIER v1.1 §4.3 | REMPLACÉE → classification multi-axes (EXT06) |
| `cash_registers` | M02b | DOSSIER v1.1 §4.5 | Définition EXT02 (la ligne du dossier §4.5 est retirée, INC-02) |
| `cash_drawers` | M02b | DOSSIER v1.1 §4.5 | Définition EXT02 (dossier §4.5 renvoyait déjà à EXT02, INC-02) |
| `cash_sessions` | M02b | DOSSIER v1.1 §4.5 | Définition EXT02 (dossier §4.5 renvoyait déjà à EXT02, INC-02) |
| `cash_counts` | M02b | DOSSIER v1.1 §4.5 | Définition EXT02 (dossier §4.5 renvoyait déjà à EXT02, INC-02) |
| `cash_count_lines` | M02b | DOSSIER v1.1 §4.5 | Définition EXT02 (dossier §4.5 renvoyait déjà à EXT02, INC-02) |
| `cash_movements` | M02b | DOSSIER v1.1 §4.5 | Définition EXT02 (dossier §4.5 renvoyait déjà à EXT02, INC-02) |
| `cash_transfers` | M02b | DOSSIER v1.1 §4.5 | Définition EXT02 (dossier §4.5 renvoyait déjà à EXT02, INC-02) |
| `drawer_events` | M02b | DOSSIER v1.1 §4.5 | Définition EXT02 (dossier §4.5 renvoyait déjà à EXT02, INC-02) |
| `sale_returns` | M02 | DOSSIER v1.1 §4.5 | REMPLACÉE → `corrective_operations` (EXT05 D70, proposée) |
| `sale_return_lines` | M02 | DOSSIER v1.1 §4.5 | REMPLACÉE → `corrective_operation_lines` (EXT05 D70, proposée) |
| `prescriptions` | M02 | DOSSIER v1.1 §4.5 | REMPLACÉE par la version EXT03 v1.1 (si EXT03 validée) |
| `permissions` | M00 | EXT02 §2.2 | MODIFIÉE v1.2 : + `stock.return.reintegrate` (D74) |
| `shift_coverage_rules` (EXT02 : `coverage_rules`) | M15 | EXT02 §2.3 | **RENOMMAGE PROPOSÉ** (INC-01, à valider) ; la table homonyme d'EXT04 devient `payer_coverage_rules` (M18) ; les sources EXT02 et EXT04 ne sont pas modifiées |
| `corrective_operations` | M19 | EXT05 §3.1 | MODIFIÉE (EXT06 v1.1 AN13) |
| `stock_buckets` | M05 | EXT05 §3.3 | MODIFIÉE v1.2 : sans `RESERVE` (D76) |
| `payer_regularizations` | M19 | EXT05 §3.4 | **À RÉEXAMINER** (INC-36) |
| `tenant_administrators` | M00 | EXT06 v1.1 §4.1 | MODIFIÉE v1.2 : mode `DESIGNATION_SUPER_ADMIN` (D72) |
| `classification_nodes` | M04 | EXT06 v1.1 §4.4 | MODIFIÉE v1.2 : source `STANDARD` / `LOCAL` (D84) |
| `service_definitions` | M04 (services) | EXT06 v1.1 §4.7 | MODIFIÉE v1.2 : simplifiée (D78) |
| `service_acts` | M04 (services) | EXT06 v1.1 §4.7 | MODIFIÉE v1.2 : simplifiée (D78) |
| `chart_of_accounts` | M20 | EXT06 v1.1 §4.9 | MODIFIÉE v1.2 : modèle standard utilisable (D83) |

### 7.5 Relations principales (résumé)

```
INSTANCE   tenants 1─n memberships n─1 users ; users(SUPER_ADMIN) ─► platform_audit_log
           tenants 1─n tenant_data_deletion_requests ; tenants 1─1 subscriptions (abonnement ou licence signée)
           backup_runs ; instance_versions 0..1─1 backup_runs
OFFICINE   tenant_administrators n─1 employees ; employees 1─n memberships (lien utilisateur ↔ collaborateur, EXT02)
           permissions 1─n role_permissions, permission_overrides ; delegations ─ opérations ⊂ permissions ; permission_exceptions
           ref_products ─(paquet)─► products 1─n product_units, product_prices, product_classifications, product_substances
           products 1─n lots 1─n stock_movements (registre) ─► stock_levels (projection par état) ; stock_reservations (logique)
           sales 1─n sale_lines n─1 lots ; sale_lines 0..1─1 prescription_lines (EXT03) ; sale_lines 1─n sale_line_payer_shares (EXT04)
           sales 1─n corrective_operations 1─n corrective_operation_lines ─► stock_movements, refunds, prescription_adjustments,
                 claim_adjustments, return_control_items (EXT05)
           cash_sessions 1─n cash_movements ; payments ─► party_ledger, due_dates (M08)
           domain_events (outbox) ─► posting_rules ─► journal_entries 1─n journal_entry_lines (M20) ; accounting_exceptions
           domain_events ─► metrics_daily (metric_definitions) ; financial_snapshots ; rx_facts (EXT03)
           audit_log (chaîné) ; sensitive_access_log (chaîné, EXT03)
```

---

## 8. Flux

### 8.1 Flux de stock (source unique : `stock_movements`)

```
Réception (M03) ─► mouvement RECEIPT par lot ─► lot (péremption, coût) ─► état DISPONIBLE
Vente (M02) ─► allocation FEFO (DISPONIBLE − réservations actives, lot ACTIF non périmé) ─► mouvement SALE
Réservation (reliquat EXT03, vente en attente) ─► stock_reservations : engagement LOGIQUE, aucun mouvement [D76]
Annulation / retour (M19) ─► mouvement CANCEL_SALE / RETURN_IN ─► état EN_CONTRÔLE (jamais DISPONIBLE directement)
    └─► décision : RÉINTÉGRÉ (stock.return.reintegrate, D74) │ QUARANTAINE │ PERTE (loss_records, M10)
Péremption / rappel ─► lot BLOQUÉ / PÉRIMÉ / RAPPELÉ ─► mouvement LOSS ou retour fournisseur ; destruction avec PV
Inventaire ─► écarts validés ─► mouvements d'ajustement motivés
Laboratoire (M16) / service (M04) ─► consommation de lots liée à la préparation ou à l'acte
```

| Règle | Contrôle | Source |
|---|---|---|
| Niveau de stock = Σ mouvements, recalculable | Test de recalcul (ST-04) | Dossier ; EXT05 |
| États `DISPONIBLE`, `EN_CONTROLE`, `QUARANTAINE`, `PERTE` ; **pas de `RESERVE`** | Référentiel `stock_buckets` | EXT05 ; D76 |
| Disponible vendable = `DISPONIBLE` − réservations actives | Calculé, jamais stocké | EXT06 §5.4 ; D76 |
| Quantité reçue figée sur le lot ; disponible lue dans `stock_levels` | Reçue = états + sorties nettes (CAT-11) | EXT06 §5.4 |
| Stock négatif bloqué ; décrément conditionnel atomique | Garde dans l'unité de travail | Dossier §6.3 |
| Aucune réintégration automatique en stock vendable | — | EXT06 AN6 ; D74 |

### 8.2 Flux de dispensation

```
Caisse (session nominative ouverte, collaborateur identifié, droits effectifs)
  ├─ comptoir : scan ─► contrôles produit (ACTIF, prescription_policy) ─► FEFO ─► lignes
  └─ ordonnance (EXT03, si validée) : prescripteur, patient (volet cloisonné), lignes prescrites ↔ délivrées,
     complétude A / M / R (D29), substitution (groupes validés, EXT06), quantités et durées [JURIDIQUE R17]
  ─► tiers payant (EXT04, si convention active) : couverture, moteur de parts, invariant total = parts
  ─► VALIDATION en une unité de travail atomique : vente, lignes, mouvements de stock, délivrances, registre réglementé
     (si applicable), parts et créances, paiements, mouvements de caisse, audit, événements (outbox)
  ─► après validation (asynchrone) : métriques, rx_facts, écritures M20, notifications, IA
```

Réglementés : pharmacien pointé (D24 proposée, [JURIDIQUE R19]) ; registre généré dans la même transaction (EXT03 v1.1
§16) ; jamais hors ligne ; jamais validés à distance (D73).

### 8.3 Flux financiers

| Événement | Chiffre d'affaires | Encaissement | Créance | Dette | Source |
|---|---|---|---|---|---|
| Vente comptant | + (date d'opération) | + caisse | — | — | Dossier ; EXT06 §5.6 |
| Vente à crédit | + | — | + client (`party_ledger`) | — | Dossier |
| Vente tiers payant | + total, une seule fois | + part patient | + organisme | — | EXT04 §7.2 |
| Règlement d'un organisme | **aucun effet** | + banque (CA encaissé) | − organisme | — | EXT04 ; EXT06 §5.6 règle 1 |
| Annulation / retour | − (date de la correction) | − restitution (session de l'exécutant) | régularisation organisme | — | EXT05 ; EXT06 MT6 |
| Facture fournisseur (marchandises) | — | — | — | + fournisseur ; coût du stock | Dossier ; EXT06 §4.8 |
| Facture de charge / dépense de caisse | — | − caisse si payée en caisse | — | + fournisseur sinon | EXT06 §4.8 ; INC-26 |
| Échéance d'emprunt | — | − banque | — | − capital ; intérêts en charge | EXT06 W10 |
| Paie importée validée | — | — | — | + salaires, organismes sociaux | EXT06 W12 ; D81 |

Grand livre auxiliaire des tiers unique : `party_ledger`. Trésorerie = caisses + coffre + banques (rapprochées et non
rapprochées distinguées). Rapprochement bancaire **proposé automatiquement, confirmé par un humain**, écarts en exceptions
(D85). Seuil de validation des dépenses paramétrable par l'administrateur (D86). Les cinq grandeurs CA, CA encaissé, marge
commerciale, marge réalisée et résultat de gestion restent distinctes (EXT06 v1.1 §5.6.1).

### 8.4 Flux comptables (M20) [DÉCIDÉ D80 ; modalités EXT06 v1.1]

```
Opération métier validée ─► événement (outbox) ─► règle de passation (version active à la date)
   ─► écriture générée en UNE unité de travail : débit = crédit sur toutes les lignes, période ouverte, comptes actifs,
      numéro de séquence, en-tête scellé ─► écriture VALIDÉE (immuable)
   └─ échec ─► AUCUNE écriture ; exception comptable (origine, montant, responsable, échéance) ;
      l'opération métier reste validée et intacte (EXT06 EX6)
Correction ─► contre-passation exacte + nouvelle écriture (même correlation_id) ; jamais de modification
Brouillons (opérations diverses) ─► EN_COURS ─► SOUMIS ─► validés par une autre personne ─► écriture
Clôture mensuelle ─► contrôles (événements tous traités, aucune exception bloquante, équilibre, intégrité, réconciliations)
   ─► VERROUILLÉE ─► export ─► EXPORTÉE (réouverture interdite après export final, D82)
```

Mention obligatoire sur tous les écrans, états et exports de M20 : **« Comptabilité à titre indicatif »** (D80).
Plan comptable standard livré, **actif et utilisable dès l'installation**, adaptable par l'administrateur, modifications
historisées, cohérence avec les règles de passation contrôlée avant activation (D83). Les notions et l'usage officiel restent
soumis à [JURIDIQUE R46].

### 8.5 Flux correctifs (M19)

```
Demande (collaborateur) ─► droits, plafonds, délais :
   même jour de référence (calendaire par défaut ; exploitation si activé, D75) ─► exécution par le collaborateur habilité
   au-delà ─► dérogation de l'administrateur (présente ou à distance, D73)
   produit réglementé ─► circuit spécifique (quatre yeux, registre) ; jamais de dérogation à distance
─► Exécution en une unité de travail : opération corrective, lignes, mouvements inverses (EN_CONTRÔLE), restitutions
   (même moyen que le paiement, D59 proposée), avoirs, régularisation tiers payant, ajustement d'ordonnance (D42),
   audit, événements
─► Contrôle de réintégration : décision par une personne explicitement autorisée par l'administrateur (D74)
─► Comptabilité et métriques : rattachées à la date de la correction (MT6) ; la vente d'origine n'est jamais modifiée
```

### 8.6 Flux d'audit

| Journal | Contenu | Propriété | Source |
|---|---|---|---|
| `platform_audit_log` | Actions du Super Admin et de l'instance | Immuable, chaîné | v1.2 (D72) |
| `audit_log` | Écritures sensibles de l'officine (avant / après, motif, collaborateur, appareil) | Immuable, chaîné | Dossier §9.3 |
| `sensitive_access_log` | Lectures de données patient, ordonnances, documents, exports nominatifs | Immuable, chaîné | EXT03 v1.1 (D36 proposée) |
| `report_query_log` | Requêtes statistiques (détection différentielle) | Immuable | EXT03 v1.1 |
| `delegation_usage` | Chaque utilisation d'une délégation | Insertion seule | EXT05 |
| `permission_exceptions` | Dérogations demandées, accordées, consommées | Transitions contrôlées | EXT06 v1.1 |
| Historiques métier | `prescriber_history`, `prescription_versions`, `claim_events`, `correction_request_events`, `coverage_status_history`, `instrument_status_history`… | Immuables | Extensions |

Vérification de la chaîne de hachage : quotidienne, après chaque restauration (BKP-02) et avant chaque export définitif.

### 8.7 Fonctionnement déconnecté

| Situation | Autorisé | Interdit | Source |
|---|---|---|---|
| Poste déconnecté du serveur (PWA) | Ventes au comptoir ; première délivrance d'une ordonnance simple | Réglementés, tiers payant, corrections (demande locale seulement), crédit au-delà du plafond connu | D10, D39, D51, D64 (proposées) |
| Mode B sans Internet | **Tout le métier** | Services distants (IA, OCR distant, messagerie, téléchargement de référentiels) | Exigence v1.2 |
| Mode A, serveur injoignable | Comme « poste déconnecté » | — | Dossier §6.7 |

---

## 9. Règles métier consolidées par domaine

Cette section **ne recopie pas** les règles détaillées des extensions : elle indique pour chaque domaine la règle de
référence, sa source et son statut. En cas de divergence, la règle retenue est celle de la colonne « Référence v1.2 ».

### 9.1 Catalogue (M04)

| Sujet | Référence v1.2 | Source | Statut |
|---|---|---|---|
| Référentiel global APEX + fiche locale liée ; aucune modification locale des données de référence sans surcharge motivée | Dossier §2.2 ; EXT06 §5.3 | CD | [PROPOSÉ] (dossier) |
| Types de produits (médicament, dispositif médical, accessoire, parapharmacie, cosmétique, complément, préparation, service, autre) | EXT06 §4.2 | CD | [PROPOSÉ] ; [JURIDIQUE R42] |
| Substances multiples, unités de vente, variantes, marques | EXT06 §4.2 | CD | [PROPOSÉ] |
| Classification multi-axes (remplace `categories`) ; **classification thérapeutique standard livrée, modifiable par l'administrateur, distincte des données réglementaires** | EXT06 §4.4 ; **D84** | CD + AP | [DÉCIDÉ] (D84) ; [JURIDIQUE R43] pour un référentiel officiel |
| Politique de prescription (`prescription_policy`) | EXT03 v1.1 §3.4 | CD | [PROPOSÉ] |
| Substitution : groupes validés par un pharmacien, structure unique `substitution_groups` | EXT06 §4.3 (remplace les champs d'EXT03, INC-21) | CD | [PROPOSÉ] ; [JURIDIQUE R4] |
| Fiscalité : catégories historisées, **aucun taux par défaut**, taux figé sur chaque ligne | EXT06 §4.5 | CD | [PROPOSÉ] ; [JURIDIQUE R44] |
| Prix : table unique historisée `product_prices` ; prix réglementé importé, non modifiable | EXT06 §4.2 ; dossier §9.5 | CD | [PROPOSÉ] ; [JURIDIQUE R51] |
| Médicament vendable seulement si validé par un pharmacien (`ACTIF`) | EXT06 §5.3 | CD | [PROPOSÉ] |
| **Services** : page de gestion ; l'administrateur définit ses services ; aucune liste imposée ; enregistrement du type d'opération, du nom du client et de la date ; aucun dossier clinique ni mesure structurée | **D78** (remplace EXT06 §4.7, §5.5 sur ces points, INC-11) | AP | [DÉCIDÉ] ; [JURIDIQUE R47] |

### 9.2 Stock et lots (M05)

| Sujet | Référence v1.2 | Source | Statut |
|---|---|---|---|
| Registre immuable ; niveaux = projection | Dossier §4.4 | CD | Principe confirmé |
| FEFO ; lot périmé, bloqué ou rappelé jamais alloué | Dossier ; EXT06 §5.4 | CD | Principe confirmé |
| États `DISPONIBLE`, `EN_CONTROLE`, `QUARANTAINE`, `PERTE` dès le Lot 3 | EXT05 (D71) sans `RESERVE` | CD + AP | D71 [PROPOSÉ] ; suppression de `RESERVE` [DÉCIDÉ D76] |
| Réservation logique (`stock_reservations`) : reliquats d'ordonnance (48 h proposés, D40), ventes en attente | **D76** ; EXT03 v1.1 D40 | AP + CD | [DÉCIDÉ] (principe) ; durée [PROPOSÉ] |
| Contrôle de réintégration ; délai de décision 48 h proposé (D68) | EXT05 workflow D | CD | [PROPOSÉ] |
| Réintégration par une personne explicitement autorisée par l'administrateur | **D74** (§6.8) | AP | [DÉCIDÉ] ; interprétation AV (INC-10) |
| Retour de médicament et remise en vente | EXT05 ; EXT03 v1.1 R14 | CD | [JURIDIQUE R14, R34, R35] |

### 9.3 Vente, caisse, ordonnances, tiers payant (M02, M02b, M17, M18)

| Sujet | Référence v1.2 | Source | Statut |
|---|---|---|---|
| Caisse nominative : une session par collaborateur et par tiroir, comptage à l'aveugle, écart validé par un tiers | EXT02 §7 | CD | Intégrée (v1.1) ; D19, D20 [PROPOSÉ] |
| Remises plafonnées, paiements multiples, ventes en attente | Dossier §2.2 | CD | Conception v1.1 |
| Lien vente ↔ ordonnance ligne à ligne ; `sales.prescription_id` supprimé | EXT03 v1.1 §3.4 | CD | [PROPOSÉ] (INC-03) |
| Complétude A / M / R, délai de complétion 7 jours | EXT03 v1.1 D29, D43 | CD | [PROPOSÉ] ; [JURIDIQUE R2] |
| Tiers payant : modèle complet livré, aucune convention active par défaut ; remboursement par l'assuré pendant une suspension | EXT04 D44, D56 | CD | [PROPOSÉ] ; conflit avec D6 / D37 (INC-06) ; [JURIDIQUE R23, R31] |
| Invariant : total de la vente = part patient + parts organismes | EXT04 | CD | [PROPOSÉ] |
| Aucun taux, plafond ou liste de remboursement fourni par défaut | EXT04 R31 | CD | Principe confirmé |

### 9.4 Opérations correctives (M19)

| Sujet | Référence v1.2 | Source | Statut |
|---|---|---|---|
| Modèle correctif unifié (annulation, retour, remboursement autonome) ; remplace `sale_returns` | EXT05 D70 | CD | [PROPOSÉ] |
| **Jour calendaire** comme règle générale ; option **jour d'exploitation** activée explicitement par l'administrateur | **D75** ; EXT06 AN9–AN13 | AP | [DÉCIDÉ] ; [JURIDIQUE R49] |
| Au-delà du jour de référence : dérogation de l'administrateur seul | EXT06 AN3 (remplace EXT05 C5, INC-30) | CD | Validé dans le principe (EXT06) |
| Produits sensibles et réglementés : circuit spécifique, jamais d'annulation par un non-pharmacien | EXT06 AN5 ; EXT05 §4.7 | CD | Validé dans le principe ; [JURIDIQUE R35, R36] |
| Aucune réintégration automatique en stock vendable | EXT06 AN6 | CD | Validé dans le principe |
| Restitution par le même moyen que le paiement ; carte confirmée manuellement | EXT05 D59, D60 | CD | [PROPOSÉ] ; [JURIDIQUE R40] |
| Avoirs : durée de validité après R39 | EXT05 D69 | CD | [PROPOSÉ] (ouverte) |
| Corrections hors ligne : demande locale seulement | EXT05 D64 | CD | [PROPOSÉ] |

### 9.5 Finance (M08)

| Sujet | Référence v1.2 | Source | Statut |
|---|---|---|---|
| Une seule table pour toutes les factures reçues (marchandises, charges, immobilisations) | EXT06 §4.8 | CD | Validé dans le principe |
| `expenses` réservée aux dépenses payées en caisse, liée au mouvement de caisse | EXT06 §4.8 ; EXT02 `cash_movements` (INC-26) | CD | Validé dans le principe |
| Seuil de validation des dépenses configurable par l'administrateur | **D86** | AP | [DÉCIDÉ] |
| Rapprochement bancaire proposé automatiquement, confirmé par un humain ; écarts en exceptions | **D85** | AP | [DÉCIDÉ] |
| Emprunts : tableau d'amortissement importé (recommandé) ou calculé puis comparé | EXT06 W10 | CD | Validé dans le principe |
| Immobilisations : registre ; durées et méthodes paramétrables, sans valeur présumée | EXT06 §4.8 | CD | [JURIDIQUE R45] |
| **Paie : import des montants mensuels uniquement**, pas de moteur de paie ; export des variables RH conservé (D22) | **D81** ; EXT02 D22 | AP + CD | [DÉCIDÉ] |
| Règlement d'organisme = encaissement soldant une créance, jamais une vente | EXT04 §7.2 ; EXT06 §5.6 | CD | Validé dans le principe |

### 9.6 Comptabilité (M20)

| Sujet | Référence v1.2 | Source | Statut |
|---|---|---|---|
| Partie double ; mention **« Comptabilité à titre indicatif »** | **D80** | AP | [DÉCIDÉ] |
| Écritures validées immuables ; correction par contre-passation et nouvelle écriture ; brouillons séparés | D80 ; EXT06 v1.1 CP1–CP13, W15 | AP + CD | [DÉCIDÉ] |
| Une seule écriture par événement et par version de règle ; idempotence ; aucun doublon après relance | EXT06 v1.1 CP4–CP7 | CD | Validé dans le principe |
| Exceptions comptables tracées jusqu'à résolution ; une erreur comptable ne touche jamais l'opération métier | EXT06 v1.1 EX1–EX11, W14 | CD | Validé dans le principe |
| **Plan comptable standard livré, actif, adaptable par l'administrateur** ; pas de validation préalable obligatoire par un expert-comptable ; modifications tracées ; cohérence avec les règles de passation | **D83** (remplace EXT06 D83 proposée et `chart_of_accounts.source = MODELE` inactif, INC-12) | AP | [DÉCIDÉ] ; [JURIDIQUE R46] pour l'usage officiel |
| Réouverture d'une période : administrateur, motif obligatoire ; interdite après export final | **D82** | AP | [DÉCIDÉ] |
| Contrôles de clôture, contrôles d'export | EXT06 v1.1 W13, CP13 | CD | Validé dans le principe |

### 9.7 Couche métriques et rapports (M12, M01)

| Sujet | Référence v1.2 | Source | Statut |
|---|---|---|---|
| Source de calcul unique : `metric_definitions`, `metrics_daily`, `financial_snapshots`, `rx_facts` | EXT06 v1.1 MT1–MT11 (remplace la structure du dossier §4.11, INC-23) | CD | Validé dans le principe |
| CA, CA encaissé, marge commerciale, marge réalisée, résultat de gestion distincts | EXT06 v1.1 §5.6.1 | CD | Validé dans le principe ; notions [JURIDIQUE R46] |
| Corrections rattachées à leur date d'exécution ; jours écoulés jamais modifiés | EXT06 v1.1 MT6 | CD | Validé dans le principe |
| Petits effectifs : k = 5 + contrôles anti-croisement | EXT03 v1.1 D34 (remplace k = 3, INC-28) | CD | [PROPOSÉ] ; [JURIDIQUE R21] |
| **Marges et rentabilité G25 soumises à une permission spécifique** | **D79** | AP | [DÉCIDÉ] |

### 9.8 Ressources humaines et laboratoire (M15, M16)

Règles d'EXT02 inchangées (intégrées en v1.1) : droits effectifs, planning, présence, congés (circuit N1 → N2), KPI à axes
séparés, évaluations immuables, préparateur ≠ contrôleur au laboratoire. Décisions D17 à D26 **non arbitrées** ; toutes les
valeurs du droit du travail restent **[JURIDIQUE]** (D17, D26).

---

## 10. Données de santé et confidentialité

### 10.1 Niveaux de confidentialité (deux échelles distinctes, à ne pas confondre)

| Échelle | Niveaux | Données | Source |
|---|---|---|---|
| **Collaborateurs** (EXT02 §11.1) | N0 public interne · N1 professionnel · N2 évaluation · N3 personnel · N4 salarial et médical | Dossier RH, KPI, rémunération, paie importée (`payroll_import_lines` = N4) | CD |
| **Patients** (EXT03 v1.1 §9) | N1 commercial · N2 identification patient · N3 médical | Fiche client, volet patient, ordonnances, délivrances, scans | CD |

Les deux échelles utilisent les mêmes codes N1 à N3 avec des sens différents [INC-29]. **Proposition** : préfixer dans
les écrans et la documentation technique — `RH-N0` à `RH-N4` et `PAT-N1` à `PAT-N3` — sans changer les règles [RECO].

### 10.2 Règles de séparation

| # | Règle | Source |
|---|---|---|
| S1 | Les données PAT-N2 / PAT-N3 et RH-N3 / RH-N4 sont cloisonnées (tables ou colonnes dédiées), chiffrées au niveau applicatif et lues uniquement avec la permission correspondante ; chaque lecture est journalisée (`sensitive_access_log`) | EXT02 ; EXT03 v1.1 |
| S2 | Aucune donnée de santé ni donnée individuelle de paie n'est transmise à un service d'IA externe ; seuls des agrégats sont autorisés | Dossier §7.5 ; EXT03 v1.1 R22 ; EXT04 D58 ; EXT06 |
| S3 | Aucun diagnostic stocké pour le tiers payant | EXT04 D49 |
| S4 | **Services (D78)** : seul le type d'opération, le nom du client et la date sont enregistrés. Le **type de service** peut toutefois révéler une information de santé (ex. une injection) : `service_acts` est donc traité comme donnée sensible — lecture réservée, journalisée, exclue de l'IA nominative [RECO, INC-32] | D78 ; analyse v1.2 |
| S5 | Les exceptions comptables ne contiennent que des références, jamais de donnée de santé ou de paie | EXT06 EX11 |
| S6 | Statistiques : seuil de petits effectifs et contrôles anti-croisement | EXT03 v1.1 D34 |
| S7 | En **mode B**, les données de santé restent sur le matériel de l'officine ; chiffrement des champs et des fichiers sensibles, verrouillage de session, guide d'installation sécurisée | v1.2 [TECH] |
| S8 | Consentements datés, révocables, prouvés (`consents`) | Dossier §9.4 |

Obligations à confirmer : **[JURIDIQUE R1, R10, R20, R29, R50]** (déclaration INPDP, hébergement, droits du patient,
transmission aux assureurs, paie).

---

## 11. Architecture IA (M13)

Principe inchangé (dossier §7) : **chiffres déterministes, IA explicative** ; lecture seule de la couche métriques et de
vues dédiées ; création de **brouillons** uniquement ; aucune exécution sensible ; chaque recommandation traçable jusqu'à
ses données.

| Élément | Règle consolidée | Source |
|---|---|---|
| Analyseurs | Dossier (A à …), RH (I à O, EXT02), ordonnances (P1 à P5, EXT03 v1.1), tiers payant (TP-A à TP-E, EXT04), antifraude des corrections (EXT05 §4.8), finance et rentabilité (EXT06 §5.12) | CD |
| Données transmises au LLM | Liste blanche de champs ; agrégats ; prescripteurs pseudonymisés ; jamais de donnée PAT-N2 / PAT-N3, RH-N3 / RH-N4, services nominatifs | EXT03 v1.1 S-IA ; v1.2 |
| Décisions | Jamais de modification de prix, de commande, de droit, de créance, d'écriture comptable ou de planning publié sans décision humaine | Dossier ; extensions |
| Rentabilité | Les indicateurs G25 alimentent des recommandations ; marges soumises à `analytics.margin.read` (D79) | EXT06 ; D79 |
| **Double mode** | Le LLM est un **connecteur sortant optionnel** (port `AiGateway`) : désactivé par défaut en mode B ; les analyseurs déterministes et le centre de recommandations restent disponibles sans LLM ; la conversation et les explications rédigées nécessitent le connecteur | v1.2 [TECH] |
| OCR | Service APEX optionnel, repli sur l'appareil ; jamais via un LLM (D41 proposée) | EXT03 v1.1 |

---

## 12. Sécurité

| Domaine | Règle | Source |
|---|---|---|
| Authentification | Mot de passe haché (algorithme robuste), MFA TOTP obligatoire pour le Super Admin et les administrateurs, PIN de caisse pour le changement rapide d'opérateur, verrouillage progressif | Dossier §9.2 ; D72 |
| Code initial | Aléatoire, usage unique, durée limitée, haché, changement obligatoire à la première connexion | D72 [DÉCIDÉ] ; modalités [TECH] |
| Sessions | Cookies `HttpOnly; Secure; SameSite=Strict`, expiration par contexte, révocation (blocage, régénération, suppression d'accès) | Dossier ; D72 |
| Step-up | MFA au moment des décisions sensibles (dérogation à distance, transfert d'administrateur, option jour d'exploitation) | EXT06 v1.1 |
| Autorisation | Droits effectifs côté serveur (§6.5) ; tests d'API directs | Dossier ; EXT06 GV12 |
| Isolation | Contexte d'officine obligatoire ; **une base physiquement distincte par pharmacie** (D3) ; routage depuis la session authentifiée ; tests MOF, DEP-03, DEP-13 | EXT06 MO1–MO10 ; D3 |
| Intégrité | Journaux chaînés ; déclencheurs d'immutabilité ; vérification après restauration | Dossier ; EXT06 |
| Applicatif | CSRF, CSP stricte, limitation de débit, validation des schémas, protection contre l'énumération (SEC-RATE-01) | Dossier ; EXT03 v1.1 |
| Fichiers | Accès via l'application après contrôle des droits ; liens temporaires ; chiffrement des fichiers sensibles | Dossier ; v1.2 |
| Secrets | Jamais dans le frontend ni dans les journaux ; coffre de l'hébergeur (A) ou coffre local chiffré (B) | v1.2 |
| Mises à jour | Paquets signés, vérification de signature, sauvegarde préalable **de chaque base**, migration base par base avec contrôle de version (UPD-07) | v1.2 ; D3 |
| Licences | Fichier signé ; jamais de blocage des données à l'expiration | v1.2 (ARB-06) |
| Super Admin | Compte immuable ; récupération à double contrôle sans e-mail ; journal plateforme | D72 ; §6.4 |

---

## 13. Architecture technique portable (SaaS et local)

Synthèse ; l'analyse complète, les options, les risques RD1 à RD10 et les tests sont dans
`MATRICE_DEPLOIEMENT_SAAS_ONPREMISE_v1.2.1.md`.

### 13.1 Constat

L'architecture v1.1 (Workers, D1, R2, hébergement géré sans cron) ne fonctionne pas sur le poste d'une pharmacie [INC-39].

### 13.2 Architecture cible [TECH, à valider]

```
Frontend unique (SPA / PWA) ─► API Hono /api/v1 (identique) ─► services métier M00–M20 (identiques)
                                                              └─► PORTS : Database · ObjectStorage · JobScheduler · Clock ·
                                                                  Secrets · LicenseVerifier · Notifier · AiGateway ·
                                                                  OcrGateway · ReferentialSource · BackupService · UpdateService
Composition « mode A » : base d'instance + une base distincte par pharmacie (D3) ; stockage objet ; file / cron ; abonnements ; console APEX
Composition « mode B » : base locale distincte par pharmacie (D3) ; dossier local ; planificateur local ; licence signée ; sauvegardes locales
Moteur de base des deux compositions : non tranché (ARB-01, après PT-13)
```

### 13.3 Choix structurants

| # | Choix | Statut |
|---|---|---|
| T1 | Hono conservé : il fonctionne sur Workers et sur Node.js ; le cœur n'utilise aucune API propre à un environnement | [TECH] |
| T2 | Un seul dialecte SQL et un seul jeu de migrations, déclencheurs compris ; le dialecte dépend du moteur retenu | [TECH] — **ARB-01** (SQLite si A1 ou A2 ; PostgreSQL si A3) |
| T3 | Unité de travail atomique = liste d'instructions avec gardes, exécutée par `batch()` (D1) ou transaction (SQLite) | [TECH] — prototype Lot 0 |
| T4 | Une base de données physiquement distincte par pharmacie dans les deux modes ; plusieurs bases par serveur possibles | **[DÉCIDÉ D3]** (v1.2.1, ARB-02 fermé) |
| T5 | Moteur d'exécution et de base : A1 Cloudflare (Workers + D1 + R2), A2 Node.js + SQLite, A3 PostgreSQL ou autre ; la recommandation A2 de la consolidation **n'est pas une décision** ; choix après prototypes comparatifs (PT-13) et tests de charge, concurrence, transactions, sauvegarde, restauration et migration | **ARB-01 — OUVERT** |
| T6 | Licences par fichier signé (mode B), lecture seule à l'expiration | [RECO] — **ARB-06** |
| T7 | Référentiels APEX diffusés en paquets signés | [TECH] |
| T8 | Connecteurs distants optionnels avec file d'attente | [TECH] |
| T9 | Sauvegarde automatique de chaque base avant sa migration ; restauration croisée entre modes | [TECH] |
| T10 | Contrôle de l'horloge du serveur local | [TECH] |

### 13.4 Organisation du code (remplace le dossier §6.2)

```
webapp/
├── src/
│   ├── core/            domaine, services métier, unité de travail, droits, audit, outbox, i18n serveur
│   │   └── ports/       interfaces d'infrastructure (aucune implémentation)
│   ├── modules/         M00 … M20 (routes, services, dépôts, schémas, événements, tests)
│   ├── adapters/
│   │   ├── saas/        base, stockage objet, tâches, secrets, licences (abonnement), sauvegarde centrale
│   │   └── local/       base locale (moteur : ARB-01), dossier local, planificateur, coffre local, licence signée, sauvegarde locale
│   ├── entry/
│   │   ├── saas.ts      composition du mode A
│   │   └── local.ts     composition du mode B (serveur local + installateur)
│   └── shared/          schémas et types partagés avec le frontend
├── frontend/            SPA / PWA unique (FR · AR · EN, responsive)
├── migrations/          un seul jeu, appliqué séparément à chaque base (dialecte : ARB-01)
├── packaging/           installateur et paquets signés du mode B
└── tests/               métier, conformité des adaptateurs, isolation, transactions, sauvegarde, mise à jour, responsive
```

Règle vérifiée automatiquement (DEP-01) : `core/` et `modules/` n'importent jamais `adapters/` ni une API d'environnement.

---

## 14. Responsive et expérience utilisateur [DÉCIDÉ — exigence du propriétaire]

### 14.1 Principes

| # | Principe | Nature |
|---|---|---|
| U1 | **Adaptation par usage** : chaque écran a un appareil principal (où il est complet et optimisé) et des appareils secondaires (où il est utilisable, éventuellement en mode réduit) | [DÉCIDÉ] |
| U2 | Une seule application (même code, mêmes contrôles de droits) ; la mise en page change, jamais les règles | [DÉCIDÉ] P14 |
| U3 | Points de rupture proposés : smartphone < 640 px · tablette 640–1 279 px · ordinateur ≥ 1 280 px ; orientation portrait et paysage | [TECH] |
| U4 | Propriétés CSS **logiques** (`margin-inline-start`…) ; `dir="rtl"` dynamique ; icônes directionnelles inversées en arabe | Dossier §6.1 |
| U5 | Cibles tactiles ≥ 48 × 48 px ; aucune action réservée au survol ; appui long documenté | EXT06 §2.1 ; EXT02 |
| U6 | Clavier complet sur ordinateur (raccourcis caisse F2, F4…) ; lecteur de codes-barres utilisable sans souris | Dossier ; EXT06 |
| U7 | Contraste conforme aux recommandations d'accessibilité usuelles (niveau AA visé) ; tailles de texte réglables ; aucune information transmise par la seule couleur | [RECO] |
| U8 | Tableaux : colonnes prioritaires sur petit écran, autres colonnes repliables ou en fiche ; aucun défilement horizontal pour les actions principales | [TECH] |
| U9 | Formulaires : un champ par ligne sur smartphone, clavier adapté (numérique pour les quantités et montants), validation immédiate, brouillon conservé en cas de coupure | [TECH] |
| U10 | Navigation : barre latérale (ordinateur), menu repliable (tablette), barre inférieure + menu (smartphone) ; les modules affichés dépendent des **permissions effectives** (D77) | [TECH] ; D77 |
| U11 | Impression : ticket 80 mm et A4 par feuilles de style dédiées ; aperçu PDF sur tous les appareils ; mise en page RTL des documents arabes | Dossier §6.1 |
| U12 | Langues **FR, AR (RTL) et EN** sur tous les écrans ; aucun texte en dur ; formats de nombres et de dates par `Intl` ; chiffres latins par défaut en arabe (D16) | Dossier ; v1.2 |
| U13 | Mention « Comptabilité à titre indicatif » visible sur tous les formats d'écran de M20 et sur les impressions | D80 |

### 14.2 Appareil principal par usage

| Usage | Ordinateur | Tablette | Smartphone |
|---|---|---|---|
| Caisse, vente, encaissement | **Principal** (clavier, scanner, tiroir, imprimante) | Utilisable (caisse d'appoint, vente en mobilité) | Consultation d'une vente, pas d'encaissement par défaut |
| Mode ordonnance, saisie des lignes | **Principal** | Utilisable | Consultation ; capture photo d'ordonnance (EXT03 §14.2, QR de session) |
| Réception de commandes, contrôle des lots | Utilisable | **Principal** (scan, saisie des lots et péremptions) | Utilisable pour un petit colis |
| Inventaire | Validation des écarts | **Principal** (comptage par scan) | Comptage ponctuel |
| Contrôle des retours (zone EN_CONTRÔLE) | Utilisable | **Principal** (check-list) | Décision rapide (si autorisé, D74) |
| Alertes, péremptions, ruptures | Utilisable | Utilisable | **Principal** (notifications, liste) |
| Validations et dérogations à distance (D73) | Utilisable | Utilisable | **Principal** (écran G28, MFA) |
| Planning, pointage, congés (collaborateur) | Utilisable | Utilisable | **Principal** (« Mon planning », pointage QR, demande de congé, EXT02) |
| Planning (gestionnaire) | **Principal** (glisser-déposer) | Utilisable (appui long) | Consultation |
| Tableaux de bord | **Principal** | Utilisable | Indicateurs clés, sans tableau détaillé |
| Comptabilité, finance, rapprochement bancaire | **Principal** | Consultation | Consultation des soldes et des exceptions |
| Paramétrage, gouvernance, matrice des droits | **Principal** | Utilisable | Consultation ; révocation d'urgence d'une délégation |
| Console Super Admin (mode A) | **Principal** | Utilisable | Blocage d'urgence d'un accès |

### 14.3 Critères d'acceptation responsive par module

Chaque module n'est accepté que si **tous** ses critères sont vérifiés sur les trois classes d'appareils, en FR, AR et EN
(tests RSP-01 à RSP-16, §15.3 ; critères transverses : §14.4).

| Module | Critères d'acceptation |
|---|---|
| M00 Gouvernance | Matrice des droits lisible sur ordinateur (lignes = modules, colonnes = actions) ; sur tablette et smartphone, vue par module en liste ; explication du droit effectif accessible partout ; révocation d'une délégation en ≤ 3 actions sur smartphone |
| Accueil `/home` | Tuiles limitées aux permissions effectives ; 2 colonnes sur smartphone, 3 sur tablette, 3 à 6 sur ordinateur ; recherche universelle en tête sur tous formats ; aucun indicateur financier |
| M01 Tableau de bord | Indicateurs clés visibles sans défilement sur smartphone ; graphiques lisibles en RTL ; lien « comment est-il calculé ? » accessible au toucher |
| M02 / M02b Vente et caisse | Ordinateur : vente complète au clavier et au scanner, objectif < 10 s pour une vente simple (dossier Lot 5) ; tablette : vente tactile utilisable ; smartphone : consultation d'une vente et de la session ; clôture de caisse impossible sans comptage, quel que soit l'appareil |
| M03 Achats / réception | Tablette : saisie d'une ligne de réception (produit, lot, péremption, quantité) sans clavier physique ; scan par caméra ou lecteur ; reprise après coupure sans perte |
| M04 Catalogue | Fiche produit en onglets sur ordinateur, en sections repliables sur smartphone ; explorateur à facettes utilisable sur tablette ; page des services (D78) utilisable sur tous formats |
| M05 Stock, inventaire, contrôle des retours | Comptage par scan sur tablette et smartphone ; check-list de contrôle tactile ; état du stock par lot consultable sur smartphone |
| M06 / M07 Fournisseurs, clients, patients | Fiches consultables sur smartphone ; données sensibles masquées par défaut et révélées par une action tracée |
| M08 Finance | Saisie des charges sur tablette et ordinateur ; rapprochement bancaire sur ordinateur ; échéances et trésorerie consultables sur smartphone |
| M09 Réglementés | Registre consultable sur ordinateur et tablette ; aucune opération réglementée validée depuis un smartphone hors de l'officine |
| M10 Péremptions | Liste des alertes et valeur à risque sur smartphone ; retrait et destruction sur tablette |
| M11 Commandes | Préparation et validation d'un bon de commande sur ordinateur et tablette ; validation simple sur smartphone avec récapitulatif |
| M12 Rapports | Rapports et exports sur ordinateur ; aperçu PDF sur tous formats ; masquage des marges respecté partout (D79) |
| M13 PHARM'OS AI | Centre de recommandations consultable sur smartphone ; avatar désactivable ; aucune action sensible depuis la conversation |
| M14 Hors-ligne | Indicateur d'état réseau visible sur tous formats ; file d'opérations en attente consultable |
| M15 RH | « Mon planning », pointage QR et demande de congé sur smartphone ; planning glisser-déposer sur ordinateur, appui long sur tablette, frise inversée en arabe |
| M16 Laboratoire | Fiche de préparation et contrôle sur tablette ; pesées saisies sans clavier physique |
| M17 Ordonnances | Mode ordonnance à la caisse sur ordinateur ; capture photo sur tablette ou smartphone appairé ; données patient jamais en aperçu de notification |
| M18 Tiers payant | Panneau de prise en charge à la caisse (ordinateur, tablette) ; créances et bordereaux sur ordinateur ; encours consultable sur smartphone |
| M19 Corrections | Assistants d'annulation et de retour sur ordinateur et tablette ; file des demandes et décisions sur smartphone (si autorisé) |
| M20 Comptabilité | Journaux, grand livre et balance sur ordinateur ; exceptions comptables consultables sur smartphone ; mention « Comptabilité à titre indicatif » sur tous formats et impressions |
| Console Super Admin | Liste des pharmacies et des accès, blocage d'urgence sur smartphone ; création et suppression d'accès sur ordinateur |

### 14.4 Critères responsive transverses [DÉCIDÉ — exigence du propriétaire, v1.2.1]

Ces critères s'appliquent à **tous les modules**, sur **ordinateur, tablette et smartphone**, en **FR, AR (RTL) et EN**, dans
**les deux modes** de déploiement. Ils complètent le §14.3 et sont repris par la matrice de déploiement (§10).

| Élément | Ordinateur | Tablette | Smartphone | FR / AR (RTL) / EN |
|---|---|---|---|---|
| Navigation | Barre latérale, raccourcis clavier | Menu repliable, gestes simples | Barre inférieure + menu ; retour toujours visible | Barre et icônes directionnelles inversées en arabe |
| Menus | Tous visibles selon les permissions (D77) | Idem, repliés | Idem, regroupés ; aucun module autorisé masqué | Libellés complets dans les trois langues, sans troncature gênante |
| Tableaux | Colonnes complètes, tri, filtres | Colonnes prioritaires | Colonnes prioritaires + détail en fiche ; aucune action principale hors écran | Ordre des colonnes inversé en RTL ; montants alignés |
| Formulaires | Saisie au clavier, tabulation logique | Saisie tactile sans clavier physique | Un champ par ligne, clavier adapté | Validation et messages d'erreur traduits ; champs RTL |
| Boutons | Raccourcis affichés | Cibles ≥ 48 px | Cibles ≥ 48 px ; action principale accessible au pouce | Position de l'action principale inversée en RTL |
| Fenêtres modales | Centrées, fermables au clavier (Échap) | Plein écran si nécessaire | Plein écran, défilement interne, bouton de fermeture visible | Boutons « Valider / Annuler » dans l'ordre de lecture |
| Recherche | Champ toujours accessible, résultats < 150 ms serveur | Idem, résultats tactiles | Idem ; scan par caméra si autorisé | Recherche en caractères latins et arabes |
| Saisie clavier et tactile | Clavier complet, lecteur de codes-barres sans souris | Tactile complet ; clavier externe possible | Tactile complet | Claviers FR, AR, EN ; chiffres latins par défaut (D16) |
| Lisibilité | Texte ≥ taille de base, contraste AA visé | Idem | Idem ; zoom sans perte de fonction | Police lisible en arabe ; aucune information par la seule couleur |
| Ergonomie de vente | Vente simple < 10 s au clavier et au scanner | Caisse d'appoint complète | Consultation d'une vente ; pas d'encaissement par défaut (§14.2) | Ticket et écran dans la langue choisie |
| Usage réel au comptoir | Fonctionne debout, avec scanner, tiroir, imprimante, en condition de file d'attente | Fonctionne d'une main, en mobilité dans l'officine | Alertes et validations utilisables en moins de 3 actions | Bascule de langue sans perte de la vente en cours |

**Règle d'acceptation** : une fonctionnalité **déclarée disponible** sur un format (§14.2) n'est validée que si elle y est
**complète et utilisable** ; **une fonctionnalité dégradée sur mobile n'est pas validée**. Les restrictions d'usage décidées
explicitement au §14.2 (par exemple « pas d'encaissement par défaut sur smartphone ») ne sont pas des dégradations : elles
doivent être visibles et expliquées à l'utilisateur [RECO — interprétation à confirmer par le propriétaire].

---

## 15. Stratégie de tests

### 15.1 Séries existantes (conservées, définies dans leurs documents)

| Document | Séries | Nombre |
|---|---|---|
| EXT02 §12.2 | Droits effectifs, isolation, planning, temps, congés, caisse, KPI, évaluations, laboratoire, IA, interface | Par domaine (non numérotés) |
| EXT03 v1.1 `05_MATRICE_TESTS.md` | S01–S14, F-*, T-ATO, T-IDE, T-REC, T-PRJ, T-LOT0, SEC-*, C-*, S-IA, S-AUD, O-* | ≈ 90 |
| EXT04 §9 | TP-01 à TP-42 | 42 |
| EXT05 §9 | AN, RE, PA, DR, DB, CX, ST, FI, OG, DL, SE, AU, RP, HL | 46 |
| EXT06 v1.1 §10 | GOV (26), ANN (17), CAT (13), SRV (7), FIN (20), NAV (4), ACC (19), EXC (11), MET (12), MOF (7), RNT (9) | 145 |
| Matrice de déploiement §8 | DEP (12), LOC (8), LIC (6), BKP (6), UPD (6) | 38 |

**Tests devenus obsolètes ou à réécrire** (non supprimés des sources, signalés ici) :

| Test | Raison | Remplacé par |
|---|---|---|
| EXT05 AN-02 (annulation < 15 min, réintégration directe) | D61 caduque ; aucune réintégration automatique | EXT06 ANN-02 à ANN-05, ANN-18 à ANN-21 |
| EXT05 DL-01 à DL-06 (délégant désigné) | D66 caduque | EXT06 GOV-04 à GOV-08 (règles identiques, gestionnaire = administrateur) |
| EXT06 v1.1 SRV-01, SRV-02 (autorisation réglementaire bloquante) | D78 : aucune autorisation bloquante | SRV-10 |
| EXT06 v1.1 SRV-07 (mesures chiffrées) | D78 : aucune mesure structurée | SRV-11 |
| EXT06 v1.1 FIN : plan de comptes inactif avant validation | D83 | FIN-21 |

### 15.2 Tests nouveaux de la v1.2 (arbitrages D72 à D86)

Préfixes vérifiés libres dans tous les documents ; numérotation à la suite des séries existantes.

| ID | Décision | Scénario | Résultat attendu |
|---|---|---|---|
| ADM-01 | D72 | Tentative de modification, désactivation ou suppression du Super Admin (interface, API, requête SQL) | Refus ; tentative journalisée dans `platform_audit_log` |
| ADM-02 | D72 | Création d'une pharmacie et de son accès administrateur | Base d'officine créée, code initial généré, haché, à usage unique et durée limitée |
| ADM-03 | D72 | Première connexion avec le code initial | Changement de mot de passe et enrôlement MFA obligatoires avant tout autre écran |
| ADM-04 | D72 | Réutilisation du code initial, ou code expiré | Refus |
| ADM-05 | D72 | Blocage puis déblocage d'un accès | Sessions révoquées au blocage ; données intactes ; accès rétabli |
| ADM-06 | D72 | Régénération du mot de passe d'un administrateur | Nouveau code initial ; sessions révoquées ; **aucune donnée modifiée** (empreinte de la base identique hors journaux) |
| ADM-07 | D72 | Suppression de l'accès d'un administrateur | Accès révoqué ; compte conservé en suppression logique ; **aucune donnée de l'officine supprimée** |
| ADM-08 | D72 | Recréation de l'accès (même personne, puis autre personne) | Même base d'officine ; autre personne = désignation avec qualification vérifiée et justificatif |
| ADM-09 | D72 | Super Admin tente de lire une vente, une ordonnance, une écriture | Refus sans ouverture temporaire par l'administrateur de l'officine |
| ADM-10 | D72 | Demande de suppression des données d'une pharmacie | Export proposé, contrôle de conservation, double validation, délai de grâce, certificat ; jamais déclenchée par la suppression d'un accès ou l'expiration d'une licence |
| ANN-18 | D74 | Collaborateur sans autorisation explicite tente une réintégration | Refus |
| ANN-19 | D74 | Collaborateur autorisé par réglage individuel de l'administrateur | Réintégration exécutée, autorisation référencée |
| ANN-20 | D74 | Autorisation accordée par un adjoint (et non par l'administrateur) | Refus |
| ANN-21 | D74 | Exécutant de l'opération corrective qui réintègre lui-même | Refus (second collaborateur exigé), sauf administrateur |
| NAV-05 | D77 | Deux collaborateurs de même rôle, l'un avec un module coupé | Accueils différents, conformes aux permissions effectives |
| NAV-06 | D77 | Délégation active donnant accès à un module | Tuile visible pendant la délégation, retirée à son expiration |
| NAV-07 | D77 | Appel d'API d'un module dont la tuile est masquée | Refus serveur |
| SRV-08 | D78 | Installation neuve | Aucun service prédéfini ; page de gestion vide |
| SRV-09 | D78 | Création d'un service par l'administrateur ; tentative par un autre collaborateur | Créé ; refus pour l'autre collaborateur |
| SRV-10 | D78 | Enregistrement d'un acte : type, nom du client, date | Enregistré sans autre champ obligatoire ; aucune autorisation réglementaire bloquante |
| SRV-11 | D78 | Recherche d'un champ de mesure clinique ou d'observation dans le modèle et les écrans | Absent |
| SRV-12 | D78 | Consultation du registre des actes par un collaborateur non autorisé ; envoi à l'IA | Refus et journalisation ; aucun acte nominatif transmis à l'IA |
| CAT-14 | D84 | Installation neuve | Classification thérapeutique standard disponible |
| CAT-15 | D84 | Modification d'un nœud standard par l'administrateur | Modifiée, historisée |
| CAT-16 | D84 | Changement de classification d'un médicament | `prescription_policy`, classe réglementaire et statut officiel inchangés |
| FIN-21 | D83 | Installation neuve | Plan comptable standard actif ; écritures générées sans validation préalable par un expert-comptable |
| FIN-22 | D83 | Désactivation d'un compte utilisé par une règle de passation active | Refus tant que la règle n'est pas modifiée |
| FIN-23 | D83 | Modification du plan par l'administrateur | Historisée (avant / après, motif) |
| FIN-24 | D80 | Écrans, états, impressions et exports de M20, sur les trois formats d'écran | Mention « Comptabilité à titre indicatif » présente partout |
| FIN-25 | D86 | Dépense au-dessus du seuil configuré | Validation par l'administrateur exigée ; seuil modifiable par l'administrateur seul |

### 15.3 Tests responsive (RSP)

| ID | Scénario | Résultat attendu |
|---|---|---|
| RSP-01 | Chaque module sur smartphone, tablette et ordinateur (portrait et paysage) | Critères du §14.3 respectés ; aucune action principale inaccessible |
| RSP-02 | Vente simple au clavier et au scanner sur ordinateur | Objectif < 10 s tenu ; aucune souris nécessaire |
| RSP-03 | Impression ticket 80 mm et A4 en FR, AR, EN | Mise en page conforme, RTL pour l'arabe |
| RSP-04 | Bascule FR ↔ AR ↔ EN sans rechargement | Textes, sens de lecture, icônes et alignements corrects |
| RSP-05 | Nombres, montants (3 décimales) et dates dans les trois langues | Formats conformes ; chiffres latins par défaut en arabe (D16) |
| RSP-06 | Réception d'une commande sur tablette sans clavier physique | Saisie complète produit / lot / péremption / quantité |
| RSP-07 | Pointage QR et demande de congé sur smartphone | Fonctionnels (EXT02) |
| RSP-08 | Validation à distance sur smartphone (D73) | MFA, confirmation, motif ; aucune donnée patient en aperçu de notification |
| RSP-09 | Tableaux longs (journaux, créances) sur smartphone | Colonnes prioritaires, détail en fiche, pas de défilement horizontal pour les actions |
| RSP-10 | Contrastes, tailles de texte, cibles tactiles | Niveau visé atteint (U5, U7) |
| RSP-11 | Navigation au clavier seul et lecteur d'écran sur les parcours principaux | Parcours complets |
| RSP-12 | Mêmes écrans servis en mode A et en mode B | Rendu identique (DEP-07) |
| RSP-13 | Fenêtres modales sur les trois formats et en RTL | Fermeture visible, plein écran sur smartphone, ordre des boutons correct (§14.4) |
| RSP-14 | Recherche produit sur les trois formats, en caractères latins et arabes | Résultats pertinents dans le délai cible ; sélection tactile et clavier |
| RSP-15 | Scénario de comptoir : file de clients, scanner, tiroir, imprimante, bascule de langue en cours de vente | Aucune perte de vente ; objectif < 10 s tenu |
| RSP-16 | Toute fonctionnalité déclarée disponible sur smartphone (§14.2) | Complète et utilisable ; sinon non validée (§14.4) |

### 15.4 Catégories transverses exigées pour chaque lot

| Catégorie | Contenu minimal | Séries de référence |
|---|---|---|
| Isolation multi-officines | Accès croisés refusés (404), sessions mono-officine, séquences indépendantes ; routage vers la base de la pharmacie authentifiée | MOF, DEP-03, DEP-13, SEC-TEN-01 |
| Transactions atomiques | Échec injecté à chaque étape ; gardes ; concurrence ; idempotence | T-ATO, T-IDE, DB, ACC-02 à 07, DEP-05 |
| Immuabilité | UPDATE / DELETE refusés sur les registres ; contre-passation unique | SE-01, ACC-01, ACC-08, ACC-12, ADM-01 |
| Sauvegarde et restauration | Sauvegarde, restauration, export par base ; chaîne de hachage vérifiée ; aucune incidence sur les autres bases | BKP-01 à BKP-07 |
| Fonctionnement local sans connexion | Exploitation complète hors Internet ; tâches rattrapées | LOC |
| Migration et mise à jour | Paquets signés, retour arrière, saut de versions ; migration base par base avec version | UPD-01 à UPD-07, DEP-02, DEP-09 |
| Responsive | Critères §14.3 et §14.4 | RSP-01 à RSP-16 |
| Sécurité | Droits, injection, énumération, secrets, chiffrement | SEC-*, GOV, DEP-10 |
| Non-régression | Suite complète exécutée dans les deux modes à chaque lot | Toutes séries |

---

## 16. Plan de développement révisé (proposition — aucun développement lancé)

### 16.1 Principes

1. Un lot n'est terminé que si la **Definition of Done** du dossier (§10) est respectée, complétée par : tests exécutés
   **dans les deux modes**, critères responsive du module, tests d'isolation, tests de sauvegarde lorsque des données sont
   créées, et absence de test bloquant ouvert.
2. Aucun lot ne démarre tant que ses **prérequis** (décisions, points réglementaires, prototypes) ne sont pas levés.
3. Les lots qui créent des tables définitives (1, 2, 3, 5) intègrent dès le départ les colonnes et contraintes prévues par
   les extensions (éviter les migrations de rattrapage, EXT04 §12, EXT05 §12).

### 16.2 Séquence proposée

| Lot | Contenu | Prérequis | Tests clés |
|---|---|---|---|
| **P0 — Prototypes** (nouveau, avant tout lot) | Unité de travail atomique sur D1 et SQLite (gardes, contraintes différées, échec contrôlé) ; déclencheurs d'immutabilité et de transitions ; recherche plein texte (FTS5) dans les deux moteurs ; recalcul d'un jour de métriques dans la limite d'exécution ; une base distincte par pharmacie (routage, migrations sur N bases — D3) ; installateur local et moteur de base sur le système d'exploitation cible des pharmacies ; sauvegarde à chaud ; signature de paquets ; **comparatif des moteurs (PT-13)** | ARB-05, ARB-06, ARB-09 (ARB-02 fermé ; ARB-01 arbitré **à l'issue** de P0) | T-LOT0-01, DEP-04, DEP-05, PT-01 à PT-13 |
| **0. Socle** | Structure portable (ports, adaptateurs, deux points d'entrée) ; migrations uniques ; ULID ; millimes ; i18n FR / AR / EN + RTL ; design system responsive ; erreurs ; outbox ; audit chaîné ; séquences ; CI exécutant les tests dans les deux modes ; contrôle automatique du contexte d'officine | P0 concluant | DEP-01, DEP-02, DEP-07, RSP-04, MOF-07 |
| **1. Plateforme et gouvernance** | Base d'instance ; Super Admin immuable et journal plateforme (D72) ; cycle de vie des accès ; demandes de suppression de données ; abonnements et licences signées ; utilisateurs, sessions, MFA, PIN ; collaborateurs, qualifications, habilitations ; moteur de droits effectifs complet (rôles, réglages individuels, délégations, dérogations, coupure de module, licence) ; administrateur de l'officine ; validation à distance (D73) ; paramètres (jour de référence, seuils) ; accueil selon les permissions (D77) | Lot 0 ; R48 ; ARB-04 à ARB-07, ARB-11, ARB-12 | ADM, GOV, NAV-05 à 07, LIC, MOF |
| **1b. Installation locale et exploitation** (nouveau) | Installateur, assistant de première configuration, sauvegardes locales planifiées et second support, restauration guidée, mises à jour signées, tableau de santé, contrôle d'horloge | Lot 1 | LOC, BKP, UPD |
| **2. Catalogue** | Référentiel en paquets signés ; fiche locale ; types, substances, unités, variantes, marques ; classification multi-axes et **classification thérapeutique standard modifiable** (D84) ; catégories fiscales sans taux par défaut ; prix historisés ; groupes de substitution ; `prescription_policy` ; recherche universelle ; page des services (D78) | Lot 1 ; R42, R43, R44, R51 ; D4 | CAT, CAT-14 à 16, SRV-08, SRV-09, DEP-11 |
| **3. Stock et lots** | Registre, lots, FEFO, états sans `RESERVE` (D76), réservations logiques, ajustements, valorisation | Lot 2 | ST-01, ST-04, CAT-10, CAT-11 |
| **4. Fournisseurs et achats** | Réception par lots, factures (nature marchandises / charges / immobilisations), retours fournisseurs | Lot 3 | Dossier Lot 4 |
| **5. Caisse et ventes** | Caisse nominative ; vente ; colonnes de tiers payant et invariant dès la création ; statuts et idempotence ; annulation du jour de référence (D75) ; dérogations | Lots 3, 4 ; R49 ; D59, D60 | ANN-01 à ANN-17, DB, CX |
| **5b. Ordonnances — cœur** | M17 (si EXT03 v1.1 validée) | Lot 5 ; D27–D43 ; R1–R13 | EXT03 v1.1 S, F, T, SEC |
| **5c. OCR** (optionnel) | Connecteur OCR, repli sur l'appareil | Lot 5b ; D41 ; R16 | O-01 à O-07, LOC-06 |
| **5d. Opérations correctives** | M19 complet ; contrôle de réintégration avec autorisation explicite (D74) | Lots 5, 5b ; D59–D70 ; R34–R41 | AN, RE, PA, DR, ST, FI, ANN-18 à 21 |
| **6. Clients et crédit** | Fiches, plafonds, règlements, fidélité, consentements | Lot 5 | Dossier Lot 6 |
| **7. Finance** | Instruments, échéances, charges, seuil de validation (D86), avoirs et acomptes fournisseurs, emprunts, rapprochement bancaire confirmé (D85) | Lots 4, 6 | FIN-01 à FIN-07, FIN-25, EXC-08 |
| **7b. Tiers payant** | M18 (si EXT04 validée) ; conventions inactives par défaut | Lots 5b, 7 ; D44–D58 ; R23–R33 ; INC-06 tranché | TP-01 à TP-42 |
| **7c. Comptabilité et paie importée** | M20 : plan standard actif et adaptable (D83), règles versionnées, écritures immuables, brouillons, exceptions, clôture, réouverture (D82), exports, mention « à titre indicatif » (D80) ; immobilisations ; import de paie (D81) | Lots 7, 7b ; R45, R46, R50 | ACC, EXC, FIN-08 à FIN-24 |
| **8. Péremptions et pertes** | Seuils, retraits, destructions | Lots 3, 4 | Dossier Lot 8 |
| **9. Inventaires** | Sessions, comptage tablette, écarts | Lot 3 | RSP-06 |
| **9b. Laboratoire** | M16 | Lots 3, 1 | EXT02 « Laboratoire » |
| **9c. Services** | Registre des actes simplifié (D78), facturation, consommables | Lots 2, 5 ; R47 | SRV-10 à SRV-12 |
| **10. Réglementés** | Registre généré, rectifications | Lots 3, 5, 5b ; D5 ; R18, R35, R36 | T-REC-02 |
| **10b. RH opérationnelle** | Planning, pointage, congés, export des variables de paie | Lots 1, 5 ; D17 | EXT02 §12.2 |
| **11. Métriques et rapports** | `metric_definitions`, `metrics_daily`, clichés, G23, G24, G25 (marges sous D79), `rx_facts` | Lots 5–9 ; R46 (notions) | MET, RNT, FIN-15 |
| **11b. Performance et formation** | EXT02 | Lots 10b, 11 | EXT02 « KPI » |
| **12. Commandes intelligentes** | M11 | Lots 4, 11 | Dossier Lot 12 |
| **13. PHARM'OS AI** | Analyseurs, connecteur LLM optionnel | Lots 11, 12 ; D7 | S-IA, TP-40, LOC-06 |
| **14. Hors-ligne du poste** | PWA, file locale, synchronisation | Lot 5 ; D10, D39, D64 | LOC-03, F-HL |
| **15. Industrialisation SaaS** | Console Super Admin complète, supervision, facturation APEX, migration croisée A ↔ B | Tous | DEP-09, BKP, UPD |

### 16.3 Changements par rapport au plan v1.1

| Changement | Raison |
|---|---|
| Ajout de **P0** (prototypes) avant le Lot 0 | Risques de portabilité et d'atomicité bloquants (§19) |
| Lot 0 : architecture portable et tests dans les deux modes | Double mode (P14) |
| Lot 1 : Super Admin, cycle de vie des accès, licences, droits complets | D72, D73, D77 ; le moteur de droits doit naître complet (EXT05 §12) |
| Ajout du **Lot 1b** (installation locale) | Mode B |
| Lot 15 réduit à l'industrialisation SaaS (les licences et sauvegardes sont avancées aux Lots 1 et 1b) | Mode B indépendant d'APEX dès les premières versions |
| Lots 5b, 5c, 5d, 7b, 7c, 9c, 11c (EXT03 à EXT06) **conditionnés** à la validation de leurs extensions | Décisions non arbitrées |

---

## 17. Points réglementaires et nature des énoncés

### 17.1 Règle

Aucun taux, plafond, durée, obligation ou autorisation légale n'est présumé. Chaque énoncé du dossier relève de l'une des
quatre natures suivantes, et ne change jamais de nature sans décision tracée :

| Nature | Marque | Exemple | Qui la modifie |
|---|---|---|---|
| 1. Règle métier décidée | [DÉCIDÉ] | « Comptabilité à titre indicatif » (D80) | Le propriétaire |
| 2. Choix technique | [TECH] | Unité de travail atomique avec gardes (T3) | L'architecte, après prototype ; validation du propriétaire |
| 3. Recommandation | [PROPOSÉ] / [RECO] | Réservation de reliquat de 48 h (D40) | Arbitrage du propriétaire |
| 4. Obligation juridique à confirmer | [JURIDIQUE Rxx] | Taux de TVA par catégorie (R44) | Juriste, expert-comptable, autorité compétente |

### 17.2 Synthèse des points ouverts

| Plage | Domaine | Source | Nb |
|---|---|---|---|
| R1–R13 | Ordonnances, données de santé, substitution, conservation, hébergement | EXT03 v1.0, reprise en v1.1 | 13 |
| R14–R22 | Retours, OCR, quantités maximales, registre électronique, dispensation, droits des patients, réidentification, LLM | EXT03 v1.1 | 9 |
| R23–R33 | Tiers payant (convention CNAM, pièces, conservation, facturation, refacturation, données aux assureurs, taux et listes, comptabilité) | EXT04 | 11 |
| R34–R41 | Retours et remise en vente, réglementés, délégation des actes, avoirs, remboursement par carte, conservation | EXT05 | 8 |
| **R42–R51** | **Catégories réglementaires, référentiel thérapeutique, TVA et fiscalité, immobilisations, règles comptables, services pharmaceutiques, administrateur et délégations, annulations et facturation, données personnelles et paie, prix et marges réglementés** | EXT06 v1.1 (précisés en v1.1 et v1.2) | 10 |
| **R52–R55** | Accès de l'éditeur aux données et journal plateforme ; suppression des données d'une pharmacie ; enregistrement nominatif des services ; responsabilités en installation locale | **Nouveaux en v1.2** | 4 |

Le détail (hypothèse de conception, question précise, interlocuteurs, lot bloqué) figure dans
`REGISTRE_POINTS_OUVERTS_PHARMOS_v1.2.1.md` §1. Les points R42 à R51 sont **conservés ouverts** ; la v1.2 précise seulement la
portée de R43 (D84), R46 (D80, D83), R47 (D78) et R48 (D72).

### 17.3 Effet des arbitrages D78, D83 et D84 sur les points réglementaires

| Arbitrage | Ce qu'il tranche | Ce qu'il **ne** tranche **pas** |
|---|---|---|
| D78 Services | Aucune liste imposée ; enregistrement minimal ; pas de dossier clinique | La licéité de chaque prestation en officine, le personnel habilité, la tarification [JURIDIQUE R47] ; la base légale de l'enregistrement nominatif [JURIDIQUE R54] |
| D83 Plan comptable | Un modèle standard est livré et utilisable sans validation préalable | La conformité au système comptable applicable et l'usage officiel des écritures [JURIDIQUE R46] |
| D84 Classification | Une classification standard interne est livrée et modifiable | L'existence et l'usage d'un référentiel officiel [JURIDIQUE R43] ; les données réglementaires restent distinctes |

---

## 18. Décisions

Matrice complète : `MATRICE_DECISIONS_D1_D86_v1.2.1.md`.

| Statut | Nb | Décisions |
|---|---|---|
| **ARBITRÉE** | **16** | **D3** (v1.2.1 : base physiquement distincte par pharmacie) ; D72 à D86 |
| PROPOSÉE | 63 | D1, D4, D5, D7–D9, D11–D14, D16, D17–D26, D27–D36, D38–D43, D44–D58, D59, D60, D62–D65, D67–D71 |
| À RÉEXAMINER | **5** | D2, D6, D10, D15 (double mode) ; D37 (conflit tiers payant) |
| CADUQUE | 2 | D61 (remplacée par la règle du jour calendaire et D75) ; D66 (remplacée par l'administrateur unique) |

**Conséquence** : **70 décisions sur 86** ne sont pas arbitrées (contrôle : 16 + 63 + 5 + 2 = 86). La v1.2.1 est une **base consolidée** et non une conception
validée. L'arbitrage de D3 **ne valide pas** le moteur de base (ARB-01). Les lots dépendant de décisions non arbitrées sont signalés au §16.2.

Questions nouvelles sans numéro de décision : ARB-01 à ARB-14 (`REGISTRE_POINTS_OUVERTS_PHARMOS_v1.2.1.md` §3) ; **ARB-02 est fermé** (rattaché à D3), 13 restent ouvertes.

---

## 19. Risques

### 19.1 Risques techniques bloquants (à lever avant le Lot 0)

| # | Risque | Impact | Mitigation | Prototype |
|---|---|---|---|---|
| RB1 | Atomicité différente entre D1 (`batch`) et SQLite (transaction) : la même opération pourrait laisser un état partiel dans un mode | Critique | Unité de travail commune avec gardes ; tests d'échec injecté dans les deux modes | PT-01, PT-02 |
| RB2 | Contraintes différées, `RAISE(ABORT)` et déclencheurs d'immutabilité non supportés de façon identique | Élevé | Prototype ; solution de repli documentée (EXT06 CP8) | PT-03 |
| RB3 | Une base par pharmacie (D3, arbitrée) : routage, migrations sur N bases, limites de l'hébergeur selon le moteur | Élevé | Prototypes ; comparaison des moteurs ; **aucun repli vers une base partagée** | PT-05, PT-06, PT-13 |
| RB4 | Recalcul des métriques ou génération comptable au-delà des limites d'exécution (Workers) | Moyen | Découpage par dimension, tâches différées | PT-04 |
| RB5 | Installation locale : système d'exploitation, droits d'administration, antivirus, mises à jour, horloge | Élevé | Prototype d'installateur sur le matériel cible (ARB-08) | PT-07, PT-08 |
| RB6 | Sauvegarde à chaud cohérente de chaque base sans bloquer la caisse | Élevé | Mécanisme de sauvegarde en ligne du moteur retenu (ARB-01) ; test de charge | PT-09 |
| RB7 | Recherche plein texte (FTS5) disponible dans les deux moteurs | Moyen | Prototype ; repli par trigrammes (EXT06 §7) | PT-10 |
| RB8 | Divergence progressive des deux modes | Critique | Ports et adaptateurs, suite de conformité, CI double (DEP-01, DEP-04) | PT-11 |
| RB9 | Signature et vérification des paquets (mises à jour, référentiels, licences) hors ligne | Moyen | Prototype de la chaîne de signature | PT-12 |

### 19.2 Autres risques (reprise du dossier §11.1, complétée)

| # | Risque | Impact | Mitigation |
|---|---|---|---|
| RG1 | Fuite de données entre pharmacies | Critique | Base physiquement distincte par pharmacie (D3), routage depuis la session authentifiée, contexte obligatoire, tests MOF, DEP-03, DEP-13 |
| RG2 | Réglementation non validée (registres, facturation électronique, services, comptabilité) | Élevé (légal) | Paramétrage, aucune valeur présumée, lots conditionnés aux points R |
| RG3 | Compromission du compte Super Admin (compte unique, identifiant de messagerie public) | Critique | MFA forte, récupération à double contrôle sans e-mail, aucun accès aux données métier par défaut, journal plateforme, alertes (§6.4) |
| RG4 | Perte de données en mode B (panne, vol, rançongiciel) | Critique | Sauvegardes planifiées, second support, chiffrement, restauration guidée, responsabilités contractuelles (R55) |
| RG5 | 70 décisions non arbitrées | Élevé | Arbitrage par lot avant chaque développement (§16.2) |
| RG6 | Erreurs d'arrondi | Élevé | Millimes entiers, arrondis centralisés |
| RG7 | Cohérence du stock en accès concurrents | Élevé | Registre immuable, gardes atomiques, idempotence |
| RG8 | Hallucinations du LLM | Moyen | Chiffres déterministes, outils en lecture seule, connecteur optionnel |
| RG9 | Volume d'écrans (≈ 90 avec les extensions), trois langues, trois formats | Élevé | Design system unique dès le Lot 0, critères §14.3, tests RSP automatisés |
| RG10 | Comptabilité mal interprétée comme officielle | Moyen | Mention obligatoire « Comptabilité à titre indicatif » (D80), exports destinés à l'expert-comptable |
| RG11 | Données de santé déduites des services enregistrés (D78) | Moyen | Traitement sensible de `service_acts` (§10.2 S4), R54 |

---

## 20. Traçabilité des modifications

### 20.1 Ce que la v1.2 change par rapport au dossier v1.1

| § v1.2 | Changement | Origine |
|---|---|---|
| En-tête, §1 | Statut de consolidation, inventaire documentaire, méthode, règle de priorité | Mission v1.2 |
| §2.3, §6.1–6.4 | Super Admin APEX, cycle de vie des accès, suppression des données distincte, récupération | D72 |
| §3.2 | Principes P14 à P20 | Double mode, D72, D80, D83, D84, responsive |
| §4 | Cartographie consolidée M00–M20 (M15 à M20 ajoutés), niveau plateforme, matrice des dépendances | EXT02–EXT06 |
| §5.1, §13 | Double mode de déploiement, architecture portable, organisation du code | Exigence du propriétaire |
| §5.2, §14 | Responsive par usage, critères par module | Exigence du propriétaire |
| §5.3 | Anglais exigé sur tous les écrans | Exigence du propriétaire |
| §6.5–6.10 | Droits effectifs consolidés, délégations, dérogations (D73), réintégration (D74), accueil (D77), corrections des rôles par défaut | EXT05, EXT06, D73, D74, D77 |
| §7 | Modèle consolidé : répartition des bases, 4 tables nouvelles, 14 tables modifiées, inventaire de 243 tables | v1.2 ; extensions |
| §8 | Flux consolidés (stock, dispensation, finance, comptabilité, corrections, audit, déconnecté) | Extensions ; D76, D80, D85 |
| §9 | Règles par domaine avec référence et statut | Extensions ; D74–D86 |
| §10 | Deux échelles de confidentialité ; services traités comme sensibles | EXT02, EXT03 ; D78 |
| §11 | IA en connecteur optionnel | Double mode |
| §15 | Tests nouveaux (ADM, ANN-18 à 21, NAV-05 à 07, SRV-08 à 12, CAT-14 à 16, FIN-21 à 25, RSP) ; tests obsolètes signalés | D72–D86 ; responsive |
| §16 | Lot P0, Lot 1b, Lot 1 étendu, Lot 15 réduit ; lots conditionnés | Double mode ; arbitrages |
| §17 | Nature des énoncés ; R52 à R55 | Mission v1.2 |
| §19 | Risques bloquants RB1 à RB9 | Double mode |

### 20.1 bis Ce que la v1.2.1 change par rapport à la v1.2

| § v1.2.1 | Changement | Origine |
|---|---|---|
| En-tête, §1 | Version 1.2.1, historique, livrables v1.2 conservés ; interprétation de D72 tranchée ; 244 tables | Mission v1.2.1 |
| §4, §5.1, §5.4 | Une base physiquement distincte par pharmacie dans les deux modes | Arbitrage D3 |
| §7.2 | Titre [DÉCIDÉ D3] ; suppression de l'alternative « base partagée » ; règles d'exploitation par base | Arbitrage D3 |
| §7.3.2, §7.4 | Renommages `shift_coverage_rules` / `payer_coverage_rules` présentés comme **proposés** ; M18 : 32 tables ; total 244 | INC-01 ; contrôle d'inventaire |
| §12, §13 | Isolation et migrations par base ; moteur neutre (T2, T5) ; T4 décidée | D3 ; ARB-01 ouvert |
| §14.4, §15.3, §15.4 | Critères responsive transverses ; RSP-13 à RSP-16 | Exigence du propriétaire |
| §16.2 | P0 : ARB-02 retiré des prérequis ; PT-13 | D3 ; ARB-01 |
| §17.1, §18, §19 | Exemple neutre ; 16 / 63 / 5 / 2 ; 70 décisions ; RB3, RB6, RG1, RG5 | D3 |

### 20.2 Sections du dossier v1.1 remplacées en v1.2

| Section v1.1 | Remplacée par | Remarque |
|---|---|---|
| §2.2 (liste des modules M00–M16) | §2.2, §4.2 | M17 à M20 ajoutés |
| §3.2 Cartographie | §4 | — |
| §4 Modèle de données | §7 et documents sources | La v1.1 reste la référence des colonnes non modifiées |
| §6.1, §6.2, §6.4–§6.7 | §13 et matrice de déploiement | Cloudflare devient un adaptateur possible |
| §9.1 Rôles | §6.10 | — |
| §10 Plan | §16 | — |
| §11.1 Risques, §11.2 Décisions, §11.4 Extensions | §19, §18, §1.1 | — |

Les sections non citées (§1 analyse des captures, §5 flux détaillés initiaux, §7 moteur IA, §8 avatar, §9.2–§9.5) restent
valables **telles que rédigées dans la v1.1**, sous réserve des compléments indiqués dans la présente version.

### 20.3 Documents non modifiés

Vérification par empreintes SHA-256 avant et après la consolidation : `CONCEPTION_PHARMOS_v1.md`, EXT02, EXT03 v1.0,
EXT04, EXT05, EXT06 v1.0, EXT06 v1.1, rapport de revue EXT06 v1.1, archive EXT03 v1.1 — **identiques**. Aucun fichier de
code (`src/`, configuration) n'a été modifié ; aucune table créée ; aucune migration ; aucun déploiement. **v1.2.1** : même vérification,
étendue aux cinq livrables v1.2, **identiques** ; aucun code, schéma, migration ni déploiement.

### 20.4 Journal des décisions

| Date | Événement | Référence |
|---|---|---|
| 2026-11-03 | Dossier v1.0 | — |
| 2026-11-03 | Dossier v1.1 (intégration d'EXT02) | EXT02 |
| 2026-11-03 | Propositions EXT03, EXT03 v1.1 (audit), EXT04, EXT05 | — |
| 2026-11-03 | EXT06 v1.0 ; revue ; EXT06 v1.1 validée dans son principe ; D73 et D75 arbitrées | Revue EXT06 |
| 2026-11-03 | Arbitrages D72, D74, D76 à D86 ; exigences du double déploiement et du responsive | Prompt de consolidation v1.2 |
| 2026-11-03 | Dossier v1.2 (consolidation) soumis à validation | `CONCEPTION_PHARMOS_v1.2.md` (conservé) |
| 2026-11-04 (enregistrement ; date d'arbitrage non précisée) | **D3 arbitrée** : base de données physiquement distincte par pharmacie ; ARB-02 fermé | Mission v1.2.1 |
| 2026-11-04 | **Dossier v1.2.1 (correction documentaire) soumis à validation** | Le présent document |

*Fin du dossier de conception consolidé v1.2.1 — correction documentaire uniquement, aucun code.*