# PHARM'OS — Extension fonctionnelle n° 06 : Gouvernance des droits, catalogue, services et finance

**Produit** : PHARM'OS — APEX AI DEV
**Version** : 1.0 (proposition) — 2026-11-03
**Statut** : **CONCEPTION TECHNIQUE — AUCUN CODE**. Les décisions D72–D86 sont arbitrées dans la matrice maître D1–D86 v1.2.1, mais les exigences réglementaires R42–R51 et les amendements PO postérieurs doivent encore être réconciliés. Le dossier principal (`../01_CONCEPTION/CONCEPTION_PHARMOS_v1 (1).md` v1.1) n'est pas modifié.
**Base de conception** : dossier principal v1.1, EXT02 (intégrée), EXT03 v1.1 (dossier d'audit), EXT04 et EXT05 (propositions).

> **Règle de ce document : aucun doublon.** EXT06 crée un seul module nouveau (**M20 Comptabilité**, partie de
> PHARM'OS FINANCE & ACCOUNTING) et une seule famille de tables nouvelle par besoin réellement absent. Tout le reste
> **réutilise, complète ou remplace explicitement** une table existante. Le §0 donne la correspondance complète.
> Repères : **[À VALIDER Rxx]** = point réglementaire (§9) · **[ARBITRAGE Dxx]** = décision du propriétaire (§11).

---

## 0. Analyse des chevauchements (à lire en premier)

| Demande EXT06 | Existant | Traitement EXT06 |
|---|---|---|
| Délégations nominatives, dates, plafonds, révocation, historique, pas de redélégation, réglementés exclus | EXT05 §5.3 (`delegations`, `delegation_usage`, `delegable_permissions`, règles G1–G10) | **Réutilisé.** EXT06 ajoute : (1) le **pharmacien administrateur** unique comme seul gestionnaire (proposition de gouvernance ; D66 est caduque dans la matrice maître et ne vaut pas arbitrage PO) ; (2) la date de début devient obligatoire, elle l'était déjà en fin ; (3) les dépassements de plafond réservés à l'administrateur |
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

**Bilan** : 1 module nouveau (M20) ; 40 tables nouvelles (17 gouvernance, catalogue et fiscalité ; 3 services ; 12 finance ; 7 comptabilité M20 ; 1 métriques — détail en fin de document) ; remplacements : `categories`, `product_prices_history`, champ `products.tva_bp`, champs de substitution d’EXT03, bucket `RESERVE` d’EXT05 ;
1 table restreinte (`expenses`) ; 0 table en double.

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
   des règles de passation paramétrables. L'expert-comptable reste responsable des comptes officiels.
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
Absence de l'administrateur : aucune dérogation possible ; l'opération attend (ou est refusée) [ARBITRAGE D73]
```

### 3.5 W5 — Annulation d'une vente (EXT05 workflow A, amendé)

```
Vente non validée                               → abandon (aucune écriture corrective) — inchangé
Vente validée, produit non remis :
  ├─ produit non sensible, même jour calendaire (fuseau de l'officine), dans les droits et plafonds
   │      → annulation par le collaborateur habilité ; produit en EN_CONTROLE
   │      → décision immédiate simplifiée par un second collaborateur uniquement si le pharmacien-administrateur
   │        lui a explicitement accordé le droit de réintégration corrective (D74)
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
   → type de service et champs éventuels définis/configurés par le pharmacien-administrateur ; aucune liste de services
     tunisienne n'est imposée par APEX ; les données éventuellement collectées restent limitées au service configuré,
     soumises à R47 et protégées selon leur sensibilité
   → notification : uniquement type d'opération/service, nom du client et date
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

### 3.13 W13 — Clôture mensuelle de pré-comptabilité

```
Contrôles automatiques : sessions de caisse clôturées et validées, bordereaux traités, rapprochements bancaires faits,
  écritures en attente, réconciliations (stock, créances, dettes, caisse) sans écart
  → génération des écritures manquantes → balance de contrôle (débit = crédit)
  → validation de la période par l'administrateur (ou comptable habilité) → période VERROUILLEE
   → export vers le logiciel comptable ; tous les écrans, états et exports comptables portent la mention
     « Comptabilité à titre indicatif »
  → correction après verrouillage : écriture de régularisation dans la période ouverte suivante ;
    réouverture exceptionnelle par l'administrateur avec motif, interdite après export définitif [ARBITRAGE D82]
```

---

## 4. Tables et relations

Conventions du dossier principal (§4.1). **N** = nouvelle table · **C** = table existante complétée · **R** = table remplacée.
Les tables **(immuable)** n'acceptent que des insertions (déclencheurs SQLite, comme EXT03 v1.1 §17.3).

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
| `permission_exceptions` | **N** (immuable) | id, tenant_id, type (`DEPASSEMENT_PLAFOND`, `DEROGATION_DELAI`), permission_code, demandeur_employee_id, operation_ref (type + id), valeur_demandee, plafond_applicable, motif, statut (`DEMANDEE`, `ACCORDEE`, `REFUSEE`, `EXPIREE`, `CONSOMMEE`), decide_par_employee_id (administrateur), methode (`PIN_PRESENT`, `A_DISTANCE`), decide_le, expire_le | Usage unique : `CONSOMMEE` dès l'exécution ; CHECK(decide_par = administrateur actif) |

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
| `stock_reservations` (dossier) | **C** | réservation logique au niveau produit (`product_id`, quantité, origine, expiration, statut), sans mouvement physique | Réservation bloquante ; allocation FEFO à la vente ; aucune bucket `RESERVE` ; dérogation ADMIN tracée et écarts FEFO tracés (DP-3/DP-4) |
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

### 4.9 Comptabilité (Partie 7E) — M20 nouveau

| Table | Colonnes principales | Remarques |
|---|---|---|
| `chart_of_accounts` | id, tenant_id, code, libelle, classe, type (`BILAN`, `GESTION`), lettrable (bool), tiers_type, actif, source (`MODELE`, `LOCAL`) | Modèle comptable standard APEX préconfiguré et adaptable par la pharmacie ; disponible sans validation préalable obligatoire d'un expert-comptable (D83) ; usage indicatif uniquement, sans remplacer les comptes officiels |
| `journals` | id, tenant_id, code (`VT` ventes, `AC` achats, `CA` caisse, `BQ` banque, `OD` opérations diverses, `TP` tiers payant facultatif), libelle, compte_contrepartie | — |
| `posting_rules` | id, tenant_id, evenement (`sale.completed`, `sale.voided`, `sale.returned`, `supplier_invoice.validated`, `payment.recorded`, `payer_remittance.allocated`, `claim.written_off`, `cash_session.closed`, `loss.recorded`, `payroll.validated`, `loan.instalment_paid`…), journal_id, lignes (JSON : compte ou règle de compte, sens, montant = expression sur l'événement), version, valid_from, valide_par | Paramétrable ; testé par jeu de référence ; versionné |
| `accounting_periods` | id, tenant_id, exercice, mois, statut (`OUVERTE`, `EN_CLOTURE`, `VERROUILLEE`, `EXPORTEE`), verrouillee_par, verrouillee_le | — |
| `journal_entries` (immuable) | id, tenant_id, journal_id, period_id, numero (séquence par journal et exercice), date, libelle, source_event_type, source_id, posting_rule_version, statut (`BROUILLON`, `VALIDEE`), reverses_id, correlation_id | CHECK(Σ débit = Σ crédit) vérifié à l'insertion des lignes ; aucune modification, contre-passation seulement |
| `journal_entry_lines` (immuable) | id, entry_id, account_id, tiers_type, tiers_id, debit, credit, libelle, lettrage_code, analytique (JSON : famille, univers) | INDEX(account_id, date) |
| `accounting_exports` | id, tenant_id, period_id, format (paramétrable : CSV, Excel, format du logiciel du comptable), fichier_key, sha256, exporte_par, exporte_le | Un export définitif fige la période |

Le **grand livre** et la **balance** sont des **vues** calculées sur `journal_entry_lines` (aucune table de soldes saisie).
Le **grand livre auxiliaire des tiers** reste `party_ledger`. Une réconciliation quotidienne vérifie qu'il concorde avec les comptes de tiers de M20.

### 4.10 Couche métriques (Partie 7F-G)

| Table | N/C/R | Ajouts |
|---|---|---|
| `metrics_daily` (dossier §4.11) | **C** | Dimensions : `product_type`, `classification_node`, `manufacturer`, `brand`, `tax_category`, `sale_mode` (EXT03), `payer` (EXT04), `payment_mode`, `service` ; mesures : `ca_ht`, `ca_ttc`, `cout_vendu`, `marge_brute`, `nb_lignes`, `quantite`, `ca_part_organismes`, `achats_ht`, `charges_par_categorie`, `encaissements`, `decaissements` |
| `financial_snapshots` | **N** | Clichés mensuels figés à la clôture de période (CA, marge, charges fixes et variables, résultat d'exploitation, résultat net, trésorerie, créances, dettes), pour comparer des périodes closes sans recalcul divergent |

### 4.11 Relations

```
tenant_administrators n─1 employees ; app_modules 1─n permissions ; permissions 1─n role_permissions, permission_overrides
delegations (EXT05) ─ operations ⊂ permissions ; permission_exceptions n─1 permissions, ─► opération (type, id)
products n─1 ref_products ; products 1─n product_substances n─1 ref_dci ; products n─1 brands, ref_laboratories, ref_forms
products 1─n product_units, product_prices, product_classifications n─1 classification_nodes n─1 classification_axes
products n─1 tax_categories 1─n tax_rates ; products 0..1─n products (variantes) ; products 1─n product_variant_attributes
substitution_groups 1─n substitution_group_members n─1 products
products 1─n lots 1─n stock_levels (par bucket), stock_movements ; products 1─n stock_reservations (réservation logique au niveau produit)
products(SERVICE) 1─1 service_definitions 1─n service_acts 1─n service_act_consumables ─► stock_movements
service_acts 0..1─1 sale_lines ; service_acts n─0..1 customers
supplier_invoices n─1 suppliers ; 0..1─1 expense_categories ; 0..1─1 fixed_assets ; supplier_credit_notes, supplier_advances n─1 suppliers
loans 1─n loan_schedule_lines 1─1 due_dates ; bank_statements 1─n bank_statement_lines 1─n bank_reconciliations ─► payments…
payroll_imports 1─n payroll_import_lines n─1 employees
événements métier ─► posting_rules ─► journal_entries 1─n journal_entry_lines n─1 chart_of_accounts ; journal_entries n─1 accounting_periods
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
| AN2 | Vente validée, produit non sensible : annulation possible par un collaborateur habilité **jusqu'à la fin du jour calendaire de la vente** (minuit, fuseau de l'officine), dans ses plafonds | ancienne proposition D61 (15 minutes, caduque) et §4.3 « auto-correction » |
| AN3 | Au-delà du jour calendaire, **seul l'administrateur** peut autoriser l'annulation (dérogation W4), quel que soit le rôle du demandeur | EXT05 §4.2 C5 (validation par un pharmacien) |
| AN4 | **Règle proposée, non arbitrée** : les modalités d'attribution des droits `sale.void.execute` et `sale.void.approve` doivent être définies dans le référentiel PO ; D66 est caduque et ne permet pas de conclure que l'administrateur est l'unique délégant ni d'autoriser une délégation par défaut | Ancienne référence EXT05 D66, caduque |
| AN5 | Produits sensibles (`sensitivity_flags` non vide) ou réglementés : circuit spécifique obligatoire (EXT05 §4.7) ; la règle du jour calendaire ne les rend jamais annulables par un non-pharmacien | — |
| AN6 | **Aucune annulation n'entraîne une réintégration automatique en stock vendable** : le produit passe en `EN_CONTROLE`, puis la décision suit le workflow D d'EXT05 (récupération physique, intégrité, conservation, péremption, restrictions réglementaires) | EXT05 `DISPONIBLE_DIRECT` (voir D74) |
| AN7 | Toutes les corrections passent par des mouvements inverses liés à l'opération initiale (`reverses_id`, `correlation_id`) — inchangé | — |
| AN8 | Une session de caisse clôturée n'est jamais modifiée : si l'annulation du même jour intervient après la clôture de la session d'origine, la restitution se fait dans la session ouverte de l'exécutant (inchangé) | — |

**Application de D74 (arbitrage confirmé)** : `DISPONIBLE_DIRECT` est supprimé. Une décision immédiate simplifiée par un
second collaborateur est possible uniquement si le pharmacien-administrateur lui a explicitement accordé le droit de
réintégration corrective. Le contrôle reste tracé et utilise la check-list réduite (produit présent, scellé, péremption lue).
Sans habilitation explicite, aucune réintégration par le second collaborateur n'est autorisée.

**Jour calendaire** : déterminé par l'horodatage serveur converti dans le fuseau de l'officine (`tenants.fuseau`), jamais par
l'horloge du poste. Une vente validée à 23 h 58 n'est annulable sans dérogation que jusqu'à 23 h 59 59 le même jour
**[ARBITRAGE D75 : option « jour d'exploitation » pour les officines de garde de nuit]**.

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
| **Perte** | Bucket `PERTE` non vendable, visible avec quantité et ancienneté « en attente de destruction » ; la destruction physique est une opération distincte, différée et tracée | Non | `stock_levels` + `loss_records` |

- **Correction d'EXT05** : le bucket `RESERVE` est retiré. Une réservation n'est pas un mouvement physique, et la modéliser
  comme un bucket créerait deux sources pour la même information (D76).
- **FEFO** : l'allocation à la vente choisit le lot `ACTIF` du bucket `DISPONIBLE` à la péremption la plus proche, non
  périmé, non bloqué, en déduisant les réservations. Un lot `RAPPELE` ou `BLOQUE` n'est jamais alloué.
- **Rapprochement CAT-11 (P81, DP1-R9)** : `reçu + corrections nettes = disponible + en contrôle + quarantaine + perte + sorties nettes`. « Reçu » désigne uniquement les réceptions physiques et les régularisations hors commande. Les retours clients et les contre-passations de réception sont comptabilisés dans les mouvements nets, et non dans « reçu ». La quantité disponible est lue dans `stock_levels`, jamais déduite d'une quantité disponible stockée sur le lot.
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

### 5.6 Définitions financières uniques (Partie 7D)

Chaque grandeur a **une seule définition**, utilisée par tous les écrans, rapports, exports et l'IA.

| Grandeur | Définition | Source | Ne contient pas |
|---|---|---|---|
| **Chiffre d'affaires (CA) HT / TTC** | Σ lignes de vente validées − annulations − avoirs (par date d'opération), tous payeurs confondus | `sale_lines`, `corrective_operation_lines` | Règlements d'organismes, encaissements, TVA (pour le HT) |
| **Coût des ventes** | Σ quantités vendues nettes × coût du lot figé sur la ligne | `sale_lines.cout_unitaire` | Pertes, charges |
| **Marge commerciale (brute)** | CA HT − coût des ventes | Couche métriques | Charges d'exploitation |
| **Créances** | Clients (crédit) + organismes (EXT04) + autres, à une date | `party_ledger` | CA futur |
| **Dettes** | Fournisseurs (marchandises et charges) + salaires et organismes sociaux + emprunts (capital restant) + fiscales | `party_ledger`, `loans` | Engagements non facturés |
| **Encaissements** | Entrées réelles d'argent : caisse (part patient), banque (règlements d'organismes, clients) | `payments` (in), `cash_movements` | Créances non réglées |
| **Décaissements** | Sorties réelles : fournisseurs, salaires, emprunts, impôts, remboursements clients | `payments` (out), `cash_movements` | Dettes non payées |
| **Trésorerie** | Soldes caisse (sessions + coffre) + soldes bancaires (rapprochés, et non rapprochés en distinction) | M08 | Créances, chèques non remis (affichés à part) |
| **Charges d'exploitation** | Charges de la période par catégorie (date de la pièce ou de rattachement), hors achats de marchandises stockés, hors intérêts et charges exceptionnelles | `supplier_invoices` (nature CHARGE), `expenses`, paie importée, dotations | Remboursement du capital d'emprunt, investissements (sauf dotation) |
| **Résultat d'exploitation (indicatif)** | Marge commerciale + autres produits d'exploitation (services) − charges d'exploitation − dotations − pertes de stock − pertes sur créances | Couche métriques | Charges financières et exceptionnelles, impôt sur le résultat |
| **Résultat net (indicatif)** | Résultat d'exploitation − charges financières (intérêts) ± éléments exceptionnels − impôt estimé | Idem | — |
| **Charges fixes / variables** | Selon `expense_categories.nature_analytique` | Paramétrage | — |

Règles :
1. **Un règlement CNAM ou d'assurance est un encaissement qui solde une créance**, jamais une vente (EXT04 §7.2). Le CA
   de la vente tiers payant est constaté une seule fois, à la validation.
2. Les résultats calculés par PHARM'OS sont **indicatifs** (gestion). Le résultat officiel est établi par l'expert-comptable ;
   l'écran l'indique explicitement **[À VALIDER R46]**.
3. Les remboursements de capital d'emprunt sont des décaissements, pas des charges ; les investissements sont des décaissements
   et des immobilisations, amortis selon les règles validées.
4. Les variations de stock (achats stockés non vendus) n'affectent pas la marge : la marge utilise le coût des produits vendus.
5. Chaque indicateur affiché porte un lien « comment est-il calculé ? » vers sa définition et ses données.

### 5.7 Tableau de bord financier (G23)

| Indicateur | Définition (§5.6) | Comparaisons |
|---|---|---|
| Chiffre d'affaires | CA HT et TTC | Période précédente, N−1 |
| Marge brute | Montant et taux | Idem |
| Charges fixes, charges variables | Par catégorie | Budget (si saisi), N−1 |
| Résultat d'exploitation (indicatif) | — | Idem |
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
de bord commercial = CA du tableau de bord financier = journal des ventes M20.

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
| `governance.exceptions.grant` (dépassements, dérogations de délai) | SETTINGS / SPECIFIC | **Non** | Administrateur |
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
| `analytics.finance.read` | ANALYTICS / READ | Oui | Administrateur, comptable |
| `analytics.sales.read` | ANALYTICS / READ | Oui | Administrateur, adjoint, responsable achats |
| `analytics.margin.read` | ANALYTICS / SPECIFIC | Oui | Administrateur, adjoint, comptable |

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

**Dépendances techniques à confirmer** : disponibilité de FTS5 dans D1 pour l'index de recherche (à prototyper au Lot 0,
avec une solution de repli par tables de trigrammes si nécessaire) ; volume des écritures comptables (une à plusieurs
écritures par vente : à mesurer avec la volumétrie cible du dossier §2.3).

---

## 8. Impacts sur les modules existants

Toutes les modifications ci-dessous sont **proposées et non appliquées**.

### 8.1 Dossier principal (../01_CONCEPTION/CONCEPTION_PHARMOS_v1 (1).md v1.1)

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
| §4.11 | `metrics_daily` étendu ; `financial_snapshots` |
| §7 IA | Les analyseurs financiers n'utilisent que des agrégats ; aucune donnée de paie individuelle |
| §9.1 Rôles | Notion de pharmacien administrateur ; matrice modules × actions |
| §10 Plan | Voir §12 |

### 8.2 Extensions

| Extension | Impact d'EXT06 |
|---|---|
| **EXT02** (intégrée) | Catalogue `permissions` complété (`module_code`, `action`) ; le titulaire devient « pharmacien administrateur » unique ; aucune autre modification |
| **EXT03 v1.1** | `products.substitution_group_id` et `ref_products.generic_group_code` remplacés par `substitution_groups` ; liste initiale des spécialités ; accès direct depuis l'accueil |
| **EXT04** | Accès direct depuis l'accueil ; listes de référence (`reference_list_items`) rapprochées du catalogue enrichi ; écritures de créances générées par M20 ; aucune table modifiée |
| **EXT05** | **Amendements** : D61 (15 min) remplacé par la règle du jour calendaire ; `DISPONIBLE_DIRECT` supprimé ou remplacé (D74) ; bucket `RESERVE` retiré ; `delegation.manage` remplacé par `governance.delegations.manage` (D66 caduque ; règle de délégation à confirmer dans le registre PO) ; `delegations.valid_from` obligatoire ; liste d'opérations structurée |

### 8.3 Points de compatibilité

- La règle du jour calendaire (EXT06) et la neutralisation du droit à délivrance au retour (EXT03 D42) sont indépendantes :
  la première détermine **qui** peut annuler, la seconde **l'effet** sur l'ordonnance.
- Le tiers payant (EXT04) ne change pas les définitions de CA et de marge : le CA inclut la part organisme, la créance est
  suivie à part (§5.6).
- Les écritures comptables M20 ne remplacent ni `party_ledger` (grand livre des tiers), ni les registres métier : elles en
  sont **dérivées**. En cas d'écart, ce sont les registres métier qui font foi, et l'écriture est régénérée.

---

## 9. Exigences réglementaires

Numérotation à la suite d'EXT05 (R34 à R41). Tant qu'un point n'est pas confirmé, la valeur la plus prudente s'applique.

| # | Sujet | Hypothèse de conception | Question précise | Interlocuteurs | Échéance |
|---|---|---|---|---|---|
| R42 | Catégories réglementaires de produits | Types `MEDICAMENT`, `DISPOSITIF_MEDICAL`, `ACCESSOIRE`, `PARAPHARMACIE`, `COSMETIQUE`, `COMPLEMENT_ALIMENTAIRE`… | Quelles catégories ont des obligations distinctes (traçabilité des dispositifs, matériovigilance, numéros de série, cosmétovigilance) ? | DPM, ministère de la Santé | Avant le lot 2 |
| R43 | Référentiel de classification thérapeutique et statut de commercialisation | Axe paramétrable ; ATC si licence et source compatibles | Existe-t-il un référentiel officiel tunisien (classes thérapeutiques, statuts AMM, arrêts de commercialisation) exploitable ? Licence d'utilisation de l'ATC ? | DPM, PCT | Avant le lot 2 |
| R44 | TVA et fiscalité des produits et des services | Catégories fiscales historisées, aucun taux par défaut | Taux applicables par catégorie (médicaments, dispositifs, parapharmacie, prestations) ; droit de timbre ; facture électronique | Expert-comptable, administration fiscale | Avant le lot 2 |
| R45 | Immobilisations et amortissements | Registre ; durées et méthodes paramétrables | Durées et méthodes admises ; seuil d'immobilisation | Expert-comptable | Avant le lot 7c |
| R46 | Pré-comptabilité et plan de comptes | Écritures générées, validées et exportées ; résultats indicatifs | Plan de comptes applicable (système comptable des entreprises), mentions des journaux, conditions d'acceptation d'une pré-comptabilité tenue par un logiciel, conservation des pièces | Expert-comptable, Ordre des experts-comptables | Avant le lot 7c |
| R47 | Services pharmaceutiques autorisés en officine | Aucun service actif par défaut ; activation par l'administrateur | Quelles prestations (mesure de tension, injections, premiers soins, dépistages, conseils) sont autorisées en officine, par quel personnel, avec quelles traces et quelles tarifications ? | Ordre des pharmaciens, ministère de la Santé | Avant le lot 9c |
| R48 | Responsabilité du pharmacien administrateur et délégation | Administrateur unique, qualification de pharmacien | Le responsable légal (titulaire) peut-il désigner un autre pharmacien comme administrateur du système ? Limites de la délégation d'actes administratifs et financiers | Ordre, juriste | Avant le lot 1 |
| R49 | Délai d'annulation d'une vente | Jour calendaire pour les produits non sensibles | Existe-t-il des règles fiscales sur l'annulation d'un ticket ou d'une facture (délai, mention, numérotation, caisse enregistreuse) ? | Administration fiscale, expert-comptable | Avant le lot 5 |
| R50 | Données de paie dans un logiciel de gestion | Import des montants, accès restreint N4 | Conditions de traitement et de conservation des données de paie ; déclaration INPDP | INPDP, juriste | Avant le lot 7c |
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
| CAT-11 | Contrôle `reçu + corrections nettes = disponible + en contrôle + quarantaine + perte + sorties nettes` ; « reçu » = réceptions et régularisations seulement, retours clients et contre-passations inclus dans les mouvements nets | Égalité sur un jeu de référence, selon DP1-R9 (P81) |
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

### 10.6 Accueil et navigation (S-NAV)

| ID | Scénario | Résultat attendu |
|---|---|---|
| NAV-01 | Tuiles affichées pour un caissier | Seulement les modules autorisés |
| NAV-02 | Utilisation tactile et scanner sans souris | Toutes les actions de l'accueil sont accessibles |
| NAV-03 | Accueil en arabe (RTL) | Disposition inversée, libellés traduits |
| NAV-04 | Accès direct Médecins / Organismes | Ouvre les écrans d'EXT03 / EXT04, filtrés ; aucune donnée dupliquée |

---

## 11. Décisions arbitrées — registre historique PO

Numérotation à la suite d'EXT05 (D59 à D71). Les décisions D72 à D86 étaient marquées ARBITRÉES dans la matrice maître D1–D86 v1.2.1. La matrice v1.3.3 (12/11/2026) a été retrouvée, mais son contenu détaillé n'a pas encore été relu ; les statuts ci-dessous sont conservés comme historique de la version v1.2.1 et doivent être revalidés contre v1.3.3 avant toute utilisation comme état courant.

**Réserve de traçabilité D72 :** le tableau EXT06 décrit D72 comme la procédure de secours en cas de perte d'accès du pharmacien administrateur. Les échanges PO identifient également D72 comme l'arbitrage relatif au SUPERADMIN APEX immuable (`apexproib@gmail.com`). Ces deux formulations ne doivent pas être fusionnées ni implémentées sous un même identifiant avant comparaison avec la matrice maître et ses éventuels addenda. La règle de secours APEX reste à rattacher à un identifiant non ambigu.

| # | Question | Options | Recommandation |
|---|---|---|---|
| **D72** | Perte d'accès du pharmacien administrateur | Procédure de secours APEX sur justificatifs · Second administrateur de secours | **Procédure de secours APEX** (double contrôle, sans accès aux données) ; un administrateur suppléant affaiblirait la règle « un seul responsable » |
| **D73** | Dérogations en l'absence de l'administrateur | Aucune · Validation à distance (smartphone, MFA) | **Validation à distance** autorisée ; sinon l'opération attend |
| **D74** | Réintégration directe d'EXT05 (`DISPONIBLE_DIRECT`) | Supprimer · Remplacer par une décision immédiate par un second collaborateur | **Remplacer** par une décision immédiate tracée, avec check-list réduite ; le second collaborateur doit disposer d'un droit de réintégration corrective explicitement accordé par le pharmacien-administrateur |
| **D75** | Définition du « jour » d'annulation | Jour calendaire (minuit) · Jour d'exploitation (heure de bascule paramétrable, ex. 6 h) | Jour calendaire par défaut ; jour d'exploitation en option pour les officines de nuit |
| **D76** | Bucket `RESERVE` d'EXT05 | Conserver · Retirer (réservation = engagement) | **Retirer** |
| **D77** | Page affichée après connexion | Accueil · Tableau de bord · Selon le rôle | **Selon le rôle**, modifiable par l'utilisateur dans les limites de ses droits |
| **D78** | Configuration des services et contenu des notifications | Catalogue prédéfini · Types configurés par la pharmacie | Types de service et champs éventuels configurés par le pharmacien-administrateur, sans catalogue tunisien imposé par APEX ; notification limitée au type d'opération/service, au nom du client et à la date ; toute donnée de service reste soumise à R47 et aux contrôles de confidentialité |
| **D79** | Visibilité des marges | Tous les rôles du module · Permission dédiée | **Permission dédiée** `analytics.margin.read` |
| **D80** | Profondeur du module comptable | A. Exports seulement (journaux) · B. Pré-comptabilité en partie double (M20) · C. Comptabilité générale complète | **B** : écritures générées et exportées ; le comptable tient les comptes officiels ; affichage et exports portent explicitement la mention « Comptabilité à titre indicatif » |
| **D81** | Moteur de paie | Import des montants · Moteur intégré | **Import des montants** (sans moteur) tant que les règles ne sont pas validées |
| **D82** | Réouverture d'une période comptable | Interdite · Administrateur avant export définitif | Administrateur avec motif, **interdite après export définitif** |
| **D83** | Plan de comptes livré | Aucun · Modèle standard préconfiguré et adaptable | Modèle comptable standard préconfiguré, adaptable, sans validation préalable obligatoire d'un expert-comptable ; mention « Comptabilité à titre indicatif » obligatoire |
| **D84** | Référentiel de classification thérapeutique | ATC · Référentiel local · Classification APEX | Selon R43 ; à défaut, classification APEX maintenue et sourcée |
| **D85** | Rapprochement bancaire automatique | Automatique · Proposé puis confirmé | **Proposé puis confirmé** (cohérent avec EXT04 D55) |
| **D86** | Seuil de validation des charges par l'administrateur | Montant fixe · Toutes les charges | Montant paramétrable |

---

## 12. Positionnement dans le plan de développement

EXT06 touche des fondations (droits, catalogue, stock) qui doivent être correctes **dès les premiers lots**, et des
fonctions avancées (comptabilité, tableaux de bord) qui viennent plus tard.

| Lot | Contenu EXT06 | Justification |
|---|---|---|
| **0. Socle** (ajout) | Prototype de l'index de recherche (FTS5 ou repli) ; règle de date serveur et fuseau | Risques techniques à lever tôt |
| **1. Plateforme** (ajout) | `tenant_administrators`, `app_modules`, catalogue `permissions` complété (module × action), coupure de module, `permission_exceptions`, matrice des droits (G3), désignation et transfert de l'administrateur | Le contrôle des droits conditionne tous les autres lots |
| **2. Catalogue** (ajout) | Types de produits, substances multiples, marques, unités de vente, variantes, classification multi-axes, catégories fiscales et taux, prix historisés, groupes de substitution, explorateur, recherche universelle, nœuds initiaux des familles | Remplacer `categories` et `tva_bp` avant toute donnée réelle |
| **3. Stock & lots** (ajout) | Lots complétés, buckets sans `RESERVE`, FEFO avec réservations | Structure de stock définitive |
| **5. Caisse & ventes** (ajout) | Règle du jour calendaire, dérogation administrateur pour les annulations ; **accueil opérationnel** (G1) | Exploitation quotidienne |
| **5d. Opérations correctives** (EXT05 amendé) | Suppression de `DISPONIBLE_DIRECT` (D74), décision immédiate tracée | — |
| **7. Finance** (ajout) | Nature des factures, catégories de charges, avoirs et acomptes fournisseurs, charges récurrentes, emprunts, rapprochement bancaire | Dettes et trésorerie fiables |
| **7c. Comptabilité & paie importée** (nouveau, après 7 et 7b) | M20 : plan de comptes, journaux, règles de passation, écritures, périodes, grand livre, balance, exports ; immobilisations ; import de paie | Dépend de tous les événements financiers ; validation R45, R46, R50 |
| **9c. Services pharmaceutiques** (nouveau, après 5 et 10b) | Définitions, activation réglementaire, actes, consommables, facturation | Après R47 et les habilitations RH |
| **11. Métriques & rapports** (ajout) | Dimensions et mesures §4.10, `financial_snapshots`, tableaux de bord financier (G23) et commercial (G24) | Couche métriques unique |
| **13. IA** (ajout) | Analyses financières et commerciales sur agrégats | Après 11 |

**Prérequis bloquants** :
- avant le lot 1 : R48 ; décisions D72, D73 ;
- avant le lot 2 : R42, R43, R44, R51 ; décisions D76, D84 ;
- avant le lot 5 : R49 ; décisions D74, D75, D77 ;
- avant le lot 7c : R45, R46, R50 ; décisions D80, D81, D82, D83 ;
- avant le lot 9c : R47 ; décision D78.

### Récapitulatif des tables EXT06

| Catégorie | Tables |
|---|---|
| Nouvelles — gouvernance, catalogue, fiscalité (17) | `tenant_administrators`, `app_modules`, `permission_exceptions`, `ref_product_substances`, `product_substances`, `brands`, `product_units`, `product_variant_attributes`, `product_type_fields`, `product_prices`, `substitution_groups`, `substitution_group_members`, `classification_axes`, `classification_nodes`, `product_classifications`, `tax_categories`, `tax_rates` |
| Nouvelles — services (3) | `service_definitions`, `service_acts`, `service_act_consumables` |
| Nouvelles — finance (12) | `supplier_credit_notes`, `supplier_advances`, `expense_categories`, `recurring_charge_templates`, `loans`, `loan_schedule_lines`, `fixed_assets`, `bank_statements`, `bank_statement_lines`, `bank_reconciliations`, `payroll_imports`, `payroll_import_lines` |
| Nouvelles — comptabilité M20 (7) | `chart_of_accounts`, `journals`, `posting_rules`, `accounting_periods`, `journal_entries`, `journal_entry_lines`, `accounting_exports` |
| Nouvelles — métriques (1) | `financial_snapshots` |
| Complétées | `permissions`, `permission_overrides`, `delegations`, `ref_products`, `products`, `ref_laboratories`, `lots`, `stock_levels`, `supplier_invoices`, `expenses` (restreinte), `metrics_daily` |
| Remplacées | `categories` (→ classification multi-axes), `product_prices_history` (→ `product_prices`), `products.tva_bp` (→ catégories fiscales), `products.substitution_group_id` et `ref_products.generic_group_code` d'EXT03 (→ `substitution_groups`), bucket `RESERVE` d'EXT05 |
| Doublons créés | **Aucun** |
