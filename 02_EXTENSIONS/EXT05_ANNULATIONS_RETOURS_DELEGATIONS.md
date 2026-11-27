# PHARM'OS — Extension fonctionnelle n° 05 : Annulations, retours, remboursements et délégation des droits

**Produit** : PHARM'OS — APEX AI DEV
**Version** : 1.0 (proposition) — 2026-11-03
**Statut** : **PROPOSITION TECHNIQUE — AUCUN CODE**. La matrice PO D1–D86 v1.3.5 confirme notamment D61 et D66 **CADUQUES** ; D59, D60, D62–D65 et D67–D71 restent **PROPOSÉES** dans la matrice et ne sont pas globalement arbitrées. D74 et D75, arbitrées dans EXT06, sont propagées ici comme règles de fonctionnement : octroi individuel explicite pour `stock.return.decide`, sans attribution par rôle, et jour calendaire par défaut avec option GARDES/PERMANENTE soumise aux conditions PO. Cette extension reste une proposition technique non autorisée à l'implémentation. Le dossier principal (`../01_CONCEPTION/CONCEPTION_PHARMOS_v1 (1).md` v1.1)
n'est pas modifié : les changements nécessaires sont listés au §3.6 et ne seront reportés qu'après validation.
**Module créé** : **M19 Opérations correctives et délégations** (transverse)
**Base de conception** : dossier principal v1.1 (§5.2 retours, §9.3 audit), **EXT02** (caisses nominatives §7, droits effectifs §1.2),
**EXT03 v1.1** (dossier `03_AUDIT_EXT03_v1.1` : solde des ordonnances §15, réglementés §16, atomicité §19, décision D42),
**EXT04** (régularisation des créances d'organismes).

> Repères : **[À VALIDER Rxx]** = point réglementaire (§8) · **[ARBITRAGE Dxx]** = décision du propriétaire (§10).

---

## Sommaire

1. Architecture fonctionnelle
2. Écrans et workflows (A annulation, B retour, C remboursement / régularisation)
3. Tables et relations
4. Règles métier
5. Permissions et délégation
6. Dépendances avec les autres modules
7. Impacts sur la comptabilité et les stocks
8. Exigences réglementaires
9. Matrice de tests
10. Décisions nécessitant arbitrage
11. Position dans le plan de développement

---

## 1. Architecture fonctionnelle

### 1.1 Ce que le dossier prévoit déjà, et ce qui manque

| Élément existant | Référence | Limite constatée |
|---|---|---|
| Annulation avant clôture de session, avoir après | Dossier §5.2 | Frontière fondée sur la session et non sur la remise du produit ; pas de demande / validation ; pas de délégation |
| Droits `sales.void.own`, `sales.void.any`, `sales.refund`, PIN responsable | EXT02 §7.4 | Pas de distinction entre demander, exécuter et valider ; pas de plafonds par collaborateur ; pas de délégation datée |
| Contre-écritures sur les délivrances, neutralisation du droit au retour | EXT03 v1.1 §2.5, §15, D42 | Sort physique du produit « selon l'état », sans workflow de contrôle ni quarantaine |
| Réglementés : quarantaine, jamais de remise en vente automatique | EXT03 v1.1 §16 (A5) | Pas de procédure détaillée de décision |
| Régularisation des créances d'organismes | EXT04 §2.13 | Renvoie au présent document |

EXT05 unifie ces éléments dans un **moteur d'opérations correctives** unique, appelé par tous les écrans (caisse,
journal des ventes, fiche ordonnance, créances). Aucun écran n'écrit lui-même d'écriture inverse.

### 1.2 Principes structurants

1. **Rien ne s'efface.** La vente d'origine reste intacte ; seul son statut évolue (`ANNULEE`, `PARTIELLEMENT_RETOURNEE`,
   `RETOURNEE`). Chaque effet est neutralisé par une **écriture inverse datée du jour de l'opération corrective**, liée à
   l'écriture d'origine (`reverses_id`) et regroupée par un `correlation_id`.
2. **Trois opérations distinctes, liées à la vente d'origine** :

   | Opération | Question à laquelle elle répond | Effets |
   |---|---|---|
   | **A. Annulation** | La vente n'aurait pas dû exister (erreur de saisie, client parti, double encaissement) | Neutralise **tout** : document, stock, délivrances, registre, paiements, créances |
   | **B. Retour** | Des produits délivrés reviennent à la pharmacie | Avoir sur les lignes retournées ; sort physique décidé par contrôle ; droit à délivrance selon D42 |
   | **C. Remboursement / régularisation** | Qui doit rendre combien à qui, et par quel moyen | Restitution au patient (espèces, carte, crédit, avoir), régularisation de l'organisme payeur |

   A et B produisent un **document correctif** (`corrective_operations`). C est la face financière de A ou de B, ou une
   opération autonome **sans retour de produit** (erreur de prix, double encaissement, geste commercial autorisé).
3. **Pas de double comptabilisation** : pour une ligne de vente, Σ quantités annulées et retournées ≤ quantité vendue ;
   pour chaque payeur, Σ montants restitués ≤ montant payé par ce payeur. Ces bornes sont des **gardes conditionnelles
   dans la transaction**, pas de simples contrôles d'écran (§4.4).
4. **Le stock disponible n'est jamais réalimenté automatiquement.** Un produit revenu entre dans un état non vendable
   (`EN_CONTROLE`) jusqu'à une décision humaine habilitée : réintégration, quarantaine ou perte.
5. **Demander, exécuter et valider sont trois droits différents**, attribuables séparément, avec plafonds, et
   **délégables de façon datée** par le titulaire (§5).
6. **Séparation des tâches** : au-delà des seuils, la personne qui valide n'est ni le demandeur, ni l'auteur de la vente d'origine.
7. **Atomicité et idempotence** identiques à la validation de vente (EXT03 v1.1 §19) : tout ou rien, rejouable sans effet
   double, état réel consultable après une coupure.
8. **La date compte** : une correction produit ses effets à sa propre date, sans jamais modifier une période close
   (session de caisse clôturée, journal exporté, bordereau transmis, inventaire validé).

### 1.3 Vocabulaire

| Terme | Définition |
|---|---|
| **Remise** (du produit) | Moment où le patient a quitté le comptoir avec le produit. Avant la remise, le produit est réputé n'être jamais sorti de la pharmacie. |
| **Annulation avant remise** | Vente validée, produit non remis (client parti, erreur constatée immédiatement). Le produit est physiquement au comptoir. |
| **Annulation après remise** | Vente à neutraliser alors que le produit est sorti : exige un retour physique effectif ou une déclaration motivée de non-retour. |
| **Document correctif** | Opération `ANNULATION` (`ANN-AAAA-nnnnnn`) ou `RETOUR` (`AVR-AAAA-nnnnnn`), numérotée en séquence continue, immuable une fois exécutée. |
| **Demande** | Intention de correction saisie par un collaborateur sans droit d'exécution ; aucun effet tant qu'elle n'est pas validée et exécutée. |
| **Délégation** | Attribution temporaire et plafonnée, par le titulaire, d'un droit qu'il détient, à un collaborateur nommé (§5.3). |

> Le cahier des charges cite « l'annulation avant délivrance ». Une vente non encore **validée** (brouillon, en attente)
> n'a aucun effet : elle est simplement **abandonnée** (statut `ABANDONNEE`, journalisé), sans document correctif. EXT05
> distingue donc : vente non validée (abandon), vente validée non remise (annulation avant remise), vente remise
> (annulation après remise ou retour).

### 1.4 Sous-modules

| Sous-module | Contenu |
|---|---|
| **M19.1 Moteur d'opérations correctives** | Périmètre corrigeable, écritures inverses, gardes, transaction atomique, idempotence |
| **M19.2 Demandes et validations** | Demande, file de validation, validation au PIN ou à distance, expiration, refus motivé |
| **M19.3 Contrôle de réintégration** | États de stock non vendables, check-list, quarantaine, décision, perte |
| **M19.4 Remboursements et régularisations** | Restitution par mode de paiement, crédit client, avoir, régularisation des organismes (EXT04) |
| **M19.5 Délégations** | Délégations datées et plafonnées, révocation, journal, rapport mensuel |
| **M19.6 Traçabilité et états** | Journal des opérations correctives, états, alertes antifraude, rapprochements |

### 1.5 Place dans l'architecture

```
 Caisse / journal des ventes / fiche ordonnance / créances
                │  (demande ou exécution)
                ▼
 M19.2 Demandes & validations ◄── M19.5 Délégations ◄── EXT02 droits effectifs (rôle ∩ habilitations ∩ présence)
                │
                ▼
 M19.1 MOTEUR D'OPÉRATIONS CORRECTIVES (transaction atomique, gardes, idempotence)
   ├─► M02 ventes : statut, document correctif, lignes corrigées
   ├─► M05 stock : mouvements inverses vers EN_CONTROLE (jamais directement en disponible)
   ├─► M17 ordonnances : contre-délivrances + neutralisation / restitution (D42)
   ├─► M09 réglementés : écritures de rectification au registre (séquence EXT03 v1.1 §16)
   ├─► M02b caisse nominative : sortie de caisse dans la session de l'exécutant
   ├─► M08 finance : paiements inverses, crédit client, party_ledger
   ├─► M18 tiers payant (EXT04) : annulation de créance ou régularisation au bordereau
   └─► audit_log + domain_events (outbox)
                │
                ▼
 M19.3 Contrôle de réintégration ─► DISPONIBLE | QUARANTAINE | PERTE (M10)
```

---

## 2. Écrans et workflows

### 2.1 Écrans

| Code | Écran | Route | Usage |
|---|---|---|---|
| X1 | **Fiche vente — panneau « Corriger »** | `/sales/:id` | Vue de ce qui reste corrigeable par ligne et par payeur ; boutons Annuler, Retour, Rembourser (selon droits) ; historique des corrections |
| X2 | Assistant d'annulation | `/sales/:id/void` | Motif, constat de remise, sort des produits, restitution, validation |
| X3 | Assistant de retour | `/sales/:id/return` | Sélection des lignes, quantités et lots, état constaté, droit à délivrance (D42), restitution |
| X4 | Assistant de remboursement autonome | `/sales/:id/refund` | Restitution sans retour de produit (erreur de prix, double encaissement) |
| X5 | Recherche de vente pour correction | `/pos` (touche dédiée) | Scan du ticket (QR), n° de vente, n° d'ordonnance, carte du client |
| X6 | **File des demandes** | `/corrections/requests` | Demandes en attente, âge, montant, demandeur ; validation ou refus motivé ; notification au demandeur |
| X7 | **Zone de contrôle des retours** | `/stock/returns-control` | Produits `EN_CONTROLE` et `QUARANTAINE` : check-list, décision, impression d'étiquette de quarantaine |
| X8 | Journal des opérations correctives | `/corrections` | Liste filtrable (type, motif, collaborateur, montant, période, statut) ; détail complet ; export |
| X9 | **Délégations** | `/settings/delegations` | Création, plafonds, période, révocation, historique, rapport d'utilisation |
| X10 | Mes droits | `/me/permissions` | Ce que le collaborateur peut faire, plafonds restants du jour, délégations reçues |
| X11 | Tableau de bord des corrections | `/analytics/corrections` | Taux d'annulation et de retour, par motif, par poste, par heure ; alertes antifraude |

### 2.2 Panneau « Corriger » de la fiche vente (X1)

```
┌─ VENTE V-2026-004512 — validée le 03/11/2026 10:42 — Caisse 2 — vendeur S*** — encaissant A*** ──────┐
│ Statut : VALIDÉE · Ordonnance ORD-2026-000123 · Organisme : Mutuelle Y (créance CRE-2026-000881, non transmise) │
├──────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ Ligne              Lot      Vendu  Annulé  Retourné  Corrigeable   Payé patient  Payé org.  Remboursable │
│ PRODUIT A 30 cp    L23      2      0       0         2             14,000        6,000      20,000       │
│ PRODUIT B          L07      1      0       1         0             —             —          0,000        │
├──────────────────────────────────────────────────────────────────────────────────────────────────────┤
│ Paiements : espèces 14,000 (session S-118, clôturée) · Remboursé à ce jour : 12,500 (AVR-2026-000034)  │
│ [Annuler la vente]  [Retour de produits]  [Remboursement sans retour]  [Historique (2)]               │
│ Vos droits : retour jusqu'à 50,000 sans validation · annulation : demande seulement                   │
└──────────────────────────────────────────────────────────────────────────────────────────────────────┘
(montants fictifs)
```

Le panneau calcule **côté serveur** les quantités et montants encore corrigeables ; un bouton n'apparaît que si le droit
effectif existe (sinon « Demander »). L'affichage n'est qu'une aide : les gardes sont réévaluées dans la transaction.

### 2.3 Workflow A — Annulation de vente

```
1. Ouverture depuis la fiche vente (X1) ou la caisse (X5) → chargement du périmètre corrigeable
   (refus si la vente est déjà ANNULEE, RETOURNEE, ou a déjà fait l'objet d'un retour : l'annulation
    porte toujours sur la totalité restante ; sinon utiliser le workflow B)
2. Saisie : motif (liste + texte libre obligatoire si « Autre »), constat de remise :
     ○ Produit non remis, présent au comptoir           → annulation AVANT REMISE
     ○ Produit remis puis rapporté maintenant           → annulation APRÈS REMISE avec retour physique
     ○ Produit remis et non rapporté (cas exceptionnel) → annulation APRÈS REMISE sans retour (D63)
3. Contrôles (§4) : droits effectifs, plafonds, délai, séparation des tâches, réglementés, créances, périodes closes
4. Décision du moteur :
     a. Droit d'exécution suffisant           → exécution immédiate (étape 6)
     b. Droit de demande seulement            → DEMANDE créée (étape 5)
     c. Droit d'exécution sous validation     → validation au PIN par un habilité présent, ou DEMANDE
5. DEMANDE : file X6, notification aux valideurs habilités ; expiration paramétrable (défaut : fin de journée) ;
   validation ou refus motivé ; le valideur peut modifier le sort du stock, pas le périmètre
6. EXÉCUTION — transaction atomique (§4.5) :
     document ANN-… ; statut de la vente → ANNULEE ; écritures inverses : stock (vers EN_CONTROLE,
     cf. §4.6), délivrances (EXT03 : annulation = droit restitué), registre réglementé (rectification), créances
     d'organismes (EXT04), paiements et caisse (workflow C), points de fidélité, audit, événements
7. Impression : bon d'annulation (et reçu de remboursement si restitution) ; ticket d'origine marqué « annulé »
   lors de toute réimpression
```

**Annulation avant remise : sort du stock.** Même si le produit n'a jamais quitté le comptoir, aucune annulation ne le
réintègre automatiquement en stock vendable. Le produit passe en `EN_CONTROLE`. Une décision immédiate simplifiée par un
second collaborateur n'est possible que si le pharmacien-administrateur lui a explicitement accordé le droit de
réintégration corrective (D74) ; sinon, le workflow normal de contrôle s'applique. La règle de délai est celle du jour
calendaire (D75), et non l'ancienne fenêtre de 15 minutes (D61, caduque).

### 2.4 Workflow B — Retour partiel ou total après vente

```
1. Ouverture : fiche vente ou scan du ticket ; si le ticket est absent, recherche par patient, ordonnance, date
   (retour sans ticket : droit spécifique `return.without_receipt`, D62)
2. Sélection des lignes : quantité ≤ corrigeable ; lot retourné (pré-rempli avec le lot vendu ; un lot différent
   est refusé : le produit n'est pas celui vendu, sauf décision pharmacien tracée)
3. Constat à réception (pour chaque ligne) : produit physiquement récupéré (obligatoire pour un retour),
   conditionnement scellé / ouvert / endommagé, date de péremption lue, déclaration du patient sur la conservation
4. Droit à délivrance sur l'ordonnance (EXT03 v1.1 D42) :
     ● neutralisation par défaut (le reste à délivrer n'augmente pas)
     ○ restitution (erreur de délivrance avant sortie, etc.) → pharmacien + motif
5. Restitution financière (workflow C) : calcul automatique de la part de chaque payeur à restituer
6. Contrôles, puis exécution immédiate ou demande (mêmes règles que A)
7. EXÉCUTION — transaction atomique :
     document AVR-… ; lignes de retour ; statut de la vente (PARTIELLEMENT_RETOURNEE / RETOURNEE) ;
     mouvements de stock RETOUR_CLIENT vers EN_CONTROLE (jamais vers DISPONIBLE) ; contre-délivrances et ajustement
     de neutralisation ; rectification au registre si réglementé ; régularisations financières ; audit ; événements
8. Le produit rejoint physiquement la zone de contrôle (étiquette imprimée) → contrôle de réintégration (§2.6)
```

### 2.5 Workflow C — Remboursement financier ou régularisation d'un organisme

C est **toujours** calculé par le moteur à partir des paiements d'origine ; le collaborateur choisit le **moyen** de
restitution, jamais le montant dû par payeur.

```
Entrée : montant TTC à neutraliser (issu de A ou B, ou d'une erreur constatée en C autonome)
1. Répartition par payeur, au prorata exact des parts de chaque ligne (EXT04 sale_line_payer_shares) :
     part patient à restituer   = Σ part_patient des lignes / quantités concernées
     part organisme à régulariser = Σ part de chaque organisme concerné
2. Part patient — moyen de restitution (ordre de préférence par défaut, D59) :
     a. même moyen que le paiement d'origine (carte → remboursement carte selon le terminal ; espèces → espèces) ;
     b. espèces depuis la session de caisse OUVERTE de l'exécutant (jamais la session d'origine si elle est clôturée) ;
     c. crédit sur le compte client (diminution de sa dette, ou avoir utilisable) ;
     d. avoir nominatif (bon numéroté, durée de validité paramétrable) ;
   — vente à crédit non encore payée : diminution de la créance client, aucune sortie de caisse ;
   — paiement mixte : restitution répartie en commençant par les moyens non-espèces (D59) ;
   — chèque encore en portefeuille (M08) : restitution du chèque ou annulation de l'instrument, sinon espèces/avoir
3. Part organisme — régularisation (EXT04) selon l'état de la créance :
     non transmise (FACTUREE, A_COMPLETER, A_TRANSMETTRE) → lignes de créance ANNULEES, créance recalculée ;
     transmise, non réglée → ligne de régularisation négative au prochain bordereau (ou avoir à l'organisme si exigé) ;
     déjà réglée           → dette envers l'organisme (party_ledger), régularisée sur un règlement futur ou remboursée
   La part organisme n'est JAMAIS rendue au patient en espèces.
4. Remboursement autonome (sans retour de produit) : motif obligatoire (ERREUR_PRIX, DOUBLE_ENCAISSEMENT,
   ERREUR_MOYEN_PAIEMENT, GESTE_COMMERCIAL) ; aucun mouvement de stock ; aucun effet sur l'ordonnance ;
   la correction de prix produit un avoir de montant (pas de modification de la ligne d'origine)
5. Le remboursement réel par terminal de carte est confirmé par l'exécutant (référence de transaction) ; en l'absence
   d'intégration au terminal, la confirmation est manuelle et tracée (D60)
```

### 2.6 Workflow D — Contrôle de réintégration (M19.3)

```
Produit EN_CONTROLE (lot, quantité, origine ANN/AVR, date d'entrée, délai de décision) → zone X7
Check-list obligatoire (paramétrable par catégorie de produit) :
  ☐ récupération physique confirmée et quantité comptée
  ☐ conditionnement intègre (scellé, notice présente, aucune altération)
  ☐ conditions de conservation conformes (chaîne du froid : refus par défaut si non garantie)
  ☐ date de péremption ≥ seuil minimal de revente (paramétrable, ex. 90 jours)
  ☐ règles produit : non réglementé, non fractionné, non préparation, non dispositif à usage unique ouvert
Décision par un collaborateur identifié auquel le pharmacien-administrateur a explicitement accordé `stock.return.decide` (D74), distinct de celui qui a reçu le retour au-delà du seuil :
  → RÉINTÉGRÉ   : mouvement EN_CONTROLE → DISPONIBLE (même lot)
  → QUARANTAINE : mouvement EN_CONTROLE → QUARANTAINE (avis pharmacien, retour fournisseur, rappel de lot)
  → PERTE       : uniquement après parcours utilisateur complet avec confirmation explicite de la quantité et des conséquences irréversibles (P86) ; création du loss_record (M10), destruction ultérieure (PV)
Délai de décision dépassé (défaut 48 h) → alerte ; aucune réintégration automatique, jamais
Produit réglementé : décision réservée au pharmacien habilité, quatre yeux, écriture au registre ; RÉINTÉGRÉ interdit
  par défaut [À VALIDER R35]
```

### 2.7 Workflow E — Délégation (M19.5)

```
Titulaire → « Nouvelle délégation » : délégataire, droits délégués (parmi ceux qu'il détient et qui sont délégables),
  plafonds (montant par opération, montant cumulé par jour, nombre par jour), périmètre (types de produits exclus,
  réglementés exclus par défaut), période (début, fin obligatoire, maximum paramétrable), motif
  → contrôles : le délégataire possède les habilitations et qualifications requises (EXT02) ; aucun droit non délégable
  → confirmation par PIN ou MFA du titulaire → active à la date de début ; notification au délégataire
  → révocation à tout moment (effet immédiat, sessions de droits invalidées) ; expiration automatique
  → rapport : opérations réalisées sous délégation, consommation des plafonds
```

### 2.8 Workflow F — Coupure de connexion pendant une opération corrective

| Moment | État serveur | Comportement |
|---|---|---|
| Avant réception | Rien | Message « non exécutée » ; nouvel essai avec la **même** clé d'idempotence |
| Pendant la transaction | Tout ou rien | Au retour : `GET /corrections/:key/status` affiche l'état réel |
| Après la transaction, avant la réponse | Exécutée | Le nouvel essai renvoie le résultat initial, sans double remboursement |
| Remboursement carte lancé, réponse du terminal perdue | Opération en `A_CONFIRMER` (part carte) | Le reste de l'opération est exécuté ; la part carte attend une confirmation manuelle (référence du terminal) ou une annulation tracée ; aucune sortie d'espèces de remplacement automatique |
| Hors ligne | — | **Aucune opération corrective hors ligne** (D64) : les plafonds, l'état de la vente et des créances ne sont pas vérifiables ; seule une **demande locale** peut être préparée et sera soumise à la reconnexion |

---

## 3. Tables et relations

Conventions du dossier principal (§4.1) : `tenant_id` partout, ULID, montants en millimes, quantités en plus petite unité,
colonnes d'audit, aucune suppression physique, `employee_id` sur toute écriture humaine. Les tables **(immuable)**
n'acceptent que des insertions (déclencheurs SQLite bloquant `UPDATE`/`DELETE`, comme EXT03 v1.1 §17.3).

### 3.1 Opérations correctives

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `corrective_operations` (immuable après exécution) | id, tenant_id, **numero** (`ANN-…` / `AVR-…` / `RMB-…`), type (`ANNULATION`, `RETOUR`, `REMBOURSEMENT_AUTONOME`), **original_sale_id**, constat_remise (`NON_REMIS`, `REMIS_RETOURNE`, `REMIS_NON_RETOURNE`), motif_code, motif_texte, **requested_by_employee_id**, **executed_by_employee_id**, **approved_by_employee_id** (nullable), approval_method (`AUCUNE`, `PIN_PRESENT`, `DEMANDE_A_DISTANCE`, `QUATRE_YEUX`), delegation_id (si exécution sous délégation), cash_session_id (session de l'exécutant si sortie d'espèces), montant_ttc_total, montant_patient, montant_organismes, statut (`EXECUTEE`, `PARTIELLE_A_CONFIRMER`), **idempotency_key**, **correlation_id**, validation_batch_id, executed_at (horodatage serveur), device_id | UNIQUE(tenant_id, numero) ; UNIQUE(tenant_id, idempotency_key) ; INDEX(tenant_id, original_sale_id) ; INDEX(tenant_id, executed_at) ; CHECK(approved_by ≠ requested_by) lorsque approval_method ≠ `AUCUNE` |
| `corrective_operation_lines` (immuable) | id, operation_id, **original_sale_line_id**, product_id, lot_id, quantite_unites (> 0), montant_ttc, part_patient, part_organismes (JSON par payeur), prescription_line_id (nullable), droit_delivrance (`RESTITUE`, `NEUTRALISE`, `SANS_OBJET`), etat_constate (`SCELLE`, `OUVERT`, `ENDOMMAGE`, `NON_RECUPERE`), destination_initiale (`EN_CONTROLE`, `QUARANTAINE`), stock_movement_id, dispensation_reversal_id, adjustment_id (neutralisation EXT03), controlled_register_id (nullable) | INDEX(original_sale_line_id) |
| `sale_line_correction_totals` (projection) | sale_line_id, qte_vendue, qte_annulee, qte_retournee, **qte_corrigeable**, montant_patient_restitue, montants_organismes_regularises (JSON) | Recalculable depuis `corrective_operation_lines` ; jamais utilisée seule pour décider (§4.4) |
| `correction_reasons` | id, tenant_id, code, libelle_fr, libelle_ar, applicable_a (types), exige_texte (bool), exige_validation (bool), categorie (`ERREUR_SAISIE`, `CLIENT`, `PRODUIT`, `PRIX`, `PAIEMENT`, `REGLEMENTAIRE`, `AUTRE`) | Référentiel paramétrable ; sert aux statistiques et à l'antifraude |

### 3.2 Demandes et validations

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `correction_requests` | id, tenant_id, type, original_sale_id, **payload** (JSON : lignes, quantités, constat, sort du stock proposé, restitution proposée), motif_code, motif_texte, requested_by_employee_id, requested_at, statut (`EN_ATTENTE`, `APPROUVEE`, `REFUSEE`, `EXPIREE`, `ANNULEE_PAR_DEMANDEUR`, `EXECUTEE`), decided_by_employee_id, decided_at, motif_refus, expire_at, operation_id (si exécutée), version | INDEX(tenant_id, statut) ; une seule demande `EN_ATTENTE` par vente |
| `correction_request_events` (immuable) | id, request_id, type (création, notification, consultation, décision, expiration, exécution), employee_id, at, details | Historique complet de la demande |

### 3.3 États de stock non vendables et contrôle de réintégration

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `stock_buckets` (référentiel) | code (`DISPONIBLE`, `EN_CONTROLE`, `QUARANTAINE`, `PERTE`), vendable (bool), valorise (bool) | Seul `DISPONIBLE` est vendable et alloué par FEFO |
| `return_control_items` | id, tenant_id, operation_line_id, product_id, lot_id, quantite_unites, statut (`EN_CONTROLE`, `QUARANTAINE`, `REINTEGRE`, `PERTE`), entre_le, decision_attendue_avant, checklist (JSON : point, réponse, auteur), decision, decide_par_employee_id, decide_le, second_valideur_employee_id (réglementés, seuils), stock_movement_id (décision), loss_record_id (si perte), motif | INDEX(tenant_id, statut) ; une décision est définitive (une erreur se corrige par un nouveau mouvement motivé) |
| `return_control_checklists` | id, tenant_id, categorie_produit, points (JSON), seuil_peremption_jours, version | Check-lists paramétrables par catégorie (froid, réglementé, dispositif…) |

### 3.4 Remboursements et régularisations

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `refunds` (immuable) | id, tenant_id, operation_id, original_payment_id, mode (`ESPECES`, `CARTE`, `CHEQUE_RESTITUE`, `CREDIT_COMPTE`, `AVOIR`), montant, cash_session_id (si espèces), cash_movement_id, card_reference, statut (`EFFECTUE`, `A_CONFIRMER`, `ANNULE`), confirme_par_employee_id, confirme_le, voucher_id | INDEX(operation_id) ; INDEX(original_payment_id) |
| `store_credit_vouchers` (avoirs) | id, tenant_id, **numero** (`BAV-…`), customer_id (nullable si au porteur, **[ARBITRAGE D59]**), montant_initial, solde, valid_until, statut (`ACTIF`, `UTILISE`, `EXPIRE`, `ANNULE`), operation_id | UNIQUE(tenant_id, numero) |
| `store_credit_movements` (immuable) | id, voucher_id, sale_id ou operation_id, montant (signé), at, employee_id | Solde = Σ mouvements |
| `payer_regularizations` (lien EXT04) | id, operation_id, claim_id, claim_line_id, type (`ANNULATION_LIGNE`, `REGULARISATION_BORDEREAU`, `DETTE_ORGANISME`), montant, claim_adjustment_id | Une ligne par part d'organisme régularisée |

### 3.5 Délégations

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `delegable_permissions` (catalogue) | permission_code, delegable (bool), exige_qualification (JSON), plafond_max_delegable, reglemente_exclu_par_defaut (bool) | Fixé par APEX et le profil pays ; non modifiable localement pour les droits réglementaires |
| `delegations` | id, tenant_id, **delegator_employee_id** (titulaire ou délégant habilité), **delegate_employee_id**, permissions (JSON), plafond_par_operation_millimes, plafond_jour_millimes, nb_max_jour, perimetre (JSON : exclusions de produits, réglementés, types d'opérations), valid_from, **valid_to (NOT NULL)**, motif, statut (`PROGRAMMEE`, `ACTIVE`, `EXPIREE`, `REVOQUEE`), revoque_par, revoque_le, motif_revocation, confirmation_method (`PIN`, `MFA`) | INDEX(tenant_id, delegate_employee_id, statut) ; CHECK(delegator ≠ delegate) ; CHECK(valid_to > valid_from) ; durée ≤ maximum paramétré |
| `delegation_usage` (immuable) | id, delegation_id, operation_id ou request_id, montant, at | Consommation des plafonds ; source du rapport |
| `permission_overrides` | id, tenant_id, employee_id, permission_code, effet (`ACCORDE`, `RETIRE`), plafonds (JSON), valid_from, valid_to (nullable), motif, accorde_par | Réglage individuel permanent (en complément du rôle) ; distinct d'une délégation temporaire |

### 3.6 Modifications proposées aux tables existantes (non appliquées)

| Table (référence) | Modification | Raison |
|---|---|---|
| `sales` (dossier §4.5) | statuts `ABANDONNEE`, `ANNULEE`, `PARTIELLEMENT_RETOURNEE`, `RETOURNEE` ; + `delivered_at` (remise constatée, facultatif), + `corrected_amount_total` (projection) | Distinguer remise et validation ; statut lisible |
| `sale_returns` / `sale_return_lines` (§4.5) | **Remplacées** par `corrective_operations` / `corrective_operation_lines` | Un seul modèle pour annulations, retours, remboursements |
| `stock_levels` (§4.4) | + `bucket` (clé : produit, lot, bucket) | Stock non vendable distinct ; FEFO sur `DISPONIBLE` uniquement |
| `stock_movements` (§4.4) | types `CANCEL_SALE`, `RETURN_IN` (vers `EN_CONTROLE`), `RETURN_RELEASE` (→ `DISPONIBLE`), `RETURN_QUARANTINE`, `RETURN_LOSS` ; + `bucket_from`, `bucket_to`, `reverses_id`, `correlation_id` | Traçabilité des transferts entre états |
| `prescription_dispensations` (EXT03) | types `ANNULATION` / `RETOUR` alimentés par M19 avec `reverses_id` | Déjà prévu ; source unique : le moteur M19 |
| `prescription_adjustments` (EXT03 v1.1) | `NEUTRALISATION_RETOUR` / `RESTITUTION_RETOUR` écrits par M19 | Déjà prévu (D42) |
| `controlled_register` (§4.9) | sens `RECTIFICATION_ANNULATION`, `ENTREE_RETOUR_QUARANTAINE`, `SORTIE_DESTRUCTION` ; + `corrective_operation_id` | Régularisation réglementaire |
| `payments` (§4.5) | + `reverses_id` ; direction `out` pour les restitutions | Paiements inverses liés |
| `cash_movements` (EXT02 §2.5) | `REMBOURSEMENT` lié à `refund_id` et à la vente d'origine | La sortie d'espèces se fait dans la session de l'exécutant |
| `claim_adjustments` (EXT04) | types `ANNULATION_VENTE`, `RETOUR_PRODUIT` alimentés par M19 | Déjà prévu |
| `loyalty_ledger` (§4.7) | écriture inverse des points | Fidélité neutralisée |
| `permissions` (EXT02 §2.2) | permissions du §5.1 ; les anciennes `sales.void.own`, `sales.void.any`, `sales.refund` sont **remplacées** | Granularité demandée |
| `document_sequences` | types `ANN`, `AVR`, `RMB`, `BAV` | Numérotation continue |
| `quality_events` (EXT02 §2.7) | événements `ANNULATION_ATYPIQUE`, `RETOUR_NON_CONFORME` (automatiques, à confirmer) | Lien antifraude / qualité |

### 3.7 Relations

```
sales 1─n corrective_operations 1─n corrective_operation_lines n─1 sale_lines
corrective_operations 0..1─1 correction_requests ; correction_requests 1─n correction_request_events
corrective_operation_lines 1─0..1 stock_movements (RETURN_IN / CANCEL_SALE) ; 1─0..1 return_control_items
return_control_items 1─0..1 stock_movements (décision) ; 0..1─1 loss_records (M10)
corrective_operation_lines ─► prescription_dispensations (reverses_id) ; ─► prescription_adjustments
corrective_operation_lines ─► controlled_register (rectification)
corrective_operations 1─n refunds n─1 payments (origine) ; refunds 0..1─1 cash_movements ; 0..1─1 store_credit_vouchers
corrective_operations 1─n payer_regularizations ─► claim_adjustments (EXT04)
delegations 1─n delegation_usage n─1 corrective_operations ; employees 1─n delegations (délégant / délégataire)
```

---

## 4. Règles métier

### 4.1 Matrice des situations

| Situation | Opération | Stock | Ordonnance (EXT03) | Registre M09 | Finance patient | Organisme (EXT04) |
|---|---|---|---|---|---|---|
| Vente non validée | Abandon | Libération des réservations | Rien | Rien | Rien (aucun paiement) | Rien |
| Validée, non remise, dans le jour calendaire D75 | A avant remise | `EN_CONTROLE` | Contrôle ; décision immédiate simplifiée uniquement avec droit explicite D74 | Rectification « annulation » | Remboursement intégral (même moyen) | Lignes de créance annulées |
| Validée, non remise, hors conditions ci-dessus | A avant remise | `EN_CONTROLE` | Idem | Idem | Idem | Idem |
| Remise puis rapportée | A après remise **ou** B total | `EN_CONTROLE` | A : droit restitué · B : neutralisé (défaut D42) | Rectification / entrée quarantaine | Remboursement | Annulation ou régularisation selon l'état de la créance |
| Remise, non rapportée | A sans retour (exceptionnel, D63) | Aucun mouvement (produit sorti) | Délivrance maintenue (le produit a été délivré) **ou** contre-délivrance selon motif, pharmacien | Aucun mouvement physique ; mention au registre si réglementé | Remboursement | Régularisation |
| Retour partiel | B | `EN_CONTROLE` sur les quantités retournées | Contre-délivrance partielle, neutralisation | Entrée quarantaine si réglementé | Prorata patient | Prorata organisme |
| Erreur de prix / double encaissement | C autonome | Aucun | Aucun | Aucun | Restitution de l'écart | Régularisation si la part organisme est concernée |

### 4.2 Conditions d'éligibilité (contrôlées côté serveur, dans cet ordre)

| # | Contrôle | Résultat si échec |
|---|---|---|
| C1 | La vente appartient au tenant et est `VALIDEE`, `PARTIELLEMENT_RETOURNEE` (pour B/C) | Refus |
| C2 | Quantité demandée ≤ quantité corrigeable (recalculée depuis les registres) | Refus « déjà corrigé » |
| C3 | Montant restitué par payeur ≤ montant payé par ce payeur − déjà restitué | Refus |
| C4 | Annulation : aucun retour antérieur sur la vente (sinon passer par B) | Refus avec orientation |
| C5 | Délai depuis la vente ≤ délai maximal par type (A, B) paramétrable | Validation pharmacien requise, ou refus au-delà du délai absolu |
| C6 | Droit effectif de l'exécutant (rôle ∩ habilitation ∩ présence ∩ overrides ∩ délégation active) et plafonds non dépassés | Bascule en demande |
| C7 | Séparation des tâches : exécutant ≠ vendeur d'origine au-delà du seuil d'auto-correction ; valideur ≠ demandeur | Validation par un tiers |
| C8 | Produit réglementé : séquence §4.7 | Blocage ou circuit spécifique |
| C9 | Produit non retournable par nature (préparation magistrale, produit au froid sans garantie, fractionné, dispositif ouvert) : retour possible sans réintégration vendable ; entrée en `EN_CONTROLE` ou `QUARANTAINE`, puis décision selon le parcours autorisé | Aucune perte directe ; la décision PERTE suit le parcours utilisateur complet et la confirmation explicite de quantité et d'irréversibilité (P86) |
| C10 | Créance d'organisme en litige ou bordereau en cours de clôture | Validation du responsable tiers payant |
| C11 | Session de caisse de l'exécutant ouverte si une sortie d'espèces est nécessaire | Proposer un autre moyen (crédit, avoir) ou ouvrir une session |
| C12 | Période close (inventaire validé, journal exporté) : la correction est datée du jour, jamais rétroactive | Information |

### 4.3 Plafonds et seuils (valeurs par défaut paramétrables, D65)

| Paramètre | Défaut proposé |
|---|---|
| Auto-correction par l'auteur de la vente sans validation | Annulation avant remise dans le jour calendaire D75 ; au-delà : autorisation de l'administrateur |
| Montant maximal par opération sans validation (par rôle) | Caissier 0 (demande) · préparateur : retour ≤ montant paramétrable · adjoint : plafond élevé · titulaire : illimité |
| Cumul journalier par collaborateur | Paramétrable ; au-delà, validation obligatoire |
| Délai maximal d'un retour | Paramétrable par catégorie de produit ; au-delà, refus ou validation du titulaire |
| Délai d'expiration d'une demande | Fin de la journée d'exploitation |
| Délai de décision d'un contrôle de réintégration | 48 h, puis alerte au titulaire |

### 4.4 Garanties contre la double comptabilisation

1. **Bornes recalculées dans la transaction** : la quantité corrigeable et les montants restituables sont relus depuis
   `sale_lines`, `corrective_operation_lines`, `refunds`, `payer_regularizations` au moment de l'exécution ; l'insertion
   des lignes est conditionnelle (`INSERT … SELECT … WHERE qte_corrigeable >= ?`).
2. **Verrou optimiste sur la vente** : `UPDATE sales SET version = version + 1 … WHERE id = ? AND version = ?` ; si aucune
   ligne n'est modifiée, la transaction échoue (deux corrections simultanées : la seconde échoue proprement).
3. **Idempotence** : clé unique par opération (`idempotency_key`) ; un second envoi renvoie le résultat initial.
4. **Une seule demande en attente par vente** ; l'exécution d'une demande vérifie qu'elle est toujours `APPROUVEE` et non expirée.
5. **Chaque écriture inverse référence son original** (`reverses_id`) ; une même écriture d'origine ne peut être inversée
   qu'à hauteur de son montant ou de sa quantité (contrainte vérifiée par réconciliation quotidienne).
6. **Réconciliations quotidiennes** (incident P1 en cas d'écart) :
   - Σ quantités vendues − annulées − retournées = Σ mouvements nets `SALE` + `CANCEL_SALE` + `RETURN_IN` par ligne ;
   - Σ paiements − Σ restitutions = encaissé net par vente ;
   - pour chaque ligne : délivrances nettes (EXT03) cohérentes avec les quantités nettes vendues ;
   - registre réglementé ↔ mouvements (EXT03 v1.1 §16.3) ;
   - créances d'organismes ↔ parts organismes nettes (EXT04 §4.4).

### 4.5 Transaction d'exécution (tout ou rien)

```
PRÉPARATION (lecture) : contrôles C1–C12 ; calcul des lignes, des parts par payeur, des destinations de stock ;
                         clé d'idempotence ; résolution des droits effectifs et de la délégation éventuelle
BATCH ATOMIQUE (validation_batch_id unique) :
  1. INSERT idempotency_record (UNIQUE)                          → doublon : rien n'est écrit, résultat initial renvoyé
  2. UPDATE sales (statut, version + 1) WHERE version = ?         (garde)
  3. INSERT corrective_operations + corrective_operation_lines   (conditionnel aux quantités corrigeables)
  4. stock : INSERT stock_movements (CANCEL_SALE ou RETURN_IN) ; UPDATE stock_levels du bucket cible
  5. ordonnance : INSERT prescription_dispensations négatives (+ prescription_adjustments de neutralisation)
  6. réglementé : INSERT controlled_register (rectification, numéro d'ordre continu)
  7. finance patient : INSERT payments (out, reverses_id), refunds, cash_movements (session de l'exécutant),
     store_credit_vouchers / party_ledger (crédit client)
  8. organismes : INSERT claim_adjustments / payer_regularizations ; mise à jour des projections de créance
  9. fidélité : écriture inverse de loyalty_ledger
 10. délégation : INSERT delegation_usage ; demande → EXECUTEE
 11. INSERT return_control_items (si EN_CONTROLE), audit_log, domain_events (outbox)
 12. Garde finale : si 2 ou 3 n'ont affecté aucune ligne, échec intégral
APRÈS (asynchrone, rejouable) : métriques, rx_facts nets, notifications, rapports, alertes antifraude
```

Le remboursement par terminal de carte est **hors transaction** : la part carte est enregistrée `A_CONFIRMER` dans le
batch, puis confirmée (référence du terminal) ou annulée par une action tracée (workflow F).

### 4.6 Réintégration en stock

| Destination initiale | Conditions | Suite |
|---|---|---|
| `EN_CONTROLE` | Toute annulation ou tout retour avec quantité physique à contrôler, y compris avant remise | Contrôle tracé ; décision de réintégration selon D74 (droit explicite) ou workflow normal |
| `PERTE` | Après passage en `EN_CONTROLE`/`QUARANTAINE`, uniquement via le parcours utilisateur complet avec confirmation explicite de la quantité et des conséquences irréversibles (P86) | Aucun passage direct par API ; droit non accordé par défaut ; création du `loss_record` après confirmation |
| — (aucun mouvement) | Produit non rapporté | Rien n'entre en stock |

Statuts finaux demandés par le cahier des charges, et leur correspondance :

| Statut demandé | État technique |
|---|---|
| Réintégré au stock disponible | bucket `DISPONIBLE` (via `EN_CONTROLE` ou décision `REINTEGRE`) |
| En attente de contrôle | bucket `EN_CONTROLE` |
| En quarantaine | bucket `QUARANTAINE` |
| Déclaré non réutilisable / perte | bucket `PERTE` + `loss_record` (M10) |

Les buckets non vendables sont **valorisés** dans la valeur du stock mais signalés séparément (quarantaine, contrôle) ;
la perte sort de la valorisation.

### 4.7 Médicaments réglementés

Séquence obligatoire, non désactivable (cohérente avec EXT03 v1.1 §16) :

```
1. Toute annulation ou retour d'une ligne réglementée exige l'habilitation CONTROLLED_DISPENSE (ou dédiée) et un
   pharmacien pointé ; la validation est en quatre yeux au-delà du seuil (défaut : toujours)
2. Annulation avant remise (produit au comptoir) : rectification du registre (annulation de la sortie) ; produit en
   EN_CONTROLE (aucune réintégration directe) ; réintégration possible uniquement par décision selon habilitation et règle PO applicable [À VALIDER R35]
3. Retour après remise : entrée au registre en QUARANTAINE ; RÉINTÉGRATION INTERDITE par défaut ; décision :
   destruction (PV, registre) ou retour fournisseur selon la réglementation [À VALIDER R34, R35]
4. Retour sans ticket : interdit pour un réglementé
5. Annulation sans retour physique d'un réglementé : interdite (le produit est sorti ; seule une rectification motivée
   du titulaire est possible, avec déclaration éventuelle [À VALIDER R36])
6. Tout se fait dans le même batch que la correction ; échec du registre = échec de l'opération
```

### 4.8 Antifraude et contrôle interne

Signaux transmis à l'analyseur L d'EXT02 (anomalies de caisse) et à un analyseur dédié :

- annulations ou retours après encaissement espèces par le même collaborateur, au-dessus de la normale des pairs (score robuste) ;
- remboursements en espèces sur des ventes payées par carte (interdit par défaut, D59) ;
- retours sans ticket répétés, ou pour un même client ;
- corrections juste avant la clôture de session ;
- utilisation intensive d'une délégation (plafond atteint de façon répétée) ;
- demandes approuvées toujours par le même valideur pour le même demandeur ;
- contrôles de réintégration décidés systématiquement en « réintégré » sans délai.

Les alertes sont réservées au titulaire (audience restreinte, EXT02 §9.3) et décrivent des faits, sans jugement.

---

## 5. Permissions et délégation

### 5.1 Catalogue des permissions (remplace `sales.void.*` et `sales.refund` d'EXT02 §7.4)

| Permission | Description | Délégable | Plafonnable |
|---|---|---|---|
| `sale.read` | Consulter une vente et ses corrections (portée : les siennes / sa session / toutes) | — | — |
| `sale.void.request` | Demander une annulation | — (droit de base) | — |
| `sale.void.execute` | Annuler une vente dans les limites (montant, délai, auto-correction) | Oui | Oui |
| `sale.void.approve` | Valider une annulation demandée par un autre (PIN présent ou à distance) | Oui (pharmacien) | Oui |
| `sale.return.request` | Demander un retour | — | — |
| `sale.return.execute` | Effectuer un retour dans les limites | Oui | Oui |
| `sale.return.approve` | Valider un retour demandé | Oui (pharmacien) | Oui |
| `sale.return.without_receipt` | Retour sans ticket (D62) | Oui | Oui |
| `sale.return.restore_right` | Restituer le droit à délivrance sur l'ordonnance (D42) | Pharmacien uniquement | — |
| `stock.return.decide` | Décider de la réintégration, de la quarantaine ou de la perte (workflow D) ; droit individuel accordé explicitement par le pharmacien-administrateur (D74) | **Non par défaut ; octroi individuel explicite ADMIN uniquement** | Oui (valeur) |
| `stock.return.decide.controlled` | Même décision pour un produit réglementé | **Non** | — |
| `refund.execute` | Effectuer un remboursement financier (espèces, carte, crédit, avoir) | Oui | Oui |
| `refund.autonomous` | Remboursement sans retour de produit (erreur de prix, double encaissement) | Oui | Oui |
| `refund.cash_on_card_sale` | Rembourser en espèces une vente payée par carte (exception, D59) | Titulaire | Oui |
| `refund.confirm_card` | Confirmer ou annuler une part carte `A_CONFIRMER` | Oui | — |
| `correction.history.read` | Consulter le journal des opérations correctives (portée : soi / équipe / tout) | — | — |
| `correction.controlled.execute` | Annulation ou retour d'une ligne réglementée | **Non** | — |
| `delegation.manage` | Créer, modifier, révoquer des délégations | **Règle à confirmer** ; D66 est caduque, aucun délégant désigné n'est autorisé par défaut | — |
| `delegation.report.read` | Consulter les rapports d'utilisation des délégations | — | — |

Les huit opérations demandées par le cahier des charges (consulter, demander une annulation, annuler dans les limites,
valider une annulation, effectuer un retour, valider la réintégration, rembourser, consulter l'historique) correspondent
respectivement à `sale.read`, `sale.void.request`, `sale.void.execute`, `sale.void.approve`, `sale.return.execute`,
`stock.return.decide`, `refund.execute` et `correction.history.read`.

### 5.2 Matrice par rôle (valeurs par défaut, configurables par pharmacie et par collaborateur)

| Permission | Titulaire | Adjoint | Préparateur | Caissier | Achats/stock | Comptable | Auditeur |
|---|---|---|---|---|---|---|---|
| `sale.read` | tout | tout | sa session + ses ventes | sa session | — | tout | tout |
| `sale.void.request`, `sale.return.request` | ✔ | ✔ | ✔ | ✔ | — | — | — |
| `sale.void.execute` | ✔ illimité | ✔ plafond élevé | annulation avant remise dans le jour calendaire D75, selon droits et plafonds | annulation avant remise dans le jour calendaire D75, selon droits et plafonds | — | — | — |
| `sale.return.execute` | ✔ | ✔ | ✔ plafond bas | — | — | — | — |
| `sale.void.approve`, `sale.return.approve` | ✔ | ✔ | — | — | — | — | — |
| `sale.return.without_receipt` | ✔ | ✔ | — | — | — | — | — |
| `sale.return.restore_right` | ✔ | ✔ | — | — | — | — | — |
| `stock.return.decide` | — | — | — | — | — | — | — |
| `stock.return.decide.controlled`, `correction.controlled.execute` | ✔ | — (aucun droit par rôle ; habilitation et autorisation explicites du pharmacien-administrateur, quatre yeux) | — | — | — | — | — |
| `refund.execute` | ✔ | ✔ | ✔ (dans le plafond du retour) | ✔ (exécution d'une opération validée) | — | — | — |
| `refund.autonomous` | ✔ | ✔ | — | — | — | — | — |
| `refund.cash_on_card_sale` | ✔ | — | — | — | — | — | — |
| `correction.history.read` | tout | tout | soi | soi | — | tout | tout |
| `delegation.manage` | ✔ | — | — | — | — | — | — |
| `delegation.report.read` | ✔ | — | — | — | — | — | ✔ |

### 5.3 Mécanisme de délégation

**Trois niveaux de configuration, du plus général au plus particulier** :

1. **Rôle** (`role_permissions`, EXT02) : permissions et plafonds par défaut. `stock.return.decide` est exclue de toute attribution par défaut, y compris pour le rôle Titulaire, Adjoint ou Achats/stock (D74).
2. **Réglage individuel permanent** (`permission_overrides`) : le pharmacien-administrateur accorde ou retire explicitement un droit ou un
   plafond à un collaborateur nommé (par pharmacie). Pour `stock.return.decide`, cet octroi individuel explicite est obligatoire ; il ne peut être déduit du rôle, d'une habilitation métier ou d'une délégation générique (D74). L'opération est motivée et tracée.
3. **Délégation temporaire** (`delegations`) : le titulaire confie pour une période **bornée** (date de fin obligatoire)
   un droit qu'il détient, avec plafonds et périmètre. Cas typiques : absence du titulaire, garde, remplacement.

**Calcul du droit effectif** (complète EXT02 §1.2) :

```
droit_effectif(collaborateur, permission, opération) =
     (rôle ∪ overrides ACCORDE ∪ délégations ACTIVES) − overrides RETIRE
  ∩  habilitations et qualifications exigées par la permission (valides à la date)
  ∩  statut actif du collaborateur ∩ politique de présence de la permission
  ∩  plafonds : min(plafond du rôle ou de l'override ou de la délégation, plafond pays)
               − consommation du jour (delegation_usage + opérations du jour)
  ∩  périmètre de la délégation (produits exclus, réglementés exclus)
```

**Règles de délégation** :

| # | Règle |
|---|---|
| G1 | On ne délègue que ce que l'on détient, et dans la limite de son propre plafond |
| G2 | Les droits marqués non délégables (réglementés, gestion des délégations, droits exigeant une qualification que le délégataire n'a pas) ne peuvent jamais être délégués, quel que soit le paramétrage local |
| G3 | Pas de sous-délégation : un délégataire ne peut pas déléguer à son tour |
| G4 | Date de fin obligatoire ; durée maximale paramétrable (défaut : 30 jours) ; renouvellement = nouvelle délégation |
| G5 | Confirmation par PIN ou MFA du délégant ; notification immédiate au délégataire |
| G6 | Révocation immédiate possible ; invalidation des caches de droits ; les opérations déjà exécutées restent valides |
| G7 | Toute opération réalisée sous délégation porte `delegation_id` et alimente `delegation_usage` |
| G8 | Rapport mensuel au titulaire : délégations actives, opérations réalisées, plafonds consommés, alertes |
| G9 | La délégation ne lève jamais la séparation des tâches : un délégataire ne valide pas sa propre demande |
| G10 | Les limites légales (qui peut dispenser, annuler ou rectifier un réglementé) sont portées par le profil pays et priment sur toute délégation **[À VALIDER R37]** |

### 5.4 Validation supplémentaire des opérations sensibles

| Opération | Validation supplémentaire par défaut |
|---|---|
| Annulation après remise | Pharmacien (PIN présent ou demande) |
| Annulation sans retour physique | Titulaire, motif détaillé |
| Retour au-delà du plafond du collaborateur | Pharmacien |
| Retour sans ticket | Pharmacien ; refus pour un réglementé |
| Restitution du droit à délivrance | Pharmacien, motif |
| Toute correction d'un réglementé | Quatre yeux (deux pharmaciens habilités, ou titulaire) |
| Remboursement en espèces d'une vente payée par carte | Titulaire |
| Remboursement autonome au-delà du seuil | Titulaire |
| Décision de perte au-delà du seuil de valeur | Titulaire |
| Correction par l'auteur de la vente hors fenêtre d'auto-correction | Tiers habilité |

Méthodes de validation : **PIN présent** (le valideur saisit son PIN sur le poste de l'exécutant) ou **demande à distance**
(file X6, validation depuis un autre poste ou un smartphone). La méthode est enregistrée.

---

## 6. Dépendances avec les autres modules

| Module | Dépendance | Sens | Remarque |
|---|---|---|---|
| M00 Plateforme | Séquences `ANN`, `AVR`, `RMB`, `BAV` ; audit chaîné ; notifications ; idempotence | M19 consomme | — |
| **M15 RH / droits (EXT02)** | Droits effectifs, habilitations, présence, PIN, séparation des tâches | M19 consomme et étend | Délégations et overrides ajoutés au calcul |
| **M02 Vente** | Statuts de vente, lignes, ticket (QR), remise constatée | Bidirectionnelle, forte | Les anciens `sale_returns` sont remplacés |
| **M02b Caisse nominative (EXT02 §7)** | Sortie d'espèces dans la session ouverte de l'exécutant ; théorique de caisse | M19 alimente | La session d'origine clôturée n'est jamais modifiée |
| **M05 Stock** | Buckets non vendables, mouvements inverses, FEFO sur `DISPONIBLE` | M19 alimente | Changement structurant : `bucket` dans `stock_levels` |
| M10 Péremptions & pertes | `loss_records` et destructions issus des retours | M19 alimente | — |
| **M17 Ordonnances (EXT03 v1.1)** | Contre-délivrances, neutralisation / restitution (D42), recalcul du solde | M19 alimente | Moteur unique d'écriture |
| **M09 Réglementés** | Rectifications, quarantaine, destruction ; séquence §4.7 | M19 alimente | Même batch |
| **M18 Tiers payant (EXT04)** | Annulation de lignes de créance, régularisation au bordereau, dette organisme | M19 alimente | Workflow C |
| M08 Finance | Paiements inverses, crédit client, avoirs, `party_ledger`, chèques en portefeuille | M19 alimente | — |
| M07 Clients | Fidélité (points inverses), compte client, avoirs nominatifs | M19 alimente | — |
| M12 Rapports / métriques | Ventes nettes, CA net, taux d'annulation et de retour | M12 consomme | Rapports en net, par date d'opération |
| M13 IA | Analyseur antifraude (§4.8), lien avec l'analyseur L d'EXT02 | M13 consomme | Agrégats ; alertes nominatives réservées au titulaire |
| M14 Hors-ligne | Aucune opération corrective hors ligne ; demandes locales seulement | — | D64 |

Événements de domaine ajoutés : `correction.requested`, `correction.approved`, `correction.rejected`,
`correction.expired`, `sale.voided`, `sale.returned`, `refund.executed`, `refund.card_pending`, `refund.card_confirmed`,
`return.controlled`, `return.released`, `return.quarantined`, `return.lost`, `delegation.granted`,
`delegation.revoked`, `delegation.expired`, `delegation.limit_reached`.

---

## 7. Impacts sur la comptabilité et les stocks

### 7.1 Principes comptables

1. **Aucune modification de l'écriture d'origine.** Le chiffre d'affaires de la date de vente reste celui enregistré ;
   la correction crée une écriture inverse **datée du jour de la correction** (avoir pour un retour, écriture
   d'annulation pour une annulation). Les journaux déjà exportés ne sont pas modifiés **[À VALIDER R38]**.
2. **CA net = ventes − annulations − avoirs**, calculé par période d'opération. Les rapports proposent les deux vues :
   par date de vente (analyse) et par date d'opération (comptable).
3. Un **remboursement** est un décaissement (caisse ou banque) ou une diminution de créance client ; un **avoir** est une
   dette envers le client jusqu'à son utilisation ou son expiration (traitement à l'expiration : **[À VALIDER R39]**).
4. La **part organisme** est régularisée sur la créance (EXT04 §7.2), jamais par la caisse.
5. La **TVA** suit l'avoir : TVA collectée inverse à la date de l'avoir ; le traitement du droit de timbre sur les avoirs
   est **[À VALIDER R38]**.

### 7.2 Effets par cas (montants fictifs : vente de 100 TTC, dont 30 patient espèces et 70 organisme)

| Cas | CA | Caisse / banque | Créance organisme | Créance / avoir client | Stock | Perte |
|---|---|---|---|---|---|---|
| Annulation avant remise, créance non transmise | −100 (écriture d'annulation) | −30 espèces (session de l'exécutant) | −70 (lignes annulées) | — | + qté vers `EN_CONTROLE` | — |
| Retour total, créance transmise non réglée | −100 (avoir) | −30 | −70 via régularisation au bordereau suivant | — | + qté `EN_CONTROLE` | — |
| Retour partiel (40 TTC : 12 patient, 28 organisme) | −40 | −12 | −28 | — | + qté retournée `EN_CONTROLE` | — |
| Retour, restitution en avoir | −100 | 0 | −70 | + 30 (avoir) | + qté `EN_CONTROLE` | — |
| Retour, puis décision « perte » | −100 | −30 | −70 | — | `EN_CONTROLE` → `PERTE` | + coût du lot (charge) |
| Retour, créance déjà réglée | −100 | −30 | dette de 70 envers l'organisme | — | + qté `EN_CONTROLE` | — |
| Vente à crédit non payée, annulée | −100 | 0 | — | −100 (dette client annulée) | selon remise | — |
| Erreur de prix (remboursement autonome de 5) | −5 (avoir de montant) | −5 | — | — | aucun | — |
| Remise non rapportée, annulation exceptionnelle | −100 | −30 | −70 | — | aucun | + coût (produit perdu, qualification à valider) |

### 7.3 Stocks

| Mouvement | De | Vers | Valorisation |
|---|---|---|---|
| `CANCEL_SALE` (avant remise, conditions remplies) | sortie annulée | `DISPONIBLE` | Coût du lot d'origine |
| `CANCEL_SALE` / `RETURN_IN` | sortie annulée | `EN_CONTROLE` | Coût du lot d'origine (valorisé, non vendable) |
| `RETURN_RELEASE` | `EN_CONTROLE` | `DISPONIBLE` | Inchangée |
| `RETURN_QUARANTINE` | `EN_CONTROLE` | `QUARANTAINE` | Valorisé, non vendable, signalé |
| `RETURN_LOSS` | `EN_CONTROLE` / `QUARANTAINE` | `PERTE` | Sort de la valorisation ; charge |

- Le **lot d'origine** est toujours conservé (aucun retour n'est réaffecté à un autre lot).
- Le **CUMP** n'est pas recalculé par un retour : le coût réintégré est le coût figé sur la ligne de vente (`cout_unitaire`), cohérent avec la marge annulée.
- L'inventaire compte séparément les buckets ; un écart sur `EN_CONTROLE` ou `QUARANTAINE` est un incident.
- Les produits `EN_CONTROLE` ne sont jamais proposés par FEFO, ni comptés dans le stock disponible des commandes intelligentes (M11), mais apparaissent dans un indicateur « en attente de décision ».

---

## 8. Exigences réglementaires

Numérotation à la suite d'EXT04 (R23 à R33). Tant qu'un point n'est pas confirmé, la règle la plus stricte s'applique
(pas de réintégration d'un produit retourné réglementé, validation pharmacien pour toute réintégration).

| # | Sujet | Hypothèse de conception | Question précise | Interlocuteurs | Échéance |
|---|---|---|---|---|---|
| R34 | Reprise et remise en vente d'un médicament rapporté par un patient | Réintégration seulement après contrôle (intégrité, conservation, péremption) ; jamais automatique | Un médicament délivré puis rapporté peut-il être remis en vente ? À quelles conditions ? Certaines catégories sont-elles exclues (froid, réglementés, fractionnés) ? (complète R14 d'EXT03 v1.1) | Ordre des pharmaciens, DPM | Avant le lot 5d |
| R35 | Retour d'un médicament réglementé | Quarantaine ; réintégration interdite ; destruction ou retour fournisseur | Procédure applicable (registre, déclaration, destruction, témoins, PV) | DPM, Ordre | Avant le lot 10 |
| R36 | Annulation d'une délivrance réglementée sans retour physique | Interdite, sauf rectification motivée du titulaire | Comment régulariser au registre une erreur de saisie sur un réglementé déjà sorti ? | DPM | Avant le lot 10 |
| R37 | Délégation des actes pharmaceutiques | Délégation limitée par le profil pays ; réglementés non délégables | Quelles opérations (annulation, retour, validation, rectification) peuvent être confiées à un préparateur ou à un non-pharmacien, et sous quelle supervision ? | Ordre, Code de la santé | Avant le lot 5d |
| R38 | Facturation des avoirs et annulations | Écriture inverse datée de l'opération ; TVA inverse | Mentions obligatoires d'un avoir ; traitement du timbre ; numérotation ; obligation éventuelle de facture électronique (TTN / El Fatoora) pour les avoirs | Expert-comptable, administration fiscale | Avant le lot 5d |
| R39 | Avoirs clients non utilisés | Durée de validité paramétrable | Durée minimale légale d'un avoir ; traitement comptable à l'expiration | Expert-comptable, juriste | Avant activation des avoirs |
| R40 | Remboursement par carte | Remboursement via le terminal, sinon autre moyen | Conditions contractuelles du terminal (remboursement, délai) ; interdiction éventuelle de rembourser en espèces une vente par carte | Banque / acquéreur | Avant le lot 5d |
| R41 | Données conservées sur les opérations correctives | Conservation alignée sur les ventes | Durée de conservation des justificatifs d'annulation et de retour | Juriste, expert-comptable | Avant production |

---

## 9. Matrice de tests

### 9.1 Scénarios demandés

| ID | Scénario demandé | Préconditions | Résultat attendu |
|---|---|---|---|
| AN-01 | **Annulation avant délivrance** (vente non validée) | Vente en brouillon / en attente | Statut `ABANDONNEE`, réservations libérées, aucun mouvement, aucun document correctif, journalisé |
| AN-02 | **Annulation avant remise** (vente validée, produit au comptoir) | Produit non remis, dans les droits et le jour calendaire D75 | Document ANN, statut `ANNULEE`, stock `EN_CONTROLE` ; réintégration uniquement après contrôle, avec droit explicite D74 pour la décision immédiate simplifiée ; délivrance contre-écrite, restitution tracée |
| AN-03 | **Annulation après délivrance** (produit remis puis rapporté) | Vente de la veille, session clôturée | Validation pharmacien ; stock `EN_CONTROLE` ; remboursement dans la session **courante** de l'exécutant ; session d'origine inchangée |
| RE-01 | **Retour partiel** | 2 lignes, retour d'1 unité sur la ligne 1 | Avoir sur 1 unité ; statut `PARTIELLEMENT_RETOURNEE` ; quantité corrigeable restante exacte ; prorata patient / organisme exact |
| RE-02 | **Retour total** | Toutes les lignes | Statut `RETOURNEE` ; somme des avoirs = total de la vente ; aucune quantité corrigeable restante |
| RE-03 | **Produit non réintégrable** | Conditionnement ouvert / produit froid sans garantie | Entrée `EN_CONTROLE` ou `QUARANTAINE` ; aucun passage direct en PERTE ; parcours utilisateur complet et confirmation quantité/conséquences avant PERTE (P86) ; remboursement selon la politique |
| RE-04 | **Produit réglementé** | Ligne réglementée, retour après remise | Habilitation et quatre yeux exigés ; écriture au registre dans le même batch ; quarantaine ; réintégration refusée |
| PA-01 | **Vente payée en espèces** | Paiement espèces | Remboursement espèces dans la session ouverte de l'exécutant ; théorique de caisse diminué ; session fermée impossible |
| PA-02 | **Vente payée par carte** | Paiement carte | Part carte `A_CONFIRMER` puis confirmée avec référence ; remboursement en espèces refusé sauf droit titulaire |
| PA-03 | **Vente avec prise en charge assurance** | Part organisme 70 %, créance non transmise puis transmise | Non transmise : lignes de créance annulées ; transmise : régularisation au bordereau suivant ; le patient ne reçoit que sa part |
| DR-01 | **Annulation demandée par un employé** | Caissier sans droit d'exécution | Demande créée, aucun effet ; notification aux valideurs ; expiration en fin de journée si non traitée |
| DR-02 | **Annulation validée par un pharmacien** | Demande DR-01 | Validation (PIN ou à distance) → exécution ; demandeur, exécutant et valideur tracés ; valideur ≠ demandeur |
| DR-03 | **Tentative d'annulation sans habilitation** | Collaborateur sans `sale.void.request` ou appel direct à l'API | 403 côté serveur ; tentative journalisée ; alerte si répétée |
| DB-01 | **Double annulation** | Deux requêtes simultanées sur la même vente (deux postes) | Une seule réussit ; la seconde échoue sur le verrou de version ; aucun double remboursement |
| DB-02 | **Double envoi** (double clic, même clé) | Même `idempotency_key` | Résultat initial renvoyé ; aucune écriture supplémentaire |
| CX-01 | **Perte de connexion pendant l'opération** | Coupure simulée avant, pendant, après le batch | Tout ou rien ; état réel consultable ; nouvel essai sans effet double |

### 9.2 Tests complémentaires

| ID | Domaine | Scénario | Résultat attendu |
|---|---|---|---|
| AN-04 | Annulation | Annulation demandée après un retour partiel | Refus avec orientation vers un retour du reste |
| AN-05 | Annulation | Annulation sans retour physique (D63) | Titulaire seulement ; aucun mouvement de stock ; motif détaillé |
| AN-06 | Annulation | Vente à crédit non payée annulée | Créance client annulée ; aucune sortie de caisse |
| RE-05 | Retour | Retour d'un lot différent du lot vendu | Refus, sauf décision pharmacien tracée |
| RE-06 | Retour | Retour sans ticket | Droit spécifique exigé ; refus pour un réglementé |
| RE-07 | Retour / ordonnance | Retour avec neutralisation puis nouvelle délivrance sur la même ordonnance | Le reste à délivrer n'a pas augmenté (D42) ; dépassement refusé |
| RE-08 | Retour / ordonnance | Retour avec restitution du droit (pharmacien) | Reste à délivrer augmenté ; motif tracé |
| RE-09 | Retour | Délai maximal de retour dépassé | Validation titulaire ou refus |
| ST-01 | Stock | Produit `EN_CONTROLE` | Jamais alloué par FEFO, absent du stock disponible de M11 |
| ST-02 | Stock | Décision de réintégration par la personne ayant reçu le retour, au-delà du seuil | Refus (séparation des tâches) |
| ST-03 | Stock | Délai de décision dépassé | Alerte ; aucune réintégration automatique |
| ST-04 | Stock | Réconciliation buckets ↔ mouvements | Égalité |
| FI-01 | Finance | Paiement mixte (carte + espèces) | Restitution répartie selon D59 ; totaux exacts |
| FI-02 | Finance | Restitution en avoir, puis utilisation partielle sur une vente | Solde de l'avoir exact ; mouvements tracés |
| FI-03 | Finance | Chèque encore en portefeuille | Restitution du chèque ou annulation de l'instrument (M08) |
| FI-04 | Finance | Réconciliation paiements − restitutions par vente | Égalité |
| FI-05 | Finance | Remboursement autonome (erreur de prix) | Avoir de montant ; aucun mouvement de stock ; ligne d'origine inchangée |
| OG-01 | Tiers payant | Créance déjà réglée | Dette envers l'organisme ; aucun remboursement de la part organisme au patient |
| DL-01 | Délégation | Délégation de 7 jours avec plafond journalier | Opérations acceptées jusqu'au plafond, puis bascule en demande ; `delegation_usage` exact |
| DL-02 | Délégation | Délégation d'un droit non délégable (réglementé) | Refus à la création |
| DL-03 | Délégation | Sous-délégation | Refus |
| DL-04 | Délégation | Délégation expirée ou révoquée pendant une opération | L'opération en cours est re-contrôlée dans la transaction et échoue ; les opérations antérieures restent valides |
| DL-05 | Délégation | Délégataire sans la qualification requise | Refus à la création |
| DL-06 | Délégation | Délégataire validant sa propre demande | Refus |
| SE-01 | Sécurité | Tentative de `DELETE` ou `UPDATE` sur une opération exécutée (API et SQL) | Refus (aucune route ; déclencheur SQLite) |
| SE-02 | Sécurité | Modification du montant à restituer côté client | Montant recalculé côté serveur ; écart refusé |
| AU-01 | Audit | Trace complète d'une opération | Vente d'origine, demandeur, exécutant, valideur, délégation, motif, horodatage, montants, quantités, lots, situation du stock et situation financière présents |
| AU-02 | Audit | Chaînage de l'audit après une série d'opérations | Chaîne de hachage intacte |
| RP-01 | Rapports | CA par date de vente vs par date d'opération | Les deux vues concordent sur la période totale |
| HL-01 | Hors ligne | Tentative de correction hors ligne | Refusée ; demande locale soumise à la reconnexion |

---

## 10. Décisions nécessitant arbitrage

Numérotation à la suite d'EXT04 (D44 à D58).

| # | Question | Options | Recommandation |
|---|---|---|---|
| **D59** | Moyens de restitution au patient | Même moyen que le paiement d'origine · espèces libres · avoir · crédit | **Même moyen par défaut** ; espèces pour une vente par carte interdites sauf titulaire ; avoir nominatif proposé, avoir au porteur désactivé |
| **D60** | Remboursement par carte | Intégration au terminal · confirmation manuelle | **Confirmation manuelle** en v1 (référence du terminal), intégration ultérieure |
| **D61** | Fenêtre d'auto-annulation avant remise (réintégration directe) | 0 (toujours contrôle) · 15 min · 30 min | **CADUQUE** — remplacée par la règle du jour calendaire portée par D75 (EXT06) |
| **D62** | Retours sans ticket | Interdits · Autorisés avec droit spécifique | Autorisés avec droit spécifique, interdits pour les réglementés |
| **D63** | Annulation sans retour physique | Interdite · Exceptionnelle (titulaire) | Exceptionnelle, titulaire, motif détaillé ; interdite pour les réglementés |
| **D64** | Opérations correctives hors ligne | Autorisées · Demande locale seulement | **Demande locale seulement** |
| **D65** | Plafonds par défaut par rôle | Valeurs proposées au §4.3 | À fixer avec les pharmacies pilotes |
| **D66** | Qui peut gérer les délégations | Titulaire seul · titulaire + adjoint désigné | **CADUQUE** — ne pas utiliser cette recommandation comme règle PO ; règle de gouvernance à réconcilier avec les décisions et permissions ultérieures |
| **D67** | Durée maximale d'une délégation | 7 j · 30 j · 90 j | **30 jours**, renouvelable par nouvelle délégation |
| **D68** | Délai de décision d'un produit `EN_CONTROLE` | 24 h · 48 h · 7 j | **48 h**, puis alerte |
| **D69** | Avoirs : durée de validité | Selon R39 | À fixer après validation juridique |
| **D70** | Remplacement des tables `sale_returns` par le modèle correctif unifié | Remplacer · Conserver en parallèle | **Remplacer** (une seule source) |
| **D71** | Buckets de stock (`EN_CONTROLE`, `QUARANTAINE`) dans `stock_levels` dès le lot 3 | Dès le lot 3 · Plus tard | **Dès le lot 3** (évite une migration de la table de stock) |

---

## 11. Position dans le plan de développement

| Lot | Contenu EXT05 | Justification |
|---|---|---|
| **1. Plateforme** (ajout) | `permission_overrides`, `delegations`, `delegable_permissions`, intégration au calcul des droits effectifs | Le moteur de droits doit naître complet |
| **3. Stock & lots** (ajout) | États physiques `DISPONIBLE`, `EN_CONTROLE`, `QUARANTAINE`, `PERTE` ; réservations logiques hors buckets ; types de mouvements correctifs ; `reverses_id` | Structure de stock définitive dès le départ (D71), sans bucket `RESERVE` (D76) |
| **5. Caisse & ventes** (ajout) | Statuts de vente, idempotence, abandon, annulation avant remise (le minimum vital pour exploiter une caisse) | Une caisse ne peut pas être livrée sans annulation |
| **5d. Opérations correctives** (nouveau, après 5b et 7) | Moteur M19 complet : workflows A, B, C, D, demandes et validations, contrôle de réintégration, avoirs, journal, états | Dépend des ordonnances (EXT03), de la finance et des buckets |
| **7b. Tiers payant** (EXT04) | Régularisation des créances appelée par M19 | Après 5d, ou 5d complété au lot 7b |
| **10. Réglementés** (ajout) | Séquence §4.7, rectifications et quarantaine au registre | Après validation R35, R36 |
| **11. Rapports** (ajout) | CA net par date d'opération, taux d'annulation et de retour, rapport des délégations | Couche métriques unique |
| **13. IA** (ajout) | Analyseur antifraude §4.8 | Après 11 |

Prérequis bloquants avant le lot 5d : R34, R37, R38, R40 ; décisions D59, D63, D70, D71. D61 est caduque et ne constitue plus un prérequis ; la règle de jour calendaire relève de D75 (EXT06).

---

## Annexe — Récapitulatif de la traçabilité exigée

| Élément exigé | Où il est conservé |
|---|---|
| Vente d'origine | `corrective_operations.original_sale_id`, `corrective_operation_lines.original_sale_line_id` |
| Collaborateur demandeur | `requested_by_employee_id` (+ `correction_requests`) |
| Collaborateur exécutant | `executed_by_employee_id` (+ session de caisse, poste) |
| Pharmacien validateur | `approved_by_employee_id`, `approval_method` |
| Délégation utilisée | `delegation_id`, `delegation_usage` |
| Motif | `motif_code`, `motif_texte` |
| Date et heure | `executed_at` (horodatage serveur UTC + fuseau du tenant), événements de demande |
| Montants | Par ligne, par payeur (`part_patient`, `part_organismes`), `refunds`, `payer_regularizations` |
| Quantités et lots | `corrective_operation_lines.quantite_unites`, `lot_id` |
| Situation du stock | Mouvements (`bucket_from`, `bucket_to`), `return_control_items` et leur décision |
| Situation financière | `payments` inverses, `cash_movements`, `store_credit_*`, `party_ledger`, `claim_adjustments` |
| Corrélation de toutes les écritures | `correlation_id`, `validation_batch_id`, `audit_log` chaîné |
