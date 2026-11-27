# PHARM'OS — Extension fonctionnelle n° 06 : Gouvernance des droits, catalogue, services et finance

**Produit** : PHARM'OS — APEX AI DEV
**Version** : **1.1** (corrections après revue architecturale et fonctionnelle) — 2026-11-03
**Statut** : **VALIDÉE DANS SON PRINCIPE, CORRECTIONS CIBLÉES INTÉGRÉES — SOUMISE À REVUE DOCUMENTAIRE**. Aucun code,
aucune migration. Le dossier principal (`CONCEPTION_PHARMOS_v1.md` v1.1) n'est pas modifié. La version 1.0
(`EXT06_CATALOGUE_GOUVERNANCE_FINANCE.md`) est conservée sans modification. Les changements sont listés au §8 et ne
seront reportés dans la v1.2 qu'après validation.
**Base de conception** : dossier principal v1.1, EXT02 (intégrée), EXT03 v1.1 (dossier d'audit), EXT04 et EXT05 (propositions).
**Rapport de revue associé** : `EXT06_v1.1_RAPPORT_DE_REVUE.md` (modifications par section, matrices, cohérence).

> **Historique des versions**
>
> | Version | Date | Objet |
> |---|---|---|
> | 1.0 | 2026-11-03 | Proposition initiale |
> | 1.1 | 2026-11-03 | Revue : (1) immutabilité et cycle de vie des écritures M20 ; (2) exceptions comptables ; (3) D73 retenue, validation à distance encadrée ; (4) D75 retenue, option « jour d'exploitation » ; (5) définition technique de la couche métriques ; (6) compatibilité multi-officines ; (7) analyse de rentabilité commerciale ; (8) contrôle des impacts. Corrections induites : `permission_exceptions` (transitions contrôlées au lieu de « immuable »), double comptage CA services / autres produits supprimé |
>
> Les passages modifiés ou ajoutés en v1.1 sont repérés par **[v1.1]**.

> **Règle de ce document : aucun doublon.** EXT06 crée un seul module nouveau (**M20 Comptabilité**, partie de
> PHARM'OS FINANCE & ACCOUNTING) et une seule famille de tables nouvelle par besoin réellement absent. Tout le reste
> **réutilise, complète ou remplace explicitement** une table existante. Le §0 donne la correspondance complète.
> Repères : **[À VALIDER Rxx]** = point réglementaire (§9) · **[ARBITRAGE Dxx]** = décision du propriétaire (§11).

---

## 0. Analyse des chevauchements (à lire en premier)

| Demande EXT06 | Existant | Traitement EXT06 |
|---|---|---|
| Délégations nominatives, dates, plafonds, révocation, historique, pas de redélégation, réglementés exclus | EXT05 §5.3 (`delegations`, `delegation_usage`, `delegable_permissions`, règles G1–G10) | **Réutilisé.** EXT06 ajoute : (1) le **pharmacien administrateur** unique comme seul gestionnaire (tranche D66 et le durcit) ; (2) la date de début devient obligatoire, elle l'était déjà en fin ; (3) les dépassements de plafond réservés à l'administrateur |
| Modules accessibles par employé, matrice accès / consultation / création / … | EXT02 §2.2 catalogue `permissions` (module, sensibilité, habilitation, présence) ; `role_permissions` ; EXT05 `permission_overrides` | **Complété.** Ajout des colonnes `module_code` et `action` au catalogue `permissions`, plus un référentiel `app_modules`. **Aucune** table de matrice parallèle |
| Annulation le jour calendaire, dérogation par l'administrateur | EXT05 §2.3, §4.3, D61 (15 min) | **Remplace** D61 et la destination `DISPONIBLE_DIRECT` d'EXT05 (§5.2) |
| Statuts de stock contrôle / quarantaine / perte, mouvements inverses | EXT05 §3.3 (`stock_buckets`), §4.4, §7.3 | **Réutilisé.** Une correction : le bucket `RESERVE` d'EXT05 est supprimé, la réservation reste un engagement sur le stock disponible (`stock_reservations` du dossier principal) |
| Fiche médicament | Dossier §4.3 `ref_products`, `products`, `ref_dci`, `ref_laboratories`, `ref_forms`, `product_barcodes` ; EXT03 `prescription_policy`, `substitution_group_id` | **Complété** (statut de commercialisation, marque, unités de vente, sensibilité). Équivalents et substitution : **unification** des notions d'EXT03 dans une seule structure (§4.3) |
| Fiche lot séparée, FEFO | Dossier §4.4 `lots`, `stock_movements`, `stock_levels` | **Complété** (date de réception, quantité reçue, numéro de série facultatif). La quantité disponible reste **calculée**, jamais stockée sur le lot |
| Classification multiple | Dossier §4.3 `categories` (un seul parent, une catégorie par produit) | **Remplacé** par une classification multi-axes (§4.4). `categories` et `products.categorie_id` sont supprimés |
| Catégorie de TVA | Dossier `products.tva_bp` (taux saisi sur le produit) | **Remplacé** par des catégories fiscales historisées (`tax_categories`, `tax_rates`) |
| Accessoires, dispositifs, parapharmacie, beauté | `products.type` (médicament / parapharmacie / dispositif / autre) | **Complété** : types précisés, modèles et variantes (pointures, tailles), attributs par type. Aucune table produit séparée |
| Services pharmaceutiques | Absent (EXT02 M16 Laboratoire traite les préparations, pas les prestations) | **Nouveau** : un service est un `product` de type `SERVICE` (non stocké), avec une extension `service_definitions` et un registre `service_acts`. Il est vendu par les mêmes `sale_lines` |
| Répertoire des médecins et spécialités | EXT03 M17 (`prescribers`, `medical_specialties`) | **Réutilisé.** EXT06 ajoute l'accès direct depuis l'accueil et la liste initiale des spécialités (données, pas de table) |
| Organismes et conventions, suspension | EXT04 M18 (`payers`, `payer_agreements`, `agreement_suspensions`, §4.6) | **Réutilisé.** Accès direct depuis l'accueil. La règle demandée (consultable mais non proposée après suspension) existe déjà (EXT04 E2, W10) |
| Charges, fournisseurs, dettes, acomptes, rapprochements | Dossier §4.6–4.8 (`supplier_invoices`, `suppliers`, `party_ledger`, `due_dates`, `payments`, `bank_accounts`, `expenses`) | **Complété** : nature des factures (marchandises / charges), catégories de charges, emprunts, rapprochement bancaire, immobilisations. `expenses` est restreint aux dépenses payées en caisse |
| Personnel (variables financières) | EXT02 §5.5 (export des variables de paie), `employee_compensation` | **Complété** : import mensuel des montants de paie, sans moteur de paie |
| Créances CNAM et assurances réglées | EXT04 §4.7, §7 (règlement = encaissement lettré, jamais une vente) | **Réutilisé** et repris dans les définitions financières (§5.6) |
| Comptabilité (journaux, grand livre, balance, exports) | Dossier : « PHARM'OS n'est pas un logiciel de comptabilité » ; EXT04 §7.3 (journaux exportés) | **Nouveau module M20** : pré-comptabilité en partie double générée depuis les événements (§4.8). `party_ledger` reste le grand livre auxiliaire des tiers |
| Tableaux de bord financier et commercial | M01, M12, `metrics_daily`, EXT03 `rx_facts`, EXT04 projections | **Complété** : nouveaux indicateurs dans la **même** couche métriques. Aucun calcul propre aux tableaux de bord |
| Page d'accueil par familles | Dossier §3.3 : `/` = M01 Tableau de bord | **Nouveau point d'entrée** `/home` (accueil opérationnel). M01 reste le tableau de bord. Le choix de la page affichée après connexion dépend du rôle |
| **[v1.1]** Validation à distance par l'administrateur | EXT05 `corrective_operations.approval_method = DEMANDE_A_DISTANCE` ; `permission_exceptions` (v1.0) ; MFA du dossier (`users.mfa_secret`) | **Complété** : colonnes d'autorisation à usage unique sur `permission_exceptions` (§4.1). **Aucune table nouvelle** |
| **[v1.1]** Brouillons d'écritures comptables | v1.0 : statut `BROUILLON` dans `journal_entries` déclarée immuable (contradiction) | **Séparé** : `journal_entry_drafts` (modifiable, jamais comptabilisée) ; `journal_entries` ne contient plus que des écritures validées (§4.9) |
| **[v1.1]** Suivi des anomalies comptables | Absent. `quality_events` (EXT02) concerne la qualité RH, `claims` (EXT04) les rejets d'organismes : aucun ne couvre la comptabilité | **Nouveau** : `accounting_exceptions` (§4.9, §5.13) |
| **[v1.1]** Définition technique des métriques | Dossier §4.11 `metrics_daily` (tenant_id, date, cle, dimension, valeur) ; EXT03 `rx_facts` ; EXT04 projections | **Complété** : clé unique, grains, rattachement, recalcul (§4.10, §5.6 bis) + référentiel `metric_definitions` (définitions versionnées, source unique) |
| **[v1.1]** Préparation multi-officines | Dossier `tenants`, `memberships`, `sites` (D15 : « modèle prêt, activation plus tard ») | **Aucune table nouvelle** : principes d'architecture MO1–MO10 (§1.4, §5.11) ; tables futures **réservées, non créées** |
| **[v1.1]** Option « jour d'exploitation » (gardes) | Dossier `settings` ; EXT02 `duty_periods` (gardes) | **Réutilisés** : paramètre versionné dans `settings`, période de garde lue dans `duty_periods` (§5.2) |
| **[v1.1]** Analyse de rentabilité commerciale | M12, analyseur C du dossier (faible rotation), `product_demand_stats`, `stock_levels` | **Complété** : indicateurs dans la couche métriques (§5.12), écran G25. Aucune table nouvelle |

**Bilan [v1.1]** : 1 module nouveau (M20) ; **43 tables nouvelles** (17 gouvernance, catalogue et fiscalité ; 3 services ;
12 finance ; 9 comptabilité M20 ; 2 métriques — détail en fin de document ; v1.0 : 40, + `journal_entry_drafts`,
`accounting_exceptions`, `metric_definitions`) ; remplacements : `categories`, `product_prices_history`, champ
`products.tva_bp`, champs de substitution d'EXT03, bucket `RESERVE` d'EXT05 ; 1 table restreinte (`expenses`) ;
0 table en double.

---

## Sommaire

1. Architecture fonctionnelle
2. Nouveaux écrans
3. Workflows
4. Tables et relations
5. Règles métier
6. Permissions
7. Dépendances
8. Impacts sur les modules existants
9. Exigences réglementaires
10. Matrice de tests
11. Décisions à arbitrer
12. Positionnement dans le plan de développement

---

## 1. Architecture fonctionnelle

### 1.1 Les quatre évolutions et leur place

```
┌──────────────────────────────── ACCUEIL OPÉRATIONNEL (/home) ────────────────────────────────┐
│  Recherche universelle (nom · DCI · code-barres · famille · laboratoire · marque · service)  │
│  [Médicaments] [Dispositifs & accessoires] [Parapharmacie & beauté] [Services]               │
│  [Médecins & spécialités → M17]  [CNAM, assurances & conventions → M18]                      │
└──────────────────────────────────────────────────────────────────────────────────────────────┘
        │                         │                          │                         │
        ▼                         ▼                          ▼                         ▼
 PARTIE 1 GOUVERNANCE      PARTIE 3-4 CATALOGUE        PARTIE 2 CORRECTIONS     PARTIE 7 FINANCE
 (M00 + EXT02 + EXT05)     (M04 + M05 étendus)         (M19 EXT05 amendé)       (M08 étendu + M20 nouveau)
 · administrateur unique   · fiche produit enrichie    · règle du jour          · charges et dettes
 · modules × actions       · classification multi-axes   calendaire             · emprunts, immobilisations
 · délégations (EXT05)     · substitution unifiée      · dérogation réservée    · rapprochement bancaire
 · plafonds, dérogations   · lots et FEFO                à l'administrateur     · pré-comptabilité (M20)
                           · dispositifs, variantes    · contrôle de            · tableaux de bord
                           · services (type SERVICE)     réintégration            (couche métriques)
                                     │
                                     ▼
                      COUCHE MÉTRIQUES UNIQUE (dossier §3.2, EXT03 rx_facts, EXT04 créances)
                      → M01 Tableau de bord · tableaux de bord financier et commercial · M12 · M13 IA
```

### 1.2 Principes

1. **Une seule source par donnée.** Un produit, un lot, un médecin, un organisme ou une permission n'existe qu'à un endroit.
   Les écrans d'accès direct (accueil, tuiles) sont des **vues** sur ces sources.
2. **Le pharmacien administrateur est l'autorité de gouvernance de l'officine.** Il est seul à créer, modifier et révoquer les
   délégations, à accorder les réglages individuels et à autoriser un dépassement de plafond ou une dérogation de délai.
3. **Droits vérifiés côté serveur à chaque requête.** L'interface masque ce qui n'est pas autorisé, mais ce masquage n'est
   jamais la seule barrière. L'accès à un module n'implique aucune action dans ce module.
4. **Le catalogue décrit, la configuration décide.** Les informations réglementaires (classe, statut de prescription,
   substitution, prix réglementé) proviennent de référentiels sourcés et sont validées par un pharmacien. Rien n'est déduit
   automatiquement d'un libellé.
5. **Distinguer clairement les grandeurs financières** : chiffre d'affaires, marge, créances, dettes, encaissements,
   décaissements, trésorerie, résultats. Chacune a une définition, une source et une formule uniques (§5.6).
6. **La pré-comptabilité est dérivée, jamais saisie en double** : les écritures sont générées depuis les événements métier par
   des règles de passation versionnées. L'expert-comptable reste responsable des comptes officiels. **[v1.1]** Une écriture
   validée est **strictement immuable** ; toute correction passe par une contre-passation et une nouvelle écriture. Une erreur
   comptable **ne bloque, ne modifie et ne supprime jamais** une opération métier validée : elle ouvre une exception comptable
   suivie jusqu'à résolution (§5.13, §5.14).
7. **Aucune valeur réglementaire ou fiscale n'est fournie par défaut** : taux de TVA, plan de comptes, durées d'amortissement,
   services autorisés et substitutions sont saisis à partir de sources, puis validés.

### 1.3 Modules concernés

| Code | Module | Statut dans EXT06 |
|---|---|---|
| M00 | Plateforme (administrateur, modules, permissions) | Complété |
| M04 | Catalogue | Complété (classification, fiscalité, substitution, variantes, services) |
| M05 | Stock | Complété (lots, buckets amendés) |
| M08 | Finance (devient **PHARM'OS FINANCE**) | Complété (charges, emprunts, banque, immobilisations, paie importée) |
| **M20** | **Comptabilité (PHARM'OS ACCOUNTING)** | **Nouveau** |
| M01 / M12 | Tableaux de bord et rapports | Complétés (indicateurs financiers et commerciaux) |
| M17, M18, M19 | Ordonnances, tiers payant, corrections | Réutilisés (M19 amendé sur deux règles) |
| — | Accueil opérationnel | Nouvel écran (pas un module : un point d'entrée) |

### 1.4 Compatibilité multi-officines **[v1.1]**

EXT06 ne développe **aucun module multi-officines**. Elle fixe les règles qui permettront plus tard à un propriétaire
d'exploiter plusieurs officines sans refonte, et sans jamais affaiblir l'isolation entre tenants.

```
                     (futur, non créé)  ORGANISATION / PROPRIÉTAIRE
                                  │   vue consolidée en LECTURE SEULE, agrégats uniquement,
                                  │   sur autorisation explicite de chaque administrateur d'officine
            ┌─────────────────────┼─────────────────────┐
            ▼                     ▼                     ▼
   TENANT = OFFICINE A    TENANT = OFFICINE B    TENANT = OFFICINE C
   · son administrateur   · son administrateur   · …
   · ses données, ses     · ses données, ses
     utilisateurs, ses      utilisateurs, ses
     permissions            permissions
   · sa comptabilité M20  · sa comptabilité M20
   · ses paramètres       · ses paramètres
   · `sites` = dépôt ou annexe de CETTE officine (dossier D15), jamais une autre officine
```

- **Une officine = un tenant.** C'est la seule lecture compatible avec l'exigence d'un administrateur, d'une
  comptabilité et de paramètres propres à chaque officine. Le modèle `sites` du dossier (D15) reste réservé aux dépôts et
  annexes d'une même officine. Cette lecture **précise** D15 sans la modifier (à confirmer lors de l'intégration v1.2).
- Les règles détaillées (MO1 à MO10) sont au §5.11 ; les tests au §10.10.

---

## 2. Nouveaux écrans

| Code | Écran | Route | Partie | Usage |
|---|---|---|---|---|
| G1 | **Accueil opérationnel** | `/home` | 8 | Recherche universelle, 6 tuiles, raccourcis selon le rôle ; tactile, compatible avec l'écran de caisse |
| G2 | Gouvernance — administrateur de l'officine | `/settings/governance` | 1 | Administrateur en titre, historique des désignations, procédure de transfert |
| G3 | **Matrice des droits** | `/settings/permissions` | 1 | Lignes = modules, colonnes = actions ; vue par rôle, par collaborateur (droit effectif expliqué), écarts au rôle |
| G4 | Délégations (écran X9 d'EXT05 complété) | `/settings/delegations` | 1 | Création, plafonds, dates, opérations, révocation, historique ; réservé à l'administrateur |
| G5 | Dérogations | `/settings/overrides` | 1-2 | Dépassements de plafond et dérogations de délai accordés, demandés, refusés |
| G6 | Explorateur de catalogue | `/catalog?type=&axis=` | 3 | Navigation par type de produit et par axe de classification ; filtres combinables (facettes) |
| G7 | Fiche produit (complétée) | `/catalog/:id` | 3 | Onglets : identité, classification, prix et fiscalité, équivalents et substitution, lots, mouvements, variantes, réglementaire, historique |
| G8 | Fiche lot | `/catalog/:id/lots/:lotId` | 3 | Réception, péremption, quantités par état, prix d'achat, fournisseur, statut, mouvements |
| G9 | Groupes de substitution | `/catalog/substitution-groups` | 3 | Groupes, membres, source, validation pharmacien |
| G10 | Axes de classification | `/settings/classification` | 3 | Arbres par axe (familles commerciale et thérapeutique, univers) |
| G11 | Catégories fiscales | `/settings/tax` | 3, 7 | Catégories et taux historisés, avec leur source |
| G12 | **Catalogue des services** | `/services` | 4 | Prestations, tarifs, durée, habilitations, activation réglementaire |
| G13 | Registre des prestations | `/services/acts` | 4 | Prestations réalisées (date, heure, collaborateur, patient facultatif, facturation) |
| G14 | Charges | `/finance/charges` | 7 | Saisie, pièces, échéances, statut de paiement, catégories |
| G15 | Emprunts et crédits | `/finance/loans` | 7 | Contrats, tableaux d'amortissement, échéances |
| G16 | Banque et rapprochement | `/finance/bank` | 7 | Import des relevés, rapprochement proposé puis confirmé |
| G17 | Immobilisations | `/finance/assets` | 7 | Registre des investissements |
| G18 | Paie importée | `/finance/payroll` | 7 | Import mensuel des montants de paie, contrôle avec les variables RH |
| G19 | Plan de comptes et règles de passation | `/accounting/setup` | 7 | Comptes, journaux, correspondances événement → écriture |
| G20 | Journaux | `/accounting/journals/:code` | 7 | Ventes, achats, caisse, banque, opérations diverses |
| G21 | Grand livre et balance | `/accounting/ledger`, `/accounting/trial-balance` | 7 | Consultation, lettrage des tiers, export |
| G22 | Périodes comptables | `/accounting/periods` | 7 | Clôture mensuelle, verrouillage, réouverture contrôlée |
| G23 | **Tableau de bord financier** | `/analytics/finance` | 7F | Indicateurs du §5.7 |
| G24 | **Tableau de bord commercial** | `/analytics/sales` | 7G | Indicateurs du §5.8 |
| G25 | **Analyse de rentabilité commerciale** **[v1.1]** | `/analytics/profitability` | 7G | Indicateurs du §5.12 : marge × rotation, capital immobilisé, risque de péremption, familles, laboratoires, marques |
| G26 | **Exceptions comptables** **[v1.1]** | `/accounting/exceptions` | 7E | File des anomalies : type, événement d'origine, montant, responsable, échéance, statut, résolution (W14) |
| G27 | Brouillons d'écritures **[v1.1]** | `/accounting/drafts` | 7E | Opérations diverses et régularisations en préparation, soumission, validation par une seconde personne (W15) |
| G28 | Décision à distance de l'administrateur **[v1.1]** | `/approvals/:id` (appareil enrôlé) | 1 | Écran W4-D : opération identifiée, motif, confirmation explicite, MFA |

Les écrans existants restent la référence pour leur domaine : `/prescribers` (EXT03 E4), `/payers` (EXT04 T1),
`/corrections` (EXT05 X8), `/` (M01 Tableau de bord).

### 2.1 Accueil opérationnel (G1)

```
┌──────────────────────────────────────────────────────────────────────────────────────────┐
│ 🔍 [ Nom commercial, DCI, code-barres, famille, laboratoire…            ] [Scanner]        │
│    Suggestions : AMOXICILLINE (DCI, 6 produits) · AMOXIL 500 mg · Labo X · Famille…       │
├──────────────────────────────────────────────────────────────────────────────────────────┤
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐                                       │
│  │ 💊           │ │ 🦴           │ │ 🧴           │                                       │
│  │ Médicaments  │ │ Dispositifs  │ │ Parapharmacie│                                       │
│  │ 18 240 réf.  │ │ & accessoires│ │ & beauté     │                                       │
│  └──────────────┘ └──────────────┘ └──────────────┘                                       │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐                                       │
│  │ 🩺 Services  │ │ 👨‍⚕️ Médecins  │ │ 🏛 CNAM,     │                                       │
│  │ pharmaceut.  │ │ & spécialités│ │ assurances   │                                       │
│  └──────────────┘ └──────────────┘ └──────────────┘                                       │
├──────────────────────────────────────────────────────────────────────────────────────────┤
│ Raccourcis (selon le rôle) : [Nouvelle vente F2] [Ordonnance F4] [Réception] [Corrections] │
└──────────────────────────────────────────────────────────────────────────────────────────┘
(chiffres fictifs ; les tuiles non autorisées par la matrice des droits ne sont pas affichées)
```

- Cibles tactiles d'au moins 48 × 48 px, contrastes élevés, disposition RTL en arabe, utilisable avec un lecteur de codes-barres sans souris.
- Une tuile ouvre l'explorateur de catalogue filtré (G6) ou l'écran du module concerné (M17, M18, G12).
- La recherche universelle interroge un **index unique** (§5.10) et renvoie des résultats regroupés : produits, molécules,
  familles, laboratoires et marques, services, plus médecins et organismes si les droits le permettent.
- L'accueil **n'affiche aucun indicateur financier** : il reste un point d'entrée opérationnel (les tableaux de bord sont à part).

---

## 3. Workflows

### 3.1 W1 — Désignation et transfert du pharmacien administrateur

```
Création de l'officine (onboarding, dossier Lot 1) → le titulaire déclaré devient administrateur
  (qualification « pharmacien » vérifiée, EXT02 §2.2 ; MFA obligatoire)
Transfert (vente de l'officine, changement de titulaire, intérim long) :
  administrateur sortant initie → désigne un collaborateur ayant la qualification de pharmacien vérifiée
  → l'entrant accepte (MFA) → effet à une date → toutes les délégations accordées par le sortant restent valides
    jusqu'à leur fin, mais l'entrant peut les révoquer
Perte d'accès de l'administrateur (décès, incapacité, conflit) :
  procédure de secours hors application : demande écrite au support APEX AI DEV avec justificatifs
  (document officiel désignant le nouveau responsable) → double contrôle APEX → désignation tracée
  [ARBITRAGE D72] — le support APEX n'obtient jamais d'accès aux données à cette occasion
```

### 3.2 W2 — Configuration des droits d'un collaborateur

```
Administrateur → Matrice des droits (G3) → collaborateur
  1. Rôle de base (EXT02) : modules et actions par défaut
  2. Modules accessibles : cocher / décocher un module entier (désactive toutes ses actions, sans les effacer)
  3. Actions par module : accès, consultation, création, modification, validation, annulation, export, archivage
  4. Plafonds : montant par opération, montant par jour, nombre par jour, délai
  5. Le système affiche pour chaque case le **droit effectif** et sa raison (rôle, réglage individuel, délégation,
     habilitation manquante, qualification absente, action non délégable)
  6. Validation par MFA de l'administrateur → enregistrement dans `permission_overrides` (EXT05), audit
```

### 3.3 W3 — Délégation (EXT05 §2.7, amendé)

Identique à EXT05 W-E, avec trois changements : seul l'**administrateur** peut déléguer (plus de « délégant désigné ») ;
la **date de début est obligatoire** ; la liste des opérations est sélectionnée parmi les couples (module, action) de la
matrice, ce qui interdit toute délégation « globale ».

### 3.4 W4 — Dépassement de plafond ou dérogation de délai

```
Collaborateur atteint un plafond (montant, nombre, délai) → opération bloquée, message explicite
  → « Demander une dérogation » : motif, opération concernée (pré-remplie)
  → notification à l'administrateur (et uniquement à lui)
  → l'administrateur accorde (unitaire : cette opération seulement) ou refuse (motif)
  → si accordée : l'opération est exécutée par le demandeur ou par l'administrateur ; `permission_exceptions`
    enregistre la dérogation, liée à l'opération ; elle ne peut pas être réutilisée
Dérogation présentielle : l'administrateur saisit son PIN et confirme sur le poste du collaborateur
Absence de l'administrateur : validation à distance (W4-D ci-dessous) ; à défaut, l'opération attend ou est refusée
```

#### W4-D — Validation à distance par le pharmacien administrateur **[v1.1 — D73 retenue]**

```
1. DEMANDE (poste du collaborateur)
   Le serveur crée `permission_exceptions` statut DEMANDEE avec :
     operation_ref (type + id), operation_fingerprint = SHA-256 de la forme canonique de l'opération
     (type, id, lignes, quantités, montants, tenant, demandeur), valeur_demandee, plafond_applicable, motif du demandeur,
     expire_le = maintenant + délai paramétrable (`settings.remote_approval_ttl`, borné par le serveur)
   → notification à l'administrateur actif UNIQUEMENT (canal in-app sur son appareil enrôlé ; aucun lien d'action
     dans un e-mail ou un SMS)

2. EXAMEN (appareil de l'administrateur)
   Écran de décision : opération identifiée (numéro, client masqué selon les droits, lignes, montants, demandeur,
   heure, motif), plafond dépassé, historique des dérogations du demandeur sur 30 jours

3. DÉCISION
   a. Motif de décision OBLIGATOIRE (texte, longueur minimale paramétrée), y compris en cas de refus
   b. Confirmation explicite : case « J'autorise cette opération précise » + bouton distinct (aucune validation implicite,
      aucune validation groupée de plusieurs demandes)
   c. MFA OBLIGATOIRE au moment de la décision (step-up : une session déjà authentifiée ne suffit pas)
      — facteur enrôlé de l'administrateur (TOTP, `users.mfa_secret` du dossier) ; le PIN de caisse n'est PAS un facteur
        accepté à distance
   d. Le serveur vérifie, dans une seule transaction :
        décideur = administrateur actif à cet instant (`tenant_administrators`) ET décideur ≠ demandeur
        session de l'administrateur valide, MFA vérifié depuis moins de N secondes (paramètre borné)
        statut = DEMANDEE et maintenant < expire_le
        empreinte de l'opération inchangée
      → ACCORDEE (ou REFUSEE) ; émission d'une autorisation à usage unique, stockée uniquement sous forme de
        hachage (`approval_token_hash`), liée à : exception_id, operation_fingerprint, demandeur, tenant, expiration

4. EXÉCUTION (poste du demandeur ou de l'administrateur)
   Dans le batch D1 de l'opération : UPDATE permission_exceptions SET statut = CONSOMMEE, consumed_at, consumed_by
     WHERE id = ? AND statut = ACCORDEE AND expire_le > now AND operation_fingerprint = ? → exactement 1 ligne
   0 ligne → l'opération entière est annulée (autorisation déjà utilisée, expirée, révoquée ou opération modifiée)

5. CAS PARTICULIERS
   - Opération modifiée après la demande (une ligne, un montant) → empreinte différente → nouvelle demande obligatoire
   - Administrateur transféré entre la demande et la décision → demande EXPIREE ; nouvelle demande au nouvel administrateur
   - Administrateur transféré entre la décision et l'exécution → autorisation REVOQUEE (contrôle à l'exécution)
   - Aucune délégation, aucun réglage individuel ne permet à un autre collaborateur de décider à sa place (GV3, GV13)
   - Produits réglementés : la validation à distance ne remplace JAMAIS le quatre-yeux présentiel ni les circuits
     spécifiques d'EXT05 §4.7 ; elle n'est pas proposée pour ces opérations [À VALIDER R48]
```

Journalisation complète (`audit_log` chaîné) : demande, notification, consultation de la demande par l'administrateur,
décision, échec MFA, tentative de réutilisation, expiration, révocation, consommation. Aucun secret (autorisation, code
MFA) n'est journalisé.

### 3.5 W5 — Annulation d'une vente (EXT05 workflow A, amendé)

```
Vente non validée                               → abandon (aucune écriture corrective) — inchangé
Vente validée, produit non remis :
  ├─ produit non sensible, même jour de référence [v1.1 : jour calendaire par défaut, jour d'exploitation si
  │  l'option est activée — §5.2], fuseau de l'officine, dans les droits et plafonds
  │      → annulation par le collaborateur habilité ; produit en EN_CONTROLE (sauf exception D74)
  ├─ produit non sensible, jour suivant ou plus  → dérogation de l'ADMINISTRATEUR obligatoire (W4)
  └─ produit sensible ou réglementé              → circuit spécifique EXT05 §4.7 (quatre yeux, registre) ;
                                                   jamais de réintégration directe
Vente validée, produit remis :
  → annulation après remise ou retour (EXT05 workflows A/B) ; même règle de jour calendaire ;
    au-delà, administrateur
```

### 3.6 W6 — Création d'un produit dans le catalogue

```
Recherche dans le référentiel APEX (`ref_products`) par code-barres ou nom
  ├─ trouvé → création de la fiche locale liée (données de référence en lecture, données locales modifiables)
  └─ absent → produit local : type (médicament, dispositif, parapharmacie, accessoire, service…)
     → champs obligatoires selon le type (§5.3) → classification sur chaque axe obligatoire
     → catégorie fiscale → prix → seuil d'alerte
  → contrôles : doublon (code-barres, nom normalisé + dosage + forme + laboratoire), cohérence prix / coût
  → MÉDICAMENT ou produit réglementé : statut A_VALIDER → validation par un pharmacien (classe réglementaire,
    statut de prescription, substitution) → ACTIF
  → AUTRES TYPES : ACTIF directement (sauf règle locale)
```

### 3.7 W7 — Gestion des équivalences et substitutions

```
Import des groupes de substitution depuis une source officielle ou APEX sourcée → statut A_VALIDER
  ou création manuelle par un pharmacien (source obligatoire : référence officielle ou motif)
  → validation par un pharmacien habilité (`catalog.substitution.validate`) → ACTIF
  → utilisé par EXT03 (qualification automatique des substitutions) et par la caisse (proposition d'équivalents en stock)
Les « équivalents » non substituables (même DCI, autre forme ou dosage) sont affichés à titre informatif :
  ils ne sont jamais proposés comme substitution automatique.
```

### 3.8 W8 — Réalisation et facturation d'une prestation

```
Collaborateur ouvre le catalogue des services (G12) ou ajoute une prestation au panier de caisse
  → contrôles : service ACTIF (autorisation réglementaire valide à la date), collaborateur habilité
    (habilitation liée au service, EXT02), présence pointée si exigée
  → saisie : date et heure (horodatage serveur par défaut), patient facultatif ou obligatoire selon le service,
    consentement si exigé, observations non médicales ou cliniques selon le service [ARBITRAGE D78]
  → produits consommés éventuels (seringue, coton, pansement) : sortie de stock liée à la prestation
  → facturation : prestation gratuite (registre seul) ou ligne de vente au tarif (même caisse nominative)
  → enregistrement dans `service_acts` (immuable ; correction par acte d'annulation lié)
```

### 3.9 W9 — Saisie et paiement d'une charge

```
Saisie (G14) : fournisseur ou tiers, catégorie de charge, date, montants HT / TVA / TTC, échéance, pièce justificative (scan)
  → nature MARCHANDISES : rapprochée d'une réception (Achats, dossier W §5.3), alimente le coût du stock
  → nature CHARGE : aucun effet sur le stock
  → validation (seuil paramétrable, administrateur au-delà) → dette fournisseur (`party_ledger`) + échéance (`due_dates`)
  → paiement (espèces depuis la caisse, chèque, virement, prélèvement) → lettrage → statut de paiement
  → génération des écritures de pré-comptabilité (M20) par les règles de passation
Charges récurrentes (loyer, abonnements) : modèle mensuel qui génère des brouillons à valider (aucune charge validée automatiquement)
```

### 3.10 W10 — Emprunts et échéances

```
Contrat d'emprunt : prêteur, capital, taux, durée, date de début, périodicité, frais, document
  → tableau d'amortissement IMPORTÉ depuis le document de la banque (recommandé) ou calculé puis comparé
  → chaque échéance = capital + intérêts + frais → `due_dates` (à payer)
  → paiement (prélèvement bancaire) → rapprochement → écritures : capital (dette), intérêts (charge financière)
```

### 3.11 W11 — Rapprochement bancaire

```
Import du relevé (CSV, Excel ; format OFX ou autre selon les banques) → lignes de relevé
  → rapprochement proposé : chèques remis (M08), règlements d'organismes (EXT04), virements fournisseurs, prélèvements,
    remises de cartes bancaires
  → confirmation humaine (comptable ou administrateur) ; écarts laissés visibles (frais, agios, rejets)
  → lignes non rapprochées : création d'une écriture (frais bancaires, agios) ou investigation
  → solde bancaire théorique = solde du relevé (contrôle)
```

### 3.12 W12 — Paie importée (sans moteur de paie)

```
Fin de mois : export des variables RH (EXT02 §5.5 : jours, absences, heures supplémentaires, gardes)
  → le comptable ou le logiciel de paie calcule les salaires
  → import du journal de paie (fichier) : par collaborateur, brut, cotisations salariales et patronales, net, retenues
  → contrôle : les collaborateurs et la période correspondent aux variables exportées ; écarts signalés
  → validation administrateur → dettes (salaires nets, organismes sociaux, impôt retenu à la source) + échéances
  → paiements → écritures M20
Données individuelles de paie : niveau N4 d'EXT02 (titulaire seul par défaut), jamais transmises à l'IA
```

### 3.13 W13 — Clôture mensuelle de pré-comptabilité **[v1.1 : contrôles complétés]**

```
Contrôles automatiques (bloquants sauf mention) :
  · sessions de caisse clôturées et validées ; bordereaux traités ; rapprochements bancaires faits
  · TOUS les événements métier de la période sont traités : chacun a son écriture validée OU une exception comptable
    ouverte (aucun événement « en suspens » silencieux)
  · aucune exception comptable BLOQUANTE ouverte sur la période ; exceptions MAJEURES résolues ou justifiées (motif)
  · brouillons d'écritures de la période : comptabilisés, abandonnés ou explicitement reportés (motif)
  · balance de contrôle : Σ débit = Σ crédit sur la période ET pour chaque écriture (recalcul depuis les lignes)
  · intégrité : nombre de lignes et empreinte (`lines_hash`) de chaque écriture recalculés et conformes
  · réconciliations : stock, créances (`party_ledger` ↔ comptes de tiers M20), dettes, caisse, métriques ↔ journal des ventes
  → validation de la période par l'administrateur (ou comptable habilité) → période VERROUILLEE
  → cliché `financial_snapshots` figé
  → export vers le logiciel comptable (contrôles d'export §5.13) → EXPORTEE si export définitif
  → correction après verrouillage : écriture de régularisation dans la période ouverte suivante ;
    réouverture exceptionnelle par l'administrateur avec motif, interdite après export définitif [ARBITRAGE D82]
```

### 3.14 W14 — Traitement d'une exception comptable **[v1.1]**

```
Détection (génération d'écriture, rapprochement bancaire, réconciliation des tiers / de la caisse / du stock / des
métriques, validation d'un brouillon, contrôle d'intégrité, contrôle d'export)
  → `accounting_exceptions` créée OU, si la même anomalie est déjà ouverte (même `detection_key`), occurrences + 1
    · type, gravité, événement métier d'origine (type + id + correlation_id), période,
      MONTANT concerné et MONTANT d'écart (millimes)
    · responsable affecté automatiquement (règle : comptable habilité, sinon administrateur), échéance selon la gravité
  → l'opération métier reste VALIDÉE et inchangée ; aucune écriture partielle n'existe (batch atomique)
Analyse (G26) : le responsable consulte l'opération d'origine, l'erreur, l'historique des occurrences
  → résolution par UNE des voies suivantes :
    1. CORRECTION_PARAMETRAGE puis REGENERATION : compte activé, ou règle de passation corrigée (nouvelle version validée
       à quatre yeux) → l'événement est retraité (traitement idempotent) → écriture validée liée à l'exception
    2. ECRITURE_REGULARISATION : brouillon d'opération diverse (W15) lié à l'exception, validé par une seconde personne
    3. CONTRE_PASSATION_ET_REPRISE : l'écriture erronée est contre-passée, puis une nouvelle écriture est passée
       (nouvelle version de règle ou opération diverse) ; l'écriture d'origine reste visible
    4. ECART_JUSTIFIE : aucune écriture (ex. décalage de date bancaire qui se résorbe) ; justification obligatoire
  → statut RESOLUE (responsable) → CLOSE par une autre personne pour les gravités BLOQUANTE et MAJEURE
  → si le contrôle d'origine détecte à nouveau l'anomalie : REOUVERTE
Échéance dépassée → escalade à l'administrateur ; nombre et montant des exceptions ouvertes visibles sur G23
```

### 3.15 W15 — Cycle de vie d'un brouillon d'écriture **[v1.1]**

Concerne uniquement les **opérations diverses manuelles** et les **régularisations**. Les écritures automatiques ne
passent jamais par un brouillon.

```
EN_COURS ──(auteur : soumettre)──────────► SOUMIS ──(valideur ≠ auteur : valider)──► COMPTABILISE (figé)
   │  ▲                                     │   │                                      └─► écriture validée créée
   │  └──(auteur : retirer la soumission)───┘   └─(valideur : rejeter, motif)──► REJETE
   │                                                                                │
   └──(auteur : abandonner)──► ABANDONNE (figé)        (auteur : reprendre) ◄───────┘ → EN_COURS, version + 1
```

- Seul un brouillon `EN_COURS` est modifiable (verrou optimiste par numéro de version). Un brouillon n'a **pas de numéro
  d'écriture** et n'apparaît **jamais** dans le grand livre, la balance, les exports ou les métriques.
- La validation exécute **un seul batch atomique** (§5.13) : brouillon `SOUMIS` (garde conditionnelle), valideur ≠ auteur,
  période ouverte, comptes actifs, Σ débit = Σ crédit sur l'ensemble des lignes, attribution du numéro, insertion des lignes
  puis de l'en-tête, passage du brouillon à `COMPTABILISE`. Si une seule condition échoue, **rien** n'est écrit.
- Les écritures **automatiques** sont validées directement par le traitement si toutes les conditions sont réunies ; sinon
  aucune écriture n'est créée et une exception est ouverte (W14).

### 3.16 W16 — Option « jour d'exploitation » pour les annulations **[v1.1 — D75 retenue]**

```
Administrateur → Paramètres de l'officine → Annulations → « Jour de référence »
  Valeur à la création de chaque officine : JOUR CALENDAIRE (minuit, fuseau de l'officine) — non modifiable par APEX
  → option JOUR D'EXPLOITATION, deux variantes :
      GARDES : s'applique uniquement aux ventes validées pendant une période de garde (`duty_periods`, EXT02) ;
               le jour de référence se termine à la fin de la période de garde
      PERMANENTE : heure de bascule fixe (officine ouverte la nuit) ; heure saisie par l'administrateur, bornée par le serveur
  → motif obligatoire + référence de la décision interne (texte ou pièce jointe)
  → date d'effet : au plus tôt au début du prochain jour de référence (jamais rétroactive)
  → MFA de l'administrateur
  → enregistrement : paramètre `cancellation.reference_day` dans `settings` (dossier §4.2), versionné
    (valeur, variante, heure, date d'effet, motif, auteur) ; `audit_log` avant / après
  → notification à tous les collaborateurs ayant un droit d'annulation ; bandeau dans les paramètres ; rappel de la règle
    en vigueur sur l'écran d'annulation
Retour au jour calendaire : même procédure.
Aucune mise à jour du référentiel, aucun import, aucun autre rôle, aucune délégation ne peut modifier ce paramètre.
```

---

## 4. Tables et relations

Conventions du dossier principal (§4.1). **N** = nouvelle table · **C** = table existante complétée · **R** = table remplacée.
Les tables **(immuable)** n'acceptent que des insertions (déclencheurs SQLite, comme EXT03 v1.1 §17.3).
**[v1.1]** Les tables **(transitions contrôlées)** n'acceptent que des changements de statut autorisés (liste fermée de
transitions, vérifiée par déclencheur) ; les états finaux sont figés ; chaque transition est journalisée dans `audit_log`.

### 4.1 Gouvernance (Partie 1)

| Table | N/C/R | Colonnes principales (ajouts en gras pour C) | Contraintes / index |
|---|---|---|---|
| `tenant_administrators` | **N** (immuable) | id, tenant_id, employee_id, valid_from, valid_to, mode (`CREATION`, `TRANSFERT`, `SECOURS_APEX`), initie_par, accepte_le, justificatif_key, motif | Une seule désignation active par officine ; qualification « pharmacien » vérifiée exigée |
| `app_modules` | **N** (référentiel APEX) | code (`POS`, `CATALOG`, `STOCK`, `PURCHASES`, `SUPPLIERS`, `CUSTOMERS`, `PRESCRIPTIONS`, `PAYERS`, `CONTROLLED`, `EXPIRY`, `ORDERS`, `LAB`, `SERVICES`, `HR`, `CASH`, `FINANCE`, `ACCOUNTING`, `REPORTS`, `ANALYTICS`, `AI`, `SETTINGS`, `AUDIT`), libelle_fr/ar, ordre, licence_requise | Licence : un module non souscrit est inaccessible à tous |
| `permissions` (EXT02 §2.2) | **C** | **module_code**, **action** (`ACCESS`, `READ`, `CREATE`, `UPDATE`, `VALIDATE`, `CANCEL`, `EXPORT`, `ARCHIVE`, `SPECIFIC`), **scope_options** (`OWN`, `TEAM`, `ALL`), **plafonnable** | UNIQUE(code) ; les permissions spécifiques existantes (`rx.substitute`, `cash.drawer.open`…) gardent leur code, avec action `SPECIFIC` |
| `role_permissions` (dossier §4.2) | inchangée | — | — |
| `permission_overrides` (EXT05 §3.5) | **C** | **granted_by_employee_id** doit être l'administrateur (contrôle) ; **module_disabled** (bool) pour couper un module entier | — |
| `delegations` (EXT05 §3.5) | **C** | **valid_from NOT NULL** ; **operations** (liste de couples module × action) remplace le champ `permissions` libre ; **delegator** = administrateur obligatoirement | CHECK(valid_from < valid_to) ; CHECK(delegator = administrateur actif à la date de création) |
| `delegable_permissions` (EXT05) | inchangée | Le profil pays marque non délégables les opérations réglementées | — |
| `permission_exceptions` | **N** (transitions contrôlées) **[v1.1]** | id, tenant_id, type (`DEPASSEMENT_PLAFOND`, `DEROGATION_DELAI`), permission_code, demandeur_employee_id, operation_ref (type + id), **operation_fingerprint** (SHA-256 de la forme canonique de l'opération), valeur_demandee, plafond_applicable, motif (demandeur), statut (`DEMANDEE`, `ACCORDEE`, `REFUSEE`, `EXPIREE`, `CONSOMMEE`, **`REVOQUEE`**), decide_par_employee_id (administrateur), methode (`PIN_PRESENT`, `A_DISTANCE`), **motif_decision**, **confirmation_explicite_at**, **mfa_verified_at**, **mfa_method**, **decision_session_id**, **decision_device_id**, decide_le, expire_le, **approval_token_hash**, **consumed_at**, **consumed_by_employee_id**, **revoked_at**, **revoke_reason** | Transitions : DEMANDEE → ACCORDEE / REFUSEE / EXPIREE ; ACCORDEE → CONSOMMEE / EXPIREE / REVOQUEE ; autres états figés. CHECK(decide_par = administrateur actif à la décision) ; CHECK(decide_par ≠ demandeur) ; CHECK(statut ∈ {ACCORDEE, REFUSEE} ⇒ motif_decision NOT NULL) ; CHECK(methode = `A_DISTANCE` ∧ statut = ACCORDEE ⇒ mfa_verified_at ∧ confirmation_explicite_at NOT NULL) ; UNIQUE(approval_token_hash) ; consommation conditionnelle (W4-D §4). *v1.0 la déclarait « immuable » alors qu'elle change de statut : corrigé* |

### 4.2 Catalogue — fiche produit (Partie 3A, C, D)

| Table | N/C/R | Colonnes principales | Contraintes / index |
|---|---|---|---|
| `ref_products` (dossier §4.3) | **C** | **statut_commercialisation_ref** (`COMMERCIALISE`, `SUSPENDU`, `ARRETE`, `RUPTURE_DECLAREE`), **source**, **version_ref** ; `tva_bp` **supprimé** au profit de `tax_category_code_ref` | — |
| `ref_product_substances` | **N** | ref_product_id, dci_id, dosage_valeur, dosage_unite, rang | Gère les associations de plusieurs molécules (DCI multiples), absentes du modèle actuel (un seul `dci_id`) |
| `products` (dossier §4.3) | **C** | **product_type** (`MEDICAMENT`, `DISPOSITIF_MEDICAL`, `ACCESSOIRE`, `PARAPHARMACIE`, `COSMETIQUE`, `COMPLEMENT_ALIMENTAIRE`, `PREPARATION`, `SERVICE`, `AUTRE` **[À VALIDER R42]**), **brand_id**, **manufacturer_id** (= laboratoire), **marketing_status** (local, hérité du référentiel), **sensitivity_flags** (JSON : `REGLEMENTE`, `CHAINE_FROID`, `FRACTIONNE`, `USAGE_UNIQUE`, `SUR_MESURE`, `NON_REPRENABLE`), **sale_units** (via `product_units`), **tax_category_id**, **stock_tracked** (faux pour un service), **lot_tracking** (`OBLIGATOIRE`, `FACULTATIF`, `AUCUN`), **serial_tracking** (bool), **variant_of_id** (modèle parent), **status** (`A_VALIDER`, `ACTIF`, `INACTIF`, `ARCHIVE`) ; `categorie_id` et `tva_bp` **supprimés** | INDEX(tenant_id, product_type, status) ; INDEX(tenant_id, brand_id) ; INDEX(tenant_id, manufacturer_id) |
| `product_substances` | **N** | product_id, dci_id, dosage_valeur, dosage_unite, rang | Copie locale pour les produits locaux ; sinon hérité de `ref_product_substances` |
| `brands` | **N** | id, tenant_id (NULL = APEX), nom, manufacturer_id | UNIQUE(COALESCE(tenant_id,''), nom_normalise) |
| `ref_laboratories` (dossier) | **C** | rôle étendu à **fabricant / exploitant / distributeur** (`roles` JSON) | Pas de table « fournisseur » ici : le fournisseur reste `suppliers` |
| `product_units` | **N** | id, product_id, code (`BOITE`, `PLAQUETTE`, `UNITE`, `FLACON`, `PAIRE`, `ML`…), facteur_vers_unite_base (entier), vendable (bool), achetable (bool), code_barres (facultatif), prix_vente (si différent), par_defaut | UNIQUE(product_id, code) ; remplace `unites_par_boite` + `vente_fractionnee` par une liste générique |
| `product_variant_attributes` | **N** | product_id, attribut (`POINTURE`, `TAILLE`, `COTE`, `COULEUR`, `TEINTE`, `CONTENANCE`, `SPF`), valeur | Variantes (chaussures orthopédiques, attelles, maquillage, écrans solaires) : chaque variante est un `product` à part entière, avec son stock et son code-barres, rattachée au modèle par `variant_of_id` |
| `product_type_fields` | **N** (référentiel) | product_type, champ, obligatoire (bool), validation | Champs obligatoires par type (§5.3) |
| `product_prices` | **N** (historisée) | id, product_id, unit_code, type (`VENTE`, `ACHAT_REFERENCE`, `PRIX_PUBLIC_REGLEMENTE`), montant_millimes, valid_from, valid_to, source, saisi_par | Remplace `product_prices_history` (dossier) : une seule table de prix, historisée et sourcée |

### 4.3 Substitution et équivalences (unification EXT03)

| Table | N/C/R | Colonnes principales | Remarques |
|---|---|---|---|
| `substitution_groups` | **N** | id, tenant_id (NULL = APEX), code, libelle, type (`GENERIQUE_OFFICIEL`, `EQUIVALENT_LOCAL_VALIDE`), dci_signature (DCI + dosage + forme), source_document_id, statut (`A_VALIDER`, `ACTIF`, `RETIRE`), valide_par, valide_le, version | Remplace `products.substitution_group_id` et `ref_products.generic_group_code` (EXT03 §3.4) par une seule structure |
| `substitution_group_members` | **N** | group_id, product_id ou ref_product_id, role (`PRINCEPS`, `GENERIQUE`, `MEMBRE`), valid_from, valid_to | Un produit peut appartenir à un seul groupe actif d'un même type |
| — Équivalents informatifs | — | **Aucune table** : calculés à partir des substances (même DCI, autre dosage ou forme) | Affichage seulement, jamais une substitution |

### 4.4 Classification multi-axes (remplace `categories`)

| Table | N/C/R | Colonnes principales | Contraintes / index |
|---|---|---|---|
| `classification_axes` | **N** | id, tenant_id (NULL = APEX), code (`UNIVERS`, `FAMILLE_COMMERCIALE`, `FAMILLE_THERAPEUTIQUE`, `FORME`, `USAGE`…), libelle_fr/ar, multi_valeurs (bool), obligatoire_pour (JSON des types de produits), source (`APEX`, `OFFICIEL`, `LOCAL`) | UNIQUE(COALESCE(tenant_id,''), code) |
| `classification_nodes` | **N** | id, axis_id, parent_id, code, libelle_fr, libelle_ar, ordre, actif, source_ref (ex. code ATC si la source le permet **[À VALIDER R43]**) | Arbre par axe ; un nœud utilisé n'est jamais supprimé, seulement désactivé |
| `product_classifications` | **N** | product_id, axis_id, node_id, principal (bool) | UNIQUE(product_id, node_id) ; un seul `principal` par axe |
| `categories` (dossier §4.3) | **R** | Supprimée ; son contenu correspond à l'axe `FAMILLE_COMMERCIALE` | — |

Axes demandés et leur source :

| Critère demandé | Implémentation |
|---|---|
| Famille commerciale | Axe `FAMILLE_COMMERCIALE` (arbre : Médicaments, Dispositifs, Parapharmacie & beauté, Accessoires… puis sous-familles des Parties 3C et 3D) |
| Famille thérapeutique | Axe `FAMILLE_THERAPEUTIQUE` (référentiel officiel ou ATC si utilisable) |
| Forme pharmaceutique | `ref_forms` existant, exposé comme axe de recherche (pas de duplication) |
| Laboratoire | `products.manufacturer_id` (référentiel `ref_laboratories`) |
| Marque | `products.brand_id` (`brands`) |
| Type de produit | `products.product_type` |
| Catégorie de TVA | `products.tax_category_id` (§4.5) |

Forme, laboratoire, marque, type et TVA sont des **attributs à une seule valeur**. Les familles sont des **axes multi-valeurs**.
La recherche par facettes les combine tous.

### 4.5 Fiscalité

| Table | N/C/R | Colonnes principales | Remarques |
|---|---|---|---|
| `tax_categories` | **N** | id, tenant_id (NULL = référentiel pays), code, libelle, pays | Ex. « TVA médicaments », « TVA parapharmacie » — **libellés indicatifs, valeurs à confirmer** |
| `tax_rates` | **N** (historisée) | id, tax_category_id, taux_bp, valid_from, valid_to, source_document_id, valide_par | **Aucun taux fourni par défaut** **[À VALIDER R44]** ; le taux appliqué est figé sur chaque ligne de vente et d'achat |

### 4.6 Lots et stock (Partie 3B)

| Table | N/C/R | Colonnes principales | Remarques |
|---|---|---|---|
| `lots` (dossier §4.4) | **C** | **date_reception**, **quantite_recue_unites**, **prix_achat_unitaire** (= `cout_unitaire` existant, renommé pour clarté), **supplier_id** (existant), **receipt_line_id** (existant), **numero_serie** (facultatif, dispositifs), statut (`ACTIF`, `BLOQUE`, `PERIME`, `RAPPELE`, `EPUISE`) | La **quantité disponible n'est pas stockée** sur le lot : elle est lue dans `stock_levels` (bucket `DISPONIBLE`) |
| `stock_levels` (dossier + EXT05) | **C** | clé (produit, lot, **bucket**) ; buckets : `DISPONIBLE`, `EN_CONTROLE`, `QUARANTAINE`, `PERTE` | Le bucket `RESERVE` d'EXT05 est **retiré** (§5.4) |
| `stock_reservations` (dossier) | inchangée | Engagement sur le stock `DISPONIBLE`, sans mouvement | Disponible vendable = `DISPONIBLE` − réservations actives |
| `stock_movements` (dossier + EXT05) | inchangée par EXT06 | — | Historique des mouvements du lot = vue filtrée sur `lot_id` |

### 4.7 Services pharmaceutiques (Partie 4)

| Table | N/C/R | Colonnes principales | Contraintes / index |
|---|---|---|---|
| `service_definitions` | **N** | product_id (PK, `product_type = SERVICE`), description, duree_indicative_min, tarif_par_defaut (via `product_prices`), gratuit (bool), habilitation_requise_code (EXT02), patient_requis (bool), consentement_requis (bool), donnees_saisies (JSON : liste des champs), consommables_par_defaut (JSON), **autorisation_reglementaire** (`NON_REQUISE`, `REQUISE`), autorisation_ref, autorisation_valid_from, autorisation_valid_to, source_document_id, **actif** | Activation possible seulement si l'autorisation est valide à la date (§5.5) |
| `service_acts` | **N** (immuable) | id, tenant_id, numero (`SRV-…`), product_id (service), customer_id (facultatif selon le service), performed_by_employee_id, performed_at, duree_reelle_min, sale_line_id (si facturé), observations (chiffrées si sensibles), mesures (JSON chiffré, ex. tension mesurée si le service le prévoit), consent_id, statut (`REALISE`, `ANNULE`), reverses_id | INDEX(tenant_id, performed_at) ; INDEX(product_id) ; lecture des données patient journalisée (`sensitive_access_log`, EXT03) |
| `service_act_consumables` | **N** | act_id, product_id, lot_id, quantite, stock_movement_id | Sortie de stock des consommables |

### 4.8 Finance (Partie 7 A–D) — M08 complété

| Table | N/C/R | Colonnes principales | Remarques |
|---|---|---|---|
| `supplier_invoices` (dossier §4.6) | **C** | **nature** (`MARCHANDISES`, `CHARGE`, `IMMOBILISATION`), **expense_category_id** (si charge), **asset_id** (si immobilisation), **document_key** (pièce obligatoire), **payment_status** (`A_PAYER`, `PARTIELLEMENT_PAYEE`, `PAYEE`, `EN_RETARD`, `CONTESTEE`) | **Une seule table** pour toutes les factures reçues, marchandises et charges : pas de table « charges » parallèle |
| `supplier_credit_notes` | **N** | id, tenant_id, supplier_id, numero, date, montant_ht, tva, ttc, invoice_id (nullable), motif, document_key, statut | Avoirs fournisseurs (absents du dossier, où l'avoir n'était qu'un statut de facture) |
| `supplier_advances` | **N** | id, tenant_id, supplier_id, payment_id, montant, imputable_sur (bon de commande, facture), solde | Acomptes versés, imputés ensuite sur les factures |
| `expense_categories` | **N** | id, tenant_id (NULL = modèle APEX), code (`SALAIRES`, `CHARGES_SOCIALES`, `LOYER`, `ELECTRICITE_EAU`, `TELECOM`, `INTERETS`, `IMPOTS_TAXES`, `ASSURANCES`, `MAINTENANCE`, `ENTRETIEN`, `FOURNITURES`, `CHARGES_EXCEPTIONNELLES`, `AUTRES`), parent_id, nature_analytique (`FIXE`, `VARIABLE`, `MIXTE`), compte_par_defaut (M20), actif | « Crédits et échéances » relèvent de `loans` ; « investissements » de `fixed_assets` |
| `expenses` (dossier §4.8) | **C** (restreinte) | Dépenses **payées en caisse** uniquement (petites dépenses) ; + **expense_category_id**, **justificatif obligatoire** au-delà d'un seuil | Toute autre charge passe par `supplier_invoices` |
| `recurring_charge_templates` | **N** | id, tenant_id, supplier_id, expense_category_id, montant_previsionnel, periodicite, jour, actif | Génère des **brouillons**, jamais des charges validées |
| `loans` | **N** | id, tenant_id, preteur, reference, capital, taux_bp, duree_mois, date_debut, periodicite, frais, document_key, statut | — |
| `loan_schedule_lines` | **N** | loan_id, numero, date_echeance, capital, interets, assurance, frais, total, due_date_id, statut | Importé depuis le document de la banque (recommandé) |
| `fixed_assets` | **N** | id, tenant_id, libelle, categorie, date_acquisition, date_mise_en_service, valeur_acquisition, invoice_id, duree_amortissement_mois, methode, statut (`EN_SERVICE`, `CEDE`, `REFORME`), document_key | Registre des immobilisations ; durées et méthode **[À VALIDER R45]** |
| `bank_statements` | **N** | id, tenant_id, bank_account_id, periode, solde_ouverture, solde_cloture, fichier_key, importe_par | — |
| `bank_statement_lines` | **N** | id, statement_id, date_operation, date_valeur, libelle, montant (signé), reference, statut (`A_RAPPROCHER`, `RAPPROCHEE`, `IGNOREE`) | — |
| `bank_reconciliations` | **N** | id, statement_line_id, cible_type (`PAYMENT`, `PAYER_REMITTANCE`, `BANK_DEPOSIT`, `LOAN_LINE`, `JOURNAL_ENTRY`), cible_id, montant, propose_par (`AUTO`, `MANUEL`), confirme_par_employee_id, confirme_le | Confirmation humaine obligatoire |
| `payroll_imports` | **N** | id, tenant_id, periode, source (fichier, logiciel), fichier_key, statut (`A_CONTROLER`, `VALIDE`, `REJETE`), valide_par | — |
| `payroll_import_lines` | **N** (cloisonnée N4) | import_id, employee_id, brut, cotisations_salariales, cotisations_patronales, impot_retenu, net, autres (JSON) | Lecture : `hr.salary.read` ; jamais transmises à l'IA |
| `party_ledger`, `due_dates`, `payments`, `bank_accounts`, `bank_deposits`, `payment_instruments` (dossier) | inchangées | Nouveaux types d'écriture et d'origine : `AVOIR_FOURNISSEUR`, `ACOMPTE`, `ECHEANCE_EMPRUNT`, `PAIE`, `ORGANISME_SOCIAL` | Grand livre auxiliaire des tiers unique |

### 4.9 Comptabilité (Partie 7E) — M20 nouveau **[v1.1 : cycle de vie et immutabilité corrigés]**

**Correction v1.0** : `journal_entries` était déclarée immuable tout en portant les statuts `BROUILLON` / `VALIDEE`, ce qui
supposait de modifier une écriture. En v1.1, **les brouillons sont séparés** : `journal_entries` ne contient que des
écritures validées, insérées une seule fois et jamais modifiées.

| Table | N/C/R | Colonnes principales | Contraintes / index |
|---|---|---|---|
| `chart_of_accounts` | **N** | id, tenant_id, code, libelle, classe, type (`BILAN`, `GESTION`), lettrable (bool), tiers_type, actif, source (`MODELE`, `LOCAL`) | Modèle importé et validé par l'expert-comptable **[À VALIDER R46]** ; aucun plan imposé. UNIQUE(tenant_id, code) ; un compte mouvementé n'est jamais supprimé, seulement désactivé |
| `journals` | **N** | id, tenant_id, code (`VT` ventes, `AC` achats, `CA` caisse, `BQ` banque, `OD` opérations diverses, `TP` tiers payant facultatif), libelle, compte_contrepartie | UNIQUE(tenant_id, code) |
| `posting_rules` | **N** (transitions contrôlées ; contenu figé dès l'activation) **[v1.1]** | id, tenant_id, **rule_code**, evenement (`sale.completed`, `sale.voided`, `sale.returned`, `supplier_invoice.validated`, `payment.recorded`, `payer_remittance.allocated`, `claim.written_off`, `cash_session.closed`, `loss.recorded`, `payroll.validated`, `loan.instalment_paid`…), journal_id, lignes (JSON : compte ou règle de compte, sens, montant = expression sur l'événement), **version**, valid_from, valid_to, **statut** (`PROJET`, `ACTIVE`, `RETIREE`), **auteur_employee_id**, valide_par, **content_hash** | UNIQUE(tenant_id, rule_code, version) ; seules les transitions PROJET → ACTIVE → RETIREE sont permises ; le contenu (`lignes`, `content_hash`) d'une version activée n'est jamais modifié : toute évolution crée une nouvelle version (quatre yeux, `accounting.setup`) ; jeu de référence exécuté avant activation ; **une seule version active par (événement, date)** |
| `accounting_periods` | **N** | id, tenant_id, exercice, mois, statut (`OUVERTE`, `EN_CLOTURE`, `VERROUILLEE`, `EXPORTEE`), verrouillee_par, verrouillee_le | UNIQUE(tenant_id, exercice, mois) |
| `journal_entry_drafts` | **N** **[v1.1]** (transitions contrôlées) | id, tenant_id, journal_id, period_id (prévue), date, libelle, **lignes** (JSON : compte, tiers, débit, crédit, libellé, analytique), origine (`MANUELLE`, `REGULARISATION`, `RESOLUTION_EXCEPTION`), accounting_exception_id (nullable), statut (`EN_COURS`, `SOUMIS`, `REJETE`, `ABANDONNE`, `COMPTABILISE`), **version** (verrou optimiste), auteur_employee_id, soumis_le, valideur_employee_id, motif_rejet, posted_entry_id | Seul `EN_COURS` est modifiable ; `ABANDONNE` et `COMPTABILISE` sont figés ; CHECK(valideur ≠ auteur) ; **aucun effet comptable** (ni grand livre, ni balance, ni export, ni métriques) |
| `journal_entries` | **N** (**immuable**) **[v1.1]** | id, tenant_id, journal_id, period_id, numero (séquence par journal et exercice), date, libelle, **origine** (`AUTOMATIQUE`, `MANUELLE`, `CONTRE_PASSATION`, `REGULARISATION`), source_event_type, source_id, **source_event_id** (identifiant de l'événement outbox), **posting_rule_id**, **posting_rule_version**, **generation_key**, **draft_id** (si manuelle), reverses_id, correlation_id, **accounting_exception_id** (si l'écriture résout une exception), **total_debit**, **total_credit**, **line_count**, **lines_hash**, validated_by (employé ou `SYSTEME`), validated_at | Statut **supprimé** : toute ligne présente est une écriture validée. **UNIQUE(tenant_id, generation_key)** (voir §5.13) ; UNIQUE(tenant_id, journal_id, exercice, numero) ; **UNIQUE(reverses_id)** (une écriture n'est contre-passée qu'une fois) ; CHECK(total_debit = total_credit AND total_debit > 0) ; déclencheurs : refus de tout UPDATE et DELETE ; refus d'insertion si période ≠ `OUVERTE` ou `EN_CLOTURE` |
| `journal_entry_lines` | **N** (**immuable**) | id, entry_id (**clé étrangère différée** vers `journal_entries`, CP8), **line_no**, account_id, tiers_type, tiers_id, debit, credit, libelle, analytique (JSON : famille, univers) | UNIQUE(entry_id, line_no) ; CHECK(debit ≥ 0 AND credit ≥ 0 AND (debit = 0) ≠ (credit = 0)) ; refus d'UPDATE et DELETE ; insertion refusée si l'en-tête est déjà « scellé » (§5.13) ; INDEX(account_id) |

| `accounting_exceptions` | **N** **[v1.1]** (transitions contrôlées) | id, tenant_id, numero (`EXC-…`), **type** (`GENERATION_ECHEC`, `REGLE_ABSENTE`, `COMPTE_INACTIF_OU_ABSENT`, `DESEQUILIBRE_DETECTE`, `PERIODE_FERMEE`, `ECART_RAPPROCHEMENT_BANCAIRE`, `ECART_TIERS`, `ECART_CAISSE`, `ECART_STOCK_VALORISATION`, `ECART_METRIQUES_JOURNAL`, `VALIDATION_BROUILLON_REFUSEE`, `INTEGRITE_ECRITURE`, `EXPORT_REFUSE`), **gravite** (`BLOQUANTE`, `MAJEURE`, `MINEURE`), **source_event_type**, **source_id**, **source_event_id**, **correlation_id**, **period_id**, **montant_concerne_millimes**, **montant_ecart_millimes**, **detection_key** (empreinte : type + origine + version de règle), occurrences, premiere_detection_le, derniere_detection_le, details (JSON : message technique, contrôle, valeurs attendues / constatées), **responsable_employee_id**, **echeance**, statut (`OUVERTE`, `EN_ANALYSE`, `RESOLUE`, `CLOSE`, `REOUVERTE`), **mode_resolution** (`REGENERATION`, `ECRITURE_REGULARISATION`, `CONTRE_PASSATION_ET_REPRISE`, `ECART_JUSTIFIE`), resolution_entry_id, justification, resolu_par, resolu_le, clos_par, clos_le | UNIQUE(tenant_id, detection_key) WHERE statut ∉ {`CLOSE`} (une anomalie ouverte = une ligne) ; CHECK(statut ∈ {RESOLUE, CLOSE} ⇒ mode_resolution NOT NULL ∧ justification NOT NULL) ; CHECK(gravité ∈ {BLOQUANTE, MAJEURE} ∧ statut = CLOSE ⇒ clos_par ≠ resolu_par) ; jamais supprimée ; INDEX(tenant_id, statut, gravite) ; INDEX(tenant_id, source_event_type, source_id) |
| `accounting_exports` | **N** | id, tenant_id, period_id, format (paramétrable : CSV, Excel, format du logiciel du comptable), **type** (`PROVISOIRE`, `DEFINITIF`), fichier_key, sha256, **entry_count**, **total_debit**, **total_credit**, exporte_par, exporte_le | Un export `DEFINITIF` fige la période (`EXPORTEE`) ; refusé si un contrôle d'export échoue (§5.13) |

*Lettrage* : aucune table nouvelle. Le lettrage des tiers reste porté par `party_ledger` (dossier). La v1.0 plaçait
`lettrage_code` sur les lignes d'écriture, qui sont immuables, alors qu'un lettrage évolue : la colonne est retirée.

Le **grand livre** et la **balance** sont des **vues** calculées sur `journal_entry_lines` (aucune table de soldes saisie).
Le **grand livre auxiliaire des tiers** reste `party_ledger` ; une réconciliation quotidienne vérifie qu'il concorde avec les
comptes de tiers de M20 (écart → exception `ECART_TIERS`).

### 4.10 Couche métriques (Partie 7F-G) **[v1.1 : définition technique complétée]**

| Table | N/C/R | Colonnes principales | Contraintes / index |
|---|---|---|---|
| `metric_definitions` | **N** **[v1.1]** (référentiel APEX versionné) | code (`ca_ht`, `ca_ttc`, `ca_encaisse`, `cout_vendu`, `marge_commerciale`, `marge_realisee`…), version, libelle_fr/ar, **formule** (texte de référence, §5.6), **source** (tables ou événements), **grain** (`EVENEMENT_JOUR`), **dimensions_autorisees** (liste), **agregation** (`SOMME`, `COMPTE`, `MOYENNE_PONDEREE`, `DERNIERE_VALEUR`), **additive** (bool), **date_rattachement** (`DATE_OPERATION`, `DATE_ENCAISSEMENT`, `DATE_PIECE`), unite (`MILLIMES`, `UNITES`, `NOMBRE`, `BP`), valid_from, statut | UNIQUE(code, version) ; **une seule définition active par code** ; les écrans, rapports, exports, contrôles et l'IA ne référencent que ce code |
| `metrics_daily` (dossier §4.11) | **C** **[v1.1]** | tenant_id, **date** (jour local de l'officine), **metric_code** (remplace `cle`), **metric_version**, **dimension_type** (liste fermée définie en MT3, §5.6 bis : `TOTAL`, `PRODUCT`, `PRODUCT_TYPE`, `CLASSIFICATION_NODE`, `CLASSIFICATION_PRINCIPAL`, `MANUFACTURER`, `BRAND`, `TAX_CATEGORY`, `SALE_MODE`, `PRESCRIBER`, `SPECIALTY`, `PAYER`, `PAYER_MODE`, `PAYMENT_MODE`, `SERVICE`, `EMPLOYEE`, `EXPENSE_CATEGORY`), **dimension_id** (`'*'` pour `TOTAL`), valeur (entier), **nb_evenements**, **computed_at**, **computation_run_id** | **UNIQUE(tenant_id, date, metric_code, metric_version, dimension_type, dimension_id)** ; INDEX(tenant_id, metric_code, date) ; montants en millimes entiers ; aucune autre table ne stocke ces valeurs |
| `financial_snapshots` | **N** | id, tenant_id, period_id, metric_code, metric_version, dimension_type, dimension_id, valeur, fige_le, source_run_id | Clichés mensuels figés à la clôture de période, mêmes codes que `metrics_daily` ; jamais recalculés après `VERROUILLEE` ; UNIQUE(tenant_id, period_id, metric_code, dimension_type, dimension_id) |

La définition fonctionnelle de chaque grandeur est au §5.6 ; les règles techniques (grain, rattachement, annulations,
recalcul, contrôles) au §5.6 bis ; les indicateurs de rentabilité au §5.12.

### 4.11 Relations

```
tenant_administrators n─1 employees ; app_modules 1─n permissions ; permissions 1─n role_permissions, permission_overrides
delegations (EXT05) ─ operations ⊂ permissions ; permission_exceptions n─1 permissions, ─► opération (type, id)
products n─1 ref_products ; products 1─n product_substances n─1 ref_dci ; products n─1 brands, ref_laboratories, ref_forms
products 1─n product_units, product_prices, product_classifications n─1 classification_nodes n─1 classification_axes
products n─1 tax_categories 1─n tax_rates ; products 0..1─n products (variantes) ; products 1─n product_variant_attributes
substitution_groups 1─n substitution_group_members n─1 products
products 1─n lots 1─n stock_levels (par bucket), stock_movements ; stock_reservations n─1 lots
products(SERVICE) 1─1 service_definitions 1─n service_acts 1─n service_act_consumables ─► stock_movements
service_acts 0..1─1 sale_lines ; service_acts n─0..1 customers
supplier_invoices n─1 suppliers ; 0..1─1 expense_categories ; 0..1─1 fixed_assets ; supplier_credit_notes, supplier_advances n─1 suppliers
loans 1─n loan_schedule_lines 1─1 due_dates ; bank_statements 1─n bank_statement_lines 1─n bank_reconciliations ─► payments…
payroll_imports 1─n payroll_import_lines n─1 employees
événements métier (outbox) ─► posting_rules (version) ─► journal_entries 1─n journal_entry_lines n─1 chart_of_accounts
journal_entries n─1 accounting_periods ; journal_entries 0..1─0..1 journal_entries (reverses_id : contre-passation)   [v1.1]
journal_entry_drafts 0..1─0..1 journal_entries (comptabilisation) ; accounting_exceptions ─► événement d'origine, 0..1 écriture de résolution
metric_definitions 1─n metrics_daily, financial_snapshots ; financial_snapshots n─1 accounting_periods             [v1.1]
```

---

## 5. Règles métier

### 5.1 Gouvernance des droits (Partie 1)

| # | Règle | Contrôle |
|---|---|---|
| GV1 | Chaque officine a **un et un seul** pharmacien administrateur actif, titulaire d'une qualification de pharmacien vérifiée (EXT02) | Contrainte d'unicité sur `tenant_administrators` ; MFA obligatoire |
| GV2 | Seul l'administrateur crée, modifie ou révoque une délégation, un réglage individuel (`permission_overrides`) ou une coupure de module | CHECK serveur ; refus 403 sinon, tentative journalisée |
| GV3 | Seul l'administrateur accorde un dépassement de plafond ou une dérogation de délai, opération par opération | `permission_exceptions` à usage unique |
| GV4 | Délégation : nominative, date de début **et** date de fin obligatoires, plafonds financiers et opérationnels, liste explicite d'opérations (module × action) | Aucune délégation « tous droits » |
| GV5 | Pas de redélégation ; aucune délégation d'une opération sur médicament réglementé, ni de la gestion des droits | `delegable_permissions` (EXT05) ; profil pays prioritaire |
| GV6 | Révocation immédiate : les droits sont réévalués à la requête suivante (cache invalidé) ; une opération en cours est recontrôlée dans sa transaction | — |
| GV7 | Historique complet et non modifiable : création, modification, révocation, expiration, chaque utilisation | `audit_log` + `delegation_usage` |
| GV8 | L'administrateur ne peut pas se déléguer à lui-même, ni s'accorder une dérogation (il dispose déjà du droit) ; ses propres opérations sensibles restent tracées et, pour les réglementés, soumises au quatre-yeux prévu par EXT05 | — |
| GV9 | Accès à un module ≠ droit d'agir : l'action `ACCESS` ouvre le module (menu, écran), chaque autre action est vérifiée séparément | Matrice §6.1 |
| GV10 | Couper un module à un collaborateur désactive toutes ses actions, y compris celles venant d'une délégation ; cela ne supprime aucune donnée et ne retire pas l'historique | `module_disabled` |
| GV11 | L'action `ARCHIVE` correspond à une suppression **logique** et n'existe que pour les objets qui le permettent (produit sans mouvement, brouillon) ; les documents validés ne sont jamais archivables ni supprimables | Liste blanche par type d'objet |
| GV12 | Le contrôle est **toujours** côté serveur : un appel d'API sans droit est refusé même si l'interface l'a laissé passer | Tests S-GOV (§10) |
| GV13 **[v1.1]** | **Validation à distance (D73 retenue)** : seul l'administrateur actif peut décider à distance, avec MFA au moment de la décision, confirmation explicite, motif obligatoire, opération identifiée par son empreinte, autorisation à usage unique et expirante, journalisation complète ; aucune délégation, aucun réglage individuel, aucun autre collaborateur ne peut s'y substituer ; non proposée pour les produits réglementés | W4-D ; `permission_exceptions` (CHECK) ; tests GOV-16 à GOV-26 |

**Ordre d'évaluation d'un droit** (complète EXT02 §1.2 et EXT05 §5.3) :

```
1. Module souscrit par l'officine (licence) ?                        non → refus « module non disponible »
2. Module coupé pour ce collaborateur (module_disabled) ?            oui → refus « module non autorisé »
3. Action accordée (rôle ∪ réglages individuels ∪ délégation active) − retraits ?
4. Habilitation, qualification, statut, présence exigés par la permission ?
5. Périmètre (propres / équipe / tous) et périmètre de la délégation (réglementés exclus…) ?
6. Plafonds (opération, jour, délai) non dépassés ?                  dépassement → dérogation administrateur (W4)
→ AUTORISÉ | REFUSÉ (motif lisible) | DÉROGATION REQUISE
```

### 5.2 Annulations et retours (Partie 2) — amendements à EXT05

| # | Règle | Remplace dans EXT05 |
|---|---|---|
| AN1 | Vente non validée : abandon, sans écriture corrective (inchangé) | — |
| AN2 | Vente validée, produit non sensible : annulation possible par un collaborateur habilité **jusqu'à la fin du jour calendaire de la vente** (minuit, fuseau de l'officine), dans ses plafonds | D61 (15 minutes) et §4.3 « auto-correction » |
| AN3 | Au-delà du jour calendaire, **seul l'administrateur** peut autoriser l'annulation (dérogation W4), quel que soit le rôle du demandeur | EXT05 §4.2 C5 (validation par un pharmacien) |
| AN4 | Les droits d'annulation (`sale.void.execute`, `sale.void.approve`) ne s'accordent que par l'administrateur (rôle, réglage individuel ou délégation) | EXT05 D66 |
| AN5 | Produits sensibles (`sensitivity_flags` non vide) ou réglementés : circuit spécifique obligatoire (EXT05 §4.7) ; la règle du jour calendaire ne les rend jamais annulables par un non-pharmacien | — |
| AN6 | **Aucune annulation n'entraîne une réintégration automatique en stock vendable** : le produit passe en `EN_CONTROLE`, puis la décision suit le workflow D d'EXT05 (récupération physique, intégrité, conservation, péremption, restrictions réglementaires) | EXT05 `DISPONIBLE_DIRECT` (voir D74) |
| AN7 | Toutes les corrections passent par des mouvements inverses liés à l'opération initiale (`reverses_id`, `correlation_id`) — inchangé | — |
| AN8 | Une session de caisse clôturée n'est jamais modifiée : si l'annulation du même jour intervient après la clôture de la session d'origine, la restitution se fait dans la session ouverte de l'exécutant (inchangé) | — |

**Point à arbitrer (D74)** : EXT05 prévoyait une réintégration directe pour un produit resté au comptoir (annulation dans
les 15 minutes). La consigne d'EXT06 exige un contrôle systématique. Proposition : supprimer `DISPONIBLE_DIRECT` et utiliser
une **décision immédiate simplifiée** : le contrôle peut être décidé tout de suite par un second collaborateur habilité
(check-list réduite : produit présent, scellé, péremption lue). Cela reste un contrôle tracé, mais sans délai.

**Jour de référence [v1.1 — D75 retenue]** :

| # | Règle |
|---|---|
| AN9 | **Règle générale : jour calendaire.** Le jour est déterminé par l'horodatage serveur converti dans le fuseau de l'officine (`tenants.fuseau`), jamais par l'horloge du poste. Une vente validée à 23 h 58 n'est annulable sans dérogation que jusqu'à 23 h 59 59 le même jour |
| AN10 | **Option « jour d'exploitation »** pour les pharmacies de garde : activée **uniquement** par l'administrateur, de façon explicite (W16), avec motif, MFA et date d'effet non rétroactive ; documentée dans les paramètres de l'officine (`settings`, clé `cancellation.reference_day`, versionnée) ; variante GARDES (périodes de `duty_periods`, EXT02) ou PERMANENTE (heure de bascule bornée par le serveur) |
| AN11 | **Aucune modification silencieuse** : la valeur par défaut est le jour calendaire pour toute officine ; aucune mise à jour du référentiel, aucun import, aucune migration, aucun autre rôle ni aucune délégation ne peut changer ce paramètre ; tout changement est journalisé (avant / après), notifié aux collaborateurs ayant un droit d'annulation et affiché sur l'écran d'annulation |
| AN12 | L'option ne modifie **que** la limite du délai d'annulation sans dérogation. Elle ne modifie ni AN5 (produits sensibles et réglementés : circuit spécifique, jamais d'annulation par un non-pharmacien), ni AN6 (aucune réintégration automatique en stock vendable), ni le jour de rattachement des métriques et de la comptabilité (MT1) |
| AN13 | La règle appliquée à chaque annulation (calendaire ou exploitation, version du paramètre) est enregistrée avec l'opération corrective (`corrective_operations` d'EXT05, complétée de **reference_day_rule** et **reference_day_setting_version**), pour pouvoir justifier a posteriori la décision |

### 5.3 Catalogue : champs et contrôles par type (Partie 3)

| Champ demandé | Médicament | Dispositif / accessoire | Parapharmacie / beauté | Service | Source |
|---|---|---|---|---|---|
| Nom commercial | Obligatoire | Obligatoire | Obligatoire | Désignation obligatoire | `products.nom`, `nom_ar` |
| DCI / molécules (une ou plusieurs) | Obligatoire | — | Facultatif (actifs) | — | `product_substances` |
| Dosage | Obligatoire | — | — | — | `product_substances.dosage_*` |
| Forme pharmaceutique | Obligatoire | — | Facultatif (crème, gel…) | — | `ref_forms` |
| Famille thérapeutique | Obligatoire **[À VALIDER R43 : référentiel]** | — | — | — | Axe `FAMILLE_THERAPEUTIQUE` |
| Laboratoire fabricant | Obligatoire | Fabricant facultatif | Facultatif | — | `manufacturer_id` |
| Marque | Facultatif | Facultatif | Recommandé | — | `brand_id` |
| Médicaments équivalents | Calculés (informatif) | — | — | — | Substances |
| Substitution autorisée | Groupe validé par un pharmacien | — | — | — | `substitution_groups` |
| Code-barres | Au moins un (EAN ou interne) | Au moins un par variante | Au moins un par variante | — | `product_barcodes` |
| Prix d'achat | Par lot (réel) + prix de référence | Idem | Idem | Coût éventuel | `lots.prix_achat_unitaire`, `product_prices` |
| Prix de vente | Obligatoire (réglementé si applicable) | Obligatoire | Obligatoire | Tarif ou gratuit | `product_prices` |
| TVA | Catégorie fiscale obligatoire | Idem | Idem | Idem | `tax_category_id` |
| Unité de vente / conditionnement | Obligatoire (`product_units`) | Obligatoire (paire, unité…) | Obligatoire | — | `product_units` |
| Seuil d'alerte | Facultatif | Facultatif | Facultatif | — | `seuil_min` |
| Statut de commercialisation | Hérité du référentiel, modifiable localement avec motif | Local | Local | Actif / inactif | `marketing_status` |
| Variantes (pointure, taille, teinte, SPF…) | — | Fréquent | Fréquent | — | `variant_of_id`, `product_variant_attributes` |
| Suivi des lots | Obligatoire | Selon le dispositif (numéro de série possible) | Facultatif | Aucun | `lot_tracking`, `serial_tracking` |

Règles :
1. Un médicament n'est vendable que s'il est `ACTIF`, c'est-à-dire **validé par un pharmacien** pour ses données réglementaires.
2. Un produit au statut de commercialisation `ARRETE` ou `SUSPENDU` reste vendable sur son stock existant, sauf blocage
   explicite (rappel de lot : statut `RAPPELE` sur le lot) ; il est exclu des propositions de commande (M11).
3. Les données de référence (référentiel APEX) ne sont pas modifiables localement ; une divergence locale est enregistrée
   comme surcharge motivée et signalée lors de la mise à jour du référentiel.
4. Les **sous-familles demandées** (chaussures et semelles orthopédiques, plâtres, attelles, bandages, pansements, thermomètres,
   tensiomètres, premiers soins, aides à la mobilité ; écrans et huiles solaires, soins du visage et du corps, capillaires,
   hygiène, bébé, dermatologie, maquillage, bucco-dentaire) sont livrées comme **nœuds initiaux** de l'axe
   `FAMILLE_COMMERCIALE`, modifiables par l'officine. Ce sont des données, pas des tables.
5. Le classement « dispositif médical » d'un produit a des conséquences réglementaires (traçabilité, matériovigilance) :
   le type `DISPOSITIF_MEDICAL` est distinct de `ACCESSOIRE` **[À VALIDER R42]**.

### 5.4 Lots, FEFO et états du stock (Partie 3B)

| Notion | Définition | Vendable | Source |
|---|---|---|---|
| **Disponible** | Bucket `DISPONIBLE` | Oui | `stock_levels` |
| **Réservé** | Engagement sur le disponible (vente en attente, reliquat d'ordonnance), sans mouvement | Non (pour les autres ventes) | `stock_reservations` |
| **Disponible vendable** | Disponible − réservations actives | — | Calculé |
| **En contrôle** | Bucket `EN_CONTROLE` (retours, annulations, réceptions à contrôler si activé) | Non | `stock_levels` |
| **Quarantaine** | Bucket `QUARANTAINE` (rappel, suspicion, réglementé retourné) | Non | `stock_levels` |
| **Perte** | Bucket `PERTE` jusqu'à destruction | Non | `stock_levels` + `loss_records` |

- **Correction d'EXT05** : le bucket `RESERVE` est retiré. Une réservation n'est pas un mouvement physique, et la modéliser
  comme un bucket créerait deux sources pour la même information (D76).
- **FEFO** : l'allocation à la vente choisit le lot `ACTIF` du bucket `DISPONIBLE` à la péremption la plus proche, non
  périmé, non bloqué, en déduisant les réservations. Un lot `RAPPELE` ou `BLOQUE` n'est jamais alloué.
- **Quantité reçue / disponible** : la quantité reçue est figée sur le lot à la réception ; la quantité disponible est
  toujours lue dans `stock_levels`. Contrôle permanent : reçue = disponible + en contrôle + quarantaine + perte + Σ sorties nettes.
- Le **prix d'achat applicable** est celui du lot (coût réel de réception, remises et gratuités réparties). Le CUMP du produit
  reste calculé pour la valorisation (dossier §4.3).

### 5.5 Services pharmaceutiques (Partie 4)

| # | Règle |
|---|---|
| SV1 | Un service est un produit de type `SERVICE`, non stocké (`stock_tracked = faux`), vendu par les mêmes lignes de vente et la même caisse nominative |
| SV2 | Un service n'est **activable** que si son autorisation réglementaire est `NON_REQUISE` ou valide à la date ; à l'échéance de l'autorisation, il est désactivé automatiquement (traitement paresseux) et n'est plus proposé |
| SV3 | **Aucun service n'est livré actif.** Les exemples (prise de tension, injections, premiers soins, conseil) sont des modèles désactivés, à activer par l'administrateur après vérification de leur cadre réglementaire **[À VALIDER R47]** |
| SV4 | Seul un collaborateur détenant l'habilitation liée au service peut le réaliser (EXT02) ; la présence peut être exigée |
| SV5 | Un acte réalisé est enregistré dans `service_acts` (immuable) avec date, heure, durée, collaborateur, patient si requis ; une erreur se corrige par un acte d'annulation lié |
| SV6 | Les données de santé éventuellement saisies (mesures, observations) sont chiffrées, accessibles selon les droits et journalisées ; elles ne sont jamais transmises à l'IA externe |
| SV7 | Les consommables (seringue, coton, pansement) sortent du stock par lot, liés à l'acte |
| SV8 | Un service gratuit est enregistré au registre sans ligne de vente ; un service facturé crée une ligne de vente au tarif en vigueur (TVA selon sa catégorie fiscale, **[À VALIDER R44]**) |
| SV9 | Les annulations de services facturés suivent EXT05 (sans mouvement de stock pour le service, mouvement inverse pour les consommables non utilisés) |

### 5.6 Définitions financières uniques (Partie 7D) **[v1.1 : distinctions complétées]**

Chaque grandeur a **une seule définition**, enregistrée dans `metric_definitions` (§4.10) et utilisée par tous les écrans,
rapports, exports, contrôles et l'IA. Les montants sont en millimes entiers.

#### 5.6.1 Les cinq grandeurs à ne jamais confondre

| Grandeur (code) | Définition | Date de rattachement | HT / TTC | Contient | Ne contient pas |
|---|---|---|---|---|---|
| **Chiffre d'affaires** (`ca_ht`, `ca_ttc`) | Σ lignes de vente validées (produits **et services**) − annulations − retours et avoirs clients, tous payeurs confondus (part patient + part organismes) | Date de l'opération (validation de la vente ; pour une correction : date d'exécution de la correction) | Les deux | Ventes à crédit, part tiers payant non encore réglée | Règlements d'organismes, encaissements, TVA (pour le HT), ventes non validées |
| **CA encaissé** (`ca_encaisse`) | Σ encaissements **imputés à des ventes** : part patient payée en caisse, règlements de clients à crédit, règlements d'organismes lettrés sur des créances de ventes − remboursements clients | Date de l'encaissement (date de valeur du règlement pour la banque) | TTC (montants réellement reçus) | Encaissements de ventes d'une période antérieure | Ventes non encore réglées, encaissements sans lien avec une vente (apport, emprunt, remboursement d'un fournisseur) |
| **Marge commerciale** (`marge_commerciale`) | CA HT − coût des ventes (Σ quantités nettes vendues × coût du lot figé sur la ligne ; pour un service : coût des consommables liés) | Date de l'opération | HT | Effet des remises accordées aux clients | Pertes de stock, ristournes fournisseurs non affectées aux lots, charges |
| **Marge réalisée** (`marge_realisee`) | Marge commerciale − démarque (pertes, péremptions, casse, écarts d'inventaire valorisés au coût) − créances clients et organismes passées en perte (montant HT) + ristournes et avoirs fournisseurs de fin de période non affectés aux lots | Date de chaque composante (opération, constat de perte, passage en perte, pièce fournisseur) | HT | Ce qui reste effectivement des ventes après les pertes commerciales | Charges d'exploitation, charges financières |
| **Résultat de gestion** (`resultat_gestion`, **indicatif**) | Marge réalisée + autres produits de gestion (produits non issus des ventes, ex. rémunérations ou subventions saisies avec pièce) − charges d'exploitation (y compris la paie importée) − dotations aux amortissements validées | Date de pièce ou de rattachement de chaque élément | HT | — | Charges financières, éléments exceptionnels, impôt sur le résultat, remboursement du capital d'emprunt, investissements |

*Correction v1.1* : la v1.0 ajoutait « autres produits d'exploitation (services) » à la marge commerciale, alors que les
services sont déjà vendus par les lignes de vente et donc inclus dans le CA : ce double comptage est supprimé. Le
« résultat d'exploitation (indicatif) » de la v1.0 prend le nom unique de **résultat de gestion**.

#### 5.6.2 Autres grandeurs

| Grandeur (code) | Définition | Source | Ne contient pas |
|---|---|---|---|
| **Coût des ventes** (`cout_vendu`) | Σ quantités vendues nettes × coût du lot figé sur la ligne | `sale_lines.cout_unitaire`, `service_act_consumables` | Pertes, charges |
| **Créances** (`creances`) | Clients (crédit) + organismes (EXT04) + autres, à une date | `party_ledger` | CA futur |
| **Dettes** (`dettes`) | Fournisseurs (marchandises et charges) + salaires et organismes sociaux + emprunts (capital restant) + fiscales | `party_ledger`, `loans` | Engagements non facturés |
| **Encaissements** (`encaissements`) | **Toutes** les entrées réelles d'argent (dont le CA encaissé, les apports, les déblocages d'emprunt) | `payments` (in), `cash_movements` | Créances non réglées |
| **Décaissements** (`decaissements`) | Sorties réelles : fournisseurs, salaires, emprunts, impôts, remboursements clients | `payments` (out), `cash_movements` | Dettes non payées |
| **Trésorerie** (`tresorerie`) | Soldes caisse (sessions + coffre) + soldes bancaires (rapprochés, et non rapprochés en distinction) | M08 | Créances, chèques non remis (affichés à part) |
| **Charges d'exploitation** (`charges_exploitation`) | Charges de la période par catégorie, hors achats de marchandises stockés, hors intérêts et charges exceptionnelles | `supplier_invoices` (nature CHARGE), `expenses`, paie importée | Remboursement du capital, investissements (sauf dotation) |
| **Résultat net (indicatif)** (`resultat_net_indicatif`) | Résultat de gestion − charges financières (intérêts) ± éléments exceptionnels − impôt **saisi** par l'expert-comptable | Couche métriques | Aucun impôt calculé par défaut **[À VALIDER R46]** |
| **Charges fixes / variables** | Selon `expense_categories.nature_analytique` | Paramétrage | — |

Règles :
1. **Un règlement CNAM ou d'assurance est un encaissement qui solde une créance**, jamais une vente (EXT04 §7.2). Le CA de
   la vente tiers payant est constaté une seule fois, à la validation ; le règlement augmente le CA encaissé, pas le CA.
2. Les résultats calculés par PHARM'OS sont **indicatifs** (gestion). Le résultat officiel est établi par l'expert-comptable ;
   l'écran l'indique explicitement **[À VALIDER R46]**. Les libellés « marge réalisée » et « résultat de gestion » sont des
   notions de gestion internes, à confirmer par l'expert-comptable.
3. Les remboursements de capital d'emprunt sont des décaissements, pas des charges ; les investissements sont des décaissements
   et des immobilisations, amortis selon les règles validées.
4. Les variations de stock n'affectent pas la marge : la marge utilise le coût des produits vendus.
5. Chaque indicateur affiché porte un lien « comment est-il calculé ? » vers sa définition (`metric_definitions`) et ses données.
6. Les taux (taux de marge, taux d'encaissement, rotation) ne sont **jamais stockés ni moyennés** : ils sont recalculés à la
   lecture à partir de leurs composantes additives (ex. taux de marge = Σ marge / Σ CA HT sur la période).

### 5.6 bis Couche métriques : règles techniques **[v1.1]**

| # | Règle |
|---|---|
| MT1 | **Granularité** : une ligne = une officine × un jour local × une métrique (code + version) × une dimension (type + identifiant). Le jour local est calculé à partir de l'horodatage serveur et du fuseau de l'officine (`tenants.fuseau`). L'option « jour d'exploitation » des annulations (§5.2) **ne change pas** le jour de rattachement des métriques |
| MT2 | **Clé unique** : (tenant_id, date, metric_code, metric_version, dimension_type, dimension_id). Toute écriture est un UPSERT sur cette clé, jamais un incrément |
| MT3 | **Dimensions** : une seule dimension par ligne (pas de produit cartésien stocké) : `TOTAL`, `PRODUCT`, `PRODUCT_TYPE`, `CLASSIFICATION_NODE`, `CLASSIFICATION_PRINCIPAL`, `MANUFACTURER`, `BRAND`, `TAX_CATEGORY`, `SALE_MODE`, `PRESCRIBER`, `SPECIALTY` (EXT03), `PAYER`, `PAYER_MODE` (EXT04), `PAYMENT_MODE`, `SERVICE`, `EMPLOYEE`, `EXPENSE_CATEGORY`. Les analyses croisées (ex. famille × laboratoire) sont calculées à la lecture depuis la dimension `PRODUCT` et les attributs du produit |
| MT4 | **Axes multi-valeurs** : un produit classé dans trois familles apparaît dans les trois nœuds (`CLASSIFICATION_NODE`, **non additif** : la somme des nœuds dépasse le total). Pour les répartitions additives (parts, camemberts), la dimension `CLASSIFICATION_PRINCIPAL` utilise le seul nœud principal de chaque axe. L'écran indique laquelle est utilisée |
| MT5 | **Agrégation** : métriques de flux (`SOMME`) additives dans le temps et sur les dimensions mono-valeur ; métriques de stock (`DERNIERE_VALEUR` : valeur du stock, capital immobilisé, créances, dettes, trésorerie) lues à une date, **jamais additionnées dans le temps** ; ratios recalculés à la lecture (§5.6 règle 6) |
| MT6 | **Annulations et retours** : une correction est un événement à part entière, rattaché à **sa date d'exécution**. Elle diminue les mêmes métriques (CA, coût, marge, quantité) et alimente en plus des mesures de suivi (`ca_annulations_ht`, `ca_retours_ht`, `nb_corrections`). Un jour déjà écoulé n'est jamais modifié par une correction ultérieure : c'est la même règle que la contre-passation comptable (§5.13), ce qui garantit que métriques et journal des ventes concordent jour par jour. Une analyse « par date de vente d'origine » reste possible à la lecture depuis les registres (`corrective_operations.original_sale_id`) |
| MT7 | **Calcul et idempotence** : un projecteur unique consomme les événements de l'outbox (dossier §3.2) et **recalcule entièrement** les lignes du jour et de l'officine concernés à partir des registres sources (ventes, corrections, paiements, mouvements de stock, pièces). Rejouer un événement produit donc exactement le même résultat ; un doublon d'événement ne peut pas compter deux fois |
| MT8 | **Corrections et recalculs** : tant que la période comptable est `OUVERTE`, un jour peut être recalculé (même résultat attendu). Après `VERROUILLEE`, les lignes de la période sont figées et `financial_snapshots` fait foi ; un recalcul technique est autorisé uniquement en contrôle (comparaison) et tout écart ouvre une exception `ECART_METRIQUES_JOURNAL`. Une nouvelle version de définition crée une nouvelle `metric_version` calculée en parallèle ; l'ancienne reste consultable ; les clichés ne sont jamais recalculés |
| MT9 | **Date de rattachement** : fixée par la définition (`DATE_OPERATION` pour CA et marge, `DATE_ENCAISSEMENT` pour CA encaissé et encaissements, `DATE_PIECE` pour charges et achats, date du constat pour les pertes). Une même vente peut donc compter dans le CA de mars et dans le CA encaissé d'avril : c'est voulu et expliqué à l'écran |
| MT10 | **Contrôles de cohérence quotidiens** : (a) Σ des dimensions additives = `TOTAL` ; (b) `ca_ht` `TOTAL` = Σ lignes de vente − corrections du jour = solde du journal des ventes M20 du jour (hors TVA) ; (c) `ca_encaisse` = Σ paiements imputés à des ventes ; (d) `marge_commerciale` = `ca_ht` − `cout_vendu` ; (e) CA du tableau de bord commercial = CA du tableau de bord financier (même code, même version) ; (f) à la clôture : cliché = Σ `metrics_daily` de la période. Tout écart ouvre une exception comptable (§5.14) ; il n'est jamais corrigé en écrasant une valeur |
| MT11 | **Source unique** : les tableaux de bord (M01, G23, G24, G25), M12, les exports et l'IA lisent exclusivement `metrics_daily`, `financial_snapshots`, `rx_facts` (EXT03) et les projections de créances (EXT04), via les codes de `metric_definitions`. Aucun écran ne recalcule une grandeur avec sa propre formule |

### 5.7 Tableau de bord financier (G23)

| Indicateur | Définition (§5.6) | Comparaisons |
|---|---|---|
| Chiffre d'affaires | CA HT et TTC | Période précédente, N−1 |
| Marge brute | Montant et taux | Idem |
| Charges fixes, charges variables | Par catégorie | Budget (si saisi), N−1 |
| Chiffre d'affaires encaissé **[v1.1]** | `ca_encaisse` | Période précédente, N−1 ; écart CA / CA encaissé |
| Marge réalisée **[v1.1]** | `marge_realisee` (démarque et pertes sur créances en détail) | Idem |
| Résultat de gestion (indicatif) **[v1.1 : remplace « résultat d'exploitation »]** | `resultat_gestion` | Idem |
| Exceptions comptables ouvertes **[v1.1]** | Nombre et montant par gravité (§5.14) | — |
| Créances CNAM et assurances | Encours par organisme, balance âgée (EXT04 TP2) | — |
| Dettes fournisseurs | Encours, échues, à échoir 30 j | — |
| Trésorerie | Actuelle et projetée à 30 / 60 / 90 j (échéancier `due_dates`, prévision d'encaissement EXT04) | — |
| Échéances | Calendrier : fournisseurs, emprunts, impôts, salaires, chèques émis | — |
| Résultat net (indicatif) | — | — |

Accès : titulaire / administrateur et comptable ; les montants de paie individuels ne sont jamais affichés (agrégats seulement).
Les périodes closes affichent les clichés figés (`financial_snapshots`) ; la période en cours est calculée en continu.

### 5.8 Tableau de bord commercial (G24)

| Indicateur | Dimensions | Source |
|---|---|---|
| Ventes journalières, mensuelles, annuelles | Date | `metrics_daily` |
| Ventes par famille, catégorie, type | Axes de classification, `product_type` | Idem |
| Ventes par médicament, par laboratoire, par marque | Produit, `manufacturer`, `brand` | Idem |
| Marges par famille | Famille | Idem |
| Produits les plus vendus | Quantité, CA, marge | Idem |
| Produits à faible rotation | Rotation 90 j, jours sans vente, valeur immobilisée (analyseur C du dossier) | Idem + stock |
| Ventes avec et sans ordonnance | `sale_mode` (EXT03) | Idem |
| Ventes par mode de paiement et par payeur | `payment_mode`, `payer` (EXT04) | Idem |
| Évolution des achats et des ventes | Achats HT (réceptions) vs CA HT | Idem |
| Prévisions de réapprovisionnement | Suggestions de M11 (dossier analyseur D, EXT03 demande engagée) | `po_suggestions` |
| Services réalisés | Nombre, CA services | `service_acts`, `metrics_daily` |

Accès : selon la matrice (module `ANALYTICS`, action `READ`) ; les montants de marge peuvent être masqués à certains rôles
(permission dédiée `analytics.margin.read`).

**Cohérence** : les tableaux de bord **ne calculent rien eux-mêmes**. Ils lisent la couche métriques (alimentée par les
événements), `rx_facts` (EXT03) et les projections de créances (EXT04). Un contrôle quotidien vérifie que le CA du tableau
de bord commercial = CA du tableau de bord financier = journal des ventes M20 (**[v1.1]** règles MT10 et MT11 ; tout écart
ouvre une exception comptable). L'analyse de rentabilité détaillée est au §5.12 (G25).

### 5.9 Prescripteurs, spécialités, organismes (Parties 5 et 6)

- L'accès direct depuis l'accueil ouvre les écrans **existants** d'EXT03 (`/prescribers`, filtre par spécialité) et
  d'EXT04 (`/payers`, filtre par type d'organisme). Aucune donnée n'est dupliquée.
- Les spécialités demandées (cardiologie, pédiatrie, dermatologie, gynécologie, médecine générale, ORL, ophtalmologie,
  neurologie, endocrinologie, autres) sont livrées comme **valeurs initiales** du référentiel `medical_specialties`
  (EXT03), administrables.
- Organismes : les types demandés (CNAM, mutuelles, assurances privées, organismes conventionnés, autres) correspondent à
  `payers.type` (EXT04). Une convention suspendue reste consultable avec tout son historique et n'est plus proposée à la
  vente à partir de la date de suspension (EXT04 E2, W10) — exigence déjà couverte.

### 5.10 Recherche universelle et navigation (Partie 8)

| # | Règle |
|---|---|
| NV1 | Un **index de recherche unique** (table de recherche plein texte SQLite FTS5 par officine, alimentée par les événements du catalogue) couvre : nom commercial FR/AR, DCI, code-barres, laboratoire, marque, nœuds de classification, services ; médecins et organismes seulement si l'utilisateur a les droits correspondants |
| NV2 | Le scan d'un code-barres ouvre directement le produit (ou l'ajoute au panier en contexte de caisse) |
| NV3 | Normalisation : accents, casse, translittération AR ↔ FR, tolérance aux fautes (trigrammes), priorité aux produits en stock et aux plus vendus de l'officine |
| NV4 | Résultats regroupés par type, avec stock disponible vendable, prix et indicateur réglementé ; aucun résultat interdit par les droits n'est renvoyé par l'API |
| NV5 | Temps de réponse visé : < 150 ms côté serveur (dossier §2.3) ; cache local du catalogue sur le poste de caisse (dossier §6.7) |
| NV6 | Page affichée après connexion : selon le rôle (caissier → caisse ; préparateur → accueil ; titulaire → tableau de bord ou accueil, au choix) **[ARBITRAGE D77]** |

### 5.11 Compatibilité multi-officines **[v1.1]**

| # | Règle |
|---|---|
| MO1 | **Une officine = un tenant.** Chaque officine conserve son pharmacien administrateur (`tenant_administrators`), ses données, ses utilisateurs et leurs rattachements (`memberships`), ses permissions (`role_permissions`, `permission_overrides`, `delegations`), sa comptabilité M20 (plan de comptes, journaux, périodes, séquences), ses paramètres (`settings`) et son abonnement |
| MO2 | **Aucune table métier sans `tenant_id`**, et aucune requête métier sans filtre `tenant_id` issu de la session (jamais d'un paramètre client). C'est déjà une règle du dossier ; EXT06 l'étend explicitement aux 43 tables nouvelles. Seules exceptions : les référentiels APEX partagés et en lecture seule (`app_modules`, `product_type_fields`, `metric_definitions`, lignes à `tenant_id` NULL des référentiels), qui ne contiennent aucune donnée d'officine |
| MO3 | **Aucune clé étrangère entre deux officines** : une écriture, un lot, un mouvement, une délégation, une dérogation ou une exception d'une officine ne référence jamais un objet d'une autre officine. Les référentiels APEX partagés (`tenant_id` NULL) sont en lecture seule pour les officines |
| MO4 | **Séquences par officine** : numéros de documents et d'écritures comptables (`document_sequences`, séquences de journaux) ne sont jamais partagés |
| MO5 | Une même personne (`users`) peut être membre de plusieurs officines avec des rôles différents ; elle **choisit explicitement** l'officine active ; la session porte un seul `tenant_id` à la fois ; changer d'officine ouvre une nouvelle session (dossier `sessions`). Aucun droit n'est hérité d'une officine à l'autre |
| MO6 | **Aucune délégation, aucune dérogation et aucune validation à distance ne traverse les officines** : l'administrateur de l'officine A n'a aucun pouvoir dans l'officine B, même s'il en est propriétaire, sauf à y être lui-même désigné administrateur |
| MO7 | **Vue consolidée future** (non développée dans EXT06) : lecture seule ; uniquement des **agrégats** issus de `metrics_daily` et `financial_snapshots` (mêmes codes de `metric_definitions`, donc comparables) ; jamais de données nominatives de patients, d'ordonnances ou de paie ; accessible seulement à une personne explicitement autorisée **par l'administrateur de chaque officine concernée** (consentement révocable, tracé dans l'`audit_log` de chaque officine) |
| MO8 | La vue consolidée **lit** les officines, elle ne les fusionne pas : aucune écriture comptable consolidée, aucun stock commun, aucun transfert entre officines n'est prévu (un transfert éventuel serait une vente ou un achat entre deux entités, à concevoir dans une extension dédiée) |
| MO9 | **Tables futures réservées, non créées** : `organizations` (propriétaire), `organization_tenants` (rattachement validé par l'administrateur de l'officine), `organization_access_grants` (personne × officine × périmètre agrégé). Leur nom est réservé pour éviter tout doublon ultérieur ; elles ne figurent pas dans le décompte des tables |
| MO10 | Les tests d'isolation (§10.10) sont exécutés dès le Lot 0 sur les nouvelles tables, avec deux officines de test |

**Lecture de D15 (dossier)** : D15 prévoit `sites` pour « multi-officines / dépôt ». EXT06 propose de **réserver `sites` aux
dépôts et annexes d'une même officine**, et de traiter chaque officine comme un tenant distinct (MO1). Cette précision sera
soumise lors de l'intégration v1.2 ; elle ne modifie aucune table existante.

### 5.12 Analyse de rentabilité commerciale (G25) **[v1.1]**

Tous les indicateurs ci-dessous utilisent **exclusivement** les définitions du §5.6 et la couche métriques (MT11). Aucun
indicateur n'est stocké s'il peut être recalculé à partir de métriques additives ; aucun seuil n'est imposé : ils sont
paramétrables par l'officine.

| Indicateur | Définition | Métriques utilisées | Dimensions |
|---|---|---|---|
| **Produits à forte marge** | Produits dont le taux de marge commerciale (Σ marge / Σ CA HT sur la période) est au-dessus du seuil haut **et** dont la marge en valeur figure dans le décile supérieur | `marge_commerciale`, `ca_ht` | `PRODUCT` (puis attributs) |
| **Produits à faible marge** | Taux de marge sous le seuil bas, ou marge négative (vente sous le coût du lot) ; les produits à **prix réglementé** sont signalés comme tels (marge non pilotable) **[À VALIDER R51]** | Idem | Idem |
| **Produits à faible rotation** | Rotation = coût des ventes sur 90 jours / valeur moyenne du stock au coût ; jours de couverture = stock / consommation moyenne journalière ; jours sans vente (analyseur C du dossier) | `cout_vendu`, `valeur_stock_cout` (`DERNIERE_VALEUR`), `quantite` | `PRODUCT` |
| **Capital immobilisé en stock** | Valeur au coût du stock disponible + en contrôle + quarantaine, à une date ; part dormante (sans vente depuis N jours) | `valeur_stock_cout`, `valeur_stock_dormant` (`DERNIERE_VALEUR`) | `TOTAL`, `PRODUCT`, `CLASSIFICATION_PRINCIPAL`, `MANUFACTURER` |
| **Risque de péremption** | Valeur au coût des lots dont la péremption intervient dans 30 / 60 / 90 jours, **diminuée** de la consommation prévisible avant la date (`product_demand_stats`) = quantité à risque | `valeur_risque_peremption_30/60/90` (`DERNIERE_VALEUR`, calculée depuis `lots` et `stock_levels`) | `PRODUCT`, `MANUFACTURER`, `CLASSIFICATION_PRINCIPAL` |
| **Rentabilité par famille commerciale** | CA HT, marge commerciale, marge réalisée, taux de marge, part du CA, capital immobilisé, rotation, par nœud principal de l'axe `FAMILLE_COMMERCIALE` (MT4 : additif) | `ca_ht`, `marge_commerciale`, `marge_realisee`, `valeur_stock_cout` | `CLASSIFICATION_PRINCIPAL` |
| **Performance par laboratoire et par marque** | Mêmes indicateurs + démarque (pertes et péremptions) + avoirs et ristournes obtenus + nombre de références actives | Idem + `demarque_cout`, `ristournes_fournisseurs` | `MANUFACTURER`, `BRAND` |
| **Matrice marge × rotation** | Classement en quatre quadrants (forte marge / forte rotation, etc.), seuils paramétrables | Combinaison des lignes ci-dessus | `PRODUCT` |

Règles :
1. Les nouvelles métriques nécessaires (`valeur_stock_cout`, `valeur_stock_dormant`, `valeur_risque_peremption_30/60/90`,
   `demarque_cout`, `ristournes_fournisseurs`) sont ajoutées à `metric_definitions` ; aucune table nouvelle.
2. Les indicateurs sont des **aides à la décision** : ils peuvent alimenter des recommandations de l'IA (dossier §7), qui ne
   modifie jamais un prix, une commande ou un référencement sans décision humaine.
3. Accès : `analytics.profitability.read` ; les montants de marge restent soumis à `analytics.margin.read` (D79).
4. Le prix de vente d'un médicament à prix réglementé n'est jamais présenté comme un levier d'action.

### 5.13 M20 — Immutabilité, idempotence et atomicité des écritures **[v1.1]**

| # | Règle | Contrôle technique |
|---|---|---|
| CP1 | **Une écriture validée est strictement immuable** : ni modification, ni suppression, quel que soit le rôle (y compris l'administrateur et le support APEX) | Déclencheurs SQLite refusant UPDATE et DELETE sur `journal_entries` et `journal_entry_lines` ; aucune route d'API de modification ; test ACC-01 |
| CP2 | **Toute correction = contre-passation + nouvelle écriture.** La contre-passation reprend exactement les lignes de l'écriture d'origine en inversant débit et crédit, porte `reverses_id` et le même `correlation_id`, est datée dans une période ouverte ; la nouvelle écriture porte le même `correlation_id` | UNIQUE(reverses_id) : une écriture n'est contre-passée qu'une fois ; une contre-passation ne peut pas elle-même être contre-passée (CHECK : `origine` de la cible ≠ `CONTRE_PASSATION`) |
| CP3 | **Les brouillons ne sont pas des écritures** (`journal_entry_drafts`) : seuls les brouillons `EN_COURS` sont modifiables ; la comptabilisation crée l'écriture et fige le brouillon dans le même batch | W15 ; garde conditionnelle `WHERE statut = 'SOUMIS' AND version = ?` |
| CP4 | **Unicité par événement et par version de règle** : `generation_key` = SHA-256(tenant_id, source_event_type, source_id, posting_rule_id, posting_rule_version, **rôle**) où rôle ∈ {`ORIGINALE`, `CONTRE_PASSATION`, `REPRISE`}. Un événement métier ne génère donc qu'**une seule** écriture pour une version donnée de la règle | UNIQUE(tenant_id, generation_key) ; test ACC-04 |
| CP5 | **Changement de version de règle** : une nouvelle version ne s'applique qu'aux événements dont la date est postérieure à son `valid_from`. Reprendre un événement déjà comptabilisé avec la nouvelle version exige une décision explicite (exception W14, voie 3) : contre-passation de l'écriture d'origine, puis reprise ; jamais deux écritures actives pour le même événement | Contrôle à la génération : refus si une écriture `ORIGINALE` non contre-passée existe déjà pour l'événement, quelle que soit la version |
| CP6 | **Idempotence des traitements** : le générateur consomme l'outbox (`domain_events`) ; avant d'écrire, il calcule `generation_key` ; si une écriture existe déjà avec cette clé, il marque l'événement traité **sans rien écrire** (résultat identique). Les validations manuelles portent l'en-tête `Idempotency-Key` du dossier (§6) | Rejouer N fois un même événement, en parallèle ou en séquence, produit exactement une écriture (tests ACC-05, ACC-06) |
| CP7 | **Relance technique sans doublon** : un échec après l'écriture mais avant le marquage de l'événement (coupure, délai dépassé) est absorbé par CP4 et CP6 ; deux traitements concurrents du même événement : le second échoue sur la contrainte d'unicité, ce qui est traité comme « déjà fait » et non comme une erreur | Test ACC-06 (concurrence), ACC-07 (reprise après coupure) |