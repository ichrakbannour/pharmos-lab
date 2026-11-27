# PHARM'OS — Dossier de conception v1.1

**Produit** : PHARM'OS — Intelligent Pharmacy Management System
**Éditeur** : APEX AI DEV
**Statut** : Conception de référence (Étapes 1 et 2), aucun module développé
**Date** : 2026-11-03 — v1.1 : intégration de l'extension n° 02 RH & Team Management
**Documents liés** : [`EXT02_RH_TEAM_MANAGEMENT.md`](../02_EXTENSIONS/EXT02_RH_TEAM_MANAGEMENT.md) (modules M15 RH & équipes, M16 Laboratoire) — intégrée
· [`EXT03_ORDONNANCES_PRESCRIPTEURS.md`](../02_EXTENSIONS/EXT03_ORDONNANCES_PRESCRIPTEURS.md) (module M17 Ordonnances & prescripteurs) — **proposition en attente de validation**
**Marché cible initial** : officines tunisiennes (FR / AR, RTL/LTR), extensible

> Ce document sert de référence unique. Toute évolution de l'architecture, du modèle de données
> ou des règles métier doit être reportée ici (section « Journal des décisions », §11).
> Les points marqués **[À VALIDER]** dépendent d'une décision du propriétaire ou d'une
> validation réglementaire tunisienne.

---

## Sommaire

1. Analyse des captures de référence
2. Analyse fonctionnelle complète
3. Architecture générale & cartographie des modules
4. Modèle de données prévisionnel
5. Relations et flux inter-modules
6. Architecture technique recommandée
7. Architecture du moteur PHARM'OS AI
8. Avatar IA : fonctionnement
9. Rôles, permissions, sécurité, conformité
10. Plan de développement séquentiel
11. Risques techniques & points de décision

Annexe : Extension n° 02 — RH & Team Management (document séparé, partie intégrante de la conception)
Annexe : Extension n° 03 — Ordonnances, prescripteurs et analyse des prescriptions (proposition, §11.4)

---

## 1. Analyse des captures de référence

Les captures proviennent d'un logiciel d'officine égyptien (devise ج.م, interface arabe RTL).
Elles décrivent un produit fonctionnellement riche mais présentant des défauts de cohérence
dont PHARM'OS doit tirer les leçons.

### 1.1 Inventaire écran par écran

| Écran | Fonctions observées | À reprendre | À corriger / améliorer |
|---|---|---|---|
| **Écran de vente** (شاشة البيع) | Recherche nom/code-barres, filtre catégorie, lignes avec péremption, prix, unité (boîte), **nombre de plaquettes** (vente fractionnée), quantité, total ; remise montant/% avec plafond (« remise max 10 % ») ; paiement Cash / Visa / Crédit ; billets rapides 50/100/200 ; montant reçu ; boutons **Calculer/Vendre, Suspendre, Rappeler, Vider, Retour, Clients** ; bascule « afficher facture » ; onglets 1-2-3 (**ventes parallèles**) | Parcours clavier, ventes en attente multiples, vente à la plaquette, plafond de remise par rôle | Afficher le lot servi (FEFO), alerte produit réglementé, rendu monnaie, contrôle ordonnance |
| **Journal des factures** (سجل الفواتير) | KPI (total ventes, nb factures, panier moyen, retours) ; filtres période, n°, médicament, code-barres, mode de paiement, plage de dates ; statut (complète/retournée) ; export | Filtres combinables, export | Numérotation fiscale séquentielle (les n° hexadécimaux type `#D9CF5D1C` ne sont pas conformes à une facturation légale) |
| **Catalogue** (كتالوج الأدوية) | 25 059 références ; nom commercial / arabe, DCI, coût, prix, qté en boîtes + plaquettes, seuil, actions (éditer, étiquette code-barres, lier, supprimer) ; onglets soldes, **inventaire**, autres produits locaux | Référentiel volumineux, double nom FR/AR | **Coût 0.00** sur la majorité des lignes → marges fausses ; **codes-barres générés aléatoirement** (`RANDm5…`) mélangés aux EAN réels ; suppression physique |
| **Achats** (المشتريات) | Choix fournisseur, recherche, lignes qté boîtes + plaquettes, prix public, péremption MM/AA, remise %, coût total ; import Excel ; impression étiquettes ; KPI coût/marge | Saisie rapide, import Excel, étiquettes | Pas de n° de lot, pas de rapprochement bon de commande ↔ réception ↔ facture, péremption au mois seulement |
| **Périmés / avariés** (التالف) | Registre des pertes, onglets « proches péremption », « rapport des pertes » | Structure en 3 onglets | Ajouter workflow de retrait/destruction, PV, retour fournisseur |
| **Commande intelligente** (الطلب الذكي) | Qté par défaut, top-3 fournisseurs par remise moyenne, répartition par fournisseur, suggestion « produits du même fournisseur bientôt en rupture », « manquants seulement », envoi au gérant, historique | Concept très pertinent, répartition multi-fournisseurs | Quantités fixes (10) au lieu d'un calcul de besoin ; pas de délai fournisseur ni de couverture |
| **Fournisseurs** (حسابات الموردين) | Liste avec solde, fiche (dû, payé), factures, relevé détaillé, paiement global, ajout facture | Relevé de compte, paiement global | Imputation des paiements sur factures, échéancier, chèques/traites |
| **Statistiques** | Ruptures (stock **négatif −1**), proches péremption, dernières ventes, **performance caissiers** ; onglets « dormants et périmés », « **suivi des patients chroniques** » | Performance par caissier, suivi chroniques | Stock négatif autorisé ; KPI « rupture 23 ج.م » (un nombre affiché comme montant) |
| **Paramètres** | Infos pharmacie, logo, WhatsApp gérant, envoi auto rapport de clôture, licence (mensuelle, jours restants), mode nuit, affichage prix, impression auto, **menu réorganisable**, **alertes gérant WhatsApp** (nouveau client, ajustement stock, vente à crédit, **manipulation panier**, **ouverture manuelle tiroir**, **tentative de remise à zéro**, échecs de connexion, modification prix/remises…) | Alertes antifraude, licence, personnalisation | Passerelle `whatsapp-web.js` (non officielle, non compatible SaaS, contraire aux CGU Meta) |
| **Ventes à crédit** (آجل) | Dettes clients, factures payées/non payées, **« médicaments sans solde »** (vendus sans stock), actions voir/WhatsApp/annuler/solder | Relance WhatsApp, suivi | La vente sans stock doit être une exception tracée, pas un état normal |
| **Clôture / Shift** (الشيفت) | Ventes espèces, encaissements de dettes, ventes à crédit, Visa, retours, dépenses, **attendu en caisse** (fond 8 000 + …), clôturer, journal du tiroir | Modèle de session de caisse complet | Écart de caisse déclaré vs théorique, validation par responsable |
| **Clients** | Cartes clients, code, plafond de crédit, disponible, points fidélité | Plafond de crédit, points | **Client anonyme « Walk-in » avec 1 460 points** → la fidélité ne doit pas s'appliquer au client de passage |
| **Tableau de bord** | Cartes alerte stock, synthèse financière, audit des shifts ; ventes/bénéfice jour, revenu/bénéfice mois, dettes fournisseurs/clients, périmés, proches péremption ; ventes semaine ; top produits ; export rapport du jour | Cartes d'action | Incohérences de totaux (journal : 44 factures / 16 788 ; performance caissier : 37 factures / 13 638) → **les KPI doivent provenir d'une seule couche de calcul** |

### 1.2 Enseignements structurants

1. **Une seule source de vérité pour le stock** : un *grand livre des mouvements* immuable,
   avec quantités calculées par lot. Le stock négatif est interdit par défaut (exception tracée et autorisée par rôle).
2. **Unités fractionnables** : boîte → plaquettes/unités. Toutes les quantités sont stockées
   en *plus petite unité vendable* (entier), l'affichage convertit.
3. **Coût réel obligatoire** : coût d'achat par lot et CUMP ; aucun calcul de marge sur coût nul sans alerte.
4. **Codes-barres** : EAN réels distingués des codes internes (préfixe réservé, ex. `2xx` GS1 interne).
5. **Documents légaux immuables** : numérotation continue, correction par avoir / contre-écriture.
6. **KPI centralisés** : une couche « métriques » unique alimente tableau de bord, rapports et IA.
7. **Antifraude** : les alertes observées (manipulation panier, tiroir, prix) deviennent des
   événements d'audit standardisés, exploités par l'IA.
8. **Messagerie officielle** : WhatsApp Business Cloud API ou lien `wa.me` validé humainement.

---

## 2. Analyse fonctionnelle complète

### 2.1 Acteurs

| Acteur | Description |
|---|---|
| Pharmacien titulaire (propriétaire) | Responsable légal, accès complet, valide les opérations sensibles |
| Pharmacien adjoint | Délégation large, dispensation des réglementés |
| Responsable d'équipe | Planning, validation des congés (N1), du temps et des écarts de caisse de son équipe (EXT02) |
| Gestionnaire RH | Dossiers du personnel, contrats, soldes, export paie (EXT02) |
| Collaborateur (libre-service) | Consulte son planning, ses soldes et ses KPI ; demande des congés ; répond à ses évaluations (EXT02) |
| Préparateur / vendeur | Vente, conseil, réception |
| Caissier | Encaissement, session de caisse |
| Gestionnaire achats / stock | Commandes, réceptions, inventaires |
| Comptable (interne ou externe) | Lecture financière, rapprochements, exports |
| Auditeur | Lecture seule, journaux |
| Support APEX AI DEV | Aucun accès aux données par défaut ; accès temporaire sur consentement tracé |
| PHARM'OS AI | Acteur système, **lecture + propositions uniquement** |

### 2.2 Exigences fonctionnelles par module

Les douze modules du cahier des charges sont retenus, complétés par trois modules transverses
issus de l'analyse des captures :

- **M00 — Plateforme** : tenants, utilisateurs, rôles, paramètres, licence/abonnement, audit, notifications.
- **M02b — Sessions de caisse** (shift) : ouverture avec fond, mouvements de caisse, dépenses, clôture avec comptage et écart.
- **M13 — Assistant PHARM'OS AI & avatar** (§7, §8).
- **M15 — RH & équipes** (extension n° 02) : collaborateurs, équipes, affectations, qualifications, habilitations, planning, présence, congés, caisses nominatives, KPI, formations.
- **M16 — Laboratoire** (extension n° 02) : préparations magistrales et officinales tracées et contrôlées par des personnes habilitées.

> **Changement structurant v1.1** : toute opération est attribuée à un **collaborateur** (`employee_id`),
> et les droits effectifs combinent rôle, habilitations, qualifications valides, statut et présence
> (EXT02 §1.2). M02b devient une **caisse nominative** (EXT02 §7).

| Module | Exigences clés (synthèse) | Règles métier critiques |
|---|---|---|
| M01 Tableau de bord | CA J/M/A, nb ventes, panier moyen, marge, achats, charges, trésorerie, créances, dettes, valeur stock, alertes, KPI | Toutes les valeurs issues de la couche métriques ; filtre période ; drill-down vers le module source |
| M02 Vente & caisse | Recherche nom/DCI/code-barres (< 100 ms perçu), scan, ventes en attente multiples, vente à l'unité, remises plafonnées, paiements multiples (espèces, carte, chèque, crédit, mixte), retours, ticket 80 mm / facture A4 | Allocation **FEFO** automatique des lots ; blocage lot périmé ; produit réglementé → saisie ordonnance obligatoire ; vente à crédit → plafond client ; remise > plafond → validation PIN responsable |
| M02b Sessions de caisse | Ouverture, encaissements, décaissements, dépenses, retours, clôture, rapport auto | **v1.1** : une session active par collaborateur et par tiroir (EXT02 §7) ; comptage à l'aveugle ; écart justifié et validé par un tiers ; rapport immuable |
| M03 Achats | Facture fournisseur, réception (avec ou sans BC), contrôle qtés/prix, remises, gratuités (UG), lots/péremptions, mise à jour stock et coûts, retours fournisseur | Réception = mouvements d'entrée par lot ; écart prix > seuil → alerte ; facture validée immuable (avoir pour corriger) |
| M04 Catalogue | Nom commercial FR/AR, DCI, laboratoire, forme, dosage, conditionnement, unités/boîte, codes-barres multiples, PA, PV, marge, TVA, seuil, classe réglementaire, remboursement **[À VALIDER CNAM]** | **Référentiel global** (APEX) + **fiche locale** (pharmacie) liée ; import contrôlé avec rapport de différences ; pas de doublon (clé : code-barres / code référentiel) |
| M05 Stocks & inventaires | Stock dispo / réservé, mouvements, inventaire tournant ou complet, ajustements motivés, valorisation, ruptures, faible rotation | Stock = Σ mouvements ; ajustement = mouvement motivé + audit ; inventaire en sessions (comptage → écarts → validation) |
| M06 Fournisseurs | Fiche, contacts, conditions (remise, délai, franco, jours de commande), BC, factures, paiements, soldes, échéancier, relevé | Solde = grand livre tiers ; paiement imputé sur factures (lettrage) |
| M07 Clients | Fiche, historique, compte, plafond, règlements, fidélité, suivi traitements (consentement) | Client de passage sans fidélité ; données santé seulement avec consentement daté et révocable |
| M08 Échéances & instruments | Chèques reçus/émis, portefeuille, remise en banque, encaissé, rejeté, effets/traites, échéances clients/fournisseurs, calendrier, alertes | Machine à états stricte, historique des transitions, rejet → réouverture de la créance |
| M09 Réglementés | Classes paramétrables (tableaux), registre entrées/sorties, ordonnances, soldes théoriques vs physiques, états, export | Registre **généré à partir des mouvements** (pas de double saisie) ; numérotation continue ; aucune modification, uniquement rectification tracée |
| M10 Péremptions & pertes | Seuils 180/90/60/30/7 j paramétrables, liste périmés, valeur à risque, retrait, destruction, retour fournisseur | Lot périmé bloqué à la vente ; destruction = mouvement de sortie + PV |
| M11 Commandes intelligentes | Calcul des besoins, risques de rupture, quantités recommandées, comparaison fournisseurs, BC, envoi email/WhatsApp, suivi statut | Le pharmacien valide **explicitement** ; envoi uniquement après validation ; historique des versions du BC |
| M12 Factures & rapports | Journaux ventes/achats, factures, impayés, caisse, financier, stock, rentabilité, exports Excel/PDF | Rapports = vues de la couche métriques ; exports horodatés et tracés |
| M13 PHARM'OS AI | Recommandations argumentées, conversation, avatar | Lecture seule + création de **brouillons** ; jamais d'exécution sensible |
| M15 RH & équipes | Dossier collaborateur, équipes, affectations, habilitations, planning glisser-déposer, pointage, congés, caisses nominatives, KPI à quatre axes, évaluations, formations, tableau de bord RH | Aucune opération sans collaborateur identifié ; données N3/N4 cloisonnées ; aucune décision RH par l'IA (détail : EXT02) |
| M16 Laboratoire | Préparations, pesées par lot, contrôles, consommation de stock | Préparateur et contrôleur habilités et distincts |

### 2.3 Exigences non fonctionnelles

| Domaine | Exigence |
|---|---|
| Performance | Recherche produit < 150 ms serveur ; ajout ligne de vente < 100 ms (cache local) ; tableau de bord < 1,5 s |
| Disponibilité | Mode dégradé hors-ligne pour la vente (Lot 14) |
| Volumétrie | 30 000 références/pharmacie, 300 ventes/jour, 5 ans d'historique |
| Précision monétaire | Montants en **millimes (entiers)** — 1 TND = 1 000 millimes ; aucun flottant |
| Langues | FR + AR (RTL) dès le Lot 0 ; EN prévue (clés i18n, aucun texte en dur) |
| Responsive | Desktop (caisse), tablette (réception/inventaire), smartphone (consultation/alertes) |
| Traçabilité | Toute écriture sensible produit un événement d'audit immuable |
| Isolation | Aucune requête sans `tenant_id` ; tests automatisés d'isolation |

---

## 3. Architecture générale & cartographie des modules

### 3.1 Vue en couches

```
┌───────────────────────────────────────────────────────────────────────┐
│  CLIENTS : PWA Desktop (caisse) · Tablette (réception/inventaire)     │
│            · Mobile (alertes, consultation) · Avatar PHARM'OS AI      │
│  i18n FR/AR(RTL)/EN · cache local IndexedDB (catalogue, ventes en     │
│  attente, file hors-ligne) · lecteur code-barres (clavier HID/caméra) │
└──────────────────────────────┬────────────────────────────────────────┘
                               │ HTTPS · JSON · session cookie HttpOnly
┌──────────────────────────────▼────────────────────────────────────────┐
│  API GATEWAY (Hono / Cloudflare Workers)                              │
│  auth · résolution tenant · RBAC · validation (zod) · rate-limit ·    │
│  idempotency-key · journal d'audit · gestion d'erreurs normalisée     │
├───────────────────────────────────────────────────────────────────────┤
│  SERVICES MÉTIER (modules)                                            │
│  Catalogue · Stock(ledger) · Ventes/Caisse · Achats · Tiers           │
│  (clients/fournisseurs) · Finance(échéances, instruments, grand livre │
│  tiers) · Réglementés · Péremptions · Commandes · Rapports · IA       │
├───────────────────────────────────────────────────────────────────────┤
│  NOYAU TRANSVERSE                                                     │
│  Numérotation documents · Événements de domaine (outbox) · Métriques  │
│  · Notifications · Paramètres · Fichiers (PDF, imports)               │
├───────────────────────────────────────────────────────────────────────┤
│  DONNÉES : D1 (SQLite relationnel) · R2 (PDF, imports, sauvegardes)   │
│  · Queues/outbox (notifications, IA batch)                            │
├───────────────────────────────────────────────────────────────────────┤
│  INTÉGRATIONS : LLM (API) · Email (Resend/SendGrid) · WhatsApp        │
│  Business Cloud API · Imprimantes (ESC/POS via pont local) · Banque   │
└───────────────────────────────────────────────────────────────────────┘
```

### 3.2 Cartographie des modules et dépendances

```
                         ┌──────────────┐
                         │ M00 Plateforme│ (tenants, users, rôles, audit, paramètres)
                         └──────┬───────┘
              ┌─────────────────┼───────────────────────────┐
        ┌─────▼─────┐     ┌─────▼──────┐              ┌─────▼──────┐
        │M04 Catalogue│◄──│ Référentiel│              │ Tiers       │
        │  (local)    │   │ global APEX│              │ M06 / M07   │
        └─────┬──────┘    └────────────┘              └─────┬──────┘
              │                                             │
        ┌─────▼──────────────────────────┐                  │
        │ M05 STOCK — grand livre des    │◄──── lots ───────┤
        │ mouvements (source unique)     │                  │
        └─▲────▲──────▲──────▲──────▲───┘                  │
          │    │      │      │      │                       │
   ┌──────┴┐ ┌─┴────┐ │  ┌───┴───┐ ┌┴────────┐              │
   │M02    │ │M03   │ │  │M10    │ │M09      │              │
   │Ventes │ │Achats│ │  │Périm. │ │Réglem.  │              │
   └──┬────┘ └──┬───┘ │  └───────┘ └─────────┘              │
      │         │   ┌─┴──────┐                              │
      │         │   │M11 Cmd │──── BC ───► fournisseurs ────┤
      │         │   └────────┘                              │
      ▼         ▼                                           │
   ┌────────────────────────────────────────┐               │
   │ M08 FINANCE : grand livre tiers,        │◄─────────────┘
   │ paiements, chèques, traites, échéances, │
   │ sessions de caisse (M02b)               │
   └──────────────────┬─────────────────────┘
                      ▼
   ┌────────────────────────────────────────┐
   │ COUCHE MÉTRIQUES → M01 Dashboard, M12   │
   │ Rapports, M13 PHARM'OS AI               │
   └────────────────────────────────────────┘
```

**v1.1 — Couche transverse « Personnes & droits » (M15, EXT02)** : située entre M00 et tous les modules
métier, elle fournit à chaque requête le **collaborateur agissant** et ses **droits effectifs**
(rôle ∩ habilitations ∩ qualifications ∩ statut ∩ présence). Elle consomme en retour les événements
métier pour le planning (charge), les KPI et l'IA. M16 Laboratoire s'appuie sur M05 (consommation de lots)
et sur M15 (habilitations).

**Principes** :
- Un module n'écrit **jamais** directement dans les tables d'un autre module ; il appelle son service
  (ex. Ventes appelle `StockService.allocateAndIssue()`).
- Les effets secondaires asynchrones (notifications, recalcul IA, alertes) passent par des
  **événements de domaine** (`sale.completed`, `receipt.validated`, `cheque.rejected`…) écrits
  dans une table *outbox* dans la même transaction.
- L'IA et les rapports ne lisent que la couche métriques et des vues dédiées.

### 3.3 Arborescence de l'application (frontend)

```
/login · /select-pharmacy · /onboarding
/                         M01 Tableau de bord
/pos                      M02 Caisse (plein écran, onglets ventes en attente)
/pos/session              M02b Session de caisse (ouverture, mouvements, clôture)
/sales                    Journal des ventes · /sales/:id · /sales/returns
/purchases                Factures fournisseurs · /purchases/new · /purchases/:id
/receipts                 Réceptions · /receipts/:id
/catalog                  Produits · /catalog/:id (onglets fiche, lots, mouvements, prix, réglementaire)
/catalog/import           Import référentiel / Excel (aperçu des différences)
/stock                    Niveaux · /stock/movements · /stock/adjustments
/stock/inventories        Sessions d'inventaire · /stock/inventories/:id (comptage tablette)
/expiry                   M10 Péremptions · /expiry/losses · /expiry/destructions
/controlled               M09 Registre · /controlled/register/:class · /controlled/reports
/orders                   M11 Commandes · /orders/suggest · /orders/:id
/suppliers                M06 · /suppliers/:id (fiche, factures, relevé, échéancier)
/customers                M07 · /customers/:id (fiche, historique, compte, fidélité, traitements)
/credit                   Ventes à crédit / créances
/finance                  M08 Calendrier · /finance/cheques · /finance/bills · /finance/payments
/reports                  M12 Rapports · exports
/ai                       M13 Centre de recommandations · conversation
/settings                 Pharmacie · utilisateurs & rôles · caisses & imprimantes ·
                          taxes · séquences · alertes · intégrations · réglementaire · licence
/audit                    Journal d'audit
/lab                      M16 Préparations · /lab/:id (pesées, contrôles)
/hr                       M15 Tableau de bord RH
/hr/employees             Collaborateurs · /hr/employees/:id (fiche, contrat, affectations, habilitations,
                          présence, congés, KPI, évaluations, formations, documents)
/hr/teams                 Équipes, postes, règles de couverture
/hr/planning              Planning jour / semaine / mois (glisser-déposer)
/hr/attendance            Pointages, validation du temps, heures supplémentaires
/hr/leaves                Demandes, validation, calendrier collectif, soldes
/hr/cash-sessions         Sessions de caisse nominatives, écarts à valider
/hr/performance           KPI, profils de notation, évaluations, événements qualité
/hr/training              Catalogue et suivi des formations
/me                       Espace collaborateur : mon planning, mes congés, mes indicateurs, pointage
```

### 3.4 Structure type d'une page de module

- **En-tête** : titre, KPI du module (3-5 cartes cliquables), actions primaires.
- **Barre de filtres** : recherche, période, statut, tiers ; filtres sauvegardables.
- **Tableau** : tri, pagination serveur, colonnes configurables, actions par ligne, sélection multiple.
- **Panneau latéral / tiroir** : détail sans quitter la liste.
- **Pied** : totaux de la sélection, export.
- Bandeau contextuel PHARM'OS AI (recommandations du module, repliable).


---

## 4. Modèle de données prévisionnel

### 4.1 Conventions

- Toutes les tables métier portent `tenant_id` (pharmacie) ; clé primaire `id` TEXT (ULID, triable dans le temps, générable hors-ligne).
- Montants en **millimes** (`INTEGER`). Quantités en **plus petite unité** (`INTEGER`). Taux en points de base (1 % = 100).
- Colonnes communes : `created_at`, `created_by`, `updated_at`, `updated_by`, `version` (verrou optimiste).
- **Pas de suppression physique** des données métier : `archived_at` pour les référentiels ; documents
  validés immuables (`status` + documents correctifs).
- Dates de péremption stockées en `YYYY-MM-DD` (fin de mois si seul MM/AA est connu, avec indicateur `expiry_precision`).

### 4.2 Plateforme & sécurité (M00)

| Table | Colonnes principales |
|---|---|
| `tenants` | id, raison_sociale, nom_commercial, matricule_fiscal, adresse, gouvernorat, telephone, logo_key, devise (TND), langue_defaut, fuseau, statut, plan, created_at |
| `subscriptions` | id, tenant_id, plan, periodicite, debut, fin, statut, sieges, modules_actifs (JSON) |
| `sites` | id, tenant_id, nom (prévu multi-officines / dépôt) |
| `users` | id, email, telephone, nom, password_hash, mfa_secret, locale, statut, last_login_at |
| `memberships` | id, tenant_id, user_id, **employee_id** (v1.1), role_id, pin_hash (validation rapide en caisse), actif |
| `roles` | id, tenant_id (NULL = rôle système), code, libelle |
| `role_permissions` | role_id, permission_code, scope (ex. `max_discount_bp`) |
| `sessions` | id, user_id, tenant_id, device_id, ip, user_agent, expires_at, revoked_at |
| `devices` | id, tenant_id, nom (Caisse 1), type, imprimante, tiroir, dernier_contact |
| `settings` | tenant_id, cle, valeur (JSON) — seuils péremption, plafonds, TVA, alertes… |
| `document_sequences` | tenant_id, type_doc, exercice, prefixe, prochain_numero |
| `audit_log` | id, tenant_id, user_id, **employee_id** (v1.1), device_id, action, entity, entity_id, avant (JSON), apres (JSON), motif, ip, at, hash_prev, hash (chaînage anti-altération) |
| `domain_events` (outbox) | id, tenant_id, type, payload, created_at, processed_at, tentatives |
| `notifications` | id, tenant_id, user_id/role, canal (in-app/email/whatsapp), type, priorite, titre, corps, lien, lu_at, envoye_at, statut |
| `consents` | id, tenant_id, customer_id, objet (suivi_traitement, marketing, whatsapp), accorde_at, revoque_at, preuve |

### 4.3 Catalogue (M04)

| Table | Colonnes principales |
|---|---|
| `ref_products` (**global APEX**, sans tenant) | id, code_ref, nom_commercial, nom_ar, dci_id, laboratoire_id, forme, dosage, conditionnement, unites_par_boite, prix_public_ref, classe_reglementaire_ref, tva_bp, remboursable, statut_amm, source, version_ref |
| `ref_dci`, `ref_laboratories`, `ref_forms` | référentiels normalisés |
| `ref_barcodes` | ref_product_id, code (EAN/GTIN) |
| `ref_updates` | id, version, date, nb_ajouts, nb_modifs, fichier_key — journal des mises à jour |
| `products` (**fiche locale**) | id, tenant_id, ref_product_id (NULL si produit local), type (médicament / parapharmacie / dispositif / autre), nom, nom_ar, dci_id, categorie_id, unites_par_boite, vente_fractionnee (bool), prix_vente_boite, prix_vente_unite, tva_bp, cout_moyen_unite (CUMP), seuil_min, stock_cible, emplacement, controlled_class_id, ordonnance_requise, fournisseur_prefere_id, actif, archived_at |
| `product_barcodes` | tenant_id, product_id, code, type (EAN / interne) — **UNIQUE(tenant_id, code)** |
| `product_prices_history` | product_id, ancien, nouveau, motif, user, at |
| `categories` | id, tenant_id, parent_id, libelle_fr, libelle_ar |

### 4.4 Stock (M05) — source unique

| Table | Colonnes principales |
|---|---|
| `lots` | id, tenant_id, product_id, numero_lot, peremption, expiry_precision, cout_unitaire, supplier_id, receipt_line_id, statut (actif / bloqué / périmé / rappelé) | Lot obligatoire pour tout médicament (DP-2).
| `stock_movements` (**immuable**) | id, tenant_id, product_id, lot_id, site_id, type (`RECEIPT`, `SALE`, `SALE_RETURN`, `SUPPLIER_RETURN`, `ADJUST_PLUS`, `ADJUST_MINUS`, `INVENTORY`, `LOSS`, `DESTRUCTION`, `TRANSFER`, `LAB_CONSUMPTION`, `LAB_OUTPUT`), quantite (signée), cout_unitaire, doc_type, doc_id, doc_line_id, motif, user_id, **employee_id**, **approved_by_employee_id** (v1.1), at |
| `stock_levels` (projection) | tenant_id, product_id, lot_id, bucket (`DISPONIBLE`, `EN_CONTROLE`, `QUARANTAINE`, `PERTE`), quantite, maj_at — recalculable exclusivement à partir des mouvements (DP-1) |
| `stock_reservations` | id, tenant_id, product_id, quantite, origine (vente en attente / reliquat), expire_at, created_by, status | Réservation logique par produit, hors buckets physiques ; allocation FEFO au moment de la vente (DP-3).
| `inventory_sessions` | id, tenant_id, type (complet / tournant / zone), statut (ouverte / comptage / validation / clôturée), periméter (JSON), ouverte_par, validee_par |
| `inventory_counts` | session_id, product_id, lot_id, qte_theorique, qte_comptee, ecart, compteur, at |

**Règles PO de stock (DP-1 à DP-4, confirmées le 25/11/2026).** Les quatre états physiques sont `DISPONIBLE`, `EN_CONTROLE`, `QUARANTAINE` et `PERTE` ; les quantités de chaque état sont calculées à partir des mouvements immuables, jamais modifiées directement. Les réservations sont logiques et bloquantes ; le FEFO s'applique à l'allocation lors de la vente. Toute dérogation ADMIN est tracée, ainsi que tout écart au FEFO. Aucun bucket physique `RESERVE` n'est créé.

### 4.5 Ventes & caisse (M02, M02b)

| Table | Colonnes principales |
|---|---|
| `cash_registers` | id, tenant_id, device_id, nom |
| `cash_drawers`, `cash_sessions`, `cash_counts`, `cash_count_lines`, `cash_movements`, `cash_transfers`, `drawer_events` | **v1.1 : remplacés par le modèle de caisse nominative** — voir EXT02 §2.5 (session rattachée à un `employee_id`, comptage par coupures et à l'aveugle, écarts par mode, validation par un tiers, mouvements immuables) |
| `sales` | id, tenant_id, numero (séquence), type (ticket / facture), cash_session_id, customer_id, prescription_id, statut (brouillon / en_attente / validée / annulée), total_ht, total_tva, remise, timbre, total_ttc, paye, reste, **seller_employee_id**, **cashier_employee_id**, **approved_by_employee_id** (v1.1), started_at, validated_at |
| `sale_lines` | id, sale_id, product_id, lot_id, quantite_unites, prix_unitaire, remise_bp, tva_bp, total, cout_unitaire (figé → marge exacte) |
| `sale_returns` / `sale_return_lines` | retour lié à la vente d'origine, motif, réintégration stock (oui/non selon état) |
| `payments` | id, tenant_id, direction (in / out), tiers_type, tiers_id, mode (espèces, carte, chèque, virement, traite, crédit), montant, date, instrument_id, session_id, statut |
| `payment_allocations` | payment_id, document_type, document_id, montant (lettrage) |
| `prescriptions` | id, tenant_id, customer_id, prescripteur_nom, prescripteur_numero, date, numero, scan_key, statut |

### 4.6 Achats & commandes (M03, M11)

| Table | Colonnes principales |
|---|---|
| `purchase_orders` | id, tenant_id, numero, supplier_id, statut (brouillon / proposé_IA / validé / envoyé / partiellement_reçu / reçu / annulé), source (manuel / IA), canal_envoi, envoye_at, valide_par, total_estime |
| `purchase_order_lines` | po_id, product_id, qte_suggeree, qte_commandee, prix_estime, remise_bp, justification (JSON IA) |
| `po_suggestions` | id, tenant_id, run_id, product_id, besoin_calcule, couverture_jours, risque_rupture, fournisseur_recommande, alternatives (JSON), statut |
| `goods_receipts` | id, tenant_id, numero, supplier_id, po_id, bl_numero, date, statut (brouillon / contrôle / validée) |
| `goods_receipt_lines` | receipt_id, product_id, qte_commandee, qte_recue, qte_gratuite, numero_lot, peremption, prix_achat, remise_bp, ecart_prix_flag |
| `supplier_invoices` | id, tenant_id, supplier_id, numero_fournisseur, date, echeance, total_ht, tva, timbre, total_ttc, statut (brouillon / validée / partiellement_payée / payée / avoir), receipt_ids |
| `supplier_returns` | retour fournisseur (périmés, erreurs), avoir attendu |

### 4.7 Tiers (M06, M07)

| Table | Colonnes principales |
|---|---|
| `suppliers` | id, tenant_id, code, raison_sociale, matricule_fiscal, type (PCT, grossiste, laboratoire), contacts (JSON), email_commandes, whatsapp, delai_livraison_j, jours_commande, conditions_paiement_j, remise_defaut_bp, franco, actif |
| `supplier_products` | supplier_id, product_id, ref_fournisseur, dernier_prix, remise_bp, delai_j, disponible |
| `customers` | id, tenant_id, code, type (particulier / entreprise / convention), nom, telephone, email, adresse, cin_optionnel, plafond_credit, fidelite_active, statut, `is_walk_in` (client de passage unique) |
| `customer_treatments` | id, customer_id, product_id, posologie_jours, derniere_dispensation, prochaine_prevue, consent_id (obligatoire), actif |
| `loyalty_ledger` | customer_id, sale_id, points (signés), motif, at |
| `party_ledger` (grand livre tiers) | id, tenant_id, tiers_type, tiers_id, date, type (facture, avoir, paiement, rejet_cheque, ajustement), document_id, debit, credit — **solde = Σ(débit − crédit)** |

### 4.8 Finance & instruments (M08)

| Table | Colonnes principales |
|---|---|
| `bank_accounts` | id, tenant_id, banque, rib, libelle |
| `payment_instruments` | id, tenant_id, type (chèque / traite / effet), direction (reçu / émis), numero, banque, tireur, beneficiaire, tiers_type, tiers_id, montant, date_emission, date_echeance, statut, bank_account_id, remise_id |
| `instrument_status_history` | instrument_id, ancien, nouveau, date, motif, user |
| `bank_deposits` | id, tenant_id, bank_account_id, date, total, bordereau_key |
| `due_dates` (échéancier unifié) | id, tenant_id, sens (à_encaisser / à_payer), origine (facture / instrument / crédit), origine_id, tiers, montant, date, statut |
| `expenses` | id, tenant_id, categorie, montant, mode, session_id, justificatif_key |

États d'un chèque reçu : `EN_PORTEFEUILLE → REMIS_EN_BANQUE → ENCAISSÉ` ou `→ REJETÉ → (RÉPRÉSENTÉ | RECOUVREMENT | RÉGULARISÉ)` ; `ANNULÉ` uniquement avant remise.
Chèque/traite émis : `ÉMIS → REMIS_FOURNISSEUR → DÉBITÉ` ou `→ REJETÉ`.

### 4.9 Réglementés (M09)

| Table | Colonnes principales |
|---|---|
| `controlled_classes` | id, tenant_id (NULL = modèle national), code (ex. Tableau A/B/C **[À VALIDER]**), libelle, ordonnance_obligatoire, duree_conservation_ordonnance, duree_max_prescription_j, registre_obligatoire, regles (JSON) |
| `controlled_register` | id, tenant_id, class_id, numero_ordre (séquence continue par classe/an), product_id, lot_id, movement_id, sens, quantite, solde_apres, prescription_id, patient_ref, prescripteur, at, user |
| `controlled_rectifications` | register_id, motif, contre_ecriture_id, valide_par |
| `controlled_periodic_reports` | id, tenant_id, class_id, periode, statut, fichier_key |

### 4.10 Péremptions & pertes (M10)

| Table | Colonnes principales |
|---|---|
| `expiry_alert_rules` | tenant_id, seuils_jours (ex. [180,90,60,30,7]), canaux |
| `loss_records` | id, tenant_id, lot_id, quantite, motif (périmé / casse / vol / rappel / autre), valeur_cout, statut, movement_id |
| `destruction_records` | id, tenant_id, date, methode, prestataire, pv_key, lignes (loss_ids), valide_par |

### 4.11 IA (M13)

| Table | Colonnes principales |
|---|---|
| `ai_runs` | id, tenant_id, type (batch_nuit, à_la_demande), debut, fin, version_regles, statut |
| `ai_recommendations` | id, tenant_id, run_id, categorie, priorite (P1-P4), titre, probleme, donnees_utilisees (JSON), action_proposee (JSON), impact_estime_millimes, confiance, module_cible, lien, statut (nouvelle / vue / acceptée / rejetée / expirée), traite_par, traite_at, motif_rejet |
| `ai_conversations` / `ai_messages` | id, tenant_id, user_id, role, contenu, outils_appelés (JSON), tokens, at |
| `ai_feedback` | recommendation_id, utile (bool), commentaire |
| `metrics_daily` (couche métriques) | tenant_id, date, cle (ca, marge, nb_ventes…), dimension (produit / caissier / mode…), valeur |
| `product_demand_stats` | tenant_id, product_id, moyenne_7/30/90j, ecart_type, tendance, saisonnalite, derniere_maj |

### 4.12 Relations principales (résumé ERD)

```
tenants 1─n memberships n─1 users ; memberships n─1 roles 1─n role_permissions
ref_products 1─n products (par tenant) 1─n product_barcodes
products 1─n lots 1─n stock_movements ; stock_movements n─1 (sale_line | receipt_line | loss | inventory_count)
sales 1─n sale_lines n─1 lots ; sales n─1 customers ; sales n─1 cash_sessions ; sales n─0..1 prescriptions
purchase_orders 1─n po_lines ; goods_receipts n─0..1 purchase_orders ; receipts 1─n receipt_lines → lots
supplier_invoices n─n goods_receipts ; payments 1─n payment_allocations n─1 (sales | supplier_invoices)
payments 0..1─1 payment_instruments 1─n instrument_status_history
party_ledger ← sales, supplier_invoices, payments, instruments (rejets)
controlled_register 1─1 stock_movements (pour produits réglementés) ; n─0..1 prescriptions
customers 1─n customer_treatments n─1 consents
ai_recommendations → lien vers n'importe quelle entité (module_cible + entity_id)
```

---

## 5. Relations et flux inter-modules

### 5.1 Flux « Vente au comptoir »

1. Session de caisse **nominative** ouverte par l'encaissant (EXT02 §7) ; le vendeur-conseil peut préparer la vente sous son propre identifiant (changement d'opérateur par PIN).
2. Scan / recherche → produit local ; l'allocation **FEFO** réserve les lots (lot périmé ou bloqué exclu).
3. Contrôles : stock suffisant, classe réglementaire (ordonnance requise ?), plafond de remise du rôle, interactions/doublons de DCI dans le panier (alerte informative).
4. Paiement : un ou plusieurs modes ; crédit → contrôle plafond client ; chèque → création `payment_instrument` (EN_PORTEFEUILLE).
5. **Transaction atomique** (`D1 batch`) : vente validée + lignes + mouvements `SALE` + niveaux de stock + paiements + écriture `party_ledger` (si crédit) + registre réglementé (si concerné) + mouvement de caisse + audit + événement `sale.completed`.
6. Impression ticket/facture ; asynchrone : points fidélité, métriques, alertes de seuil, notification WhatsApp gérant (si paramétrée).

### 5.2 Flux « Retour / annulation »

- Annulation possible uniquement avant clôture de session et par un rôle autorisé → vente `annulée` + contre-mouvements.
- Après clôture : **avoir** (sale_return) avec réintégration en stock du lot d'origine si état conforme, sinon mouvement `LOSS`.
- Remboursement : mouvement de caisse sortant ou crédit client.

### 5.3 Flux « Approvisionnement » (M11 → M03 → M05 → M06/M08)

```
Analyse IA / manuel → Suggestions (po_suggestions)
   → Pharmacien ajuste et VALIDE → Bon de commande (validé)
   → Envoi email / WhatsApp (après confirmation explicite) → statut « envoyé »
   → Réception (BL) : contrôle qtés, lots, péremptions, prix (écarts signalés)
   → Validation réception → mouvements RECEIPT + création lots + recalcul CUMP
   → Facture fournisseur (rapprochement BC ↔ BL ↔ facture) → party_ledger (crédit fournisseur)
   → Échéance générée (due_dates) → Paiement (espèces / chèque / traite / virement) → lettrage
```

### 5.4 Flux « Chèque client »

`Réception (vente / règlement dette)` → `EN_PORTEFEUILLE` → bordereau de remise `REMIS_EN_BANQUE` →
`ENCAISSÉ` (solde) ou `REJETÉ` → écriture `party_ledger` de rejet (la créance réapparaît), alerte P1,
blocage optionnel du crédit client, suivi de régularisation.

### 5.5 Flux « Péremption »

Analyse quotidienne des lots → alertes aux seuils → recommandations IA (promotion, retour fournisseur,
transfert, priorisation FEFO) → à échéance : lot `périmé` (bloqué) → retrait (`LOSS`) → regroupement
pour destruction (PV) ou retour fournisseur (avoir attendu).

### 5.6 Flux « Inventaire »

Ouverture session (photo théorique) → comptage tablette (scan + quantité, par lot) → calcul des écarts →
revue (recomptage si écart > seuil) → validation par pharmacien → mouvements `INVENTORY` motivés → audit.
Les ventes restent possibles pendant l'inventaire tournant (les mouvements postérieurs à la photo sont intégrés).

### 5.7 Matrice d'impact inter-modules

| Événement | Stock | Finance / tiers | Réglementé | Caisse | Métriques / IA |
|---|---|---|---|---|---|
| Vente validée | − lots | créance si crédit | sortie registre | + encaissement | CA, marge, demande |
| Retour client | + lot (ou perte) | avoir | entrée registre (rectif.) | − remboursement | CA corrigé |
| Réception validée | + lots, CUMP | — | entrée registre | — | délai fournisseur réel |
| Facture fournisseur | — | dette + échéance | — | — | dettes |
| Paiement fournisseur | — | − dette, instrument | — | − si espèces | trésorerie |
| Chèque rejeté | — | + créance | — | — | risque client |
| Perte / destruction | − lot | charge (valeur coût) | sortie registre | — | pertes |
| Ajustement inventaire | ± lot | écart valorisé | rectification | — | anomalie potentielle |


---

## 6. Architecture technique recommandée

### 6.1 Choix technologiques

| Couche | Recommandation | Justification |
|---|---|---|
| Backend | **Hono + TypeScript** sur Cloudflare Workers | Edge, latence faible en Tunisie (PoP Tunis/Europe), coûts maîtrisés, déploiement simple |
| Validation | **zod** (schémas partagés front/back) | Un seul contrat d'API, erreurs cohérentes |
| Base de données | **Cloudflare D1** (SQLite) + migrations versionnées | Relationnel, transactions atomiques via `batch()`, Time Travel 30 j |
| Fichiers | **R2** : PDF factures, PV, scans d'ordonnances, imports, exports, sauvegardes | Stockage objet, URL signées |
| Frontend | **SPA TypeScript compilée par Vite** (Preact ou React — **[À DÉCIDER]**) + Tailwind, servie en statique par le Worker | Caisse très réactive, état local riche (ventes en attente, hors-ligne) |
| i18n | Fichiers de clés `fr.json` / `ar.json` (puis `en.json`), `dir="rtl|ltr"` dynamique, CSS en propriétés logiques (`margin-inline-start`…) | RTL natif sans duplication de feuilles de style |
| Chiffres & dates | `Intl.NumberFormat` / `Intl.DateTimeFormat` ; chiffres latins par défaut en AR (paramétrable) | Les captures mélangent chiffres arabes-indiens et latins → source d'erreurs de lecture |
| PWA | Service Worker, manifest, IndexedDB (catalogue + file d'opérations hors-ligne) | Installation poste/tablette, résilience réseau |
| Impression | Tickets 80 mm via impression navigateur (CSS `@page`) au Lot 3 ; pont local ESC/POS optionnel (tiroir-caisse) **[À DÉCIDER]** | Le navigateur ne peut pas ouvrir un tiroir-caisse sans pont local |
| PDF | Génération côté navigateur (HTML → impression/PDF) ; génération serveur légère pour envois automatiques | Respect des limites CPU des Workers |
| Excel | Export CSV/XLSX côté navigateur (SheetJS) ; import XLSX analysé côté navigateur puis envoyé en JSON validé | Pas de traitement lourd côté Worker |
| LLM | API compatible OpenAI (modèle configurable), appelée **uniquement côté serveur** | Clés jamais exposées au frontend |
| Email | Resend ou SendGrid (API REST) | Envoi BC, rapports |
| WhatsApp | **WhatsApp Business Cloud API (Meta)** ; mode de repli : lien `wa.me` pré-rempli ouvert par l'utilisateur | `whatsapp-web.js` (vu dans les captures) est non officiel, contraire aux CGU et incompatible SaaS |

### 6.2 Organisation du code (monorepo)

```
webapp/
├── src/
│   ├── index.tsx                 # Entrée Hono : middlewares, montage des routes, statique
│   ├── core/                     # db (helpers batch), ids (ULID), money, errors, audit,
│   │                             # sequences, events (outbox), i18n serveur, permissions
│   ├── middleware/               # auth, tenant, rbac, idempotency, rateLimit, errorHandler
│   ├── modules/
│   │   ├── platform/  catalog/  stock/  sales/  cash/  purchases/  orders/
│   │   ├── suppliers/  customers/  finance/  controlled/  expiry/
│   │   ├── reports/  metrics/  notifications/  ai/
│   │   │   └── <module>/{routes.ts, service.ts, repo.ts, schemas.ts, events.ts, tests/}
│   └── shared/                   # types et schémas zod partagés avec le frontend
├── frontend/                     # SPA (pages, composants, stores, i18n, PWA)
├── migrations/                   # 0001_platform.sql, 0002_catalog.sql, …
├── tests/                        # intégration (API), isolation tenant, calculs
└── docs/                         # ce dossier de conception, ADR, guides
```

Règle : `routes` (HTTP, validation) → `service` (règles métier, transaction) → `repo` (SQL paramétré
filtré par `tenant_id`). Seuls les services publient des événements.

### 6.3 Architecture API

- REST JSON versionnée : `/api/v1/...` ; pagination par curseur ; filtres explicites.
- Exemples :
  - `GET /api/v1/catalog/products?q=&barcode=&dci=&cursor=` · `POST /api/v1/catalog/import/preview` · `POST /api/v1/catalog/import/apply`
  - `POST /api/v1/sales` (brouillon) · `POST /api/v1/sales/:id/lines` · `POST /api/v1/sales/:id/validate` · `POST /api/v1/sales/:id/return`
  - `POST /api/v1/cash/sessions` · `POST /api/v1/cash/sessions/:id/close`
  - `POST /api/v1/receipts/:id/validate` · `POST /api/v1/supplier-invoices/:id/validate`
  - `GET /api/v1/orders/suggestions` · `POST /api/v1/orders/:id/approve` · `POST /api/v1/orders/:id/send`
  - `POST /api/v1/finance/instruments/:id/transition` (`{to: "REJETE", motif}`)
  - `GET /api/v1/controlled/register?class=&from=&to=` · `GET /api/v1/reports/:code?format=xlsx`
  - `GET /api/v1/ai/recommendations` · `POST /api/v1/ai/recommendations/:id/accept|reject` · `POST /api/v1/ai/chat`
- **Idempotence** : en-tête `Idempotency-Key` obligatoire sur toutes les validations (vente, paiement, réception) → aucun double encaissement en cas de double clic ou de reprise hors-ligne.
- **Concurrence** : verrou optimiste (`version`) ; décrément de stock conditionnel (`UPDATE … WHERE quantite >= ?`) dans le même `batch`.
- **Erreurs normalisées** : `{ error: { code: "STOCK_INSUFFICIENT", message_key, details } }` traduites côté client.

### 6.4 Multi-tenant (SaaS)

| Option | Description | Avantages | Inconvénients |
|---|---|---|---|
| A. Base partagée + `tenant_id` | Une base D1, filtrage systématique | Simple, migrations uniques | Isolation logique seulement ; limite de taille D1 (10 Go) à surveiller |
| B. **Une base D1 par pharmacie** + base « plateforme » | Routage par tenant | Isolation physique, sauvegarde/restauration/suppression par client, pas de fuite inter-tenant possible | Migrations à appliquer sur N bases, outillage nécessaire ; binding dynamique à étudier selon le mode d'hébergement |

**Décision PO D3 — option B arbitrée** : chaque pharmacie dispose de sa **propre base de données physiquement distincte**,
avec une base « plateforme » séparée pour les fonctions globales. Plusieurs bases de pharmacies peuvent résider sur un
même serveur : l'isolation physique des bases n'impose pas un serveur physique par pharmacie. Les sauvegardes,
restaurations et opérations de cycle de vie doivent pouvoir être conduites indépendamment par pharmacie.

Le `tenant_id` reste exigé dans les données métier et les contrôles d'accès comme défense en profondeur, mais ne remplace
pas la séparation physique des bases. Cette décision s'applique au modèle SaaS et doit être respectée dans le déploiement
On-Premises. L'option A (base métier partagée entre pharmacies) est conservée uniquement comme trace historique, pas comme
architecture autorisée. La couche d'accès doit router vers la base correspondant au tenant authentifié.

### 6.5 Tâches périodiques

Le mode d'hébergement géré (« Hosted ») ne permet ni cron (`triggers`) ni KV. Les traitements
périodiques (analyse IA nocturne, alertes de péremption, échéances, sauvegarde logique) sont donc
exécutés en **rattrapage paresseux** : une table `job_runs (tenant_id, job, last_run_at)` est consultée
lors des requêtes ; si un job est en retard, il est lancé via `waitUntil()` par lots courts. En cas de
déploiement sur compte Cloudflare propre (BYOK), un cron peut s'ajouter sans changer le code métier.
Le choix du mode de déploiement est un point de décision (§11).

### 6.6 Sauvegardes et reprise

- D1 Time Travel (restauration à la minute sur 30 jours).
- Export logique quotidien par pharmacie vers R2 (JSON compressé), conservation paramétrable (ex. 90 j).
- Export « réversibilité » à la demande du client (toutes ses données, format ouvert).
- Test de restauration documenté et répété à chaque lot majeur.

### 6.7 Mode hors-ligne (caisse)

- Catalogue, prix, stock indicatif et paramètres mis en cache (IndexedDB), synchronisation différentielle.
- Ventes hors-ligne enregistrées localement avec ULID + clé d'idempotence, numérotées provisoirement,
  synchronisées à la reconnexion ; conflits (stock insuffisant) remontés comme anomalies à traiter.
- Restrictions hors-ligne : pas de crédit au-delà du plafond connu, pas de réglementés **[À DÉCIDER]**.
- Livré au Lot 14, mais **prévu dès le Lot 0** (ULID, idempotence) pour éviter une refonte.

---

## 7. Architecture du moteur PHARM'OS AI

### 7.1 Principe directeur

> **Calculer de façon déterministe, expliquer avec le LLM, décider par l'humain.**

Les chiffres (besoins, risques, montants) ne sont **jamais** produits par le modèle de langage :
ils proviennent d'algorithmes testables. Le LLM reformule, explique, répond aux questions et
orchestre des outils en **lecture seule**. Toute action passe par un brouillon soumis à validation.

### 7.2 Composants

```
┌───────────────────────────────────────────────────────────────────────┐
│ 1. COLLECTE        couche métriques (metrics_daily, product_demand_   │
│                    stats), événements de domaine, journal d'audit     │
├───────────────────────────────────────────────────────────────────────┤
│ 2. ANALYSEURS      (déterministes, versionnés, testés unitairement)   │
│   • Péremption   • Rupture/prévision   • Faible rotation              │
│   • Réapprovisionnement   • Échéances   • Renouvellement traitements  │
│   • Performance commerciale   • Anomalies/fraude   • Qualité données  │
├───────────────────────────────────────────────────────────────────────┤
│ 3. MOTEUR DE RECOMMANDATIONS  normalisation, priorisation P1–P4,      │
│   dé-duplication, expiration, impact estimé, lien vers module         │
├───────────────────────────────────────────────────────────────────────┤
│ 4. COUCHE LLM      explication en FR/AR, conversation, outils RO :    │
│   search_products, get_stock, get_sales_stats, list_recommendations,  │
│   get_due_dates, get_supplier_terms, draft_purchase_order (brouillon) │
├───────────────────────────────────────────────────────────────────────┤
│ 5. GARDE-FOUS      pseudonymisation, filtrage par permissions de       │
│   l'utilisateur, aucun outil d'écriture sensible, journalisation      │
│   complète des prompts/outils, budget de tokens par pharmacie         │
├───────────────────────────────────────────────────────────────────────┤
│ 6. BOUCLE DE RETOUR  acceptée / rejetée + motif → ajustement des      │
│   seuils et pondérations par pharmacie                                │
└───────────────────────────────────────────────────────────────────────┘
```

### 7.3 Analyseurs (logique métier)

| Analyseur | Méthode | Exemple de sortie |
|---|---|---|
| **A. Péremption** | Pour chaque lot : `ventes_projetées_avant_péremption = demande_moy_jour × jours_restants` ; risque = stock_lot − ventes projetées | « Lot X : 40 unités, 18 vendables avant le 30/01 → 22 à risque (154,000 TND) » |
| **B. Rupture** | Demande lissée (moyenne exponentielle 7/30/90 j, saisonnalité hebdomadaire) ; couverture = stock / demande ; rupture si couverture < délai fournisseur + marge | « Rupture estimée dans 3 j ; délai fournisseur 2 j » |
| **C. Faible rotation** | Jours depuis dernière vente, rotation = sorties 90 j / stock moyen, valeur immobilisée | « 312 références sans vente depuis 120 j = 8 450 TND immobilisés » |
| **D. Réapprovisionnement** | Point de commande = `D × (L + R) + SS`, avec `SS = z × σ × √(L+R)` (z selon niveau de service paramétrable, ex. 95 %) ; quantité = cible − stock − en commande, arrondie au conditionnement ; plafonnée par la péremption probable | BC proposé par fournisseur |
| **Comparaison fournisseurs** | Coût net (prix − remise + UG), délai, taux de service historique, fiabilité péremptions | « Fournisseur B : −4,2 % net, délai +1 j » |
| **E. Échéances** | Calendrier `due_dates` + instruments ; projection de trésorerie J+30 | « 3 chèques fournisseurs (12 400 TND) débités sous 7 j ; trésorerie projetée négative le 12/11 » |
| **F. Renouvellement** | Uniquement si consentement actif : `prochaine_prevue = dernière_dispensation + durée_traitement` | « 6 patients à renouveler cette semaine » (sans contenu médical dans les notifications externes) |
| **G. Performance** | Évolution CA/marge par famille, produit, caissier, heure ; panier moyen | « Marge parapharmacie −3 pts sur 30 j » |
| **H. Anomalies** | Règles + écarts statistiques (z-score) : vente sous le coût, coût nul, remises atypiques par caissier, annulations après paiement, ouvertures tiroir sans vente, écarts de caisse répétés, ajustements de stock fréquents, solde réglementé théorique ≠ physique, ventes sans stock, doublons de références | « Caissier Y : 9 annulations post-encaissement cette semaine (moyenne 1) » |
| **Qualité des données** | Coûts manquants, codes-barres internes sur produits ayant un EAN, péremptions absentes | « 2 140 produits sans coût d'achat : marges non fiables » |
| **I à O. RH** (v1.1) | Couverture des shifts, conflits, sous-effectif, anomalies de caisse, tendances de productivité, besoins de formation, répartition de la charge | Voir EXT02 §9 (audiences restreintes, aucune donnée N3/N4 transmise au LLM) |

### 7.4 Format normalisé d'une recommandation

```json
{
  "id": "01J…",
  "categorie": "RUPTURE",
  "priorite": "P1",
  "titre": "Rupture probable : AMOXIL 500 mg 12 gél.",
  "probleme": "Stock 4 boîtes, demande moyenne 2,1/j, délai fournisseur 2 j.",
  "donnees_utilisees": {"stock": 4, "demande_30j": 63, "delai_j": 2, "periode": "2026-10-04/2026-11-02"},
  "action_proposee": {"type": "DRAFT_PO_LINE", "supplier_id": "…", "quantite": 30},
  "impact": {"type": "CA_PRESERVE", "montant_millimes": 270000, "confiance": 0.82},
  "module_cible": "orders",
  "lien": "/orders/suggest?product=…",
  "expire_le": "2026-11-05"
}
```

Chaque recommandation affiche donc : **problème détecté, données utilisées, priorité, action
proposée, gain ou risque potentiel**, plus son niveau de confiance et sa date d'expiration.

Priorités : **P1** risque immédiat (rupture < délai, chèque rejeté, anomalie réglementée, lot périmé en vente),
**P2** sous 7 jours, **P3** optimisation, **P4** information.

### 7.5 Règles de sécurité de l'IA

1. Aucune écriture hors brouillons (`purchase_orders.statut = proposé_IA`, notes) — garantie au niveau du
   code des outils, pas seulement du prompt.
2. L'IA voit uniquement ce que l'utilisateur connecté a le droit de voir.
3. Données envoyées au LLM **pseudonymisées** : pas de nom, téléphone ni diagnostic de patient ; agrégats privilégiés.
4. Pas de conseil thérapeutique ni de posologie générée : l'assistant est un outil de **gestion**. Les
   alertes de doublon de DCI sont des signaux informatifs issus de règles, pas un avis médical.
5. Chaque réponse cite ses sources (indicateurs, période) ; si une donnée manque, l'assistant le dit.
6. Le module fonctionne **sans LLM** (analyseurs + recommandations) : la panne ou la désactivation
   du LLM n'affecte que la conversation.


---

## 8. Avatar PHARM'OS AI : fonctionnement

### 8.1 Rôle et principes

L'avatar est la **façade** du moteur IA. Il n'apporte aucune capacité supplémentaire : il rend les
recommandations visibles, compréhensibles et actionnables. Règle absolue : **il ne bloque jamais**
le parcours de vente (pas de fenêtre modale, pas de capture du focus clavier).

### 8.2 Rendu technique

| Option | Description | Poids | Recommandation |
|---|---|---|---|
| Rendu 3D temps réel (three.js + modèle glTF) | Animations idle, parole, attention | 1,5-4 Mo + GPU | Phase 2, désactivable |
| **Rendu 3D pré-calculé** (séquences vidéo WebM alpha / sprites, ou Lottie) | Personnage 3D modélisé, animations exportées | 200-600 Ko | **Recommandé pour la v1** |
| Icône animée CSS | Repli | < 10 Ko | Mode « faible consommation » et postes anciens |

États d'animation : `repos` (respiration légère), `notification` (signe discret + pastille de couleur selon
priorité), `écoute`, `réflexion`, `réponse`, `alerte P1` (halo, sans son par défaut). Respect de
`prefers-reduced-motion`. Création graphique du personnage : **[À DÉCIDER]** (identité visuelle APEX AI DEV,
neutralité, tenue de pharmacien ou non).

### 8.3 Comportement par contexte

| Contexte | Comportement |
|---|---|
| Écran de vente | Bulle réduite dans un coin ; seules les alertes liées au panier (lot proche péremption, produit réglementé, stock bas) s'affichent en bandeau non bloquant ; le reste est différé |
| Tableau de bord | Résumé quotidien « 3 priorités du jour » à l'ouverture |
| Autres modules | Recommandations du module courant en tête du panneau |
| Fin de session de caisse | Synthèse des anomalies de la session |

### 8.4 Panneau conversationnel

- Ouverture par clic ou raccourci (`Ctrl+Espace`), panneau latéral redimensionnable, FR/AR.
- Onglets : **Priorités** (liste des recommandations triées), **Conversation**, **Historique**.
- Chaque carte de recommandation : titre, problème, données (dépliables), impact, priorité, et trois boutons :
  `Ouvrir le module` (navigation profonde avec filtres pré-appliqués), `Préparer l'action` (crée un
  brouillon : BC, liste de retrait, relance), `Ignorer` (motif demandé → apprentissage).
- Questions types : « Quels produits vont manquer cette semaine ? », « Pourquoi la marge a baissé ? »,
  « Quels chèques arrivent à échéance ? », « Prépare la commande chez [fournisseur] ».
- Les réponses citent leurs données et proposent des liens ; aucune action n'est exécutée depuis la conversation
  sans passer par l'écran du module et sa validation.
- Entrée vocale et synthèse vocale : phase ultérieure.

---

## 9. Rôles, permissions, sécurité, conformité

### 9.1 Rôles par défaut (personnalisables)

| Permission (extrait) | Titulaire | Adjoint | Préparateur | Caissier | Achats/stock | Comptable | Auditeur |
|---|---|---|---|---|---|---|---|
| Vendre | ✔ | ✔ | ✔ | ✔ | — | — | — |
| Dispenser un réglementé | ✔ | ✔ | **[À VALIDER]** | — | — | — | — |
| Remise max | illimitée | 15 % | 5 % | 5 % | — | — | — |
| Annuler une vente | ✔ | ✔ | PIN resp. | PIN resp. | — | — | — |
| Vente à crédit | ✔ | ✔ | plafond | plafond | — | — | — |
| Ouvrir/clôturer la caisse | ✔ | ✔ | ✔ | ✔ | — | — | — |
| Valider écart de caisse | ✔ | ✔ | — | — | — | — | — |
| Réception / facture fournisseur | ✔ | ✔ | saisie | — | ✔ | lecture | lecture |
| Ajustement de stock / inventaire | ✔ | ✔ | comptage | — | ✔ (validation titulaire > seuil) | — | lecture |
| Modifier prix | ✔ | ✔ | — | — | proposition | — | — |
| Valider / envoyer un BC | ✔ | ✔ | — | — | brouillon | — | — |
| Paiements fournisseurs, instruments | ✔ | ✔ | — | — | — | ✔ | lecture |
| Rapports financiers | ✔ | ✔ | — | — | — | ✔ | ✔ |
| Données patients (traitements) | ✔ | ✔ | consultation au comptoir | — | — | — | — |
| Utilisateurs & paramètres | ✔ | — | — | — | — | — | — |
| Journal d'audit | ✔ | lecture | — | — | — | — | ✔ |

**v1.1** : ce tableau définit les permissions **par rôle**. Les droits effectifs ajoutent les habilitations,
les qualifications valides, le statut et la présence du collaborateur (EXT02 §1.2). Les rôles Responsable
d'équipe, Gestionnaire RH et Collaborateur (libre-service), ainsi que les permissions `hr.*` et `cash.session.*`,
sont détaillés dans EXT02 §11.

Permissions granulaires sous la forme `module.action[:portée]` (ex. `sales.discount:max=500`),
évaluées côté serveur à chaque requête ; l'interface masque ce qui n'est pas autorisé mais
**n'est jamais la seule barrière**.

### 9.2 Authentification et sessions

- Email/téléphone + mot de passe (hachage PBKDF2 via Web Crypto ou Argon2 si disponible), MFA TOTP
  obligatoire pour le titulaire et les administrateurs.
- Cookie de session `HttpOnly; Secure; SameSite=Strict`, rotation, expiration d'inactivité (ex. 8 h caisse,
  30 min modules financiers), révocation à distance, liste des appareils.
- **Changement rapide d'opérateur par PIN** sur un poste de caisse déjà authentifié (chaque vente rattachée au bon caissier).
- Verrouillage progressif après échecs + alerte au titulaire (cf. captures).
- Protection CSRF, en-têtes de sécurité (CSP stricte), limitation de débit.

### 9.3 Journal d'audit et opérations sensibles

- Événements audités : connexions, changements de prix, remises au-delà du standard, annulations,
  retours, ajustements, modifications de paramètres et de rôles, exports, ouverture manuelle du tiroir,
  vidage de panier après scan, opérations réglementées, actions de l'IA acceptées/rejetées.
- Chaînage par hachage (`hash = H(hash_prev + contenu)`) : toute altération est détectable.
- **Pas de suppression** des documents validés : annulation / avoir / contre-écriture / rectification motivée.
- Suppression d'un référentiel (produit, tiers) = archivage ; impossible s'il existe des mouvements.
- **Règle PO d'archivage stock** : aucun produit/lot ne peut être archivé tant qu'une quantité physique subsiste dans l'un des états `DISPONIBLE`, `EN_CONTROLE`, `QUARANTAINE` ou `PERTE` ; l'état `PERTE` reste visible avec quantité et ancienneté en attente de destruction, sans destruction automatique.
- Double validation (« quatre yeux ») paramétrable : ajustements au-delà d'un seuil, écarts de caisse, rectifications réglementées.

### 9.4 Données personnelles et médicales

- Cadre tunisien : **loi organique n° 2004-63** relative à la protection des données à caractère personnel
  et instance **INPDP** — déclaration/autorisation de traitement, notamment pour les données de santé
  **[À VALIDER par un juriste]**. Anticiper le RGPD si clients ou hébergement européens.
- Minimisation : le client de passage reste anonyme ; le suivi de traitement est **optionnel** et soumis à
  un consentement explicite, daté, révocable et prouvé (`consents`).
- Chiffrement en transit (TLS) et au repos (plateforme) ; chiffrement applicatif des champs sensibles
  (téléphone, notes de traitement) **[À DÉCIDER]**.
- Accès du support APEX AI DEV : désactivé par défaut, ouverture temporaire par le titulaire, journalisée.
- Localisation de l'hébergement (exigence éventuelle d'hébergement en Tunisie) **[À VALIDER]**.

### 9.5 Conformité métier tunisienne — à valider avant les lots concernés

| Sujet | Hypothèse de conception | Validation requise |
|---|---|---|
| Tableaux de médicaments réglementés (substances vénéneuses, stupéfiants, psychotropes) | Classes paramétrables, registre continu par classe, conservation des ordonnances | Textes en vigueur, Ordre des pharmaciens, Direction de la Pharmacie et du Médicament (DPM) |
| Registres (forme, mentions, durée de conservation, format papier/numérique) | Registre numérique généré + export PDF/impression | Acceptation légale d'un registre électronique |
| Facturation | Numérotation continue, matricule fiscal, TVA par taux, **droit de timbre** sur factures | Règles fiscales en vigueur, éventuelle **facturation électronique** (TTN / El Fatoora) |
| Prix des médicaments | Prix public réglementé importé du référentiel, modification restreinte | Source officielle des prix (PCT / ministère) |
| Tiers payant CNAM | Hors périmètre v1, modèle prévu (`prescriptions`, conventions) | Décision de périmètre |
| Chèques et effets | Machine à états, échéances, rejets | Pratiques bancaires (délais, certificats de non-paiement) |

---

## 10. Plan de développement séquentiel

Chaque lot est livrable, testé et démontrable. Un lot n'est **terminé** que lorsque la *Definition of Done* est respectée :

> **DoD** : fonctionnalités opérationnelles de bout en bout (aucun bouton factice) · tests
> automatisés des calculs et des règles métier · tests d'isolation tenant · contrôles de permissions
> côté serveur testés · cohérence vérifiée avec les modules dépendants · affichage vérifié desktop,
> tablette, mobile, FR et AR (RTL) · migrations non destructives · audit des opérations sensibles ·
> README et documentation mis à jour · revue avec le propriétaire.

| Lot | Contenu | Dépend de | Critères de recette clés |
|---|---|---|---|
| **0. Socle** | Structure du code, migrations, ULID, montants en millimes, i18n FR/AR + RTL, design system (composants, tableaux, formulaires), gestion d'erreurs, outbox, audit chaîné, séquences de documents, CI de tests | — | Bascule FR↔AR sans rechargement ; tests du socle verts |
| **1. Plateforme** | Tenants, onboarding pharmacie, utilisateurs, rôles/permissions, sessions, MFA, PIN caisse, paramètres, appareils ; **v1.1** : collaborateurs (socle), équipes, postes, affectations, qualifications, habilitations, catalogue des permissions, **moteur de droits effectifs** | 0 | Un utilisateur d'une pharmacie ne peut accéder à aucune donnée d'une autre (tests) ; table de vérité des droits effectifs |
| **2. Catalogue** | Référentiel global + fiche locale, codes-barres, import Excel avec aperçu des différences, historique des prix, catégories | 1 | Import de 25 000 lignes sans doublon ; rapport de différences ; aucun écrasement silencieux |
| **3. Stock & lots** | Grand livre des mouvements, lots, FEFO, niveaux, ajustements motivés, valorisation CUMP | 2 | Niveau de stock = Σ mouvements (test de recalcul) ; stock négatif bloqué |
| **4. Fournisseurs & achats** | Fiches, conditions, réception avec lots/péremptions, contrôle prix, facture fournisseur, retours, grand livre fournisseur | 3 | Réception → stock + CUMP + dette corrects |
| **5. Caisse & ventes** | Sessions de caisse **nominatives** (EXT02 §7 : tiroirs, comptage par coupures et à l'aveugle, écarts, validation par un tiers), écran de vente (clavier/scan, ventes en attente, fractionnement, remises plafonnées, paiements multiples), tickets/factures, retours/avoirs, journal des ventes | 3, 4 (coûts) | Parcours de vente simple < 10 s ; clôture de caisse exacte ; idempotence testée |
| **6. Clients & crédit** | Fiches, plafond, ventes à crédit, règlements et lettrage, relevés, fidélité, consentements, suivi traitements | 5 | Solde client = grand livre ; client de passage sans fidélité |
| **7. Finance & instruments** | Chèques/traites/effets reçus et émis, remises en banque, rejets, échéancier unifié, calendrier, dépenses, trésorerie projetée | 4, 6 | Rejet de chèque → créance rouverte + alerte |
| **8. Péremptions & pertes** | Seuils paramétrables, tableaux à risque, blocage, retraits, destructions avec PV, retours fournisseur | 3, 4 | Lot périmé non vendable ; valeur des pertes exacte |
| **9. Inventaires** | Sessions complètes/tournantes, comptage tablette (scan), écarts, recomptage, validation | 3 | Écarts valorisés ; ventes possibles pendant l'inventaire tournant |
| **9b. Laboratoire** (v1.1) | M16 : préparations, pesées par lot, contrôles indépendants, consommation de stock | 3, 1 (habilitations) | Préparateur ≠ contrôleur ; traçabilité complète |
| **10. Réglementés** | Classes paramétrées (après validation juridique), ordonnances, registre généré, rectifications, états et exports | 3, 5, validation juridique | Registre continu, sans trou ni modification ; solde théorique = stock |
| **10b. RH opérationnelle** (v1.1) | Dossier complet, planning glisser-déposer, rotations, couverture, gardes, pointage, heures sup, congés et soldes, calendrier collectif, export paie, tableau de bord RH | 1, 5, validation du droit du travail (D17) | Voir EXT02 §12.2 |
| **11. Couche métriques, tableau de bord & rapports** | Agrégats, tableau de bord, journaux, rapports caisse/financiers/stock/rentabilité, exports Excel/PDF ; **v1.1** : dimensions collaborateur et équipe | 5–9 | Tous les KPI identiques entre tableau de bord, rapports et journaux |
| **11b. Performance & formation** (v1.1) | Événements qualité, KPI à quatre axes, profils de notation, évaluations, droit de réponse, formations | 10b, 11 | Chaque score explicable et recalculable ; axes jamais fusionnés par défaut |
| **12. Commandes intelligentes** | Statistiques de demande, calcul des besoins, comparaison fournisseurs, BC, validation, envoi email/WhatsApp, suivi, rapprochement à la réception | 4, 11 | Quantités recalculables à la main sur jeu d'essai ; aucun envoi sans validation |
| **13. PHARM'OS AI & avatar** | Analyseurs, centre de recommandations, boucle de retour, conversation LLM avec outils en lecture seule, avatar, notifications ; **v1.1** : analyseurs RH I à O, brouillons de planning | 11, 11b, 12 | Chaque recommandation traçable jusqu'à ses données ; aucune écriture hors brouillons |
| **14. PWA & hors-ligne** | Installation, cache catalogue, ventes hors-ligne, synchronisation et conflits | 5 | Coupure réseau de 30 min sans perte de vente ni doublon |
| **15. Industrialisation SaaS** | Abonnements/licences, facturation APEX, console d'administration éditeur, supervision, sauvegardes/restaurations testées, option base par pharmacie, anglais | tous | Restauration testée ; onboarding d'une pharmacie en < 30 min |

**Prochaine étape (Étape 3)** : rédiger pour chaque lot un **prompt de réalisation indépendant**
(contexte, périmètre exact, schéma SQL du lot, endpoints, écrans, règles, cas de test, DoD), à valider
lot par lot avant développement.

---

## 11. Risques techniques & points de décision

### 11.1 Risques principaux

| Risque | Impact | Mitigation |
|---|---|---|
| Cohérence du stock en accès concurrents (plusieurs caisses) | Élevé | Grand livre immuable, décréments conditionnels en `batch` atomique, idempotence |
| Erreurs d'arrondi monétaire | Élevé | Millimes entiers, règles d'arrondi centralisées et testées |
| Qualité du référentiel (coûts nuls, codes aléatoires, doublons) | Élevé | Import avec aperçu, règles de dédoublonnage, indicateurs qualité de données |
| Réglementation non validée (registres, facturation électronique) | Élevé (légal) | Paramétrage, validation juridique bloquante avant Lots 10 et 11 |
| Limites des Workers (CPU, taille de requête) pour imports, PDF, analyses | Moyen | Traitement côté navigateur, découpage en lots, jobs différés |
| Absence de cron/KV en hébergement géré | Moyen | Rattrapage paresseux (§6.5) ou déploiement sur compte propre |
| Matériel de caisse (tiroir, imprimante thermique, lecteurs) | Moyen | Impression navigateur en v1, pont local optionnel |
| Connectivité instable en officine | Moyen | Conception hors-ligne dès le socle, livraison Lot 14 |
| Hallucinations du LLM | Moyen | Chiffres déterministes, outils en lecture seule, citations des sources |
| Fuite de données inter-pharmacies | Critique | Couche d'accès imposant `tenant_id`, tests d'isolation systématiques, option base dédiée |
| WhatsApp (validation Meta, coût par conversation, modèles approuvés) | Moyen | Cloud API officielle, repli `wa.me` |
| Volume d'écrans (≈ 60) et RTL | Moyen | Design system unique dès le Lot 0, propriétés CSS logiques |

### 11.2 Décisions attendues du propriétaire (APEX AI DEV)

| # | Décision | Options | Recommandation |
|---|---|---|---|
| D1 | Marché et devise de lancement | Tunisie (TND, 3 décimales) / multi-pays | Tunisie, devise paramétrable |
| D2 | Mode d'hébergement | Hébergement géré Genspark / compte Cloudflare d'APEX AI DEV | Compte propre pour un produit commercial (cron, maîtrise, contrats) |
| D3 | Isolation SaaS | Base partagée + tenant_id / base par pharmacie | **ARBITRÉE : base physiquement distincte par pharmacie ; plusieurs bases peuvent résider sur un même serveur.** L'ancienne option partagée est conservée comme trace historique uniquement. |
| D4 | Source du référentiel médicaments | Fichier officiel, base commerciale, saisie progressive, import du parc existant | À identifier : source officielle des AMM et prix en Tunisie |
| D5 | Statut juridique des registres électroniques et facturation électronique | — | Consultation juridique avant Lot 10 |
| D6 | Tiers payant CNAM | Dans la v1 / plus tard | Plus tard, modèle prévu |
| D7 | Fournisseur LLM et hébergement des données IA | OpenAI / Anthropic / autre ; budget mensuel par pharmacie | Fournisseur configurable, données pseudonymisées |
| D8 | Messagerie | WhatsApp Business Cloud API (compte Meta APEX) / liens `wa.me` uniquement | `wa.me` en v1, Cloud API ensuite |
| D9 | Matériel de caisse | Navigateur seul / pont local (tiroir, ESC/POS) | Navigateur en v1 |
| D10 | Hors-ligne | Indispensable dès le lancement / Lot 14 | Prévu dès le socle, livré au Lot 14 |
| D11 | Avatar | Style, personnage, 3D temps réel ou pré-calculé, nom | Pré-calculé, désactivable |
| D12 | Framework frontend | Preact / React | Preact (léger) |
| D13 | Modèle commercial | Mensuel / annuel, par poste ou par officine, modules optionnels | À définir (impacte `subscriptions`) |
| D14 | Reprise de données | Import depuis logiciels existants (lesquels ?) | Prévoir des importeurs Excel génériques au Lot 2 |
| D15 | Multi-officines / dépôt | Dans la v1 / plus tard | Modèle prêt (`sites`), activation plus tard |
| D16 | Chiffres en arabe | Latins (0-9) / arabes-indiens (٠-٩) | Latins par défaut, paramétrable |
| D17 – D26 | Décisions RH (droit du travail, pointage, tiroirs, écarts, score global, paie, qualité de service, présence pharmacien, laboratoire, conservation) | — | Voir EXT02 §13.2 |
| D27 – D40 | Décisions ordonnances (modèle patient, patient non identifié, ordonnance incomplète, saisie prescrit/délivré, spécialité, référentiel, scans, petits effectifs, IA, journal, tiers payant, posologie, hors-ligne, réservation) | — | Voir EXT03 §13.1 |

### 11.4 Extensions en attente de validation

| Extension | Statut | Impact prévu sur ce dossier après validation |
|---|---|---|
| **EXT03 — Ordonnances & prescripteurs** (M17) | Proposition soumise ; arbitrages D27–D40 et exigences R1–R13 à valider | v1.2 : remplacement de `prescriptions` (§4.5) et lien vente ↔ ordonnance ligne à ligne ; ajouts à `sale_lines`, `products`, `customers`, `controlled_register`, `customer_treatments` ; module M17 dans la cartographie ; écrans `/prescriptions`, `/prescribers`, `/analytics/prescriptions` ; analyseurs P1–P5 ; nouveaux lots **5b** (ordonnances, cœur) et **11c** (tableau de bord prescriptions) |

Tant qu'une extension n'est pas validée, les sections de ce dossier restent la référence en vigueur.

### 11.3 Journal des décisions

| Date | Décision | Référence |
|---|---|---|
| 2026-11-03 | Création du dossier de conception v1.0 | — |
| 2026-11-03 | v1.1 : intégration de l'extension n° 02 RH & Team Management (M15, M16), caisses nominatives, droits effectifs, lots 9b, 10b, 11b | EXT02 |
| 2026-11-03 | Dépôt de la proposition d'extension n° 03 Ordonnances & prescripteurs (M17), non intégrée en attente des arbitrages D27–D40 | EXT03 |
