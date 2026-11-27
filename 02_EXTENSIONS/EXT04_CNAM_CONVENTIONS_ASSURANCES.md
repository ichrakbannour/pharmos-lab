# PHARM'OS — Extension fonctionnelle n° 04 : CNAM, conventions, assurances et prises en charge

**Produit** : PHARM'OS — APEX AI DEV
**Version** : 1.0 (proposition) — 2026-11-03
**Statut** : **PROPOSITION TECHNIQUE — AUCUN CODE**. Mise à jour de gouvernance du 27/11/2026 : la matrice PO D1–D86 v1.3.4 arbitre D6, D37 et D44 en retenant l'option B. Le périmètre à préparer comprend le module organismes/conventions/remboursements et le remboursement par l'assuré ; aucune convention n'est active par défaut. **Le tiers payant CNAM reste désactivé** : toute activation ultérieure exige cumulativement la vérification des textes, conventions, avenants et modalités officielles applicables, puis une validation explicite et distincte du PO. La formulation antérieure « activable par paramétrage » ne constitue donc pas une autorisation autonome d'activation. D45–D58 restent des propositions non globalement approuvées ; R23–R33 et les exigences réglementaires/conventionnelles restent à instruire. Cet arbitrage ne vaut pas autorisation de développement ou de mise en service. Le dossier principal (`../01_CONCEPTION/CONCEPTION_PHARMOS_v1 (1).md` v1.1)
n'est pas modifié : les changements nécessaires sont listés au §3.4 et ne seront reportés qu'après validation.
**Module créé** : **M18 Prises en charge & organismes payeurs**
**Base de conception** : dossier principal v1.1, EXT02 (intégrée), **EXT03 v1.1** (dossier d'audit `03_AUDIT_EXT03_v1.1`,
non encore intégré au dépôt : les références « EXT03 §15, §16, §19, D37, D42 » renvoient à cette version).
**Document lié** : `EXT05_ANNULATIONS_RETOURS_DELEGATIONS.md` (retours et annulations sur ventes avec prise en charge).

> **Règle absolue de ce document** : aucun taux, plafond, procédure ou délai de remboursement n'est présumé.
> Les valeurs numériques présentes dans les exemples sont **fictives** et signalées comme telles. Le système est livré
> **sans aucune règle de prise en charge active** : chaque règle doit être saisie à partir d'une source officielle
> ou d'une convention signée, puis validée par le titulaire.
> Repères : **[À VALIDER Rxx]** = point réglementaire (§8) · **[ARBITRAGE Dxx]** = décision du propriétaire (§10).

---

## 0. Contexte tunisien à la date du document (à surveiller)

Ce contexte a des conséquences directes sur la conception. Les informations ci-dessous sont **publiques et datées**.
Elles ne valent pas avis juridique et doivent être vérifiées au moment du développement.

| Constat | Source (consultée le 2026-11-03) | Conséquence de conception |
|---|---|---|
| Le Syndicat des pharmaciens d'officine (SPOT) a annoncé la **suspension du tiers payant CNAM à compter du 1er novembre 2026**. Les assurés paient le prix complet et se font rembourser selon les procédures applicables. | Webdo, 30/10/2026 ; Business News, 30/10/2026 ; Directinfo/TAP, 31/10/2026 | Une convention doit pouvoir être **suspendue à une date**, avec bascule automatique du mode de vente (§4.6) |
| Une suspension comparable a eu lieu en décembre 2025 : la CNAM avait alors mis en place une procédure exceptionnelle de remboursement (formulaire rempli par l'assuré et le pharmacien, ordonnance, facture, étiquettes des médicaments). Rien n'indique qu'elle sera reconduite à l'identique. | Business News, 30/10/2026 | Mode **« remboursement par l'assuré »** : PHARM'OS produit les justificatifs, sans créance pour la pharmacie (§2.4, W4) |
| Le syndicat évoque des délais de paiement de 90 à 100 jours, voire jusqu'à 180 jours, des **retenues contestées** sur les créances et la **fermeture de la plateforme numérique** de suivi des factures. | Business News, 30/10/2026 | Balance âgée sur 180 jours et plus, gestion des **retenues** et des **litiges**, import manuel des décomptes (aucune dépendance à une plateforme) |
| La convention sectorielle CNAM / pharmaciens publiée par la CNAM est datée du 14/02/2019 et a été prolongée par avenants. Selon le syndicat, elle n'a pas été renouvelée pour 2026. En tiers payant, la « décision de prise en charge » est conservée par le pharmacien et jointe au dossier. | cnam.nat.tn (`conv_ph_02_19.pdf`, extrait) ; social.gov.tn ; Webdo 28/10/2025 | Conventions **versionnées, datées, avec document source** ; pièces justificatives par dossier |
| La CNAM publie une liste des affections prises en charge intégralement (APCI) et un guide du pharmacien. Le tiers payant APCI exige une décision de prise en charge valide. | cnam.nat.tn (`listeapci.pdf`, `guide_phar.pdf`, extraits) | Notion générique d'**autorisation de prise en charge** (§3.2), sans stocker le diagnostic (D49) |

**Conséquence globale** : le module ne doit pas être conçu autour d'un fonctionnement CNAM supposé stable. Il gère
des **organismes**, des **conventions datées et suspendables** et des **règles sourcées**. La CNAM n'est qu'un
organisme paramétré parmi d'autres ; le code ne lui réserve aucun traitement spécifique.

---

## Sommaire

1. Architecture fonctionnelle
2. Écrans et workflows
3. Tables et relations
4. Règles métier (moteur de calcul, créances, règlements)
5. Permissions
6. Dépendances avec les autres modules
7. Impacts sur la comptabilité et les stocks
8. Exigences réglementaires
9. Matrice de tests
10. Décisions nécessitant arbitrage
11. Position dans le plan de développement

---

## 1. Architecture fonctionnelle

### 1.1 Principes structurants

1. **Trois notions financières distinctes, jamais confondues** :

   | Notion | Définition | Où elle vit |
   |---|---|---|
   | **Chiffre d'affaires** | Prix de vente TTC total des produits délivrés, quel que soit le payeur | `sales` / `sale_lines` (inchangé) |
   | **Créance organisme** | Part que l'organisme doit à la pharmacie, avec son cycle de vie (facturée → réglée) | `claims` / `claim_lines` + grand livre tiers (`party_ledger`, tiers `ORGANISME`) |
   | **Encaissement** | Argent effectivement reçu (patient au comptoir, organisme par virement) | `payments` / `cash_movements` / banque (M08) |

   Invariant par ligne de vente : **total TTC = part patient + Σ parts organismes**, contrôlé par une contrainte `CHECK`.
   Un rejet, une retenue ou un retard ne modifient **jamais** le chiffre d'affaires : ils agissent sur la créance (§7).

2. **Aucune règle présumée.** Une règle de prise en charge ne peut être `ACTIVE` que si elle porte une **référence de
   source** (texte, convention, avenant, courrier de l'organisme, avec date et document joint) et la **validation du
   titulaire** (quatre yeux : la personne qui saisit n'est pas celle qui valide). Si aucune règle confirmée ne s'applique,
   le moteur calcule une part organisme de **0** et affiche le motif. Il n'estime rien.
3. **Tout est daté et versionné** (organismes, conventions, règles, listes de produits, couvertures). Le calcul utilise
   les versions en vigueur **à la date de la vente**, et un instantané du calcul est figé sur la vente.
4. **Un droit n'est confirmé que par une vérification fiable** : chaque couverture porte un *niveau de vérification*
   (§4.2). Le niveau minimal exigé pour pratiquer le tiers payant est fixé par convention.
5. **Moteur de calcul déterministe et explicable** : même entrée, même résultat. Chaque montant est accompagné d'une
   trace lisible (règle, base, taux, plafond, arrondi).
6. **Une créance a un cycle de vie complet et historisé**, depuis la vente jusqu'au règlement, y compris en cas de rejet,
   de litige ou de perte. Le bordereau regroupe les créances transmises.
7. **Chaque règlement est lettré.** Tout écart (rejet, retenue, trop-perçu) est **explicite**, motivé et validé, jamais
   absorbé en silence.
8. **Indépendance vis-à-vis du pays** : types d'organismes génériques, profils de conformité par pays (EXT03 v1.1 §3.2,
   `compliance_profiles`). Les connecteurs de télétransmission sont des adaptateurs optionnels, sans effet sur le modèle.
9. **Résilience aux suspensions** : une convention peut être suspendue ou reprise à une date. La caisse applique
   automatiquement le mode de repli paramétré (paiement intégral ou remboursement par l'assuré).

### 1.2 Sous-modules

| Sous-module | Contenu |
|---|---|
| **M18.1 Organismes** | Fiches (CNAM, mutuelles, assurances privées, conventions d'entreprise, autres), identifiants de la pharmacie auprès de chaque organisme, contacts, modalités de règlement |
| **M18.2 Conventions et règles** | Conventions versionnées, périodes de suspension, règles de prise en charge sourcées et validées, listes de produits de référence, pièces exigées, délais |
| **M18.3 Couvertures des patients** | Affiliations multiples par patient, ayants droit, validité, justificatifs, niveau de vérification, autorisations de prise en charge |
| **M18.4 Moteur de calcul** | Éligibilité, choix de la règle, base, taux, plafonds, cascade obligatoire puis complémentaire, arrondis, trace |
| **M18.5 Vente avec prise en charge** | Panneau dans l'écran de vente (EXT03 mode Ordonnance), répartition patient / organismes, justificatifs |
| **M18.6 Créances et bordereaux** | Constitution des créances, contrôle des pièces, bordereaux, transmission, accusés de réception |
| **M18.7 Retours d'organisme, règlements, litiges** | Décomptes (acceptations, rejets), règlements partiels, retenues, lettrage, litiges, refacturation, passage en perte |
| **M18.8 Reporting et pilotage** | États, balance âgée, taux de rejet, délais de paiement, prévision d'encaissement, tableau de bord |

### 1.3 Modes de vente

Le mode est choisi (ou proposé) par ligne de payeur, au niveau de la vente :

| Mode | Description | Part patient | Créance pharmacie |
|---|---|---|---|
| `PATIENT_INTEGRAL` | Le patient paie tout, sans organisme | 100 % | Aucune |
| `TIERS_PAYANT` | L'organisme paie directement la pharmacie pour sa part (CNAM, assurance, mutuelle) | Reste à charge | Oui, sur l'organisme |
| `TIERS_PAYANT_PARTAGE` | Cascade de payeurs : obligatoire (ex. CNAM) puis complémentaire (mutuelle, assurance) sur le reste à charge | Reste après cascade | Une créance par organisme |
| `CONVENTION` | Convention particulière (entreprise, association, collectivité) : prise en charge totale ou partielle, facturation périodique à l'employeur | Selon convention | Oui, sur l'organisme conventionné |
| `REMBOURSEMENT_ASSURE` | Le patient paie tout, et la pharmacie lui remet les justificatifs exigés par son organisme pour se faire rembourser lui-même | 100 % | **Aucune** (aucun risque pour la pharmacie) |

Un mode n'est proposé que s'il est autorisé par la convention **en vigueur et non suspendue** à la date de la vente.

### 1.4 Place dans l'architecture

```
            M17 Ordonnances (prescripteur, ordonnance, lignes)        M07 Patients (volet patient_profiles)
                         │                                                    │
                         ▼                                                    ▼
M04 Catalogue ──► M18.4 MOTEUR DE CALCUL ◄── M18.2 Conventions & règles   M18.3 Couvertures
(listes, tarifs)         │  (déterministe, versionné, trace)
                         ▼
                 M02 Vente (répartition par ligne : part patient / parts organismes)
                   │                         │
                   ▼                         ▼
     M02b Caisse nominative           M18.6 Créances ─► Bordereaux ─► Transmission
     (part patient uniquement)               │
                                             ▼
                                  M18.7 Retours d'organisme, règlements, lettrage, litiges
                                             │
                                             ▼
                          M08 Finance : party_ledger (ORGANISME), due_dates, banque, trésorerie
                                             │
                                             ▼
                          M12 Rapports · M13 IA (agrégats, sans donnée patient)
```

---

## 2. Écrans et workflows

### 2.1 Écrans

| Code | Écran | Route | Usage |
|---|---|---|---|
| T1 | Organismes | `/payers`, `/payers/:id` | Fiche, identifiants de la pharmacie, contacts, modalités de règlement, conventions, soldes |
| T2 | Conventions | `/payers/:id/agreements/:aid` | Versions, dates, statut, **suspensions**, modes autorisés, pièces exigées, délais, documents sources |
| T3 | Règles de prise en charge | `/payers/agreements/:aid/rules` | Éditeur de règles, **simulateur**, circuit de validation (saisie, puis validation par le titulaire), historique |
| T4 | Listes de référence | `/payers/reference-lists` | Import (Excel/CSV) des listes de produits pris en charge et des tarifs, aperçu des différences, versions |
| T5 | Couvertures du patient | `/patients/:id` (onglet « Couvertures ») | Affiliations, ayants droit, validité, justificatifs, vérification, autorisations de prise en charge |
| T6 | **Caisse : panneau « Prise en charge »** | `/pos` | Choix de la couverture et du mode, répartition par ligne, alertes, pièces à joindre |
| T7 | Créances | `/claims` | Liste filtrable par organisme, statut, période, motif ; balance âgée ; actions groupées |
| T8 | Bordereaux | `/claims/batches`, `/claims/batches/:id` | Préparation, contrôle des pièces, clôture, impression et export, transmission, accusé de réception |
| T9 | Retours d'organisme | `/claims/responses` | Saisie ou import d'un décompte : acceptations et rejets par ligne, motifs |
| T10 | Règlements et lettrage | `/payers/payments` | Saisie d'un virement, lettrage automatique proposé puis confirmé, retenues, écarts |
| T11 | Litiges | `/claims/disputes` | Ouverture, échanges, pièces, issue |
| T12 | Tableau de bord tiers payant | `/analytics/payers` | Encours, balance âgée, délai moyen de paiement, taux de rejet, prévision d'encaissement |
| T13 | États | `/reports/payers/*` | États par organisme, période, statut, montant, motif de rejet ; PDF et Excel |
| T14 | Justificatifs de remboursement | Depuis la vente | Facture détaillée, formulaire ou bulletin de l'organisme (modèle paramétrable), étiquettes des produits |

### 2.2 Panneau « Prise en charge » de l'écran de vente (T6)

```
┌─ PRISE EN CHARGE ────────────────────────────────────────────────────────────┐
│ Patient : M. B*** (fiche)        Ordonnance ORD-2026-000123 (Dr X, cardiologie) │
│ Couverture 1 : [CNAM — n° ••••4521 — valide jusqu'au 31/12/2026 — Vérifiée ▾]  │
│ Couverture 2 : [Mutuelle Y — complémentaire — Justifiée ▾]  [+ ajouter]        │
│ Mode : (•) Tiers payant partagé  ( ) Remboursement par l'assuré  ( ) Intégral  │
│ ⚠ Convention CNAM suspendue depuis le 01/11/2026 → tiers payant CNAM indisponible│
│   Mode de repli proposé : Remboursement par l'assuré (justificatifs imprimés)   │
├──────────────────────────────────────────────────────────────────────────────┤
│ Produit              Total   Org. 1   Org. 2   Patient  Motif / règle          │
│ PRODUIT A 30 cp     20,000    0,000    6,000   14,000   Org.1 : convention suspendue │
│ PRODUIT B           12,500    0,000    0,000   12,500   Hors liste (aucune règle)   │
├──────────────────────────────────────────────────────────────────────────────┤
│ Totaux             32,500    0,000    6,000   26,500   [Détail du calcul]      │
│ Pièces requises : ☑ ordonnance (copie)  ☐ étiquettes produits  ☐ carte/attest. │
└──────────────────────────────────────────────────────────────────────────────┘
(montants fictifs, à titre d'illustration)
```

- Proposition automatique de la couverture du patient la plus pertinente (valide à la date du jour, convention active).
- **Le détail du calcul** affiche, pour chaque ligne et chaque payeur : la règle (code, version, source), la base, le taux,
  le plafond appliqué, l'arrondi et le motif de non-prise en charge.
- Les **pièces requises** proviennent de la convention. Une pièce bloquante manquante empêche le tiers payant pour cet
  organisme (la vente reste possible en mode intégral ou en remboursement par l'assuré).
- **Seule la part patient est encaissée** au comptoir. Le ticket indique la part de chaque organisme, sans donnée médicale.

### 2.3 Workflow W1 — Paramétrage d'un organisme et d'une convention

```
Administrateur (ou titulaire) crée l'organisme → identifiants de la pharmacie (code conventionnel, etc.)
  → crée la convention v1 : dates, modes autorisés, pièces exigées, délais de transmission et de règlement,
    périodicité des bordereaux, document source obligatoire (PDF de la convention ou de l'avenant)
  → saisit les règles de prise en charge (ou importe une liste de référence) avec, pour chacune, sa source
  → SIMULATION sur des cas types (fictifs ou ventes passées anonymisées) → résultats contrôlés
  → soumission → VALIDATION par le titulaire (≠ auteur), qui confirme avoir vérifié la source
  → statut ACTIVE à la date d'effet. Toute modification ultérieure crée une nouvelle version datée.
```

### 2.4 Workflow W2 — Enregistrement d'une couverture patient

```
Fiche patient → « Ajouter une couverture » → organisme, numéro d'affiliation (chiffré), qualité (assuré / ayant droit + lien),
  type de couverture (paramètres de l'organisme), dates de validité, rang (obligatoire / complémentaire)
  → justificatif (carte, attestation) photographié ou scanné → niveau de vérification :
       DECLAREE (déclaration orale) → JUSTIFIEE (document présenté et contrôlé par un collaborateur)
       → VERIFIEE (confirmation par un mécanisme fiable : service de l'organisme, attestation récente, décision écrite)
  → autorisations éventuelles (décision de prise en charge, accord préalable) : numéro, période, périmètre de produits
  → historique de chaque changement de statut
```

### 2.5 Workflow W3 — Vente avec prise en charge (tiers payant)

```
Vente (mode Ordonnance, EXT03) → sélection de la couverture → moteur de calcul (§4.3) par ligne
  → contrôles : convention active et non suspendue, couverture valide à la date, niveau de vérification suffisant,
    pièces bloquantes présentes, autorisation de prise en charge valide si exigée, ordonnance conforme (EXT03 classes R/M)
  → le collaborateur peut retirer une ligne de la prise en charge (le patient la paie), jamais augmenter un montant
    calculé ; une saisie manuelle de prise en charge n'existe que si la convention l'autorise (D47), avec validation
    par un pharmacien et justificatif
  → encaissement de la part patient uniquement (session de caisse nominative)
  → VALIDATION — même transaction atomique que la vente (EXT03 v1.1 §19.1), avec en plus :
       sale_line_payer_shares (instantané du calcul) + claims / claim_lines (statut FACTUREE)
       + party_ledger (débit ORGANISME) + due_dates (échéance prévisionnelle)
  → ticket (parts par payeur) ; pièces rattachées à la créance (copie d'ordonnance, étiquettes, décision)
```

### 2.6 Workflow W4 — Vente en remboursement par l'assuré

```
Mode REMBOURSEMENT_ASSURE (choisi, ou repli automatique en cas de convention suspendue)
  → le patient paie 100 % → aucune créance, aucune écriture au tiers ORGANISME
  → impression des justificatifs paramétrés par l'organisme : facture détaillée (produits, quantités, prix, codes),
    formulaire ou bulletin à compléter (modèle importé, champs pré-remplis), étiquettes ou vignettes si exigées
  → trace des justificatifs remis (date, collaborateur) ; réimpression tracée (duplicata)
```

### 2.7 Workflow W5 — Constitution et transmission d'un bordereau

```
Créances FACTUREES → contrôle automatique des pièces et des délais de transmission → statut A_TRANSMETTRE (complètes)
  ou A_COMPLETER (pièce manquante, liste affichée)
  → préparation du bordereau : organisme, convention, période, sélection (automatique selon la périodicité, ou manuelle)
  → contrôles : une créance figure dans un seul bordereau ouvert ; totaux ; délai de transmission non dépassé
  → CLÔTURE (numéro BRD-AAAA-nnnn, totaux figés, PDF + export dans le format exigé)
  → transmission (dépôt physique, portail, fichier ; API future) → date et référence de dépôt → créances TRANSMISES
  → accusé de réception saisi ou importé
```

### 2.8 Workflow W6 — Traitement du retour de l'organisme

```
Réception du décompte (papier, fichier, portail) → saisie ou import → rapprochement automatique par bordereau et dossier
  → pour chaque ligne : ACCEPTEE (montant accepté) | ACCEPTEE_PARTIELLEMENT (écart motivé) | REJETEE (code motif)
  → statut de la créance recalculé → écarts créés explicitement (aucun écart absorbé)
  → créances acceptées : EN_ATTENTE_REGLEMENT, échéance recalculée selon la convention
```

### 2.9 Workflow W7 — Rejet

```
Ligne REJETEE → qualification du motif (référentiel de l'organisme : corrigeable / non corrigeable)
  → option A « Corriger et retransmettre » : pièce ajoutée ou erreur corrigée → nouvelle tentative (compteur, délai)
  → option B « Contester » : ouverture d'un litige (W9)
  → option C « Refacturer au patient » : seulement si la convention et la réglementation le permettent [À VALIDER R28],
     patient informé ; création d'une créance client (party_ledger CLIENT), sans modifier la vente
  → option D « Passer en perte » : validation du titulaire, motif ; écriture de perte sur créance ; le CA est inchangé
```

### 2.10 Workflow W8 — Règlement, lettrage et retenues

```
Virement reçu (relevé bancaire M08 ou avis de l'organisme) → saisie du règlement (montant, date, référence, période couverte)
  → lettrage automatique proposé (référence de bordereau, montants, dates) → confirmation par le comptable ou le titulaire
  → règlement partiel : les créances restent EN_ATTENTE_REGLEMENT pour le solde
  → retenue ou déduction annoncée par l'organisme : saisie explicite (motif, montant, référence) → ACCEPTEE (charge)
     ou CONTESTEE (litige) ; elle n'est jamais confondue avec un règlement
  → trop-perçu : dette envers l'organisme (party_ledger) jusqu'à remboursement ou imputation sur un règlement suivant
```

### 2.11 Workflow W9 — Litige

```
Ouverture (rejet contesté, retenue contestée, règlement non reçu au-delà de l'échéance) → pièces, courriers, échéance de relance
  → statuts : OUVERT → EN_COURS → GAGNE (montant récupéré) | PERDU (bascule vers W7 C ou D) | TRANSIGE (montant partiel)
  → chaque étape est datée, attribuée à un collaborateur et historisée
```

### 2.12 Workflow W10 — Suspension et reprise d'une convention

```
Titulaire déclare une suspension : date d'effet, motif, source (communiqué, courrier), mode de repli
  (REMBOURSEMENT_ASSURE ou PATIENT_INTEGRAL), périmètre (tous les produits ou certaines catégories)
  → à partir de la date d'effet, la caisse ne propose plus le tiers payant pour cette convention et applique le repli
  → les créances antérieures continuent leur cycle normal (transmission, règlement)
  → reprise : date de fin de suspension, avec ou sans nouvelle version de convention ; notification aux collaborateurs
  → l'historique des périodes est conservé (une vente est toujours jugée selon l'état de la convention à sa date)
```

### 2.13 Workflow W11 — Retour ou annulation d'une vente avec prise en charge

Traité dans **EXT05** (workflows A, B et C). Principe : la part de l'organisme est régularisée **sur la créance**
(annulation de la créance si elle n'est pas transmise, ligne négative dans le bordereau suivant ou avoir à l'organisme
si elle l'est) et la part du patient lui est remboursée. Aucune régularisation ne passe par la caisse pour la part de l'organisme.

---

## 3. Tables et relations

Conventions du dossier principal (§4.1) : `tenant_id` partout, ULID, montants en **millimes**, taux en **points de base**
(1 % = 100 bp), colonnes d'audit, aucune suppression physique, `employee_id` sur toute écriture humaine (EXT02).
Les tables marquées **(immuable)** n'acceptent que des insertions ; des déclencheurs SQLite bloquent `UPDATE` et `DELETE`
(défense en profondeur, EXT03 v1.1 §17.3).

### 3.1 Organismes

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `ref_payers` (référentiel APEX, sans tenant) | id, code, nom, nom_ar, type (`ASSURANCE_MALADIE_OBLIGATOIRE`, `MUTUELLE`, `ASSURANCE_PRIVEE`, `CONVENTION_ENTREPRISE`, `ORGANISME_PUBLIC`, `AUTRE`), pays, identifiants_officiels (JSON), site, statut, version | UNIQUE(pays, code) ; fournit des fiches de départ, **sans aucune règle de prise en charge** |
| `payers` | id, tenant_id, ref_payer_id (nullable), code, nom, nom_ar, type, matricule_fiscal, adresse, contacts (JSON), email_facturation, modalites_reglement (JSON : mode, compte de réception), statut (`ACTIF`, `INACTIF`), version | UNIQUE(tenant_id, code) ; INDEX(tenant_id, type) |
| `payer_pharmacy_ids` | id, tenant_id, payer_id, type_identifiant (code conventionnel, numéro de prestataire, n° de contrat…), valeur, valid_from, valid_to, document_id | Identifiants de la pharmacie auprès de l'organisme, historisés |
| `source_documents` | id, tenant_id (NULL = APEX), type (`CONVENTION`, `AVENANT`, `CIRCULAIRE`, `LISTE_OFFICIELLE`, `COURRIER_ORGANISME`, `COMMUNIQUE`, `CONTRAT`), titre, reference, date_document, emetteur, fichier_key (R2), sha256, url_source, ajoute_par_employee_id | Toute règle et toute convention citent au moins un document source |

### 3.2 Conventions, suspensions, règles

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `payer_agreements` | id, tenant_id, payer_id, code, libelle, version, valid_from, valid_to, statut (`BROUILLON`, `A_VALIDER`, `ACTIVE`, `EXPIREE`, `RESILIEE`), rang (`OBLIGATOIRE`, `COMPLEMENTAIRE`, `UNIQUE`), modes_autorises (JSON parmi §1.3), verification_min_level, autorisation_requise_par_defaut (bool), ordonnance_requise_par_defaut (bool), periodicite_bordereau (`QUINZAINE`, `MENSUELLE`, `LIBRE`…), delai_transmission_j, delai_reglement_j, nb_retransmissions_max, refacturation_patient_autorisee (bool, D50), saisie_manuelle_autorisee (bool, D47), regle_arrondi, source_document_ids (JSON), saisi_par, valide_par, valide_le | UNIQUE(tenant_id, payer_id, code, version) ; CHECK(valide_par ≠ saisi_par) ; une seule version `ACTIVE` par code et par date |
| `agreement_suspensions` | id, agreement_id, debut, fin (nullable), motif, source_document_id, mode_repli (`REMBOURSEMENT_ASSURE`, `PATIENT_INTEGRAL`), perimetre (JSON : tout, catégories, listes), declare_par, valide_par | Périodes non chevauchantes par convention ; historique conservé |
| `agreement_required_documents` | id, agreement_id, type_piece (`ORDONNANCE_ORIGINALE`, `COPIE_ORDONNANCE`, `ETIQUETTES_PRODUITS`, `CARTE_OU_ATTESTATION`, `DECISION_PRISE_EN_CHARGE`, `FORMULAIRE_ORGANISME`, `FACTURE_DETAILLEE`, `AUTRE`), bloquant (bool), conditions (JSON : produit, montant, type de couverture), conservation (`ORIGINAL_PHARMACIE`, `TRANSMIS`, `RESTITUE_PATIENT`) | — |
| `reference_lists` | id, tenant_id, payer_id (nullable), type (`PRODUITS_PRIS_EN_CHARGE`, `TARIFS_REFERENCE`, `EXCLUSIONS`), libelle, version, valid_from, valid_to, source_document_id, statut, importe_par, valide_par | Import contrôlé avec aperçu des différences (même principe que le catalogue, dossier §2.2 M04) |
| `reference_list_items` | list_id, product_id ou code externe (rapproché du catalogue), categorie_organisme, tarif_reference_millimes (nullable), conditions (JSON), statut_rapprochement (`RAPPROCHE`, `A_RAPPROCHER`, `AMBIGU`) | UNIQUE(list_id, code externe) ; un élément non rapproché ne déclenche aucune prise en charge |
| `coverage_rules` | id, tenant_id, agreement_id, code, version, priorite, **perimetre** (JSON : produits, catégories, listes, DCI, `prescription_policy`, classes réglementaires), **conditions** (JSON : types de couverture, autorisation requise, ordonnance requise, spécialité du prescripteur si la convention le prévoit, qualité assuré/ayant droit), **base_type** (`PRIX_VENTE`, `TARIF_REFERENCE`, `MIN_PRIX_TARIF`, `RESTE_A_CHARGE`), taux_bp, montant_fixe_millimes, franchise_millimes, plafond_ligne_millimes, **plafond_periode** (JSON : montant, période, portée patient/ordonnance/famille), quantite_max, arrondi, valid_from, valid_to, **source_document_id (NOT NULL)**, source_extrait (article, page), statut (`BROUILLON`, `A_VALIDER`, `ACTIVE`, `RETIREE`), saisi_par, valide_par, valide_le | INDEX(agreement_id, statut, valid_from) ; CHECK(statut ≠ 'ACTIVE' OR (source_document_id NOT NULL AND valide_par NOT NULL AND valide_par ≠ saisi_par)) |
| `rejection_reasons` | id, tenant_id, payer_id, code, libelle_fr, libelle_ar, categorie (`DROITS`, `PIECES`, `TARIF`, `ORDONNANCE`, `DELAI`, `DOUBLON`, `AUTRE`), corrigeable (bool), action_suggeree | UNIQUE(payer_id, code) ; référentiel propre à chaque organisme, enrichi au fil des décomptes |
| `document_templates` | id, tenant_id, payer_id, type (`FORMULAIRE_REMBOURSEMENT`, `BORDEREAU`, `FACTURE_ORGANISME`), version, modele (HTML ou PDF à champs), champs (JSON), source_document_id, actif | Modèles de justificatifs pour W4 et W5 |

### 3.3 Couvertures des patients

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `patient_coverages` (**cloisonnée**, niveau N2 d'EXT03) | id, tenant_id, customer_id (volet `patient_profiles`), payer_id, agreement_id (nullable), **numero_affiliation_chiffre**, **numero_affiliation_hmac** (recherche exacte sans déchiffrer), qualite (`ASSURE`, `AYANT_DROIT`), lien_assure, assure_principal_ref (chiffré, facultatif), type_couverture_code (valeurs définies par l'organisme), rang (`OBLIGATOIRE`, `COMPLEMENTAIRE`), valid_from, valid_to, statut (`ACTIVE`, `SUSPENDUE`, `EXPIREE`, `RADIEE`), **verification_level** (`DECLAREE`, `JUSTIFIEE`, `VERIFIEE`), verification_method, verification_ref, verified_at, verified_by_employee_id, verification_expire_le, justificatif_document_id, conditions_specifiques (JSON) | INDEX(tenant_id, customer_id) ; UNIQUE(tenant_id, payer_id, numero_affiliation_hmac, customer_id) ; lecture journalisée (`sensitive_access_log`, EXT03) |
| `coverage_status_history` (immuable) | id, coverage_id, champ, avant, apres, motif, employee_id, at | — |
| `coverage_authorizations` | id, tenant_id, coverage_id, type (`DECISION_PRISE_EN_CHARGE`, `ACCORD_PREALABLE`, `AUTRE`), numero, valid_from, valid_to, perimetre (JSON : produits, DCI, catégories), plafond_millimes, document_id, statut (`ACTIVE`, `EXPIREE`, `RETIREE`), saisi_par | **Aucun diagnostic ni code d'affection** n'est stocké (D49) : seuls la référence, la période et le périmètre de produits |

### 3.4 Vente et répartition

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `sale_payer_selections` | id, sale_id, coverage_id, payer_id, agreement_id, agreement_version, rang, mode (§1.3), motif_repli (si repli automatique) | UNIQUE(sale_id, rang) |
| `sale_line_payer_shares` (immuable) | id, tenant_id, sale_line_id, rang, payer_id, coverage_id, agreement_id, agreement_version, rule_id, rule_version, base_type, base_millimes, taux_bp, montant_brut_millimes, plafond_applique (JSON), **montant_millimes**, motif_code (si 0), **trace** (JSON lisible), engine_version, calcule_le | INDEX(sale_line_id) ; instantané figé à la validation |
| `reimbursement_documents_issued` (immuable) | id, tenant_id, sale_id, payer_id, template_id, template_version, fichier_key, sha256, type (`ORIGINAL`, `DUPLICATA`), employee_id, at | Trace des justificatifs remis (W4) |

### 3.5 Créances, bordereaux, retours d'organisme

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `claims` | id, tenant_id, **numero** (séquence `CRE-AAAA-nnnnnn`), payer_id, agreement_id, agreement_version, coverage_id, customer_id, sale_id, prescription_id (nullable), date_facturation, date_limite_transmission, **montant_facture**, montant_accepte, montant_rejete, montant_regle, montant_retenu, montant_perte, montant_refacture, **statut** (§4.4), batch_id courant, tentative, echeance_previsionnelle, version | UNIQUE(tenant_id, numero) ; INDEX(tenant_id, payer_id, statut) ; INDEX(tenant_id, date_facturation) ; montants = projections recalculables (§4.4) |
| `claim_lines` | id, claim_id, sale_line_id, share_id, product_id, quantite_unites, montant_facture, montant_accepte, montant_rejete, rejection_reason_id, statut_ligne (`FACTUREE`, `TRANSMISE`, `ACCEPTEE`, `ACCEPTEE_PARTIELLEMENT`, `REJETEE`, `ANNULEE`) | INDEX(claim_id) |
| `claim_events` (immuable) | id, claim_id, claim_line_id (nullable), type (`CREATION`, `PIECE_AJOUTEE`, `MISE_EN_BORDEREAU`, `TRANSMISSION`, `ACCUSE`, `DECISION`, `RETRANSMISSION`, `REGLEMENT`, `RETENUE`, `LITIGE`, `AJUSTEMENT`), montant, avant, apres, ref_id, motif, employee_id, at | Historique complet ; source des transitions de statut |
| `claim_documents` | id, claim_id, type_piece, prescription_document_id ou fichier_key, sha256, conformite (`OK`, `MANQUANTE`, `ILLISIBLE`), controle_par | — |
| `claim_adjustments` (immuable) | id, tenant_id, claim_id, claim_line_id, type (`REJET_ACCEPTE`, `PASSAGE_PERTE`, `REFACTURATION_PATIENT`, `ANNULATION_VENTE`, `RETOUR_PRODUIT`, `CORRECTION`, `RETENUE_ACCEPTEE`, `TROP_PERCU`), montant_millimes (signé), motif, ref_operation_id (EXT05), party_ledger_entry_id, valide_par_employee_id, at | Tout écart financier passe par cette table |
| `claim_batches` (bordereaux) | id, tenant_id, **numero** (`BRD-AAAA-nnnn`), payer_id, agreement_id, periode_debut, periode_fin, statut (`OUVERT`, `CLOTURE`, `TRANSMIS`, `ACCUSE`, `TRAITE_PARTIELLEMENT`, `TRAITE`), nb_dossiers, total_facture, total_regularisations, date_cloture, date_transmission, mode_transmission, reference_depot, accuse_reference, accuse_date, fichier_key, sha256, cloture_par, transmis_par | UNIQUE(tenant_id, numero) ; un bordereau clôturé est immuable |
| `claim_batch_items` | batch_id, claim_id, sens (`FACTURATION`, `REGULARISATION`), montant | UNIQUE(claim_id) parmi les bordereaux non annulés pour le sens `FACTURATION` d'une même tentative |
| `payer_responses` (décomptes) | id, tenant_id, payer_id, batch_id (nullable), reference, date_reception, mode (`SAISIE`, `IMPORT_FICHIER`), fichier_key, statut (`A_RAPPROCHER`, `RAPPROCHE`, `PARTIEL`) | — |
| `payer_response_lines` | id, response_id, claim_line_id (après rapprochement), reference_externe, decision (`ACCEPTEE`, `ACCEPTEE_PARTIELLEMENT`, `REJETEE`), montant_accepte, rejection_reason_id, commentaire | — |

### 3.6 Règlements, retenues, litiges

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `payer_remittances` (règlements) | id, tenant_id, payer_id, date_valeur, montant, reference, periode_couverte, bank_account_id, payment_id (M08, direction `in`), statut (`A_LETTRER`, `PARTIELLEMENT_LETTRE`, `LETTRE`), saisi_par | INDEX(tenant_id, payer_id, statut) |
| `remittance_allocations` | id, remittance_id, claim_id, claim_line_id (nullable), montant, mode (`AUTO_PROPOSE`, `MANUEL`), confirme_par_employee_id, confirme_le | Σ allocations ≤ montant du règlement ; Σ par créance ≤ reste à régler |
| `payer_deductions` (retenues) | id, tenant_id, payer_id, remittance_id (nullable), montant, motif, reference_organisme, statut (`ANNONCEE`, `ACCEPTEE`, `CONTESTEE`, `ANNULEE`), claim_ids (JSON, si rattachées), dispute_id, decide_par | Jamais confondues avec un règlement |
| `claim_disputes` | id, tenant_id, payer_id, objet (`REJET`, `RETENUE`, `RETARD`, `TROP_PERCU`), claim_ids (JSON), montant_conteste, statut (`OUVERT`, `EN_COURS`, `GAGNE`, `PERDU`, `TRANSIGE`, `ABANDONNE`), montant_recupere, prochaine_relance, responsable_employee_id | — |
| `dispute_events` (immuable) | id, dispute_id, type (courrier, appel, réunion, pièce, décision), contenu, document_id, employee_id, at | — |

### 3.7 Modifications proposées aux tables existantes (non appliquées)

| Table (référence) | Modification | Raison |
|---|---|---|
| `sale_lines` (dossier §4.5) | + `part_patient_millimes`, + `part_organismes_millimes` ; **CHECK(total = part_patient + part_organismes)** ; valeur par défaut part patient = total | Invariant de répartition (déjà réservé par EXT03 v1.1 §6.3) |
| `sales` | + `payer_mode` (§1.3), + `total_part_patient`, + `total_part_organismes` | Filtrage et rapports |
| `party_ledger` (§4.7) | `tiers_type` accepte `ORGANISME` ; nouveaux types d'écriture `CREANCE_ORGANISME`, `REGLEMENT_ORGANISME`, `RETENUE`, `PERTE_SUR_CREANCE`, `REFACTURATION`, `TROP_PERCU` | Grand livre unique des tiers |
| `due_dates` (§4.8) | origine `CREANCE_ORGANISME` ; échéance recalculée à l'acceptation | Calendrier financier, trésorerie projetée |
| `payments` (§4.5) | `tiers_type = ORGANISME` pour les règlements reçus ; lien `payer_remittance_id` | Encaissement distinct de la créance |
| `customers` / `patient_profiles` (EXT03) | `organisme` et `identifiant_assure` (texte) **remplacés** par `patient_coverages` | Source unique, couvertures multiples |
| `products` (§4.3) | + `payer_codes` (JSON : codes produits attendus par chaque organisme) ; la prise en charge reste dans `coverage_rules` et `reference_lists`, jamais dans la fiche produit | Pas de taux codé dans le catalogue |
| `cash_sessions` / `cash_movements` (EXT02 §2.5) | aucun changement de structure ; le théorique ne contient que la part patient encaissée | Pas de confusion caisse / créance |
| `document_sequences` | types `CRE`, `BRD` | Numérotation |
| `metrics_daily` | dimensions `payer`, `payer_mode` ; mesures `ca_part_organismes`, `encours_organismes` | Couche métriques unique |
| `permissions`, `habilitations` (EXT02) | permissions `payer.*`, `claim.*` (§5) ; habilitation `CLAIMS_MANAGE` | Droits |
| `compliance_profiles` (EXT03 v1.1) | + bloc `tiers_payant` (types d'organismes, pièces usuelles, règles de conservation) — **sans taux** | Multi-pays |

### 3.8 Relations

```
ref_payers 1─n payers 1─n payer_pharmacy_ids ; payers 1─n payer_agreements 1─n agreement_suspensions
payer_agreements 1─n coverage_rules ; 1─n agreement_required_documents ; n─n source_documents
reference_lists 1─n reference_list_items n─1 products ; coverage_rules → reference_lists (périmètre)
customers 1─n patient_coverages n─1 payers ; patient_coverages 1─n coverage_authorizations
sales 1─n sale_payer_selections ; sale_lines 1─n sale_line_payer_shares n─1 coverage_rules (version)
sales 1─n claims (une par organisme) 1─n claim_lines 1─1 sale_line_payer_shares
claims 1─n claim_events, claim_documents, claim_adjustments ; claim_batches 1─n claim_batch_items n─1 claims
payer_responses 1─n payer_response_lines n─1 claim_lines
payer_remittances 1─n remittance_allocations n─1 claims ; payer_remittances 1─1 payments (M08)
payer_deductions n─0..1 payer_remittances ; claim_disputes 1─n dispute_events
claims ─► party_ledger (ORGANISME) ; claims ─► due_dates ; claim_adjustments ─► party_ledger
```

---

## 4. Règles métier

### 4.1 Éligibilité d'une ligne à une prise en charge (contrôlée côté serveur à la validation)

| # | Condition | Si non remplie |
|---|---|---|
| E1 | Organisme `ACTIF`, convention `ACTIVE` à la date de la vente | Part organisme = 0, motif `CONVENTION_INACTIVE` |
| E2 | Convention non suspendue à la date de la vente (pour le périmètre de la ligne) | Part = 0, mode de repli proposé, motif `CONVENTION_SUSPENDUE` |
| E3 | Mode de vente autorisé par la convention | Mode refusé |
| E4 | Couverture du patient `ACTIVE`, valide à la date de la vente, rattachée à cet organisme | Part = 0, motif `DROITS_NON_VALIDES` |
| E5 | Niveau de vérification ≥ niveau minimal exigé par la convention, vérification non expirée | Tiers payant refusé pour cet organisme ; remboursement par l'assuré possible |
| E6 | Autorisation de prise en charge valide couvrant le produit, si la règle l'exige | Part = 0, motif `AUTORISATION_ABSENTE` |
| E7 | Ordonnance conforme si la règle l'exige : ligne de vente liée à une ligne d'ordonnance (EXT03), aucun manque de catégorie R ou M | Part = 0, motif `ORDONNANCE_NON_CONFORME` |
| E8 | Produit dans le périmètre d'une règle `ACTIVE` (listes de référence rapprochées) | Part = 0, motif `HORS_PERIMETRE` |
| E9 | Pièces bloquantes exigées présentes (ou engagement de les fournir avant transmission, si la convention l'autorise) | Tiers payant refusé ou créance `A_COMPLETER` selon la convention |
| E10 | Quantité ≤ quantité maximale de la règle ; plafonds non épuisés | Part plafonnée, motif `PLAFOND_ATTEINT` |

Aucune condition n'est contournable par un rôle : un motif de non-prise en charge se lève en corrigeant la donnée
(couverture, pièce, autorisation), jamais en forçant le montant.

### 4.2 Vérification des droits

| Niveau | Signification | Exemple | Usage par défaut |
|---|---|---|---|
| `DECLAREE` | Information donnée oralement ou saisie sans pièce | Numéro dicté par le patient | Jamais suffisant pour le tiers payant ; permet de préparer les justificatifs (W4) |
| `JUSTIFIEE` | Document présenté, contrôlé visuellement et archivé (photo de la carte ou de l'attestation, date de validité lue) | Carte de soins, attestation d'affiliation | Suffisant **uniquement** si la convention le prévoit explicitement ; le droit reste affiché « non confirmé » et le risque de rejet est signalé |
| `VERIFIEE` | Confirmation par un mécanisme fiable et traçable | Service de vérification de l'organisme, attestation datée de moins de N jours, décision écrite | Niveau visé pour le tiers payant |

- Le niveau minimal est **fixé par convention** (`verification_min_level`) ; le défaut proposé est discuté en D46.
- Une vérification a une durée de validité paramétrable (`verification_expire_le`) ; au-delà, la couverture redescend à `JUSTIFIEE`.
- **Aucun connecteur de vérification n'est supposé exister.** Si un service officiel devient disponible, il sera branché
  comme adaptateur (`verification_method = SERVICE_ORGANISME`) sans changer le modèle **[À VALIDER R27]**.

### 4.3 Moteur de calcul

**Entrées** : ligne de vente (produit, quantité, prix TTC), date de vente, couvertures sélectionnées et leur rang,
versions en vigueur des conventions, règles, listes, autorisations, compteurs de plafond du patient.
**Sorties** : pour chaque payeur, un montant et une trace ; la part patient en reste.

```
Pour chaque ligne L, payeurs triés par rang (OBLIGATOIRE, puis COMPLEMENTAIRE) :
  reste = prix_TTC(L) − Σ parts déjà attribuées aux payeurs précédents
  1. Éligibilité E1–E10 (si échec : part = 0, motif, passage au payeur suivant)
  2. Règle applicable = règle ACTIVE de la convention, à la date de vente, dont le périmètre et les conditions
     correspondent ; en cas de plusieurs règles, la plus spécifique (produit > liste > DCI > catégorie), puis la priorité ;
     égalité non résolue = erreur de paramétrage (part 0, alerte au titulaire), jamais un choix arbitraire
  3. Base :
       PRIX_VENTE      → prix_TTC(L)
       TARIF_REFERENCE → tarif_reference(produit) × quantité   (absent → part 0, motif TARIF_ABSENT)
       MIN_PRIX_TARIF  → min(prix_TTC(L), tarif_reference × quantité)
       RESTE_A_CHARGE  → reste
  4. Brut = base × taux_bp / 10 000  (+ montant fixe éventuel) − franchise
  5. Plafonds : min(brut, plafond_ligne, plafond_période_restant, autorisation_restante)
  6. Borne : part = min(part, reste) ; part ≥ 0
  7. Arrondi selon la règle de la convention (défaut : au millime inférieur), le reliquat d'arrondi revient au patient
  8. Trace enregistrée (règle, version, source, base, taux, plafonds, arrondi)
part_patient(L) = prix_TTC(L) − Σ parts organismes(L)
```

**Garanties** :
- Σ parts organismes ≤ prix TTC de la ligne (CHECK en base) ; aucune part négative.
- Déterminisme : le moteur est une fonction pure, versionnée (`engine_version`), testée sur un jeu de référence.
- Les compteurs de plafond par période sont recalculés depuis `sale_line_payer_shares` (nets des annulations et retours),
  jamais depuis un compteur saisi.
- Le résultat est **recalculé côté serveur** à la validation ; l'affichage de la caisse n'est qu'un aperçu.
- **Simulateur** (écran T3) : mêmes calculs, sans écriture, pour valider une règle avant activation.

**Exemple (valeurs fictives)** : prix 20,000 ; règle obligatoire `TARIF_REFERENCE` 15,000 à un taux de *t1* ; règle
complémentaire `RESTE_A_CHARGE` à un taux de *t2* plafonné à *p*. La trace affiche chaque étape. Le résultat dépend
exclusivement des valeurs saisies et sourcées ; aucun taux n'est fourni par défaut.

### 4.4 Cycle de vie des créances

Les statuts demandés (facturés, à transmettre, transmis, acceptés, rejetés, en litige, en attente de règlement, réglés)
sont portés **par ligne de créance et par montant**. Le statut de la créance est dérivé de celui de ses lignes.

```
FACTUREE ──pièces complètes──► A_TRANSMETTRE ──bordereau transmis──► TRANSMISE ──décompte──► ┬─ ACCEPTEE ──► EN_ATTENTE_REGLEMENT ──► REGLEE
   │                                                                                       ├─ ACCEPTEE_PARTIELLEMENT (part rejetée → REJETEE)
   └─pièce manquante──► A_COMPLETER ──complétée──► A_TRANSMETTRE                           └─ REJETEE ─┬─ corrigée → A_TRANSMETTRE (tentative + 1)
                                                                                                        ├─ contestée → EN_LITIGE ─► (GAGNE → EN_ATTENTE_REGLEMENT)
EN_ATTENTE_REGLEMENT ──règlement partiel──► REGLEE_PARTIELLEMENT ──solde──► REGLEE                     ├─ refacturée au patient → REFACTUREE (D50)
Avant transmission : vente annulée ou retour total → ANNULEE (EXT05)                                    └─ abandonnée → PERDUE (titulaire)
Retard au-delà de l'échéance : alerte, puis EN_LITIGE (objet RETARD) sur décision
```

**Invariants (vérifiés quotidiennement)** :
```
montant_facture = montant_accepte + montant_rejete + montant_en_instance
montant_accepte = montant_regle + montant_retenu_accepte + reste_a_regler
montant_rejete  = rejets_en_cours + montant_refacture + montant_perte + rejets_regularises_par_retransmission
solde party_ledger(ORGANISME) = Σ restes dus des créances − Σ trop-perçus
```
Les montants des créances sont des projections recalculées depuis `claim_events`, `claim_adjustments` et
`remittance_allocations`. Un écart entre projection et recalcul est un incident P1, corrigé par reconstruction et non par saisie.

### 4.5 Bordereaux

1. Un bordereau ne contient que des créances d'**un seul organisme et d'une seule convention**, en statut `A_TRANSMETTRE`.
2. Une créance ne figure que dans un seul bordereau actif par tentative ; une retransmission crée une nouvelle tentative.
3. La clôture fige les totaux, le contenu et le fichier (sha256). Elle est irréversible : une erreur se corrige par un
   bordereau de régularisation, jamais en rouvrant le bordereau.
4. Le délai de transmission de la convention est contrôlé : une alerte se déclenche à J−5, puis la créance passe en état « hors délai »
   (le risque de rejet est signalé au titulaire, l'envoi reste possible si la convention l'admet).
5. Le format d'export (PDF, Excel, fichier structuré) suit le modèle de l'organisme (`document_templates`).

### 4.6 Suspension d'une convention

- Effet **à la date déclarée** ; une vente est toujours jugée selon l'état de la convention à sa date de validation.
- Pendant la suspension, la caisse propose uniquement le mode de repli. Les autres organismes du patient (complémentaire) restent
  utilisables s'ils le permettent sans l'obligatoire (règle `RESTE_A_CHARGE` → base = prix total si l'obligatoire est absent,
  **uniquement si la convention complémentaire le prévoit**).
- Les créances antérieures à la suspension poursuivent leur cycle ; un état dédié les suit (encours gelé, délai de paiement).
- La déclaration d'une suspension exige une source (communiqué, courrier) et la validation du titulaire ; elle est notifiée aux collaborateurs.

### 4.7 Règlements, lettrage, retenues, pertes

1. Un règlement d'organisme est un **encaissement** (M08) : il crédite la banque et solde des créances via le lettrage. Il ne crée
   jamais de chiffre d'affaires.
2. Lettrage proposé automatiquement (référence de bordereau, montant exact, puis antériorité), puis **confirmé par un humain**
   (comptable ou titulaire). Aucun lettrage automatique silencieux **[ARBITRAGE D55]**.
3. Un écart entre le montant reçu et le montant attendu reste **visible** : il est soit affecté (rejet, retenue), soit laissé
   en reste à régler. Il n'est jamais soldé implicitement.
4. Retenue annoncée : enregistrement explicite, puis acceptation (charge) ou contestation (litige). Une retenue acceptée n'efface pas l'historique.
5. Trop-perçu : dette envers l'organisme jusqu'à son remboursement ou son imputation, avec trace.
6. Passage en perte : titulaire uniquement, motif, document éventuel ; au-delà d'un seuil, double validation **[ARBITRAGE D52]**.
   Le chiffre d'affaires reste inchangé, la perte est une charge (§7).

### 4.8 Délais, échéances, alertes

| Alerte | Déclencheur | Destinataire |
|---|---|---|
| Transmission imminente | Créances `A_TRANSMETTRE` à J−5 de la date limite | Responsable tiers payant |
| Pièces manquantes | Créance `A_COMPLETER` depuis plus de N jours | Responsable, dispensateur |
| Règlement en retard | Échéance dépassée (délai conventionnel) | Titulaire, comptable |
| Taux de rejet anormal | Taux de rejet d'un organisme ou d'un motif au-dessus de la moyenne (score robuste, IA §6) | Titulaire |
| Encours élevé | Encours d'un organisme au-delà d'un seuil ou d'une part du CA | Titulaire |
| Convention arrivant à échéance | `valid_to` à J−30 | Titulaire |
| Couvertures expirées | Patients récurrents dont la couverture expire sous 30 jours | Comptoir (sans donnée médicale) |

**Balance âgée** : tranches 0–30, 31–60, 61–90, 91–180, plus de 180 jours, calculées depuis la date de transmission (option : depuis la date de facturation).

### 4.9 Données transmises aux organismes

- Seules les données exigées par la convention sont exportées (identité de l'assuré, numéro d'affiliation, produits, quantités, montants, prescripteur, références des pièces).
- Aucune donnée médicale supplémentaire (posologie, observations, diagnostic) n'est ajoutée aux exports.
- Chaque export de bordereau est journalisé dans `sensitive_access_log` (EXT03) avec le nombre de patients concernés.

### 4.10 États et exports

| État | Contenu | Filtres | Niveau de données |
|---|---|---|---|
| **TP1 Encours par organisme** | Facturé, à transmettre, transmis, accepté, rejeté, en litige, en attente de règlement, réglé, retenu, perdu | Organisme, convention, période (facturation, transmission, règlement) | Agrégé |
| **TP2 Balance âgée** | Reste dû par tranche d'ancienneté, par organisme et par bordereau | Organisme, date d'arrêté | Agrégé |
| **TP3 Rejets** | Montants et nombres de lignes rejetées par motif, par organisme, par produit, par collaborateur dispensateur (qualité de dossier), évolution | Organisme, motif, période, catégorie de motif | Agrégé ; liste nominative des dossiers selon droits (`claim.read.nominative`) |
| **TP4 Bordereaux** | Liste, statut, totaux, dates de clôture, de transmission et d'accusé, délais | Organisme, période, statut | Agrégé |
| **TP5 Règlements et lettrage** | Règlements reçus, montants lettrés, non lettrés, retenues, trop-perçus | Organisme, période | Agrégé |
| **TP6 Délais de paiement** | Délai moyen et médian entre transmission et règlement, par organisme et par mois | Organisme, période | Agrégé |
| **TP7 Répartition du chiffre d'affaires** | CA total = part patients + part organismes, par organisme, par mode, par mois ; réconciliation avec la couche métriques | Période, organisme, mode | Agrégé |
| **TP8 Justificatifs remis (remboursement par l'assuré)** | Nombre de ventes, montants, organismes concernés | Période, organisme | Agrégé |
| **TP9 Relevé d'un organisme** | Grand livre du tiers `ORGANISME` : créances, règlements, retenues, régularisations, solde | Organisme, période | Agrégé (références de dossiers) |

- Exports **PDF** (mise en page A4, en-tête de la pharmacie, filtres, date, auteur, pagination, FR/AR) et **Excel** (XLSX) pour tous les états.
- Les états agrégés sont exportables directement. Les listes nominatives de dossiers (patients) suivent le workflow d'export
  contrôlé d'EXT03 (W6 : motif, validation par un tiers, filigrane, lien limité dans le temps).
- Tous les états lisent les mêmes projections (`claims`, `claim_lines`, `party_ledger`) : les totaux concordent entre états,
  tableau de bord et relevé d'organisme (contrôle automatique).

---

## 5. Permissions

### 5.1 Catalogue (à ajouter au catalogue `permissions` d'EXT02 §11.2)

| Permission | Description | Sensibilité |
|---|---|---|
| `payer.read` | Consulter organismes, conventions, règles | 1 |
| `payer.manage` | Créer et modifier organismes, identifiants, contacts | 2 |
| `agreement.edit` / `agreement.validate` | Saisir une convention / la valider (quatre yeux) | 3 |
| `rule.edit` / `rule.validate` | Saisir une règle de prise en charge / la valider (quatre yeux, source obligatoire) | 3 |
| `reference_list.import` / `reference_list.validate` | Importer une liste ou des tarifs de référence / valider | 3 |
| `agreement.suspend` | Déclarer une suspension ou une reprise de convention | 3 |
| `coverage.read` | Voir les couvertures du patient servi (contexte de vente) | 2 |
| `coverage.read.nominative` | Rechercher et consulter les couvertures hors contexte de vente (journalisé) | 3 |
| `coverage.create` / `coverage.update` | Enregistrer et mettre à jour une couverture | 2 |
| `coverage.verify` | Porter une couverture au niveau `JUSTIFIEE` ou `VERIFIEE` | 2 |
| `sale.payer.apply` | Appliquer une prise en charge calculée lors d'une vente | 2 |
| `sale.payer.manual_share` | Saisir une part organisme manuelle (si la convention l'autorise, D47) — habilitation pharmacien | 3 |
| `claim.read` / `claim.read.nominative` | Consulter les créances (agrégées / nominatives) | 2 / 3 |
| `claim.prepare` | Compléter les pièces, préparer les bordereaux | 2 |
| `batch.close` / `batch.transmit` | Clôturer / déclarer transmis un bordereau | 3 |
| `response.record` | Saisir ou importer un décompte d'organisme | 2 |
| `remittance.record` / `remittance.allocate` | Saisir un règlement / confirmer un lettrage | 3 |
| `deduction.decide` | Accepter ou contester une retenue | 3 |
| `dispute.manage` | Ouvrir et suivre un litige | 2 |
| `claim.writeoff` | Passer une créance en perte (seuil + double validation, D52) | 3 |
| `claim.refacture` | Refacturer une part rejetée au patient (si autorisé, D50) | 3 |
| `report.payer` | États TP1 à TP9 agrégés | 2 |
| `export.claims.nominative.request` / `.approve` | Export nominatif de dossiers (workflow W6 d'EXT03) | 3 |

### 5.2 Matrice par rôle (valeurs par défaut, configurables par pharmacie et par collaborateur)

| Permission | Titulaire | Adjoint | Responsable tiers payant *(nouveau rôle, D57)* | Préparateur | Caissier | Comptable | Auditeur |
|---|---|---|---|---|---|---|---|
| `payer.read` | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| `payer.manage`, `agreement.edit`, `rule.edit`, `reference_list.import` | ✔ | ✔ | ✔ | — | — | — | — |
| `agreement.validate`, `rule.validate`, `reference_list.validate`, `agreement.suspend` | ✔ | — | — | — | — | — | — |
| `coverage.read`, `coverage.create`, `coverage.update` | ✔ | ✔ | ✔ | ✔ | lecture en vente | — | — |
| `coverage.verify` | ✔ | ✔ | ✔ | ✔ (`JUSTIFIEE`) | — | — | — |
| `coverage.read.nominative` | ✔ | ✔ | ✔ | — | — | — | journal |
| `sale.payer.apply` | ✔ | ✔ | ✔ | ✔ | ✔ | — | — |
| `sale.payer.manual_share` | ✔ | ✔ | — | — | — | — | — |
| `claim.read` | ✔ | ✔ | ✔ | ses dossiers | — | ✔ | ✔ |
| `claim.read.nominative` | ✔ | ✔ | ✔ | — | — | — | journal |
| `claim.prepare`, `response.record`, `dispute.manage` | ✔ | ✔ | ✔ | pièces seulement | — | — | — |
| `batch.close`, `batch.transmit` | ✔ | ✔ | ✔ | — | — | — | — |
| `remittance.record` | ✔ | — | ✔ | — | — | ✔ | — |
| `remittance.allocate`, `deduction.decide` | ✔ | — | — | — | — | ✔ | — |
| `claim.writeoff`, `claim.refacture` | ✔ | — | — | — | — | — | — |
| `report.payer` | ✔ | ✔ | ✔ | — | — | ✔ | ✔ |
| `export.claims.nominative.approve` | ✔ | — | — | — | — | — | — |

Règles transversales :
- Les droits effectifs suivent le moteur d'EXT02 §1.2 (rôle ∩ habilitations ∩ qualifications ∩ statut ∩ présence).
- **Séparation des tâches** : celui qui saisit une règle ne la valide pas ; celui qui enregistre un règlement ne confirme pas seul son
  lettrage au-delà d'un seuil ; celui qui demande un passage en perte ne le valide pas (D52).
- Toute délégation de ces droits par le titulaire suit le mécanisme de délégation d'**EXT05 §5** (délégation datée, plafonnée, révocable).

---

## 6. Dépendances avec les autres modules

### 6.1 Matrice

| Module | Dépendance | Sens | Remarque |
|---|---|---|---|
| M00 Plateforme | Séquences `CRE`, `BRD` ; audit ; notifications ; paramètres | M18 consomme | — |
| M15 RH / droits (EXT02) | Collaborateur agissant, habilitations, séparation des tâches | M18 consomme | Rôle « Responsable tiers payant » |
| M04 Catalogue | Produits, DCI, catégories, codes organismes | M18 consomme | Rapprochement des listes de référence |
| M07 Clients / volet patient (EXT03) | Fiche unique, `patient_profiles`, consentements | Partagé | `patient_coverages` remplace les champs texte |
| **M17 Ordonnances (EXT03)** | Lien ligne de vente ↔ ligne d'ordonnance, conformité R/M, prescripteur, pièces (scan) | M18 consomme | **Prérequis** : pas de tiers payant sur ordonnance sans EXT03 |
| **M02 / M02b Vente & caisse** | Répartition par ligne, encaissement de la part patient, transaction atomique | Bidirectionnelle, forte | Le théorique de caisse ne contient que la part patient |
| **M08 Finance** | `party_ledger` (tiers `ORGANISME`), `due_dates`, `payments`, banque, trésorerie projetée | Bidirectionnelle | Règlements = encaissements M08 |
| M05 Stock | Aucun effet direct ; indirect via les retours (EXT05) | — | — |
| M09 Réglementés | Aucun effet propre ; une vente réglementée reste soumise à sa séquence (EXT03 v1.1 §16) | — | — |
| **EXT05 Annulations / retours** | Régularisation des créances lors d'une annulation, d'un retour, d'un remboursement | Bidirectionnelle | Voir EXT05 §2 workflow C |
| M12 Rapports | États TP1 à TP9, dimensions `payer` et `payer_mode` | M12 consomme | — |
| M13 IA | Analyseurs §6.2 | M13 consomme | Agrégats sans donnée patient |
| M14 Hors-ligne | Tiers payant **interdit hors ligne** par défaut (droits, plafonds et suspensions non vérifiables) ; remboursement par l'assuré possible | — | D51 |

Événements de domaine ajoutés : `agreement.activated`, `agreement.suspended`, `agreement.resumed`, `coverage.verified`,
`claim.created`, `claim.batched`, `claim.transmitted`, `claim.decided`, `claim.rejected`, `claim.settled`, `claim.written_off`,
`remittance.recorded`, `deduction.recorded`, `dispute.opened`, `dispute.closed`.

### 6.2 PHARM'OS AI (analyseurs proposés, déterministes)

| Code | Analyse | Méthode | Exemple de recommandation |
|---|---|---|---|
| TP-A | Rejets anormaux | Taux de rejet par organisme × motif sur 12 semaines, score robuste (médiane / MAD) | « Le motif *pièce manquante* représente 40 % des rejets de l'organisme X ce mois-ci (habituel : 10 %) : vérifier la liste des pièces exigées » |
| TP-B | Prévision d'encaissement | Délais de paiement observés par organisme (distribution empirique) appliqués aux créances acceptées | « Encaissements attendus sur 30 j : 0 à 12 400 TND selon les délais observés » (valeurs fictives) |
| TP-C | Encours et dépendance | Part de l'encours organismes dans le CA, ancienneté, tendance | « L'encours de l'organisme X dépasse 90 jours de chiffre d'affaires tiers payant » |
| TP-D | Dossiers à risque avant transmission | Contrôle des pièces et des délais, motifs de rejet historiques par type de dossier | « 14 dossiers incomplets dont le délai de transmission expire dans 5 jours » |
| TP-E | Qualité de saisie | Rejets imputables à des erreurs de saisie, par poste (pas par personne nommée dans l'IA) | « Les rejets *numéro d'affiliation invalide* ont doublé : rappel de la procédure de vérification » |

Garde-fous : aucune donnée patient, aucun numéro d'affiliation, aucune ligne de dossier n'est transmise au LLM externe
(mêmes règles qu'EXT03 v1.1 §18.3). L'IA ne modifie aucune créance, aucune règle et aucun lettrage : elle signale et propose.

---

## 7. Impacts sur la comptabilité et les stocks

### 7.1 Principe

PHARM'OS tient les **registres de gestion** (ventes, créances, encaissements, grand livre des tiers) et produit des
**journaux exportables** pour le comptable. Il n'est pas un logiciel de comptabilité générale. Le plan de comptes et le
schéma d'écritures ci-dessous sont **indicatifs** et doivent être validés par un expert-comptable **[À VALIDER R32]**.

### 7.2 Effets par événement (exemple : vente de 100 avec part organisme de 70, montants fictifs)

| Événement | Chiffre d'affaires | Encaissement (caisse / banque) | Créance organisme | Créance patient | Charge |
|---|---|---|---|---|---|
| Vente validée (tiers payant) | **+100** (une seule fois) | +30 (caisse, part patient) | **+70** | — | — |
| Vente en remboursement par l'assuré | +100 | +100 | — | — | — |
| Créance transmise, acceptée | — | — | statut seulement | — | — |
| Règlement de l'organisme (70) | — | **+70** (banque) | **−70** | — | — |
| Règlement partiel (50) | — | +50 | −50 (reste 20) | — | — |
| Rejet corrigé et retransmis | — | — | statut seulement | — | — |
| Rejet refacturé au patient (20) | — | — | −20 | **+20** | — |
| Rejet passé en perte (20) | **inchangé** | — | −20 | — | **+20** (perte sur créance) |
| Retenue acceptée (5) | **inchangé** | — | −5 | — | **+5** (charge / pénalité, qualification à valider) |
| Trop-perçu (organisme paie 75 pour 70) | — | +75 | −70 et dette de 5 envers l'organisme | — | — |
| Annulation de vente avant transmission (EXT05) | −100 (écriture inverse datée de l'annulation) | −30 (remboursement du patient) | −70 (créance annulée) | — | — |
| Retour après transmission (EXT05) | −100 (avoir) | −30 | −70 via une ligne de régularisation au bordereau suivant (ou avoir à l'organisme) | — | — |

**Règles de non-confusion** :
1. Le chiffre d'affaires est constaté **une seule fois**, à la validation de la vente, pour son montant total. Il ne varie que
   par une annulation ou un retour (écritures inverses datées, EXT05), jamais par un rejet, un règlement ou une retenue.
2. La créance organisme naît à la validation de la vente (droits constatés) **[ARBITRAGE D53]**. Elle ne se transforme qu'à travers
   des écritures explicites : règlement, rejet suivi de son traitement, retenue, perte, refacturation.
3. L'encaissement ne se mêle jamais à la créance : un virement non lettré reste « à lettrer », et une créance réglée l'est par
   une allocation identifiée.
4. **TVA et droit de timbre** : la TVA est calculée sur la vente, quel que soit le payeur. La répartition patient / organisme
   porte sur le TTC. Le traitement de la TVA et du timbre sur les factures adressées aux organismes (et une éventuelle obligation
   de facture électronique) est **[À VALIDER R26]**.

### 7.3 Journaux exportés pour la comptabilité

| Journal | Contenu |
|---|---|
| Ventes | CA HT, TVA par taux, TTC, répartition patient / organismes, avoirs |
| Caisse et banque | Encaissements des patients (par mode), règlements des organismes, remboursements |
| Créances organismes | Constitution, règlements, retenues, rejets traités, pertes, trop-perçus, régularisations |
| Balance des tiers | Soldes clients, fournisseurs, organismes à une date |

Exports CSV ou Excel, au format de colonnes paramétrable (logiciel du comptable). Chaque export est horodaté et tracé.

### 7.4 Stocks

- Une prise en charge n'a **aucun effet sur le stock** : les mouvements `SALE` sont identiques avec ou sans organisme payeur.
- Le rejet d'une créance n'entraîne **aucun mouvement de stock** (le produit a été délivré).
- Les retours de produits relèvent d'**EXT05** (contrôle de réintégration) ; la régularisation de la créance est indépendante du sort physique du produit.

---

## 8. Exigences réglementaires

Numérotation à la suite d'EXT03 v1.1 (R1 à R22). Tant qu'un point n'est pas confirmé, le module applique la valeur la plus prudente :
**aucune prise en charge sans règle sourcée et validée**, et vérification `VERIFIEE` exigée.

| # | Sujet | Hypothèse de conception | Question précise | Interlocuteurs | Échéance |
|---|---|---|---|---|---|
| R23 | Statut de la convention CNAM / pharmaciens | Convention suspendue depuis le 01/11/2026 selon le syndicat ; non renouvelée pour 2026 selon le syndicat | Texte en vigueur à la date de mise en service ; conditions et procédures de reprise ; avenants | CNAM, SPOT, CNOPT | Avant le lot 7b |
| R24 | Pièces justificatives par organisme et par type de dossier | Paramétrables, bloquantes ou non | Liste officielle des pièces (ordonnance originale ou copie, étiquettes, carte, décision APCI…), sort de l'original (conservé ou transmis) | CNAM, mutuelles, assureurs | Avant le lot 7b |
| R25 | Durées de conservation | Paramétrables | Durées pour pièces, bordereaux, décomptes, couvertures (fiscal, comptable, conventionnel) | Juriste, expert-comptable, CNAM | Avant le lot 7b |
| R26 | Facturation aux organismes | TVA calculée sur la vente ; mentions paramétrables | Mentions obligatoires, TVA et timbre sur les factures aux organismes ; obligation éventuelle de facture électronique (TTN / El Fatoora) | Expert-comptable, administration fiscale | Avant le lot 7b |
| R27 | Vérification fiable des droits | Aucun service supposé ; niveau `VERIFIEE` par attestation récente ou décision écrite | Existe-t-il un service officiel de vérification des droits (CNAM, assureurs) et à quelles conditions d'accès ? | CNAM, assureurs | Avant le lot 7b |
| R28 | Refacturation d'une part rejetée au patient | Désactivée par défaut (D50) | Est-elle licite, et à quelles conditions (information préalable, délai) ? | Juriste, CNAM, conventions | Avant activation |
| R29 | Données de santé transmises aux assureurs privés et mutuelles | Transmission limitée aux données exigées par la convention | Base légale (convention, consentement du patient), déclaration INPDP, contrat de sous-traitance | INPDP, juriste | Avant le lot 7b |
| R30 | Remboursement par l'assuré pendant une suspension | Justificatifs paramétrables (modèles importés) | Formulaires et pièces exigés par la procédure exceptionnelle en vigueur ; mentions à porter par le pharmacien | CNAM | Dès la mise en service |
| R31 | Taux, plafonds, listes de produits remboursables, prix de référence | **Aucune valeur fournie** ; saisie à partir de documents officiels | Sources officielles à jour (nomenclature, listes, tarifs de référence, règles génériques) et fréquence de mise à jour | CNAM, ministère de la Santé | Avant toute activation de règle |
| R32 | Traitement comptable | Créance à la vente, perte en charge, retenue en charge | Constatation des créances, provisions pour créances douteuses, qualification des retenues, plan de comptes | Expert-comptable | Avant le lot 7b |
| R33 | Autres pays | Organismes et profils génériques | Règles locales du tiers payant pour chaque pays ouvert | Juristes locaux | Futur |

---

## 9. Matrice de tests

Jeu de données de référence **fictif** (organismes, conventions, règles et taux inventés pour les tests, jamais livrés en production).

| ID | Domaine | Scénario | Résultat attendu |
|---|---|---|---|
| TP-01 | Paramétrage | Activer une règle sans document source | Refus (contrainte) |
| TP-02 | Paramétrage | Le même collaborateur saisit et valide une règle | Refus (quatre yeux) |
| TP-03 | Paramétrage | Nouvelle version d'une règle datée du 1er du mois | Ventes antérieures inchangées ; ventes postérieures calculées avec la v2 |
| TP-04 | Paramétrage | Deux règles également spécifiques pour un produit | Part 0, alerte de paramétrage ; aucun choix arbitraire |
| TP-05 | Listes | Import d'une liste avec codes non rapprochés | Éléments `A_RAPPROCHER` sans effet sur le calcul |
| TP-06 | Couverture | Couverture `DECLAREE`, convention exigeant `VERIFIEE` | Tiers payant refusé, mode remboursement par l'assuré proposé |
| TP-07 | Couverture | Vérification expirée | Couverture redescendue à `JUSTIFIEE`, contrôle E5 appliqué |
| TP-08 | Couverture | Couverture expirée la veille de la vente | Part organisme 0, motif `DROITS_NON_VALIDES` |
| TP-09 | Calcul | Base `TARIF_REFERENCE` inférieure au prix | Part calculée sur le tarif ; écart payé par le patient |
| TP-10 | Calcul | Tarif de référence absent | Part 0, motif `TARIF_ABSENT` |
| TP-11 | Calcul | Cascade obligatoire + complémentaire sur le reste à charge | Σ parts ≤ prix ; part patient = reste ; trace complète |
| TP-12 | Calcul | Plafond de période atteint en cours de vente | Part plafonnée, compteur exact ; une annulation libère le plafond |
| TP-13 | Calcul | Arrondis sur 50 lignes de référence | Résultats identiques au jeu calculé à la main ; reliquat d'arrondi côté patient |
| TP-14 | Calcul | Même entrée exécutée deux fois | Résultat et trace identiques (déterminisme) |
| TP-15 | Vente | Tentative d'augmenter manuellement une part calculée | Refus (sauf D47 activé : pharmacien + justificatif) |
| TP-16 | Vente | Pièce bloquante manquante | Tiers payant refusé pour cet organisme ; vente possible en intégral |
| TP-17 | Vente | Validation : contrôle de l'invariant total = parts | CHECK respecté ; tentative incohérente → transaction annulée |
| TP-18 | Vente | Perte de connexion pendant la validation | Tout ou rien (vente, parts, créance, grand livre) ; reprise idempotente |
| TP-19 | Caisse | Clôture d'une session avec ventes en tiers payant | Théorique = parts patients encaissées uniquement |
| TP-20 | Suspension | Convention suspendue à J ; vente à J−1 et à J | J−1 : tiers payant ; J : repli automatique, motif affiché |
| TP-21 | Suspension | Créances antérieures à la suspension | Cycle normal, état « encours gelé » exact |
| TP-22 | Remboursement par l'assuré | Vente et impression des justificatifs | Aucune créance ; justificatifs tracés ; duplicata marqué |
| TP-23 | Bordereau | Créance incluse dans deux bordereaux ouverts | Refus |
| TP-24 | Bordereau | Modification après clôture | Impossible ; régularisation par bordereau suivant |
| TP-25 | Bordereau | Délai de transmission dépassé | Alerte, statut hors délai, décision tracée |
| TP-26 | Décompte | Acceptation partielle d'une ligne | Écart en rejet motivé ; invariants respectés |
| TP-27 | Rejet | Correction et retransmission | Nouvelle tentative, historique complet, compteur de tentatives |
| TP-28 | Rejet | Passage en perte au-delà du seuil | Double validation exigée ; CA inchangé ; charge enregistrée |
| TP-29 | Rejet | Refacturation au patient (D50 activé) | Créance client créée ; vente non modifiée |
| TP-30 | Règlement | Virement couvrant plusieurs bordereaux | Lettrage proposé ; confirmation humaine ; restes exacts |
| TP-31 | Règlement | Virement inférieur au montant attendu sans motif | Écart visible, créances non soldées |
| TP-32 | Retenue | Retenue annoncée puis contestée | Litige ouvert ; aucune écriture de charge tant que la retenue n'est pas acceptée |
| TP-33 | Trop-perçu | Règlement supérieur | Dette envers l'organisme enregistrée |
| TP-34 | Invariants | Recalcul complet des créances depuis les événements | Égalité avec les projections |
| TP-35 | Comptabilité | Réconciliation CA = parts patients + parts organismes | Égalité par jour et par mois |
| TP-36 | EXT05 | Annulation d'une vente avant transmission | Créance `ANNULEE`, remboursement du patient de sa seule part |
| TP-37 | EXT05 | Retour après transmission | Ligne de régularisation au bordereau suivant ; aucune double écriture |
| TP-38 | Droits | Préparateur tente de valider une règle | 403, tentative journalisée |
| TP-39 | Confidentialité | Consultation des couvertures hors contexte de vente | Journalisée dans `sensitive_access_log` ; refusée sans permission |
| TP-40 | Confidentialité | Charges utiles transmises au LLM | Aucun numéro d'affiliation, aucun nom ; agrégats seulement |
| TP-41 | États | TP1 à TP9 recalculés sur le jeu de référence ; exports PDF et Excel | Totaux identiques à l'écran et entre états |
| TP-42 | Hors ligne | Tentative de tiers payant hors ligne | Refusée ; remboursement par l'assuré possible |

---

## 10. Décisions nécessitant arbitrage

Numérotation à la suite d'EXT03 v1.1 (D27 à D43).

| # | Question | Options | Recommandation |
|---|---|---|---|
| **D44** | Périmètre du module à la mise en service, compte tenu de la suspension du tiers payant CNAM | A. Module complet dès la v1 · B. v1 limitée au remboursement par l'assuré et aux conventions privées, tiers payant CNAM activable par paramétrage · C. Reporter le module | **B — PROPOSITION NON ARBITRÉE**. Ne vaut pas autorisation d'activer le tiers payant CNAM ; toute activation reste bloquée tant que D37 n'est pas arbitrée par le PO. |
| **D45** | Organismes ciblés en priorité | CNAM · mutuelles · assureurs privés · conventions d'entreprise | Tous génériques ; priorités de paramétrage selon les pharmacies pilotes |
| **D46** | Niveau de vérification minimal par défaut pour le tiers payant | `JUSTIFIEE` · `VERIFIEE` | **`VERIFIEE`** ; `JUSTIFIEE` seulement si la convention le prévoit expressément |
| **D47** | Saisie manuelle d'une part organisme hors moteur | Interdite · Autorisée par convention, pharmacien + justificatif | Interdite par défaut, activable par convention |
| **D48** | Source des listes et tarifs de référence | A. Import par chaque pharmacie · B. Fichiers sourcés fournis par APEX, activation et validation par la pharmacie | **B**, avec responsabilité de validation explicitement laissée au titulaire (et mentionnée au contrat) |
| **D49** | Stockage du diagnostic ou du code d'affection (APCI) | Stocker · Ne stocker que la référence de la décision et le périmètre de produits | **Ne pas stocker** de diagnostic (minimisation) |
| **D50** | Refacturation au patient d'une part rejetée | Désactivée · Activable par convention | Désactivée par défaut (R28) |
| **D51** | Télétransmission | v1 · Ultérieure (adaptateurs) | **Ultérieure** ; v1 : exports PDF / Excel / fichiers au format de l'organisme ; tiers payant interdit hors ligne |
| **D52** | Seuil de double validation pour les passages en perte | Montant fixe · Aucun seuil | Montant paramétrable (proposition : au-delà du montant d'un panier moyen) |
| **D53** | Date de naissance de la créance | À la vente · À la transmission | **À la vente** (droits constatés, cohérence du CA) — à confirmer par l'expert-comptable (R32) |
| **D54** | TVA et timbre sur les factures aux organismes | — | Selon R26 ; aucun choix par défaut avant validation |
| **D55** | Lettrage des règlements | Automatique · Proposé puis confirmé | **Proposé puis confirmé** par un humain |
| **D56** | Mode de repli par défaut en cas de suspension | Remboursement par l'assuré · Paiement intégral | **Remboursement par l'assuré** (justificatifs imprimés) |
| **D57** | Rôle « Responsable tiers payant » | Nouveau rôle · Attribution au comptable ou à l'adjoint | Nouveau rôle, cumulable |
| **D58** | IA sur les données du tiers payant | Agrégats par organisme · Aucune | Agrégats par organisme, sans donnée patient ni numéro d'affiliation |

---

## 11. Position dans le plan de développement

| Lot | Contenu EXT04 | Justification |
|---|---|---|
| **2. Catalogue** (ajout) | `payer_codes` sur les produits | Rapprochement des listes |
| **5. Caisse & ventes** (ajout) | Colonnes `part_patient` / `part_organismes` et contrainte d'invariant **dès la création** des tables (part patient = total tant que le module est inactif) | Évite toute migration ultérieure de la table la plus volumineuse |
| **7. Finance** (ajout) | Tiers `ORGANISME` dans `party_ledger`, origine `CREANCE_ORGANISME` dans `due_dates` | Grand livre unique |
| **7b. Tiers payant** (nouveau, après 5b et 7) | M18.1 à M18.8 : organismes, conventions, suspensions, règles sourcées, simulateur, couvertures, moteur, panneau de caisse, remboursement par l'assuré, créances, bordereaux, décomptes, règlements, retenues, litiges, états TP1 à TP9 | Dépend d'EXT03 (ordonnances) et de la finance |
| **11. Rapports** (ajout) | Dimensions `payer`, journaux comptables exportés | Couche métriques unique |
| **13. IA** (ajout) | Analyseurs TP-A à TP-E | Après 7b et 11 |
| **15. SaaS** (ajout) | Référentiel APEX des organismes et des listes sourcées (D48), profils pays | Industrialisation |

Prérequis bloquants avant le lot 7b : R23 à R27, R29, R31 (au moins les sources), R32 ; décisions D44, D46, D48, D53.
