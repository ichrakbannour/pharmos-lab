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
| CP8 | **Contrôle débit = crédit avant validation définitive, sur l'ensemble des lignes, dans une transaction atomique.** Un seul `D1 batch` contient : (1) garde de période ouverte ; (2) garde de comptes actifs ; (3) réservation du numéro (UPDATE conditionnel de la séquence) ; (4) insertion de toutes les lignes ; (5) insertion de l'en-tête **en dernier**, par `INSERT … SELECT` qui recalcule Σ débit, Σ crédit, nombre de lignes et empreinte **depuis les lignes insérées**, avec la condition `HAVING Σ débit = Σ crédit AND Σ débit > 0 AND nombre = nombre attendu` ; (6) contrôle final : la clé étrangère des lignes vers l'en-tête est **différée** (vérifiée à la fin de la transaction) ; si l'en-tête n'a pas été inséré parce que la condition n'est pas remplie, la transaction échoue et **tout** est annulé | Aucune ligne « orpheline » sans en-tête ne peut subsister ; le scellement de l'en-tête interdit toute ligne ajoutée ensuite (CP9) ; tests ACC-02, ACC-03. **À prototyper au Lot 0** : comportement de D1 avec les contraintes différées dans un `batch` ; repli si elles ne sont pas prises en charge : instruction de garde finale déclenchant `RAISE(ABORT)` lorsque l'en-tête est absent |
| CP9 | **Scellement** : une fois l'en-tête inséré, toute insertion de ligne supplémentaire sur cette écriture est refusée (déclencheur : refus si un en-tête existe pour `entry_id`). Les vues de grand livre et de balance ne lisent que les lignes dont l'en-tête existe | Test ACC-08 |
| CP10 | **Aucune écriture déséquilibrée ne peut être validée ni exportée** : CHECK(total_debit = total_credit) sur l'en-tête ; à l'export, recalcul depuis les lignes, comparaison avec l'en-tête (`total_*`, `line_count`, `lines_hash`) ; un seul écart → export refusé et exception `INTEGRITE_ECRITURE` (BLOQUANTE) | Tests ACC-09, ACC-10 |
| CP11 | **Numérotation** : séquence continue par journal et exercice, attribuée dans le batch de validation (CP8) ; un batch annulé ne consomme pas de numéro (la réservation fait partie du batch) ; aucune réutilisation | UNIQUE(tenant_id, journal_id, exercice, numero) ; contrôle de continuité à la clôture |
| CP12 | **Périodes** : aucune écriture dans une période `VERROUILLEE` ou `EXPORTEE` ; une correction se date dans la première période ouverte | Déclencheur sur `journal_entries` |
| CP13 | **Contrôles d'export** : toutes les écritures de la période sont équilibrées et intègres (CP10) ; aucune exception BLOQUANTE ouverte ; nombre d'écritures et totaux de l'export = ceux de la base ; le fichier est haché (`sha256`) et conservé (R2) | `accounting_exports.entry_count`, `total_debit`, `total_credit` |

### 5.14 M20 — Gestion des exceptions comptables **[v1.1]**

| # | Règle |
|---|---|
| EX1 | **Toute anomalie comptable est enregistrée** dans `accounting_exceptions` (le type `ECART_CAISSE` désigne une **discordance entre la caisse (M08) et le compte de caisse de M20** ; l'écart de comptage d'une session reste traité par EXT02 — `quality_events` `ECART_CAISSE` et décision humaine — et produit seulement une écriture d'écart via `cash_session.closed` : aucun doublon) : échec de génération (règle absente, compte inactif, période fermée, expression invalide), déséquilibre détecté, écart de rapprochement bancaire, écart tiers (`party_ledger` ↔ M20), écart de caisse ou de valorisation du stock avec la comptabilité, écart métriques ↔ journal, refus de validation d'un brouillon, défaut d'intégrité, refus d'export |
| EX2 | **Identifiable par son origine** : chaque exception porte l'événement métier d'origine (`source_event_type`, `source_id`, `source_event_id`, `correlation_id`) et la période ; depuis G26, un lien ouvre l'opération d'origine (vente, facture, règlement, relevé) en lecture |
| EX3 | **Associée à un montant** : `montant_concerne_millimes` (montant de l'opération ou de la ligne) et `montant_ecart_millimes` (écart constaté, 0 si sans objet) ; le tableau de bord financier affiche le nombre et le montant total des exceptions ouvertes par gravité |
| EX4 | **Affectée à un responsable** dès sa création (règle d'affectation paramétrable : comptable habilité, sinon administrateur), avec une échéance fonction de la gravité (paramétrable) ; réaffectation tracée ; échéance dépassée → escalade à l'administrateur |
| EX5 | **Suivie jusqu'à résolution** : statuts `OUVERTE` → `EN_ANALYSE` → `RESOLUE` → `CLOSE`, avec `REOUVERTE` si l'anomalie est de nouveau détectée ; jamais supprimée ; la clôture des gravités BLOQUANTE et MAJEURE exige une seconde personne |
| EX6 | **Une erreur comptable ne fait jamais disparaître une opération métier validée** : la génération comptable s'exécute **après** la validation métier, dans un traitement distinct (outbox) ; son échec ne peut ni annuler, ni modifier, ni masquer la vente, la facture ou le règlement. L'événement reste en file, l'exception le rend visible, la clôture de période est bloquée tant qu'il n'est pas traité (W13) |
| EX7 | **Déduplication** : une même anomalie (même `detection_key`) ne crée qu'une exception ouverte, dont le compteur d'occurrences augmente ; une nouvelle version de règle produit une nouvelle clé |
| EX8 | **Reprise** (voie REGENERATION) : après correction du paramétrage, le retraitement de l'événement est idempotent (CP6) ; il crée au plus une écriture, liée à l'exception (`accounting_exception_id`) |
| EX9 | **Régularisation** (voie ECRITURE_REGULARISATION ou CONTRE_PASSATION_ET_REPRISE) : toujours par brouillon validé à quatre yeux (W15) ou contre-passation (CP2) ; jamais par modification d'une écriture |
| EX10 | **Contrôle** : rapport mensuel des exceptions (ouvertes, résolues, délai moyen, montants) joint au dossier de clôture ; les exceptions `ECART_JUSTIFIE` sont listées avec leur justification pour l'expert-comptable |
| EX11 | Les exceptions ne contiennent aucune donnée de santé ni de paie individuelle dans `details` (références seulement) ; elles ne sont pas transmises à l'IA externe (agrégats seulement) |

---

## 6. Permissions

### 6.1 Matrice modules × actions (structure)

Chaque module de `app_modules` expose les actions génériques ci-dessous. Une case n'existe que si l'action a un sens pour
le module (ex. pas d'`ARCHIVE` sur les ventes). Les permissions spécifiques déjà définies (EXT02 à EXT05) restent attachées
à leur module avec l'action `SPECIFIC`.

| Action | Signification | Exemple (module CATALOG) | Contrôle serveur |
|---|---|---|---|
| `ACCESS` | Ouvrir le module (menu, écrans) | Voir l'explorateur | Prérequis de toutes les autres actions |
| `READ` | Consulter les données (portée : soi / équipe / tous) | Voir une fiche produit | Filtrage des requêtes |
| `CREATE` | Créer un objet | Créer un produit local | — |
| `UPDATE` | Modifier un objet non validé ou modifiable | Modifier un prix de vente | Versionnement, motif si sensible |
| `VALIDATE` | Valider (changement d'état vers un état engageant) | Valider les données réglementaires d'un médicament | Séparation des tâches |
| `CANCEL` | Annuler par une opération inverse | — (catalogue) ; annulation d'une vente (POS) | Jamais de suppression |
| `EXPORT` | Exporter (PDF, Excel, CSV) | Exporter le catalogue | Journalisé ; données nominatives soumises au workflow W6 d'EXT03 |
| `ARCHIVE` | Suppression logique, si autorisée pour le type d'objet | Archiver un produit sans mouvement | Liste blanche (GV11) |
| `SPECIFIC` | Permission métier spécifique | `catalog.substitution.validate` | Selon la permission |

### 6.2 Permissions nouvelles d'EXT06

| Permission | Module / action | Délégable | Défaut |
|---|---|---|---|
| `governance.admin.transfer` | SETTINGS / SPECIFIC | **Non** | Administrateur |
| `governance.permissions.manage` (matrice, réglages individuels, coupure de module) | SETTINGS / SPECIFIC | **Non** | Administrateur |
| `governance.delegations.manage` | SETTINGS / SPECIFIC | **Non** | Administrateur (remplace `delegation.manage` d'EXT05) |
| `governance.exceptions.grant` (dépassements, dérogations de délai, **[v1.1]** y compris à distance) | SETTINGS / SPECIFIC | **Non** — ni délégable, ni accordable par réglage individuel, ni attribuable à un rôle | Administrateur actif **uniquement** (attaché à `tenant_administrators`, pas à un rôle) |
| `governance.exceptions.request` **[v1.1]** | SETTINGS / SPECIFIC | Oui | Tous les collaborateurs ayant un plafond |
| `governance.reference_day.manage` **[v1.1]** (option « jour d'exploitation ») | SETTINGS / SPECIFIC | **Non** | Administrateur uniquement, MFA |
| `catalog.product.create` / `.update` / `.archive` | CATALOG | Oui | Adjoint, responsable achats |
| `catalog.regulatory.validate` (classe, prescription, réglementaire) | CATALOG / VALIDATE | Pharmacien uniquement | Titulaire, adjoint |
| `catalog.substitution.validate` | CATALOG / SPECIFIC | Pharmacien uniquement | Titulaire, adjoint |
| `catalog.price.update` | CATALOG / UPDATE | Oui (plafond d'écart en %) | Titulaire, adjoint |
| `catalog.classification.manage` | CATALOG / SPECIFIC | Oui | Titulaire, adjoint |
| `tax.manage` (catégories, taux) | SETTINGS / SPECIFIC | **Non** | Administrateur (quatre yeux avec le comptable si présent) |
| `services.manage` (définitions, activation) | SERVICES / UPDATE, VALIDATE | **Non** pour l'activation | Administrateur |
| `services.perform` | SERVICES / CREATE | Selon l'habilitation du service | Collaborateurs habilités |
| `services.acts.read` (avec données patient) | SERVICES / READ | Non | Titulaire, adjoint, auteur de l'acte |
| `finance.charges.create` / `.validate` | FINANCE | Oui (plafond) | Comptable, administrateur |
| `finance.payments.execute` | FINANCE / SPECIFIC | Oui (plafond) | Comptable, administrateur |
| `finance.loans.manage`, `finance.assets.manage` | FINANCE | Non | Administrateur, comptable |
| `finance.bank.reconcile` | FINANCE / VALIDATE | Oui | Comptable |
| `finance.payroll.import` / `.validate` | FINANCE | **Non** | Administrateur (validation), comptable (import) |
| `accounting.setup` (plan, journaux, règles de passation) | ACCOUNTING / SPECIFIC | **Non** | Administrateur + comptable (quatre yeux) |
| `accounting.read`, `accounting.export` | ACCOUNTING | Oui | Comptable, administrateur, auditeur (lecture) |
| `accounting.period.close` / `.reopen` | ACCOUNTING / VALIDATE | **Non** | Administrateur (réouverture : administrateur seul, motif) |
| `accounting.drafts.create` **[v1.1]** (brouillons d'opérations diverses) | ACCOUNTING / CREATE | Oui | Comptable, administrateur |
| `accounting.drafts.post` **[v1.1]** (comptabiliser un brouillon d'autrui) | ACCOUNTING / VALIDATE | Oui (plafond) | Comptable, administrateur ; jamais l'auteur du brouillon |
| `accounting.reverse` **[v1.1]** (contre-passation manuelle) | ACCOUNTING / CANCEL | **Non** | Administrateur, comptable habilité ; motif obligatoire |
| `accounting.exceptions.read` / `.resolve` / `.close` **[v1.1]** | ACCOUNTING / READ, UPDATE, VALIDATE | Oui (sauf clôture des BLOQUANTES) | Comptable, administrateur ; clôture BLOQUANTE / MAJEURE par une autre personne que celle qui a résolu |
| `accounting.posting_rules.activate` **[v1.1]** | ACCOUNTING / VALIDATE | **Non** | Quatre yeux : auteur ≠ valideur (administrateur ou comptable) |
| `analytics.finance.read` | ANALYTICS / READ | Oui | Administrateur, comptable |
| `analytics.sales.read` | ANALYTICS / READ | Oui | Administrateur, adjoint, responsable achats |
| `analytics.margin.read` | ANALYTICS / SPECIFIC | Oui | Administrateur, adjoint, comptable |
| `analytics.profitability.read` **[v1.1]** (G25) | ANALYTICS / READ | Oui | Administrateur, adjoint, responsable achats (marges selon `analytics.margin.read`) |
| `consolidated.read` **[v1.1 — réservé, non créé]** | — | — | Vue consolidée future (MO7) ; aucune attribution possible dans EXT06 |

### 6.3 Matrice par défaut des modules (accès et actions principales)

| Module | Administrateur | Adjoint | Préparateur | Caissier | Achats / stock | Comptable | Auditeur |
|---|---|---|---|---|---|---|---|
| POS (caisse) | Tout | Tout | Vente, demande d'annulation | Vente, encaissement, demande d'annulation | — | — | Lecture |
| CATALOG | Tout | Tout sauf fiscalité | Lecture | Lecture | Créer, modifier (sans validation réglementaire) | Lecture des prix | Lecture |
| STOCK | Tout | Tout | Lecture, comptage | Lecture | Tout sauf décisions réglementées | Valorisation | Lecture |
| SERVICES | Tout | Réaliser, lire | Réaliser (si habilité) | — | — | Lecture facturation | Lecture agrégée |
| PRESCRIPTIONS (M17) | Tout | Tout | Selon EXT03 §5.2 | Contexte de vente | — | — | Journal |
| PAYERS (M18) | Tout | Selon EXT04 §5.2 | Couvertures (vente) | Lecture en vente | — | Créances, règlements | Lecture |
| FINANCE | Tout | Lecture | — | — | Factures marchandises | Tout sauf paie individuelle et validations réservées | Lecture |
| ACCOUNTING | Tout | — | — | — | — | Tout sauf réouverture | Lecture |
| ANALYTICS | Tout | Commercial | — | — | Commercial (sans marges, D79) | Financier | Lecture |
| SETTINGS / gouvernance | **Seul** | — | — | — | — | — | Lecture du journal |

« Tout » ne lève jamais les contrôles réglementaires ni la séparation des tâches.
**[v1.1]** Les permissions de gouvernance (`governance.*` marquées « administrateur uniquement ») ne sont pas des permissions
de rôle : elles sont évaluées à partir de `tenant_administrators` à l'instant de la requête. Un transfert d'administrateur
les retire immédiatement à l'ancien titulaire.

---

## 7. Dépendances

| Élément d'EXT06 | Dépend de | Est utilisé par |
|---|---|---|
| Gouvernance (administrateur, matrice, dérogations) | EXT02 (droits effectifs, qualifications), EXT05 (délégations, overrides) | Tous les modules (contrôle des droits) |
| Annulations amendées | EXT05 (moteur M19), M05 (buckets), gouvernance (dérogations) | M02, M08, EXT03, EXT04 |
| Catalogue enrichi, classification, fiscalité | Référentiel APEX (dossier §4.3), sources officielles (R42–R44) | M02, M03, M05, M11, M12, EXT03, EXT04 (listes de référence), M20 (TVA), recherche universelle |
| Substitution unifiée | Catalogue, validation pharmacien | EXT03 (qualification des substitutions), caisse |
| Lots et états du stock | M03 (réceptions), M05 | FEFO, M10, M11, EXT05, valorisation, M20 |
| Services | Catalogue (type SERVICE), EXT02 (habilitations), M02 (vente), M05 (consommables), EXT03 (`sensitive_access_log`) | M12, tableaux de bord, M20 |
| Finance étendue | M03, M06, M08, EXT02 (variables RH), EXT04 (créances) | M20, tableaux de bord, IA (agrégats) |
| M20 Comptabilité | Événements de tous les modules, `posting_rules`, plan de comptes validé (R46) | Exports comptables, tableau de bord financier, contrôles |
| Tableaux de bord | Couche métriques, `rx_facts`, projections EXT04, `financial_snapshots` | Titulaire, comptable, IA |
| Accueil et recherche universelle | Catalogue, index FTS, matrice des droits, M17, M18 | Tous les utilisateurs |
| **[v1.1]** Validation à distance (W4-D) | `tenant_administrators`, MFA (`users.mfa_secret`, dossier Lot 1), `notifications`, `devices` (appareil enrôlé), `audit_log` | W4, annulations (W5), plafonds de toutes les opérations |
| **[v1.1]** Option « jour d'exploitation » | `settings` (dossier), `duty_periods` (EXT02), gouvernance | M19 (annulations), écran d'annulation |
| **[v1.1]** Écritures M20 (immutabilité, idempotence) | Outbox `domain_events` (dossier §3.2), `document_sequences`, `posting_rules` versionnées, `accounting_periods`, déclencheurs SQLite, `D1 batch` | Grand livre, balance, exports, exceptions |
| **[v1.1]** Exceptions comptables | Générateur M20, rapprochement bancaire, réconciliations (tiers, caisse, stock, métriques), `notifications` | Clôture (W13), tableau de bord financier, contrôle de l'expert-comptable |
| **[v1.1]** Couche métriques technique | `metric_definitions`, outbox, registres sources (ventes, corrections EXT05, paiements, stock, pièces), `tenants.fuseau` | M01, G23, G24, G25, M12, IA, `financial_snapshots`, contrôles MT10 |
| **[v1.1]** Analyse de rentabilité | Couche métriques, `lots`, `stock_levels`, `product_demand_stats`, classification (nœud principal), `brands`, `ref_laboratories` | G25, IA (recommandations) |
| **[v1.1]** Compatibilité multi-officines | `tenants`, `memberships`, `sessions` (dossier), filtrage `tenant_id` | Vue consolidée future (hors EXT06) |

**Dépendances techniques à confirmer** : disponibilité de FTS5 dans D1 pour l'index de recherche (à prototyper au Lot 0,
avec une solution de repli par tables de trigrammes si nécessaire) ; volume des écritures comptables (une à plusieurs
écritures par vente : à mesurer avec la volumétrie cible du dossier §2.3).
**[v1.1]** À prototyper également au Lot 0 : (1) l'insertion d'en-tête « scellée » par `INSERT … SELECT … HAVING` et l'échec
contrôlé du batch par déclencheur `RAISE(ABORT)` dans D1 (CP8) ; (2) les déclencheurs de refus d'UPDATE / DELETE et de
transitions contrôlées ; (3) le recalcul complet d'un jour de métriques dans la limite de temps CPU d'un Worker (sinon,
découpage par dimension). Ces trois points conditionnent l'architecture de M20 et de la couche métriques.

---

## 8. Impacts sur les modules existants

Toutes les modifications ci-dessous sont **proposées et non appliquées**.

### 8.1 Dossier principal (CONCEPTION_PHARMOS_v1.md v1.1)

| Section | Impact |
|---|---|
| §2.2 M04 Catalogue | Classification multi-axes, fiscalité par catégorie, substitution unifiée, variantes, types étendus, services |
| §2.2 M08 | Devient « PHARM'OS FINANCE » : charges, emprunts, banque, immobilisations, paie importée |
| §2.2 (nouveau) M20 | Comptabilité (pré-comptabilité, journaux, grand livre, balance, périodes, exports) |
| §3.2 Cartographie | Ajout de M20 et de l'accueil opérationnel ; liens catalogue → recherche universelle |
| §3.3 Arborescence | `/home`, `/services`, `/finance/*`, `/accounting/*`, `/analytics/finance`, `/analytics/sales`, `/settings/governance`, `/settings/permissions` |
| §4.2 | `tenant_administrators`, `app_modules`, `permission_exceptions` |
| §4.3 | `categories` remplacée ; `products` et `ref_products` complétés ; `product_prices_history` remplacée par `product_prices` ; nouvelles tables §4.2–4.5 d'EXT06 |
| §4.4 | `lots` complété ; `stock_levels` avec buckets (sans `RESERVE`) |
| §4.6–4.8 | `supplier_invoices` complété ; `expenses` restreint ; nouvelles tables financières §4.8 |
| §4.11 | `metrics_daily` étendu ; `financial_snapshots` ; **[v1.1]** `metrics_daily.cle` remplacée par `metric_code` + `metric_version` + `dimension_type` / `dimension_id`, clé unique ; `metric_definitions` |
| §4.2 **[v1.1]** | `settings` : clés `cancellation.reference_day`, `remote_approval_ttl` (aucune table) ; `sites` réservé aux dépôts et annexes d'une officine (précision de D15) |
| §3.2 **[v1.1]** | Outbox : le générateur M20 et le projecteur de métriques sont des consommateurs idempotents |
| §6 **[v1.1]** | Contrôle automatique en CI du filtre `tenant_id` sur les requêtes métier (MO10) |
| §7 IA | Les analyseurs financiers n'utilisent que des agrégats ; aucune donnée de paie individuelle |
| §9.1 Rôles | Notion de pharmacien administrateur ; matrice modules × actions |
| §10 Plan | Voir §12 |

### 8.2 Extensions

| Extension | Impact d'EXT06 |
|---|---|
| **EXT02** (intégrée) | Catalogue `permissions` complété (`module_code`, `action`) ; le titulaire devient « pharmacien administrateur » unique ; **[v1.1]** `duty_periods` lue (sans modification) par l'option « jour d'exploitation » ; aucune autre modification |
| **EXT03 v1.1** | `products.substitution_group_id` et `ref_products.generic_group_code` remplacés par `substitution_groups` ; liste initiale des spécialités ; accès direct depuis l'accueil ; **[v1.1]** dimensions `prescriber`, `specialty`, `sale_mode` reprises comme `dimension_type` de `metrics_daily` ; `rx_facts` reste la source des indicateurs d'ordonnances (MT11) ; tests MOF complètent SEC-TEN-01 |
| **EXT04** | Accès direct depuis l'accueil ; listes de référence (`reference_list_items`) rapprochées du catalogue enrichi ; écritures de créances générées par M20 ; **[v1.1]** les dimensions `payer`, `payer_mode` et les mesures `ca_part_organismes`, `encours_organismes` deviennent des entrées de `metric_definitions` (`dimension_type` `PAYER`, `PAYER_MODE`) ; le règlement d'organisme alimente le CA encaissé, jamais le CA ; aucune table modifiée |
| **EXT05** | **Amendements** : **[v1.1]** `corrective_operations` complétée (`reference_day_rule`, `reference_day_setting_version`, AN13) ; D61 (15 min) remplacé par la règle du jour calendaire (**[v1.1]** avec option « jour d'exploitation » activable par l'administrateur, D75) ; les validations `approval_method = DEMANDE_A_DISTANCE` d'EXT05 reprennent les **garanties techniques** de W4-D (MFA au moment de la décision, confirmation explicite, motif, empreinte de l'opération, usage unique) ; le **décideur** reste celui prévu par EXT05 (pharmacien valideur), sauf pour les dérogations de plafond et de délai, réservées à l'administrateur ; `DISPONIBLE_DIRECT` supprimé ou remplacé (D74) ; bucket `RESERVE` retiré ; `delegation.manage` remplacé par `governance.delegations.manage` (administrateur seul, D66 tranché) ; `delegations.valid_from` obligatoire ; liste d'opérations structurée |

### 8.3 Points de compatibilité

- La règle du jour calendaire (EXT06) et la neutralisation du droit à délivrance au retour (EXT03 D42) sont indépendantes :
  la première détermine **qui** peut annuler, la seconde **l'effet** sur l'ordonnance.
- Le tiers payant (EXT04) ne change pas les définitions de CA et de marge : le CA inclut la part organisme, la créance est
  suivie à part (§5.6).
- Les écritures comptables M20 ne remplacent ni `party_ledger` (grand livre des tiers), ni les registres métier : elles en
  sont **dérivées**. En cas d'écart, ce sont les registres métier qui font foi. **[v1.1]** La v1.0 indiquait que
  « l'écriture est régénérée » : c'est corrigé. Une écriture validée n'est jamais régénérée ni remplacée ; l'écart ouvre une
  exception comptable, résolue par contre-passation et nouvelle écriture (CP2, W14).
- **[v1.1]** Le jour de référence des annulations (calendaire ou exploitation) n'a aucun effet sur le rattachement des
  métriques et des écritures, qui suivent la date de l'opération (MT1, MT6).

---

## 9. Exigences réglementaires

Numérotation à la suite d'EXT05 (R34 à R41). Tant qu'un point n'est pas confirmé, la valeur la plus prudente s'applique.

| # | Sujet | Hypothèse de conception | Question précise | Interlocuteurs | Échéance |
|---|---|---|---|---|---|
| R42 | Catégories réglementaires de produits | Types `MEDICAMENT`, `DISPOSITIF_MEDICAL`, `ACCESSOIRE`, `PARAPHARMACIE`, `COSMETIQUE`, `COMPLEMENT_ALIMENTAIRE`… | Quelles catégories ont des obligations distinctes (traçabilité des dispositifs, matériovigilance, numéros de série, cosmétovigilance) ? | DPM, ministère de la Santé | Avant le lot 2 |
| R43 | Référentiel de classification thérapeutique et statut de commercialisation | Axe paramétrable ; ATC si licence et source compatibles | Existe-t-il un référentiel officiel tunisien (classes thérapeutiques, statuts AMM, arrêts de commercialisation) exploitable ? Licence d'utilisation de l'ATC ? | DPM, PCT | Avant le lot 2 |
| R44 | TVA et fiscalité des produits et des services | Catégories fiscales historisées, aucun taux par défaut | Taux applicables par catégorie (médicaments, dispositifs, parapharmacie, prestations) ; droit de timbre ; facture électronique | Expert-comptable, administration fiscale | Avant le lot 2 |
| R45 | Immobilisations et amortissements | Registre ; durées et méthodes paramétrables | Durées et méthodes admises ; seuil d'immobilisation | Expert-comptable | Avant le lot 7c |
| R46 | Pré-comptabilité et plan de comptes | Écritures générées, validées et exportées ; résultats indicatifs ; **[v1.1]** écritures immuables, corrections par contre-passation, numérotation continue par journal | Plan de comptes applicable (système comptable des entreprises), mentions des journaux, conditions d'acceptation d'une pré-comptabilité tenue par un logiciel, conservation des pièces ; **[v1.1]** exigences d'inaltérabilité et de continuité de numérotation ; acceptabilité des notions de gestion « marge réalisée » et « résultat de gestion » ; durée de conservation des exceptions comptables | Expert-comptable, Ordre des experts-comptables | Avant le lot 7c |
| R47 | Services pharmaceutiques autorisés en officine | Aucun service actif par défaut ; activation par l'administrateur | Quelles prestations (mesure de tension, injections, premiers soins, dépistages, conseils) sont autorisées en officine, par quel personnel, avec quelles traces et quelles tarifications ? | Ordre des pharmaciens, ministère de la Santé | Avant le lot 9c |
| R48 | Responsabilité du pharmacien administrateur et délégation | Administrateur unique, qualification de pharmacien ; **[v1.1]** validation à distance avec MFA, exclue pour les réglementés | Le responsable légal (titulaire) peut-il désigner un autre pharmacien comme administrateur du système ? Limites de la délégation d'actes administratifs et financiers ; **[v1.1]** une autorisation donnée à distance (hors de l'officine) est-elle admise pour une opération de caisse ou de stock, et avec quelle preuve ? | Ordre, juriste | Avant le lot 1 |
| R49 | Délai d'annulation d'une vente | Jour calendaire pour les produits non sensibles ; **[v1.1]** option « jour d'exploitation » pour les gardes, activée par l'administrateur | Existe-t-il des règles fiscales sur l'annulation d'un ticket ou d'une facture (délai, mention, numérotation, caisse enregistreuse) ? **[v1.1]** Une annulation après minuit pendant une garde de nuit, rattachée au jour d'exploitation, est-elle admise ? Obligations de clôture journalière de caisse | Administration fiscale, expert-comptable | Avant le lot 5 |
| R50 | Données de paie et données personnelles | Import des montants, accès restreint N4 ; **[v1.1]** vue consolidée future sans données nominatives | Conditions de traitement et de conservation des données de paie ; déclaration INPDP ; **[v1.1]** conditions d'une consultation consolidée d'agrégats de plusieurs officines par leur propriétaire | INPDP, juriste | Avant le lot 7c |
| R51 | Prix réglementés et marges | Prix public réglementé importé et non modifiable pour les médicaments concernés | Source officielle des prix, règles de marge, affichage des prix | PCT, ministère du Commerce | Avant le lot 2 |

---

## 10. Matrice de tests

### 10.1 Gouvernance (S-GOV)

| ID | Scénario | Résultat attendu |
|---|---|---|
| GOV-01 | Deux administrateurs actifs pour une même officine | Refus (unicité) |
| GOV-02 | Désignation d'un administrateur sans qualification de pharmacien vérifiée | Refus |
| GOV-03 | Transfert d'administrateur : sortant initie, entrant accepte avec MFA | Effet à la date ; ancien administrateur sans droits de gouvernance ; délégations existantes conservées et révocables |
| GOV-04 | Un adjoint tente de créer une délégation (interface et API directe) | 403 ; tentative journalisée |
| GOV-05 | Délégation sans date de début | Refus |
| GOV-06 | Délégation d'une opération réglementée ou de la gestion des droits | Refus |
| GOV-07 | Redélégation par un délégataire | Refus |
| GOV-08 | Révocation pendant qu'une opération du délégataire est en cours | L'opération est recontrôlée dans la transaction et échoue ; les opérations antérieures restent valides |
| GOV-09 | Accès au module sans action `CREATE` : appel API de création | 403 |
| GOV-10 | Module coupé pour un collaborateur ayant une délégation sur ce module | Toutes les actions refusées |
| GOV-11 | Dépassement de plafond : demande, dérogation accordée, exécution | Dérogation `CONSOMMEE` ; une seconde opération identique exige une nouvelle dérogation |
| GOV-12 | Dérogation accordée par un non-administrateur | Refus |
| GOV-13 | `ARCHIVE` sur un produit ayant des mouvements, ou sur une vente | Refus (liste blanche) |
| GOV-14 | Historique : toutes les créations, modifications, révocations et utilisations d'une délégation | Présentes dans l'audit, chaîne de hachage intacte |
| GOV-15 | Explication du droit effectif dans la matrice (G3) | Raison affichée identique à la décision du serveur |
| GOV-16 **[v1.1]** | Validation à distance sans MFA (session déjà ouverte seulement) | Refus ; demande restée `DEMANDEE` |
| GOV-17 | Validation à distance sans motif de décision | Refus |
| GOV-18 | Validation à distance sans confirmation explicite (case non cochée, appel API sans champ) | Refus |
| GOV-19 | Autorisation utilisée deux fois (rejeu, double clic, deux postes) | Une seule exécution ; la seconde est rejetée et journalisée |
| GOV-20 | Autorisation expirée | Exécution refusée ; statut `EXPIREE` |
| GOV-21 | Opération modifiée (montant, ligne) après la demande | Empreinte différente : exécution refusée, nouvelle demande exigée |
| GOV-22 | Décision par un adjoint titulaire d'une délégation ou d'un réglage individuel | Refus (permission non attribuable) |
| GOV-23 | Administrateur demandeur et décideur de sa propre dérogation | Refus |
| GOV-24 | Transfert d'administrateur entre la décision et l'exécution | Autorisation `REVOQUEE`, exécution refusée |
| GOV-25 | Validation à distance pour un produit réglementé | Non proposée ; circuit EXT05 §4.7 obligatoire |
| GOV-26 | Journal d'audit d'une validation à distance | Demande, consultation, décision, MFA, consommation présents ; aucun secret journalisé ; chaîne intacte |

### 10.2 Annulations amendées (S-ANN)

| ID | Scénario | Résultat attendu |
|---|---|---|
| ANN-01 | Vente non validée abandonnée | Aucune écriture corrective, journalisé |
| ANN-02 | Produit non sensible, annulation le jour même à 22 h par un collaborateur habilité | Exécutée dans ses plafonds ; produit en `EN_CONTROLE` |
| ANN-03 | Même vente, annulation le lendemain à 8 h | Bloquée ; dérogation de l'administrateur obligatoire |
| ANN-04 | Vente validée à 23 h 58, annulation à 00 h 02 | Hors jour calendaire → dérogation (sauf option D75) |
| ANN-05 | Horloge du poste décalée de 3 h | Le jour est déterminé par l'horodatage serveur et le fuseau de l'officine |
| ANN-06 | Produit sensible (chaîne du froid) annulé le jour même par un préparateur | Circuit spécifique ; pas de réintégration directe |
| ANN-07 | Produit réglementé | Séquence EXT05 §4.7 (quatre yeux, registre) |
| ANN-08 | Annulation puis décision de contrôle | Réintégration, quarantaine ou perte ; jamais automatique |
| ANN-09 | Droits d'annulation accordés par un adjoint | Refus (administrateur seul) |
| ANN-10 **[v1.1]** | Officine nouvellement créée | Jour calendaire actif par défaut |
| ANN-11 | Activation de l'option « jour d'exploitation » par un adjoint, par import ou par mise à jour du référentiel | Refus ; paramètre inchangé |
| ANN-12 | Activation par l'administrateur avec effet rétroactif | Refus ; date d'effet au prochain jour de référence au plus tôt |
| ANN-13 | Variante GARDES : vente à 23 h 30 pendant une garde (fin 8 h), annulation à 2 h | Autorisée sans dérogation, dans les plafonds ; règle appliquée enregistrée avec l'opération |
| ANN-14 | Variante GARDES : même cas hors période de garde | Jour calendaire : dérogation exigée |
| ANN-15 | Option active, produit sensible ou réglementé | Circuit spécifique inchangé ; aucune réintégration automatique |
| ANN-16 | Changement du paramètre | Audit avant / après, notification aux collaborateurs concernés, bandeau dans les paramètres |
| ANN-17 | Annulation à 2 h pendant une garde | Métriques et écritures rattachées au jour réel de l'exécution (MT1) |

### 10.3 Catalogue et stock (S-CAT)

| ID | Scénario | Résultat attendu |
|---|---|---|
| CAT-01 | Création d'un médicament local sans DCI, dosage ou forme | Refus (champs obligatoires par type) |
| CAT-02 | Médicament créé, non validé par un pharmacien | Non vendable (`A_VALIDER`) |
| CAT-03 | Association de deux molécules | Deux lignes `product_substances` ; recherche par chacune des DCI |
| CAT-04 | Produit classé dans trois familles commerciales et une famille thérapeutique | Recherche par facettes correcte ; un seul nœud principal par axe |
| CAT-05 | Changement de taux de TVA au 1er du mois | Ventes antérieures inchangées ; nouvelles ventes au nouveau taux figé |
| CAT-06 | Variantes de chaussures orthopédiques (pointures 38 à 44) | Un produit par variante, stock et code-barres propres, regroupés sous le modèle |
| CAT-07 | Groupe de substitution non validé | Non utilisé par la caisse ni par EXT03 |
| CAT-08 | Équivalent informatif (même DCI, autre dosage) | Affiché, jamais proposé comme substitution |
| CAT-09 | Doublon de code-barres | Refus |
| CAT-10 | FEFO avec un lot `EN_CONTROLE`, un lot `RAPPELE` et une réservation | Seul le lot disponible et non réservé le plus proche de la péremption est alloué |
| CAT-11 | Contrôle reçue = disponible + contrôle + quarantaine + perte + sorties nettes | Égalité sur un jeu de référence |
| CAT-12 | Produit `ARRETE` avec stock | Vendable sur stock, exclu des propositions de commande |
| CAT-13 | Recherche universelle : nom en arabe, DCI avec faute, code-barres, laboratoire | Résultats pertinents < 150 ms ; aucun médecin renvoyé à un utilisateur sans droit |

### 10.4 Services (S-SRV)

| ID | Scénario | Résultat attendu |
|---|---|---|
| SRV-01 | Activation d'un service exigeant une autorisation absente ou expirée | Refus |
| SRV-02 | Autorisation expirée pendant la période | Service désactivé, non proposé |
| SRV-03 | Réalisation par un collaborateur non habilité | Refus |
| SRV-04 | Service facturé avec consommables | Ligne de vente au tarif, sortie de stock des consommables par lot, acte enregistré |
| SRV-05 | Service gratuit | Acte au registre, aucune ligne de vente |
| SRV-06 | Annulation d'un acte facturé | Acte d'annulation lié ; correction de la vente selon EXT05 |
| SRV-07 | Données de santé de l'acte | Chiffrées, consultation journalisée, absentes des charges utiles de l'IA |

### 10.5 Finance et comptabilité (S-FIN)

| ID | Scénario | Résultat attendu |
|---|---|---|
| FIN-01 | Facture de charge sans pièce justificative | Refus de validation |
| FIN-02 | Facture de marchandises rapprochée d'une réception | Dette fournisseur et coût du stock cohérents |
| FIN-03 | Avoir fournisseur imputé sur une facture | Solde fournisseur exact |
| FIN-04 | Acompte versé puis imputé | Solde de l'acompte et de la facture exacts |
| FIN-05 | Charges récurrentes | Brouillons générés, aucune charge validée automatiquement |
| FIN-06 | Échéance d'emprunt payée | Capital en diminution de dette, intérêts en charge financière |
| FIN-07 | Rapprochement bancaire d'un règlement d'organisme | Lettrage de la créance (EXT04) ; aucune vente créée |
| FIN-08 | Import de paie avec un collaborateur inconnu | Écart signalé, import non validable |
| FIN-09 | Définitions : CA, marge, créances, dettes, encaissements, décaissements, trésorerie | Valeurs identiques au jeu calculé à la main |
| FIN-10 | Règlement CNAM de 70 sur une vente tiers payant | CA inchangé ; encaissement +70 ; créance −70 |
| FIN-11 | Écritures générées pour une vente, une annulation, un retour, une facture, un règlement | Débit = crédit ; comptes conformes aux règles de passation ; contre-passation pour les corrections |
| FIN-12 | Période verrouillée : nouvelle correction | Écriture datée dans la période ouverte suivante |
| FIN-13 | Réouverture d'une période exportée | Refus |
| FIN-14 | Balance et grand livre recalculés | Égalité avec Σ des écritures ; tiers M20 = `party_ledger` |
| FIN-15 | CA tableau de bord commercial = financier = journal des ventes | Égalité quotidienne et mensuelle |
| FIN-16 | Période close : tableau de bord | Affiche le cliché figé, identique à la clôture |
| FIN-17 | Comptable : accès aux montants de paie individuels | Refusé (agrégats seulement) |

**Compléments S-FIN [v1.1]** :

| ID | Scénario | Résultat attendu |
|---|---|---|
| FIN-18 | Tableau de bord financier : CA, CA encaissé, marge commerciale, marge réalisée, résultat de gestion sur un même mois | Cinq valeurs distinctes, conformes au §5.6.1 et au jeu calculé à la main |
| FIN-19 | Clôture mensuelle avec un événement de la période sans écriture ni exception | Clôture refusée ; événement listé |
| FIN-20 | Écart `party_ledger` ↔ comptes de tiers M20 | Exception `ECART_TIERS` ; aucune écriture modifiée ni régénérée |
### 10.6 Accueil et navigation (S-NAV)

| ID | Scénario | Résultat attendu |
|---|---|---|
| NAV-01 | Tuiles affichées pour un caissier | Seulement les modules autorisés |
| NAV-02 | Utilisation tactile et scanner sans souris | Toutes les actions de l'accueil sont accessibles |
| NAV-03 | Accueil en arabe (RTL) | Disposition inversée, libellés traduits |
| NAV-04 | Accès direct Médecins / Organismes | Ouvre les écrans d'EXT03 / EXT04, filtrés ; aucune donnée dupliquée |

---

### 10.7 M20 — Immutabilité, idempotence et atomicité (S-ACC) **[v1.1]**

| ID | Scénario | Résultat attendu |
|---|---|---|
| ACC-01 | Modification ou suppression d'une écriture validée (API, puis requête SQL directe) | Refus par déclencheur ; aucune route d'API ; tentative journalisée |
| ACC-02 | Écriture manuelle déséquilibrée d'un millime | Batch entièrement annulé : aucune ligne, aucun en-tête, aucun numéro consommé |
| ACC-03 | Échec au milieu du batch (compte inactif sur la 3e ligne) | Rien n'est écrit ; brouillon resté `SOUMIS` ; message explicite |
| ACC-04 | Même événement, même version de règle, génération demandée deux fois | Une seule écriture (`generation_key` unique) |
| ACC-05 | Rejeu du même événement 10 fois | Une seule écriture ; événement marqué traité |
| ACC-06 | Deux traitements concurrents du même événement | Une seule écriture ; le second constate « déjà fait », sans erreur ni exception |
| ACC-07 | Coupure après l'écriture et avant le marquage de l'événement, puis relance | Aucun doublon |
| ACC-08 | Ajout d'une ligne à une écriture déjà scellée | Refus par déclencheur |
| ACC-09 | Export avec une ligne dont l'empreinte ne correspond plus (intégrité simulée en test) | Export refusé ; exception `INTEGRITE_ECRITURE` BLOQUANTE |
| ACC-10 | Export dont les totaux ou le nombre d'écritures diffèrent de la base | Export refusé |
| ACC-11 | Correction d'une écriture | Contre-passation exacte + nouvelle écriture, même `correlation_id` ; l'écriture d'origine reste visible et inchangée |
| ACC-12 | Deuxième contre-passation de la même écriture | Refus (UNIQUE `reverses_id`) |
| ACC-13 | Contre-passation d'une contre-passation | Refus |
| ACC-14 | Modification d'un brouillon `SOUMIS`, puis d'un brouillon `EN_COURS` avec une version périmée | Refus ; conflit de version |
| ACC-15 | Brouillon validé par son auteur | Refus (valideur ≠ auteur) |
| ACC-16 | Brouillons dans le grand livre, la balance, les exports et les métriques | Absents |
| ACC-17 | Nouvelle version d'une règle de passation | Événements antérieurs à `valid_from` inchangés ; reprise d'un événement déjà comptabilisé seulement par contre-passation puis reprise ; jamais deux écritures actives pour un même événement |
| ACC-18 | Batch annulé puis écriture suivante | Numérotation continue, sans trou ni réutilisation |
| ACC-19 | Écriture datée dans une période `VERROUILLEE` | Refus ; correction datée dans la première période ouverte |

### 10.8 M20 — Exceptions comptables (S-EXC) **[v1.1]**

| ID | Scénario | Résultat attendu |
|---|---|---|
| EXC-01 | Vente validée dont l'événement n'a pas de règle de passation active | Vente validée et inchangée ; exception `REGLE_ABSENTE` avec événement d'origine, montant, responsable, échéance |
| EXC-02 | Même anomalie détectée trois fois | Une seule exception ouverte, 3 occurrences |
| EXC-03 | Résolution par correction du paramétrage puis régénération | Exactement une écriture, liée à l'exception |
| EXC-04 | Résolution par écriture de régularisation | Brouillon validé à quatre yeux, lié à l'exception |
| EXC-05 | Clôture d'une exception BLOQUANTE par la personne qui l'a résolue | Refus |
| EXC-06 | Clôture de période avec une exception BLOQUANTE ouverte | Refus, liste des exceptions affichée |
| EXC-07 | Échéance dépassée | Escalade à l'administrateur, notification |
| EXC-08 | Écart de rapprochement bancaire de 12,500 TND | Exception `ECART_RAPPROCHEMENT_BANCAIRE`, montant d'écart 12 500 millimes |
| EXC-09 | `ECART_JUSTIFIE` sans justification | Refus |
| EXC-10 | Tentative de suppression ; nouvelle détection après clôture | Suppression impossible ; exception `REOUVERTE` |
| EXC-11 | Contenu de `details` pour un acte de service ou une ligne de paie | Références uniquement, aucune donnée de santé ni montant individuel de paie |

### 10.9 Couche métriques (S-MET) **[v1.1]**

| ID | Scénario | Résultat attendu |
|---|---|---|
| MET-01 | Double écriture d'une même clé | Une seule ligne (UPSERT sur la clé unique) |
| MET-02 | Rejeu d'un événement | Valeurs identiques (recalcul complet du jour) |
| MET-03 | Vente du 10, annulée le 11 | CA du 10 inchangé ; CA du 11 diminué ; `ca_annulations_ht` du 11 alimenté ; concordance jour par jour avec le journal des ventes |
| MET-04 | Σ des dimensions additives | = `TOTAL` ; `CLASSIFICATION_NODE` signalée non additive ; `CLASSIFICATION_PRINCIPAL` additive |
| MET-05 | Vente tiers payant le 28 mars, règlement CNAM le 15 avril | CA en mars ; CA encaissé de la part organisme en avril ; aucune vente en avril |
| MET-06 | Marge réalisée | = marge commerciale − démarque − pertes sur créances + ristournes non affectées (jeu calculé à la main) |
| MET-07 | Service facturé | Compté une seule fois dans le CA (pas de double comptage dans le résultat de gestion) |
| MET-08 | Recalcul d'une période verrouillée | Cliché inchangé ; écart éventuel → exception `ECART_METRIQUES_JOURNAL` |
| MET-09 | Nouvelle version de définition | Calculée en parallèle ; ancienne version consultable ; clichés inchangés |
| MET-10 | Taux de marge mensuel | = Σ marge / Σ CA HT, jamais la moyenne des taux journaliers |
| MET-11 | Même indicateur sur G23, G24, G25, M12 et dans un export | Valeurs identiques (même code, même version) |
| MET-12 | Valeur du stock sur un mois | Lue à une date, jamais additionnée sur les jours |

### 10.10 Multi-officines et isolation (S-MOF) **[v1.1]**

| ID | Scénario | Résultat attendu |
|---|---|---|
| MOF-01 | Utilisateur de l'officine B avec des identifiants de l'officine A (écritures, exceptions, dérogations, brouillons, métriques, actes de service) | 404, aucune fuite (complète SEC-TEN-01 d'EXT03 v1.1) |
| MOF-02 | Personne membre des officines A et B | Une seule officine par session ; changement = nouvelle session ; aucun droit hérité |
| MOF-03 | Administrateur de A tentant une décision à distance pour B | Refus |
| MOF-04 | Délégation ou dérogation référençant un collaborateur d'une autre officine | Refus |
| MOF-05 | Séquences de documents et d'écritures | Indépendantes par officine |
| MOF-06 | `metric_definitions` (partagé) et `metrics_daily` (par officine) | Lecture seule du référentiel ; valeurs isolées |
| MOF-07 | Requête métier sans filtre `tenant_id` | Détectée par le contrôle automatique de la CI ; build en échec |

### 10.11 Rentabilité commerciale (S-RNT) **[v1.1]**

| ID | Scénario | Résultat attendu |
|---|---|---|
| RNT-01 | Produits à forte et à faible marge selon les seuils de l'officine | Classement conforme au jeu de référence |
| RNT-02 | Médicament à prix réglementé à faible marge | Signalé « prix réglementé », jamais proposé comme levier de prix |
| RNT-03 | Rotation et jours de couverture | Conformes au calcul manuel |
| RNT-04 | Capital immobilisé | = valeur au coût des stocks disponible, en contrôle et en quarantaine à la date |
| RNT-05 | Risque de péremption à 60 jours | Valeur des lots expirant dans 60 jours, diminuée de la consommation prévisible |
| RNT-06 | Rentabilité par famille | Nœud principal ; Σ des familles = total |
| RNT-07 | Performance par laboratoire et par marque | Inclut démarque, avoirs et ristournes |
| RNT-08 | Utilisateur sans `analytics.margin.read` | Montants de marge masqués (interface et API) |
| RNT-09 | Valeurs affichées par G25 | Identiques à la couche métriques ; aucun calcul propre |

---

## 11. Décisions à arbitrer

Numérotation à la suite d'EXT05 (D59 à D71). **[v1.1]** Colonne « Statut » ajoutée : **RETENUE** = tranchée par la revue
du 2026-11-03 ; **PROPOSÉE** = recommandation en attente d'arbitrage. Aucun numéro n'a été ajouté ni réattribué.

| # | Question | Options | Recommandation | Statut |
|---|---|---|---|---|
| **D72** | Perte d'accès du pharmacien administrateur | Procédure de secours APEX sur justificatifs · Second administrateur de secours | **Procédure de secours APEX** (double contrôle, sans accès aux données) ; un administrateur suppléant affaiblirait la règle « un seul responsable » | PROPOSÉE |
| **D73** | Dérogations en l'absence de l'administrateur | Aucune · Validation à distance (smartphone, MFA) | **Validation à distance par l'administrateur, et par lui seul** : MFA au moment de la décision, confirmation explicite, motif obligatoire, opération identifiée par empreinte, autorisation à usage unique et expirante, journalisation complète, aucune substitution possible ; exclue pour les produits réglementés (W4-D, GV13) | **RETENUE [v1.1]** (R48 à confirmer pour la preuve à distance) |
| **D74** | Réintégration directe d'EXT05 (`DISPONIBLE_DIRECT`) | Supprimer · Remplacer par une décision immédiate par un second collaborateur | **Remplacer** par une décision immédiate tracée, avec check-list réduite | PROPOSÉE |
| **D75** | Définition du « jour » d'annulation | Jour calendaire (minuit) · Jour d'exploitation (heure de bascule paramétrable) | **Jour calendaire comme règle générale** ; option « jour d'exploitation » (variantes GARDES ou PERMANENTE) activée explicitement par l'administrateur, documentée et versionnée dans les paramètres de l'officine, jamais modifiée silencieusement ; exigences sur les produits sensibles et réglementés et absence de réintégration automatique inchangées (AN9 à AN13, W16) | **RETENUE [v1.1]** (R49 à confirmer) |
| **D76** | Bucket `RESERVE` d'EXT05 | Conserver · Retirer (réservation = engagement) | **Retirer** | PROPOSÉE |
| **D77** | Page affichée après connexion | Accueil · Tableau de bord · Selon le rôle | **Selon le rôle**, modifiable par l'utilisateur dans les limites de ses droits | PROPOSÉE |
| **D78** | Données saisies lors des services (mesures, observations) | Aucune · Mesures structurées chiffrées | Mesures structurées et facultatives, chiffrées, selon le service et après R47 | PROPOSÉE (après R47) |
| **D79** | Visibilité des marges | Tous les rôles du module · Permission dédiée | **Permission dédiée** `analytics.margin.read` | PROPOSÉE |
| **D80** | Profondeur du module comptable | A. Exports seulement (journaux) · B. Pré-comptabilité en partie double (M20) · C. Comptabilité générale complète | **B** : écritures générées et exportées, le comptable tient les comptes officiels | PROPOSÉE — **[v1.1]** l'option B est précisée par §5.13 et §5.14 |
| **D81** | Moteur de paie | Import des montants · Moteur intégré | **Import des montants** (sans moteur) tant que les règles ne sont pas validées | PROPOSÉE |
| **D82** | Réouverture d'une période comptable | Interdite · Administrateur avant export définitif | Administrateur avec motif, **interdite après export définitif** | PROPOSÉE |
| **D83** | Plan de comptes livré | Aucun · Modèle indicatif à valider | Modèle indicatif, **inactif tant que l'expert-comptable ne l'a pas validé** | PROPOSÉE |
| **D84** | Référentiel de classification thérapeutique | ATC · Référentiel local · Classification APEX | Selon R43 ; à défaut, classification APEX maintenue et sourcée | PROPOSÉE (après R43) |
| **D85** | Rapprochement bancaire automatique | Automatique · Proposé puis confirmé | **Proposé puis confirmé** (cohérent avec EXT04 D55) | PROPOSÉE |
| **D86** | Seuil de validation des charges par l'administrateur | Montant fixe · Toutes les charges | Montant paramétrable | PROPOSÉE |

**Points tranchés par la revue sans nouveau numéro de décision** (exigences du propriétaire, intégrées comme règles) :
immutabilité des écritures et cycle de vie des brouillons (CP1 à CP13) ; exceptions comptables (EX1 à EX11) ; définition
technique de la couche métriques (MT1 à MT11) ; compatibilité multi-officines (MO1 à MO10) ; analyse de rentabilité
commerciale (§5.12). Les seuils (rentabilité, échéances des exceptions, durée de validité d'une autorisation à distance)
sont des **paramètres de l'officine**, sans valeur réglementaire présumée.

---

## 12. Positionnement dans le plan de développement

EXT06 touche des fondations (droits, catalogue, stock) qui doivent être correctes **dès les premiers lots**, et des
fonctions avancées (comptabilité, tableaux de bord) qui viennent plus tard.

| Lot | Contenu EXT06 | Justification |
|---|---|---|
| **0. Socle** (ajout) | Prototype de l'index de recherche (FTS5 ou repli) ; règle de date serveur et fuseau ; **[v1.1]** prototypes D1 : en-tête scellé et échec contrôlé du batch, déclencheurs d'immutabilité et de transitions, recalcul d'un jour de métriques ; contrôle CI du filtre `tenant_id` ; jeu de tests d'isolation à deux officines | Risques techniques à lever tôt |
| **1. Plateforme** (ajout) | `tenant_administrators`, `app_modules`, catalogue `permissions` complété (module × action), coupure de module, `permission_exceptions`, matrice des droits (G3), désignation et transfert de l'administrateur ; **[v1.1]** MFA step-up, appareil enrôlé de l'administrateur, validation à distance (W4-D, G28), paramètre `cancellation.reference_day` (W16) | Le contrôle des droits conditionne tous les autres lots |
| **2. Catalogue** (ajout) | Types de produits, substances multiples, marques, unités de vente, variantes, classification multi-axes, catégories fiscales et taux, prix historisés, groupes de substitution, explorateur, recherche universelle, nœuds initiaux des familles | Remplacer `categories` et `tva_bp` avant toute donnée réelle |
| **3. Stock & lots** (ajout) | Lots complétés, buckets sans `RESERVE`, FEFO avec réservations | Structure de stock définitive |
| **5. Caisse & ventes** (ajout) | Règle du jour calendaire et **[v1.1]** option « jour d'exploitation » (lecture de `duty_periods`, disponible dès que le Lot 10b fournit les gardes ; sinon variante PERMANENTE seule), dérogation administrateur (présente ou à distance) pour les annulations ; **accueil opérationnel** (G1) | Exploitation quotidienne |
| **5d. Opérations correctives** (EXT05 amendé) | Suppression de `DISPONIBLE_DIRECT` (D74), décision immédiate tracée | — |
| **7. Finance** (ajout) | Nature des factures, catégories de charges, avoirs et acomptes fournisseurs, charges récurrentes, emprunts, rapprochement bancaire | Dettes et trésorerie fiables |
| **7c. Comptabilité & paie importée** (nouveau, après 7 et 7b) | M20 : plan de comptes, journaux, règles de passation **versionnées**, écritures **immuables** (CP1–CP13), **brouillons** (W15, G27), **exceptions comptables** (W14, G26), périodes et clôture complétée (W13), grand livre, balance, exports contrôlés ; immobilisations ; import de paie | Dépend de tous les événements financiers ; validation R45, R46, R50 ; **[v1.1]** prototypes du Lot 0 concluants |
| **9c. Services pharmaceutiques** (nouveau, après 5 et 10b) | Définitions, activation réglementaire, actes, consommables, facturation | Après R47 et les habilitations RH |
| **11. Métriques & rapports** (ajout) | `metric_definitions`, `metrics_daily` (clé unique, MT1–MT11), `financial_snapshots`, tableaux de bord financier (G23) et commercial (G24), **[v1.1]** analyse de rentabilité (G25) et contrôles de cohérence quotidiens | Couche métriques unique |
| **Hors plan [v1.1]** | Vue consolidée multi-officines : **non planifiée** ; nécessitera une extension dédiée (tables réservées MO9, R50) | Compatibilité préparée, aucun développement |
| **13. IA** (ajout) | Analyses financières et commerciales sur agrégats | Après 11 |

**Prérequis bloquants** :
- avant le lot 0 **[v1.1]** : aucune décision ; prototypes techniques (CP8, MT7) ;
- avant le lot 1 : R48 ; décision D72 (D73 retenue) ;
- avant le lot 2 : R42, R43, R44, R51 ; décisions D76, D84 ;
- avant le lot 5 : R49 ; décisions D74, D77 (D75 retenue) ;
- avant le lot 7c : R45, R46, R50 ; décisions D80, D81, D82, D83, D85, D86 ;
- avant le lot 9c : R47 ; décision D78 ;
- avant le lot 11 **[v1.1]** : D79 ; validation des définitions du §5.6.1 par l'expert-comptable (R46).

### Récapitulatif des tables EXT06

| Catégorie | Tables |
|---|---|
| Nouvelles — gouvernance, catalogue, fiscalité (17) | `tenant_administrators`, `app_modules`, `permission_exceptions`, `ref_product_substances`, `product_substances`, `brands`, `product_units`, `product_variant_attributes`, `product_type_fields`, `product_prices`, `substitution_groups`, `substitution_group_members`, `classification_axes`, `classification_nodes`, `product_classifications`, `tax_categories`, `tax_rates` |
| Nouvelles — services (3) | `service_definitions`, `service_acts`, `service_act_consumables` |
| Nouvelles — finance (12) | `supplier_credit_notes`, `supplier_advances`, `expense_categories`, `recurring_charge_templates`, `loans`, `loan_schedule_lines`, `fixed_assets`, `bank_statements`, `bank_statement_lines`, `bank_reconciliations`, `payroll_imports`, `payroll_import_lines` |
| Nouvelles — comptabilité M20 (9) | `chart_of_accounts`, `journals`, `posting_rules`, `accounting_periods`, **`journal_entry_drafts`** [v1.1], `journal_entries`, `journal_entry_lines`, **`accounting_exceptions`** [v1.1], `accounting_exports` |
| Nouvelles — métriques (2) | **`metric_definitions`** [v1.1], `financial_snapshots` |
| Complétées | `permissions`, `permission_overrides`, `delegations`, `ref_products`, `products`, `ref_laboratories`, `lots`, `stock_levels`, `supplier_invoices`, `expenses` (restreinte), `metrics_daily` (**[v1.1]** clé et dimensions redéfinies), **`corrective_operations`** (EXT05, **[v1.1]** règle de jour appliquée, AN13) |
| Utilisées sans modification de structure **[v1.1]** | `settings` (nouvelles clés), `duty_periods` (EXT02), `domain_events`, `document_sequences`, `notifications`, `devices`, `sessions`, `memberships`, `tenants`, `party_ledger` |
| Réservées, non créées **[v1.1]** | `organizations`, `organization_tenants`, `organization_access_grants` (MO9) — hors décompte |
| **Total des tables nouvelles** | **43** (v1.0 : 40) |
| Remplacées | `categories` (→ classification multi-axes), `product_prices_history` (→ `product_prices`), `products.tva_bp` (→ catégories fiscales), `products.substitution_group_id` et `ref_products.generic_group_code` d'EXT03 (→ `substitution_groups`), bucket `RESERVE` d'EXT05 |
| Doublons créés | **Aucun** |