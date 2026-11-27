# PHARM'OS — Extension fonctionnelle n° 03 : Ordonnances, prescripteurs et analyse des prescriptions

**Produit** : PHARM'OS — APEX AI DEV
**Version** : 1.0 (proposition) — 2026-11-03
**Statut** : **PROPOSITION TECHNIQUE — AUCUN CODE**. Les arbitrages D27–D39 ne sont pas globalement approuvés ; D37 reste « À RÉEXAMINER » selon la matrice maître D1–D86 v1.3.3. D40 est désormais **ARBITRÉE** : réservation sans avance, durée par défaut de deux jours comptables, configurable par l'administrateur ; les règles détaillées de D40 dans la matrice v1.3.3 prévalent sur toute formulation antérieure de cette proposition. Les exigences réglementaires restent à valider. Les modules existants ne sont pas modifiés :
les changements qu'ils nécessitent sont listés au §3 et ne seront reportés dans le dossier principal
qu'après validation des arbitrages du §13.
**Module créé** : **M17 Ordonnances & prescripteurs**
**Modules impactés** : M02/M02b Vente & caisse · M04 Catalogue · M05 Stock · M07 Clients/Patients · M08 Finance ·
M09 Réglementés · M11 Commandes · M12 Rapports · M13 IA · M15 RH (habilitations) · M16 Laboratoire

> Les points marqués **[À VALIDER]** relèvent de la réglementation (Tunisie, puis pays futurs) ;
> les points **[ARBITRAGE Dxx]** demandent une décision du propriétaire (§13).

---

## Sommaire

1. Vision et principes
2. Écrans et workflows
3. Modèle de données : nouvelles tables, relations, modifications des tables existantes
4. Règles métier et contrôles
5. Droits d'accès
6. Impacts sur stock, ventes, caisse, finance et statistiques
7. Reporting et états
8. Tableau de bord prescriptions
9. PHARM'OS AI
10. Confidentialité et conformité
11. Dépendances avec les autres modules
12. Position dans le plan de développement et tests
13. Arbitrages et exigences réglementaires à valider

---

## 1. Vision et principes

### 1.1 Ce que le module apporte

| Besoin | Réponse |
|---|---|
| Délivrer vite et proprement sur ordonnance | Mode « Ordonnance » intégré à l'écran de vente, sans changer d'écran |
| Suivre ce qui a été prescrit **et** ce qui a été délivré | Ordonnance structurée en lignes prescrites, avec délivrances successives (partielles, reliquats, renouvellements) |
| Fiabiliser les statistiques par médecin, spécialité, médicament | Répertoire des prescripteurs dédoublonné, spécialités issues d'un référentiel, instantané de la spécialité à la date de l'ordonnance |
| Mieux prévoir le stock | La « demande engagée » (reliquats et renouvellements attendus) alimente les commandes intelligentes |
| Respecter la confidentialité | Rapports agrégés par défaut, accès nominatif restreint et journalisé, aucune donnée nominative de patient vers l'IA externe |

### 1.2 Principes structurants

1. **Une ordonnance est un document distinct de la vente.** Une ordonnance peut être délivrée en plusieurs
   ventes (délivrance partielle, reliquat, renouvellement) et une vente peut porter plusieurs ordonnances
   (ex. famille) ainsi que des produits sans ordonnance. Le lien se fait **ligne à ligne**.
2. **Identifiant unique interne** : chaque ordonnance reçoit un numéro séquentiel non réutilisable
   (`ORD-2026-000123`), indépendant du numéro éventuel porté par le document papier.
3. **Prescrit ≠ délivré** : la quantité prescrite est saisie sur la ligne d'ordonnance ; les quantités délivrées
   sont **toujours** la somme des délivrances (registre immuable `prescription_dispensations`). Aucun compteur saisi.
4. **Stock inchangé avant validation** : enregistrer une ordonnance ne crée aucun mouvement de stock. Seule la
   validation de la vente crée les mouvements `SALE` (principe déjà posé au dossier principal §5.1).
5. **Instantanés** : le nom et la spécialité du prescripteur sont figés sur l'ordonnance au moment de la saisie ;
   modifier ensuite la fiche du médecin ne réécrit pas l'historique.
6. **Le professionnel décide** : le système contrôle et alerte (validité, quantités, substitution), le pharmacien
   valide. L'IA n'intervient jamais sur le contenu d'une prescription.
7. **Minimisation** : on ne saisit que ce qui sert à la délivrance, à la traçabilité réglementaire et aux
   statistiques. La posologie et les données cliniques ne sont pas obligatoires **[ARBITRAGE D38]**.

---

## 2. Écrans et workflows

### 2.1 Nouveaux écrans

| Code | Écran | Route | Usage |
|---|---|---|---|
| E1 | **Caisse — mode Ordonnance** | `/pos` (bascule `F4` ou bouton « Ordonnance ») | Saisie patient, prescripteur, lignes prescrites/délivrées, substitution, scan |
| E2 | Liste des ordonnances | `/prescriptions` | Recherche par n° interne ou externe, patient, prescripteur, date, statut ; filtre « reliquats à délivrer », « renouvellements dus » |
| E3 | Fiche ordonnance | `/prescriptions/:id` | En-tête, lignes (prescrit / délivré / reste), historique des délivrances, substitutions, documents, journal ; actions « Délivrer le reste », « Renouveler », « Rectifier », « Clôturer » |
| E4 | Répertoire des prescripteurs | `/prescribers` | Recherche instantanée, filtres spécialité / ville / type / statut, création |
| E5 | Fiche prescripteur | `/prescribers/:id` | Coordonnées, spécialités, historique des modifications, statistiques agrégées (selon droits) |
| E6 | Doublons de prescripteurs | `/prescribers/duplicates` | Paires candidates avec score et motifs, décision « Distincts » / « Fusionner » |
| E7 | Référentiel des spécialités | `/settings/specialties` | Administration (libellés FR/AR/EN, regroupements, activation) |
| E8 | Fiche patient — onglet Ordonnances | `/patients/:id` | Historique des ordonnances et délivrances (accès nominatif journalisé) |
| E9 | Rapports prescriptions | `/reports/prescriptions/{prescriber,specialty,product,patient}` | États A à D, impression, export |
| E10 | Tableau de bord prescriptions | `/analytics/prescriptions` | Tops, évolutions, répartitions, comparaisons |
| E11 | Journal des accès sensibles | `/audit/sensitive-access` | Consultations et exports de données patients et ordonnances |
| E12 | Demandes d'export nominatif | `/reports/exports` | Motif, validation, téléchargement à durée limitée |

### 2.2 Écran de vente en mode Ordonnance (E1)

L'objectif est de ne pas ralentir le comptoir : tout se fait au clavier et au lecteur, dans le même écran.

```
┌──────────────────────────────────────────────────────────────────────────────┐
│ [Comptoir] [Ordonnance ●]   Onglets ventes en attente 1 2 3                  │
├──────────────────────────────────────────────────────────────────────────────┤
│ ORDONNANCE ORD-2026-000123 (nouvelle)        Date ordonnance [__/__/____]    │
│ Patient   [recherche nom / tél / code ▾] [+ Nouveau]   ou ☐ Patient non identifié │
│ Prescripteur [recherche nom / ville ▾] [+ Nouveau]  → Spécialité : Cardiologie (auto) │
│ Type [Simple ▾]  Renouvelable ☐ ×[ ]   N° sur document [ ]   📷 Scanner / joindre │
├──────────────────────────────────────────────────────────────────────────────┤
│ # Produit prescrit        │ Prescrit │ Délivré │ Reste │ NS │ Lot/Pér. │ Prix │
│ 1 AMLOR 5 mg 30 cp        │ 2 bt     │ 1 bt    │ 1 bt  │    │ L23 08/27│ 9,800│
│   ↳ substitué par AMLODIPINE X 5 mg (générique, rupture) — validé par Dr Y   │
│ 2 (DCI) Paracétamol 1 g   │ 1 bt     │ 1 bt    │ 0     │    │ …        │ 2,450│
├──────────────────────────────────────────────────────────────────────────────┤
│ + Produits hors ordonnance (conseil)                                         │
│ Reste à délivrer : 1 ligne → [Patient reviendra ▾ | À commander | Non délivré]│
│                               [Valider la délivrance et encaisser]           │
└──────────────────────────────────────────────────────────────────────────────┘
```

- **Saisie rapide** : la recherche du prescripteur propose d'abord les médecins les plus fréquents de la
  pharmacie, puis la recherche phonétique FR/AR ; la spécialité est remplie automatiquement.
- **Création rapide** d'un prescripteur (nom, prénom, spécialité, ville) avec contrôle immédiat des doublons ;
  la fiche est créée au statut `A_COMPLETER`.
- **Création rapide** d'un patient (nom, prénom, téléphone facultatif) ; option « patient non identifié »
  pour les ordonnances simples si la règle le permet **[ARBITRAGE D28]**.
- **Lignes** : scan du produit délivré → la ligne prescrite correspondante est proposée (même produit ou même DCI) ;
  la quantité prescrite est saisie en boîtes ou en unités ; la quantité délivrée est proposée = reste, modifiable.
- **Substitution** : si le produit scanné diffère du prescrit, le système qualifie l'écart (même DCI-dosage-forme,
  autre dosage, autre forme) et applique les règles du §4.4.
- **Scan / photo** de l'ordonnance (caméra du poste, de la tablette ou du smartphone appairé), non bloquant sauf
  règle contraire.
- **Statut à compléter** : si l'ordonnance est incomplète et que la règle l'autorise, la vente peut être validée
  et l'ordonnance apparaît dans la liste « à compléter » **[ARBITRAGE D29]**.

### 2.3 Workflow W1 — Première délivrance

```
Caissier/vendeur bascule en mode Ordonnance
  → patient : recherche / création rapide / non identifié (si autorisé)
  → prescripteur : recherche / création rapide (contrôle doublons) → spécialité figée
  → en-tête : date, type, renouvellements, n° externe, scan
  → lignes prescrites + quantités délivrées (+ produits hors ordonnance)
  → contrôles (§4) : validité, produits sur ordonnance, quantités, substitution, réglementés, complétude
     → blocage ou validation pharmacien (PIN) selon la règle
  → réservation éventuelle des lots (aucun mouvement de stock)
  → VALIDATION DE LA VENTE — transaction atomique :
       vente + lignes (liées aux lignes d'ordonnance) + mouvements SALE (FEFO)
       + prescription_dispensations + substitutions + statut ordonnance recalculé
       + registre réglementé (si concerné) + paiements + mouvement de caisse nominatif
       + journal d'audit + événements sale.completed / prescription.dispensed
  → ticket de caisse (mention du n° ORD, sans donnée médicale) ; facture si demandée
  → reliquat : PATIENT_REVIENDRA | A_COMMANDER (crée une demande engagée pour M11) | NON_DELIVRE (motif)
```

### 2.4 Workflow W2 — Délivrance du reste ou renouvellement

```
Recherche de l'ordonnance (n° ORD, scan du ticket, patient, prescripteur)
  → la fiche affiche prescrit / délivré / reste par ligne et les renouvellements restants
  → « Délivrer le reste » ou « Renouvellement n° k » → la caisse s'ouvre pré-remplie
  → contrôles : validité, nombre de renouvellements, délai minimal entre délivrances (si règle)
  → validation de la vente → nouvelles délivrances liées à la même ordonnance
```

### 2.5 Workflow W3 — Retour ou annulation

- **Annulation** de la vente avant clôture de la session : contre-écritures dans `prescription_dispensations`
  (quantités négatives) ; le statut de l'ordonnance est recalculé ; le reste redevient disponible.
- **Retour** après clôture : avoir lié aux lignes d'origine ; même contre-écriture ; réintégration au stock
  selon l'état du produit (règle existante).
- Les statistiques sont **nettes des retours** (§7.1).

### 2.6 Workflow W4 — Rectification d'une ordonnance

```
Avant toute délivrance validée : modification libre par l'auteur ou un pharmacien (versionnée)
Après délivrance : RECTIFICATION uniquement (pharmacien, motif obligatoire)
  → nouvelle version de l'en-tête ou de la ligne, l'ancienne est conservée
  → la correction du prescripteur ou de la spécialité met à jour les faits analytiques (recalcul tracé)
  → les délivrances ne sont jamais modifiées
```

### 2.7 Workflow W5 — Gestion du répertoire des prescripteurs

```
Création (rapide en caisse ou complète en back-office)
  → normalisation (casse, accents, préfixes « Dr », translittération AR↔FR) → recherche des doublons (§4.6)
  → score ≥ seuil haut : création bloquée, choix d'une fiche existante proposé
  → seuil bas ≤ score < seuil haut : création possible, paire envoyée à l'écran E6
Modification : champs non sensibles par un pharmacien ; identifiant professionnel et fusion par le titulaire ;
  motif obligatoire ; historique versionné
Fusion B → A : B passe au statut FUSIONNE (merged_into_id = A), rien n'est supprimé ;
  les ordonnances gardent leur lien d'origine ; les rapports agrègent sur la fiche résolue ; fusion réversible (défusion tracée)
```

### 2.8 Workflow W6 — Export de données nominatives

```
Demande (rapport + filtres + motif) → niveau calculé automatiquement (AGRÉGÉ / PRESCRIPTEUR NOMINATIF / PATIENT NOMINATIF)
  → AGRÉGÉ : export immédiat (journalisé)
  → PRESCRIPTEUR NOMINATIF : permission dédiée, export immédiat journalisé
  → PATIENT NOMINATIF : validation par le titulaire (≠ demandeur), fichier filigrané, lien valable 24 h,
     téléchargement unique, purge automatique
```

---

## 3. Modèle de données

Conventions du dossier principal (§4.1) : `tenant_id` partout, ULID, montants en millimes, quantités en plus
petite unité, colonnes d'audit, pas de suppression physique, attribution `employee_id` (EXT02).

### 3.1 Choix de modélisation du patient : pas de second référentiel

Le cahier des charges impose une **source unique** pour les clients. Le patient n'est donc **pas** une
nouvelle entité concurrente de `customers` (M07) : c'est une **facette** de la même personne.

| Élément | Où | Pourquoi |
|---|---|---|
| Identité commune (nom, téléphone, code) | `customers` (existant) | Une personne = une fiche, qu'elle achète au comptoir, à crédit ou sur ordonnance |
| Données de santé et d'identification du patient | **`patient_profiles`** (nouvelle table, 1-1, cloisonnée) | Accès restreint et journalisé, séparé des données commerciales et financières |
| Historique des ordonnances | `prescriptions` → `customers.id` | — |

Le client de passage (`is_walk_in`) ne peut jamais porter une ordonnance nominative : il correspond au
cas « patient non identifié » (§4.1).

### 3.2 Nouvelles tables

#### Référentiel et répertoire

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `medical_specialties` | id, tenant_id (NULL = référentiel APEX), code, libelle_fr, libelle_ar, libelle_en, groupe (`MEDECINE_GENERALE`, `MEDICALE`, `CHIRURGICALE`, `DENTAIRE`, `AUTRE`), ordre, actif, source, version_ref | UNIQUE(COALESCE(tenant_id,''), code) ; une spécialité utilisée n'est jamais supprimée, seulement désactivée |
| `prescribers` | id, tenant_id, type (`MEDECIN`, `CHIRURGIEN_DENTISTE`, `SAGE_FEMME`, `VETERINAIRE`, `AUTRE` **[À VALIDER]**), civilite, nom, prenom, nom_ar, prenom_ar, **nom_normalise**, **cle_phonetique**, specialite_principale_id, identifiant_pro_type, identifiant_pro_valeur, telephone, telephone_normalise, email, cabinet, adresse, ville, gouvernorat, pays, observations, statut (`ACTIF`, `A_COMPLETER`, `INACTIF`, `FUSIONNE`), merged_into_id, version, created_by_employee_id, updated_by_employee_id | INDEX(tenant_id, nom_normalise) ; INDEX(tenant_id, cle_phonetique) ; INDEX(tenant_id, specialite_principale_id) ; INDEX(tenant_id, ville) ; **UNIQUE(tenant_id, identifiant_pro_type, identifiant_pro_valeur) WHERE valeur NOT NULL** ; CHECK(statut='FUSIONNE' ⇔ merged_into_id NOT NULL) |
| `prescriber_specialties` | prescriber_id, specialty_id, principale (bool), valid_from, valid_to | Spécialités secondaires, historisées |
| `prescriber_history` (**immuable**) | id, prescriber_id, version, avant (JSON), apres (JSON), champs_modifies, motif, employee_id, at | Historique des modifications consultable sur la fiche |
| `prescriber_duplicate_candidates` | id, tenant_id, prescriber_a_id, prescriber_b_id, score (0-100), motifs (JSON : nom, phonétique, téléphone, ville, identifiant), statut (`A_EXAMINER`, `DISTINCTS`, `FUSIONNES`), decide_par_employee_id, decide_le | UNIQUE(tenant_id, a_id, b_id) avec a_id < b_id |
| `prescriber_merges` | id, tenant_id, source_id, cible_id, motif, employee_id, at, annule_le, annule_par | Fusion et défusion tracées |

#### Patients

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `patient_profiles` (**cloisonnée**) | customer_id (PK), date_naissance ou annee_naissance, sexe (facultatif), identifiant_assure (facultatif, chiffré), organisme (facultatif), nom_normalise, cle_phonetique, observations_dispensation (chiffrées, facultatives), cree_par_employee_id | Accès : `patient.read.nominative` ; chaque lecture est journalisée |
| `patient_duplicate_candidates` | même structure que pour les prescripteurs | — |

#### Ordonnances et délivrances

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `prescriptions` (**remplace** la table minimale du dossier §4.5) | id, tenant_id, **numero_interne** (séquence `ORD-AAAA-nnnnnn`), numero_externe, type (`SIMPLE`, `RENOUVELABLE`, `REGLEMENTEE`, `PREPARATION`, `AUTRE`), support (`PAPIER`, `ELECTRONIQUE` (futur)), patient_customer_id (nullable), patient_non_identifie (bool), prescriber_id, **prescriber_snapshot** (JSON : nom, prénom, type, identifiant, ville), **specialty_id_snapshot**, specialty_label_snapshot, date_prescription, date_presentation, validite_jusqu_au (calculée), renouvellements_autorises, statut (`BROUILLON`, `A_COMPLETER`, `ENREGISTREE`, `PARTIELLEMENT_DELIVREE`, `DELIVREE`, `CLOTUREE`, `EXPIREE`, `ANNULEE`), motif_cloture, completude (JSON des éléments manquants), created_by_employee_id, validated_by_pharmacist_id, version, archived_at | UNIQUE(tenant_id, numero_interne) ; INDEX(tenant_id, date_prescription) ; INDEX(tenant_id, prescriber_id, date_prescription) ; INDEX(tenant_id, patient_customer_id) ; INDEX(tenant_id, statut) ; INDEX(tenant_id, numero_externe) ; CHECK(patient_customer_id NOT NULL OR patient_non_identifie) |
| `prescription_lines` | id, tenant_id, prescription_id, numero_ligne, prescribed_product_id (nullable), prescribed_dci_id (nullable), libelle_prescrit (texte, si hors catalogue), dosage, forme, **quantite_prescrite_unites**, unite_saisie (`BOITE`, `UNITE`), quantite_saisie, duree_traitement_j (facultatif), posologie_texte (facultatif **[ARBITRAGE D38]**), non_substituable (bool), renouvelable (bool), statut (`A_DELIVRER`, `PARTIELLE`, `DELIVREE`, `NON_DELIVREE`, `ANNULEE`), motif_non_delivrance, reliquat_mode (`PATIENT_REVIENDRA`, `A_COMMANDER`, `NON_DELIVRE`) | UNIQUE(prescription_id, numero_ligne) ; INDEX(tenant_id, prescribed_product_id) ; INDEX(tenant_id, prescribed_dci_id) ; CHECK(au moins un de produit / DCI / libellé) |
| `prescription_dispensations` (**immuable**) | id, tenant_id, prescription_id, prescription_line_id, **sale_id**, **sale_line_id**, occurrence (0 = initiale, k = k-ième renouvellement), type (`INITIALE`, `RELIQUAT`, `RENOUVELLEMENT`, `ANNULATION`, `RETOUR`), delivered_product_id, lot_id, **quantite_unites** (signée), montant_ttc_millimes (signé, net de remise), montant_ht_millimes, **dispenser_employee_id**, **pharmacist_employee_id** (validation), at | INDEX(prescription_line_id) ; INDEX(sale_line_id) ; INDEX(tenant_id, at) ; les annulations et retours sont des lignes négatives liées à l'original (`reverses_id`) |
| `prescription_substitutions` | id, tenant_id, dispensation_id, prescription_line_id, prescribed_product_id, prescribed_dci_id, delivered_product_id, type (`GENERIQUE_MEME_DCI`, `PRINCEPS_POUR_GENERIQUE`, `AUTRE_CONDITIONNEMENT`, `AUTRE_DOSAGE`, `AUTRE_FORME`, `AUTRE_DCI`), motif (`RUPTURE`, `PRIX`, `DEMANDE_PATIENT`, `DISPONIBILITE`, `AUTRE`), autorise_par_employee_id, accord_prescripteur (`NON_REQUIS`, `OBTENU_TELEPHONE`, `OBTENU_ECRIT`, `NON_OBTENU`), accord_ref, patient_informe (bool), commentaire | INDEX(dispensation_id) |
| `prescription_documents` | id, tenant_id, prescription_id, type (`SCAN_RECTO`, `SCAN_VERSO`, `ORDONNANCE_ELECTRONIQUE`, `ACCORD_PRESCRIPTEUR`, `AUTRE`), fichier_key (R2, **chiffré**), sha256, mime, taille, ajoute_par_employee_id, at, conserver_jusqu_au, gel_juridique (bool) | INDEX(prescription_id) ; le fichier n'est jamais modifié (une nouvelle version est un nouveau document) |
| `prescription_versions` (**immuable**) | id, prescription_id, version, instantane (JSON en-tête et lignes), motif, employee_id, at | Rectifications (W4) |
| `prescription_demand` (projection) | tenant_id, prescription_line_id, product_id ou dci_id, type (`RELIQUAT`, `RENOUVELLEMENT`), quantite_unites, date_attendue, probabilite (paramétrable), statut (`OUVERTE`, `SATISFAITE`, `EXPIREE`, `ANNULEE`) | Recalculable ; alimente M11 (§6.1) |
| `prescription_rules` | tenant_id, cle, valeur (JSON) : validité par type, délai minimal entre renouvellements, arrondi au conditionnement, tolérance de dépassement, politique de substitution, éléments obligatoires par type, autorisation « patient non identifié » | **Valeurs [À VALIDER]** |

#### Analytique (sans donnée nominative de patient)

| Table | Colonnes principales | Remarques |
|---|---|---|
| `rx_facts` | id, tenant_id, date, dispensation_id, prescription_id, prescription_line_id, **prescriber_resolved_id** (après fusion), specialty_id (selon la règle D31), product_id, dci_id, categorie_id, classe_reglementaire_id, quantite_unites, quantite_boites_eq, montant_ttc, montant_ht, est_substitution, type_dispensation, **patient_pseudo** (HMAC avec clé propre à la pharmacie, uniquement pour compter des patients distincts) | Alimentée par l'événement `prescription.dispensed` ; source unique des rapports A à C et du tableau de bord |
| `rx_metrics_monthly` | tenant_id, mois, dimension (`PRESCRIBER`, `SPECIALTY`, `PRODUCT`, `DCI`, `PRESCRIBER×PRODUCT`, `SPECIALTY×PRODUCT`), dimension_id(s), nb_ordonnances, nb_lignes, quantite_unites, montant_ttc, nb_prescripteurs, nb_patients_distincts | Agrégats précalculés, recalculables depuis `rx_facts` |

#### Confidentialité

| Table | Colonnes principales | Remarques |
|---|---|---|
| `sensitive_access_log` (**immuable**, chaîné) | id, tenant_id, employee_id, user_id, device_id, ressource (`PATIENT`, `PRESCRIPTION`, `DOCUMENT`, `RAPPORT_NOMINATIF`, `EXPORT`), ressource_id, action (`RECHERCHE`, `CONSULTATION`, `IMPRESSION`, `EXPORT`, `TELECHARGEMENT`), contexte (`CAISSE`, `BACK_OFFICE`), motif, nb_enregistrements, at, ip, hash_prev, hash | Séparé de `audit_log` pour la volumétrie et la revue dédiée **[ARBITRAGE D36]** |
| `export_requests` | id, tenant_id, demandeur_employee_id, rapport_code, filtres (JSON), niveau (`AGREGE`, `PRESCRIPTEUR_NOMINATIF`, `PATIENT_NOMINATIF`), motif, statut (`DEMANDEE`, `APPROUVEE`, `REFUSEE`, `GENEREE`, `TELECHARGEE`, `EXPIREE`, `PURGEE`), approuve_par_employee_id, fichier_key, filigrane_id, expire_le, nb_telechargements | CHECK(approuve_par ≠ demandeur) |
| `retention_policies` | tenant_id, categorie (`ORDONNANCE`, `SCAN`, `ORDONNANCE_REGLEMENTEE`, `PATIENT_INACTIF`, `JOURNAL_ACCES`, `EXPORT`), duree_mois, action_echeance (`ARCHIVER`, `ANONYMISER`, `PURGER`), base_legale, profil_conformite | **Durées [À VALIDER]** |
| `compliance_profiles` | code (`TN`, puis `MA`, `DZ`, `EU`…), libelle, regles (JSON : validité, substitution, éléments obligatoires, conservation, base légale, hébergement) | Permet d'ouvrir de nouveaux pays par paramétrage |

### 3.3 Relations

```
medical_specialties 1─n prescribers (principale) ; prescribers n─n medical_specialties (prescriber_specialties)
prescribers 1─n prescriber_history ; prescribers 0..1─1 prescribers (merged_into)
customers 1─0..1 patient_profiles ; customers 1─n prescriptions (patient)
prescribers 1─n prescriptions ; prescriptions 1─n prescription_lines 1─n prescription_dispensations
prescription_dispensations n─1 sale_lines n─1 sales ; prescription_dispensations n─1 lots
prescription_dispensations 1─0..1 prescription_substitutions
prescriptions 1─n prescription_documents ; 1─n prescription_versions
sales n─n prescriptions (via sale_lines.prescription_line_id → sale_prescriptions, vue dérivée)
prescription_lines 1─n prescription_demand → product (M11)
prescription_dispensations 1─1 rx_facts → rx_metrics_monthly
controlled_register n─1 prescriptions ; lab_preparations n─0..1 prescription_lines
customer_treatments n─0..1 prescription_lines (origine du traitement suivi, avec consentement)
```

### 3.4 Modifications des tables existantes (proposées, non appliquées)

| Table (dossier / extension) | Modification proposée | Raison |
|---|---|---|
| `prescriptions` (§4.5) | **Remplacée** par le modèle du §3.2 (`prescripteur_nom` / `prescripteur_numero` en texte → `prescriber_id` + instantané) | Statistiques fiables, dédoublonnage |
| `sales` (§4.5) | `prescription_id` (lien unique) **supprimé** au profit du lien ligne à ligne ; ajout de `mode` (`COMPTOIR`, `ORDONNANCE`, `MIXTE`) ; vue `sale_prescriptions` dérivée | Une vente peut porter plusieurs ordonnances et des produits hors ordonnance |
| `sale_lines` (§4.5) | + `prescription_line_id` (nullable), + `is_substitution`, + `dispensation_type` | Lien délivré ↔ prescrit |
| `sale_return_lines` | + `prescription_line_id` | Contre-écriture sur l'ordonnance |
| `customers` (§4.7) | + `has_patient_profile` (bool) ; `cin_optionnel` déplacé vers `patient_profiles` (chiffré) | Cloisonnement des données sensibles |
| `customer_treatments` (§4.7) | + `source_prescription_line_id`, + `prescriber_id` | Suivi des renouvellements à partir des ordonnances réelles |
| `products` (§4.3) | `ordonnance_requise` (bool) → **`prescription_policy`** (`LIBRE`, `ORDONNANCE_OBLIGATOIRE`, `ORDONNANCE_RENOUVELABLE`, `REGLEMENTE`), + `substitution_group_id` | Contrôles de vente et de substitution |
| `ref_products` (§4.3) | + `generic_group_code`, + `is_generic`, + `prescription_status_ref` | Groupes génériques issus du référentiel **[À VALIDER : source]** |
| `controlled_register` (§4.9) | `prescripteur` (texte) → `prescriber_id` + instantané ; `patient_ref` → `patient_customer_id` | Une seule source pour le prescripteur et le patient |
| `lab_preparations` (EXT02 §2.6) | + `prescription_line_id` | Préparation magistrale rattachée à l'ordonnance |
| `product_demand_stats` (§4.11) | + `rx_share` (part sur ordonnance), + `pending_rx_demand` | Prévision (§6.1, §9) |
| `po_suggestions` (§4.6) | `justification` inclut la demande engagée (reliquats, renouvellements) | Transparence des quantités proposées |
| `metrics_daily` (§4.11) | dimensions `prescriber`, `specialty`, `sale_mode` | Couche métriques unique |
| `ai_recommendations` (§4.11) | catégories `RX_TREND`, `RX_FORECAST`, `RX_VARIATION`, `RX_SPECIALTY_MIX`, `RX_REORDER` ; `sensibilite` | IA (§9) |
| `audit_log` (§4.2) | + `categorie` (`STANDARD`, `SENSIBLE`) | Filtrage des opérations sensibles |
| `document_sequences` (§4.2) | type `ORD` | Numérotation des ordonnances |
| `permissions` (EXT02 §2.2) | permissions `rx.*`, `prescriber.*`, `patient.*` (§5) | Droits |
| `habilitations` (EXT02 §2.2) | `RX_VALIDATE`, `RX_SUBSTITUTE`, `RX_OVERRIDE` | Conditionnées à la qualification de pharmacien **[À VALIDER]** |
| `kpi_definitions` (EXT02 §8.2) | KPI « ordonnances complètes », « substitutions documentées » (axe Conformité) | Qualité de dispensation, sans KPI de volume par médecin |

Comme aucun module n'est encore développé, ces changements ne nécessitent aucune migration de données :
ils seront intégrés directement dans les migrations initiales des lots concernés, une fois validés.

---

## 4. Règles métier et contrôles

Chaque contrôle a un **niveau** : `BLOQUANT` (impossible de valider), `VALIDATION` (validation par un
pharmacien habilité, avec son PIN et un motif), `ALERTE` (information non bloquante, tracée). Les niveaux
par défaut sont paramétrables dans `prescription_rules` et dans le profil de conformité du pays.

### 4.1 Patient

| Règle | Niveau par défaut |
|---|---|
| Ordonnance de type `REGLEMENTEE` : patient identifié obligatoire | BLOQUANT **[À VALIDER]** |
| Ordonnance `SIMPLE` : patient non identifié autorisé | Paramétrable **[ARBITRAGE D28]** |
| Le client de passage (`is_walk_in`) ne peut pas être sélectionné comme patient nominatif | BLOQUANT |
| Création d'un patient : contrôle de doublon (nom normalisé + phonétique + téléphone + année de naissance) | ALERTE avec proposition de la fiche existante |
| Ordonnance pour un tiers (enfant, parent) : le patient est la personne soignée ; le payeur peut être un autre client | Règle de modèle (patient ≠ client payeur de la vente) |

### 4.2 Prescripteur et spécialité

| Règle | Niveau |
|---|---|
| Prescripteur obligatoire pour toute ordonnance | BLOQUANT (création rapide autorisée) |
| Prescripteur `INACTIF` ou `FUSIONNE` : sélection impossible, la fiche cible est proposée | BLOQUANT |
| Fiche `A_COMPLETER` utilisée plus de N fois : rappel aux pharmaciens | ALERTE |
| La spécialité est **toujours** issue du référentiel `medical_specialties` (jamais en texte libre) | BLOQUANT |
| Spécialité de l'ordonnance = spécialité principale du prescripteur à la date de saisie (modifiable par un pharmacien si l'ordonnance relève d'une spécialité secondaire) | Règle de modèle |
| Identifiant professionnel : unique par type dans la pharmacie ; format vérifié si le pays en publie un **[À VALIDER]** | BLOQUANT si doublon exact |

### 4.3 Ordonnance et lignes

| Règle | Niveau |
|---|---|
| Date de prescription obligatoire, non future | BLOQUANT |
| Validité dépassée (selon le type et le profil pays) | BLOQUANT pour `REGLEMENTEE`, VALIDATION sinon **[À VALIDER]** |
| Produit à `prescription_policy = ORDONNANCE_OBLIGATOIRE` vendu en mode Comptoir | VALIDATION (bascule proposée vers le mode Ordonnance) **[À VALIDER : niveau]** |
| Produit `REGLEMENTE` : ordonnance de type `REGLEMENTEE` + registre (M09) | BLOQUANT |
| Quantité délivrée cumulée > quantité prescrite | BLOQUANT au-delà de l'arrondi au conditionnement autorisé ; VALIDATION dans la tolérance |
| Arrondi au conditionnement : 21 comprimés prescrits, boîte de 30 → délivrance d'une boîte (excédent tracé) ou fractionnement si le produit est fractionnable | Règle de calcul paramétrable |
| Renouvellement : nombre de renouvellements autorisés non dépassé, délai minimal depuis la dernière délivrance respecté | BLOQUANT / VALIDATION |
| Ligne `non_substituable` : aucune substitution | BLOQUANT |
| Ordonnance `CLOTUREE`, `EXPIREE` ou `ANNULEE` : aucune nouvelle délivrance | BLOQUANT |
| Ordonnance déjà délivrée présentée à nouveau (même numéro externe, même prescripteur, même patient) | ALERTE « possible doublon » |
| Doublon de DCI dans le panier (deux produits de même DCI) | ALERTE informative (règle, pas avis médical) |
| Scan de l'ordonnance obligatoire | Paramétrable par type (ex. obligatoire pour `REGLEMENTEE`) **[À VALIDER]** |

### 4.4 Substitution

| Situation | Niveau par défaut | Données exigées |
|---|---|---|
| Même DCI, dosage et forme (générique ↔ princeps) | ALERTE si la politique pays l'autorise **[À VALIDER]** | Motif ; patient informé |
| Autre conditionnement (même DCI, dosage, forme) | ALERTE | Quantité recalculée en unités |
| Autre dosage ou autre forme | VALIDATION (habilitation `RX_SUBSTITUTE`) | Motif ; accord du prescripteur selon la politique |
| Autre DCI | BLOQUANT par défaut ; si la politique le permet, VALIDATION + accord du prescripteur obligatoire | Accord tracé (téléphone/écrit + référence) |
| Ligne non substituable | BLOQUANT | — |

Le système qualifie automatiquement le type de substitution à partir du catalogue (DCI, dosage, forme,
groupe générique). Il **propose** des équivalents de même DCI en stock ; il ne choisit jamais à la place du pharmacien.

### 4.5 Statut de l'ordonnance (machine à états)

```
BROUILLON ──enregistrer──► ENREGISTREE ──1re délivrance──► PARTIELLEMENT_DELIVREE ──tout délivré──► DELIVREE
    │                          │                                   │                                  │
    └─(incomplète, autorisé)─► A_COMPLETER ──complétée──► (statut selon délivrances)               │
                               │                                                                  │
   renouvelable : DELIVREE (occurrence k) → reste ENREGISTREE pour l'occurrence k+1 jusqu'au dernier renouvellement
   clôture manuelle (motif) ──► CLOTUREE        date de validité dépassée ──► EXPIREE (traitement paresseux)
   annulation (aucune délivrance active) ──► ANNULEE
```

Le statut est **recalculé** à partir des délivrances : il n'est jamais saisi directement (sauf clôture et annulation motivées).

### 4.6 Détection des doublons (prescripteurs et patients)

Score de similarité de 0 à 100, déterministe et explicable :

| Critère | Poids indicatif |
|---|---|
| Identifiant professionnel identique | 100 (doublon certain) |
| Nom + prénom normalisés identiques | 45 |
| Clé phonétique identique (algorithme adapté FR et translittération AR) | 25 |
| Distance d'édition faible (≤ 2) sur nom + prénom | 20 |
| Téléphone normalisé identique | 30 |
| Même ville | 10 |
| Même spécialité | 10 |
| Prénom et nom inversés | 15 |

Seuils par défaut : ≥ 85 → création bloquée (proposition de la fiche existante, création forcée possible par un
pharmacien avec motif) ; 60–84 → création autorisée et paire envoyée à l'écran E6 ; < 60 → aucune action.
La normalisation retire les titres (« Dr », « Pr », « د. »), les accents, la ponctuation, et unifie les variantes AR.

### 4.7 Modification contrôlée des fiches

- Champs de contact : modifiables par un pharmacien ; champs d'identité et identifiant professionnel : titulaire ;
  fusion : titulaire. Motif obligatoire pour toute modification après la première utilisation.
- Chaque modification crée une ligne `prescriber_history` (avant/après), consultable sur la fiche.
- Verrou optimiste (`version`) : deux modifications concurrentes ne s'écrasent pas.

---

## 5. Droits d'accès

### 5.1 Permissions (catalogue, à ajouter à EXT02 §11.2)

| Permission | Description |
|---|---|
| `rx.dispense` | Réaliser une vente sur ordonnance |
| `rx.validate` | Valider une ordonnance ou un contrôle de niveau VALIDATION (habilitation `RX_VALIDATE`) |
| `rx.substitute` | Autoriser une substitution hors même DCI-dosage-forme (habilitation `RX_SUBSTITUTE`) |
| `rx.override` | Passer outre une règle de niveau VALIDATION avec motif (habilitation `RX_OVERRIDE`) |
| `rx.rectify` | Rectifier une ordonnance après délivrance |
| `rx.close` / `rx.cancel` | Clôturer ou annuler une ordonnance |
| `rx.read.own_context` | Voir l'ordonnance en cours de délivrance et les ordonnances ouvertes du patient servi |
| `rx.read.nominative` | Rechercher et consulter les ordonnances nominatives hors contexte de délivrance |
| `rx.document.read` | Ouvrir les scans |
| `prescriber.read` / `prescriber.create` / `prescriber.update` / `prescriber.update.identity` / `prescriber.merge` | Répertoire |
| `specialty.manage` | Administrer le référentiel local des spécialités |
| `patient.create` / `patient.read.nominative` / `patient.update` | Patients |
| `rx.report.aggregate` | Rapports agrégés (médecin, spécialité, médicament) |
| `rx.report.patient` | Rapport D par patient |
| `rx.export.prescriber` / `rx.export.patient.request` / `rx.export.patient.approve` | Exports (W6) |
| `audit.sensitive.read` | Consulter le journal des accès sensibles |

### 5.2 Matrice par rôle (valeurs par défaut)

| Permission | Titulaire | Adjoint | Préparateur | Caissier | Achats/stock | Comptable | Auditeur |
|---|---|---|---|---|---|---|---|
| `rx.dispense` | ✔ | ✔ | ✔ **[À VALIDER]** | encaissement seul | — | — | — |
| `rx.validate`, `rx.substitute`, `rx.override` | ✔ | ✔ | — | — | — | — | — |
| `rx.rectify`, `rx.close` | ✔ | ✔ | — | — | — | — | — |
| `rx.read.own_context` | ✔ | ✔ | ✔ | ✔ (sans le détail des lignes médicales) | — | — | — |
| `rx.read.nominative`, `rx.document.read` | ✔ | ✔ | contexte de délivrance seulement | — | — | — | journal seulement |
| `prescriber.read` / `create` | ✔ | ✔ | ✔ | ✔ (lecture) | lecture | — | lecture |
| `prescriber.update.identity`, `prescriber.merge` | ✔ | — | — | — | — | — | — |
| `rx.report.aggregate` | ✔ | ✔ | — | — | ✔ (par médicament) | ✔ (montants) | ✔ |
| `rx.report.patient` | ✔ | ✔ | — | — | — | — | — |
| `rx.export.patient.approve` | ✔ | — | — | — | — | — | — |
| `audit.sensitive.read` | ✔ | — | — | — | — | — | ✔ |

Les droits effectifs suivent le moteur d'EXT02 §1.2 : `rx.validate` exige par exemple l'habilitation `RX_VALIDATE`,
elle-même conditionnée à la qualification de pharmacien en cours de validité.

### 5.3 Accès contextuel

Au comptoir, le préparateur voit **uniquement** ce qui sert à la délivrance en cours : l'ordonnance servie et les
ordonnances encore ouvertes (reliquats, renouvellements) du patient sélectionné. La recherche libre dans l'historique
d'un patient exige `rx.read.nominative` et est journalisée avec un motif.

---

## 6. Impacts sur stock, ventes, caisse, finance et statistiques

### 6.1 Stock (M05) et commandes (M11)

| Point | Règle |
|---|---|
| Enregistrement d'une ordonnance | **Aucun** mouvement de stock |
| Réservation optionnelle | Une ordonnance en attente peut réserver des lots (`stock_reservations`, expiration courte), sans mouvement |
| Validation de la vente | Mouvements `SALE` par lot (FEFO), dans la même transaction que les délivrances |
| Retour / annulation | Contre-mouvements selon la règle existante + délivrances négatives |
| Reliquat `A_COMMANDER` | Crée une `prescription_demand` (quantité certaine, date attendue = J+délai fournisseur) ; M11 l'ajoute **en priorité** au besoin calculé, avec la mention « reliquat patient » dans la justification |
| Renouvellements attendus | `prescription_demand` de type `RENOUVELLEMENT` à la date prévue, pondérée par une probabilité de retour observée dans la pharmacie (taux historique de renouvellements effectivement délivrés) |
| Formule du besoin (dossier §7.3 D) | `besoin = D × (L + R) + SS + demande_engagée(L+R) − stock − en_commande`, où `D` exclut les reliquats déjà comptés pour éviter le double comptage |

### 6.2 Ventes et caisse (M02, M02b/EXT02 §7)

- La vente sur ordonnance est une vente normale : même numérotation, mêmes paiements, même session de caisse nominative.
- Le **dispensateur** (`seller_employee_id` = `dispenser_employee_id`) et l'**encaissant** (`cashier_employee_id`) peuvent différer ;
  le pharmacien qui valide un contrôle est enregistré (`pharmacist_employee_id`).
- Le ticket mentionne le numéro `ORD-…` (et le numéro externe si saisi), **sans** posologie ni donnée médicale ; les mentions
  obligatoires éventuelles sur l'ordonnance ou le ticket sont paramétrables **[À VALIDER]**.
- Annulation d'une vente sur ordonnance : mêmes droits que M02 ; si un produit réglementé est concerné, rectification du registre (M09).
- Aucun nouvel impact sur la formule du solde théorique de caisse.

### 6.3 Finance (M08)

- Aucun nouveau flux financier en v1 : les ordonnances ne changent ni les prix ni les modes de paiement.
- **Tiers payant (CNAM, mutuelles, conventions)** : aucune activation en v1 ne doit être déduite de cette proposition. D37 reste **À RÉEXAMINER** dans la matrice PO v1.3.3 ; le modèle peut préserver des points d'extension futurs, sans activer de flux de tiers payant CNAM, mutuelle ou convention tant que le périmètre n'est pas arbitré :
  `patient_profiles.organisme` / `identifiant_assure`, lien ligne à ligne vente ↔ ordonnance, qui permettront une
  future part « patient » / part « organisme » et une créance sur l'organisme dans `party_ledger`.

### 6.4 Statistiques (M12, couche métriques)

- Les rapports et le tableau de bord prescriptions lisent **uniquement** `rx_facts` et `rx_metrics_monthly`, alimentés
  par les délivrances validées : les chiffres sont donc cohérents avec le chiffre d'affaires de la couche métriques.
- Chiffre d'affaires : `CA total = CA comptoir + CA ordonnance`, réconcilié par un contrôle automatique quotidien
  (somme des `rx_facts` = somme des `sale_lines` liées à une ordonnance).
- `metrics_daily` reçoit la dimension `sale_mode` (part du CA sur ordonnance, nombre d'ordonnances par jour).

---

## 7. Reporting et états

### 7.1 Définitions communes (garantissent la cohérence)

| Mesure | Définition |
|---|---|
| **Nombre d'ordonnances** | Ordonnances distinctes ayant au moins une délivrance nette positive dans la période (date de délivrance) ; option « par date de prescription » |
| **Nombre de lignes de prescription** | Lignes prescrites distinctes délivrées au moins partiellement dans la période |
| **Quantité** | Somme nette des unités délivrées (retours déduits) ; affichage en unités et en équivalent boîtes |
| **Montant** | Somme nette TTC des délivrances (remises déduites), option HT |
| **Prescripteur** | Fiche résolue après fusion (`prescriber_resolved_id`) |
| **Spécialité** | Instantané figé sur l'ordonnance **[ARBITRAGE D31]** |
| **Médicament** | Produit délivré (option : produit prescrit, ou regroupement par DCI) |
| **Évolution** | Série mensuelle ou annuelle, variation en valeur et en %, comparaison à la période précédente et à N−1 |

### 7.2 États

| État | Contenu | Filtres | Niveau de données |
|---|---|---|---|
| **A. Par médecin** | Nb ordonnances, nb lignes, médicaments délivrés (liste), quantités par médicament, montants, évolution mensuelle/annuelle | Période, médecin(s), spécialité, ville, médicament, DCI, catégorie | Prescripteur nominatif, patients **jamais** affichés |
| **B. Par spécialité** | Nb ordonnances, médicaments délivrés, quantités et montants, comparaison entre spécialités (tableau et graphique) | Période, spécialités, groupe de spécialités, médicament | Agrégé |
| **C. Par médicament** | Nb délivrances sur ordonnance, quantités, montants, nb prescripteurs distincts, répartition par spécialité, évolution | Période, produit / DCI / catégorie, spécialité | Agrégé (liste des prescripteurs selon droits) |
| **D. Par patient** | Historique des ordonnances, dates de délivrance, médicaments délivrés, prescripteurs | Patient (obligatoire), période | **Patient nominatif** : accès `rx.report.patient`, motif, journalisé |

- **Seuil de petits effectifs** : dans les états agrégés, une cellule correspondant à moins de *k* patients distincts
  (par défaut *k* = 3) est masquée (« < 3 ») pour éviter la réidentification indirecte **[ARBITRAGE D34]**.
- **Impression** : mise en page A4 (portrait/paysage), en-tête de la pharmacie, filtres appliqués, date et auteur
  d'édition, pagination ; versions FR et AR (RTL).
- **Exports** : Excel (XLSX) et PDF ; CSV en option. Les exports agrégés sont immédiats ; les exports nominatifs patient
  suivent le workflow W6 (validation, filigrane, durée limitée).

### 7.3 Usage éthique des statistiques par médecin

Les états par médecin servent à la gestion du stock et à l'organisation de l'officine. Ils ne doivent pas servir à des
pratiques commerciales vis-à-vis des prescripteurs ni être cédés à des tiers (laboratoires, délégués médicaux). PHARM'OS
n'offre aucun export de ces données vers l'extérieur, et les conditions d'utilisation l'interdisent **[À VALIDER : déontologie]**.

---

## 8. Tableau de bord prescriptions (E10)

| Bloc | Contenu | Interaction |
|---|---|---|
| Bandeau KPI | Ordonnances, lignes délivrées, CA sur ordonnance, part du CA total, panier moyen ordonnance, taux de délivrance complète, taux de substitution — période vs période précédente vs N−1 | Clic → état détaillé |
| Top 10 prescripteurs | Par nombre d'ordonnances ou par montant (bascule), variation vs période précédente | Clic → état A filtré |
| Top 10 spécialités | Idem | Clic → état B |
| Top 20 médicaments sur ordonnance | Par quantité ou montant, avec niveau de stock et couverture en jours | Clic → état C ou fiche produit |
| Évolution mensuelle | Courbe sur 12 ou 24 mois (ordonnances, montant), superposition N−1 | Sélection d'une plage |
| Répartition par spécialité | Graphique en barres empilées (montant ou volume) ; les petites parts sont regroupées en « Autres » | Clic → filtre |
| Comparaison entre périodes | Deux périodes libres côte à côte, écarts en valeur et en % | — |
| Reliquats et renouvellements | Nombre de reliquats ouverts, quantités à commander, renouvellements attendus sur 7 j | Clic → liste E2 filtrée |
| Alertes PHARM'OS AI | Recommandations `RX_*` (§9) | Ouvrir / préparer l'action |

**Filtres dynamiques** (appliqués à tous les blocs, conservés dans l'URL et enregistrables) : période (presets et
plage libre), médecin(s), spécialité(s), médicament / DCI / catégorie, ville, type d'ordonnance.
Les données sont **agrégées** et ne montrent aucun nom de patient ; le seuil de petits effectifs (§7.2) s'applique.
Accès : `rx.report.aggregate`.

---

## 9. PHARM'OS AI

### 9.1 Répartition des rôles

| Couche | Rôle | Exemple |
|---|---|---|
| **Calculs déterministes** (testés, versionnés) | Produisent toutes les valeurs | Tendance, prévision, score d'anomalie, classement par spécialité |
| **LLM** | Explique et formule la recommandation à partir des valeurs calculées, en FR/AR | « La demande d'AMLOR 5 mg sur ordonnance a augmenté de 32 % en 3 mois, portée par 2 cardiologues… » |
| **Pharmacien** | Décide | Accepte le brouillon de commande, ignore ou ajuste |

### 9.2 Analyseurs proposés (P1 à P5)

| Code | Analyse | Méthode déterministe | Sortie |
|---|---|---|---|
| **P1 Tendances** | Évolution de la délivrance par produit, DCI, spécialité | Régression linéaire robuste (Theil-Sen) sur séries mensuelles nettes ; pente significative si l'intervalle exclut 0 et volume ≥ seuil | « Hausse de 32 % sur 3 mois » |
| **P2 Prévision** | Besoins de stock à partir de l'historique sur ordonnance | Lissage exponentiel avec saisonnalité hebdomadaire et annuelle (si ≥ 2 ans d'historique), **+ demande engagée** (reliquats certains, renouvellements pondérés) ; erreur de prévision mesurée (MAPE) et affichée | Besoin à J+7, J+14, J+30 avec intervalle |
| **P3 Variations inhabituelles** | Pics ou chutes anormaux | Score robuste `z = (x − médiane) / (1,4826 × MAD)` sur 12 semaines ; signalement si \|z\| ≥ 3,5 et volume minimal | « Délivrances de X : 3 fois la normale cette semaine » |
| **P4 Mix par spécialité** | Médicaments les plus délivrés par spécialité | Classement par part (quantité ou montant) et indice de concentration | « 80 % des délivrances de Y viennent de la dermatologie » |
| **P5 Réapprovisionnement** | Suggestions intégrées à M11 | Formule du §6.1 ; brouillon de lignes de commande avec justification complète | Brouillon de BC, jamais envoyé sans validation |

Chaque recommandation suit le format normalisé du dossier principal (§7.4) : problème, données utilisées (période,
volumes, méthode, version du calcul), priorité, action proposée, gain ou risque estimé, confiance.

### 9.3 Interdits explicites

L'IA ne doit jamais :

- établir ou suggérer un diagnostic, ni déduire une pathologie à partir des médicaments d'un patient ;
- modifier, compléter ou « corriger » une prescription, ni proposer une substitution à la place du pharmacien ;
- prendre une décision médicale ou pharmaceutique, ni valider une délivrance ;
- produire un classement ou un jugement des **prescripteurs** (« bon » ou « mauvais » médecin), ni une analyse à visée commerciale envers eux ;
- produire une analyse **par patient** : le module F « renouvellements » du dossier principal reste soumis au consentement et n'utilise que des
  dates de renouvellement calculées par des règles, sans contenu médical.

Ces interdits sont garantis **par construction** : les outils accessibles au LLM ne renvoient que des agrégats
par produit, DCI, spécialité ou prescripteur pseudonymisé, et n'exposent aucune fonction d'écriture sur les ordonnances.

### 9.4 Protection des données vers l'IA externe

| Donnée | Transmise au LLM externe ? |
|---|---|
| Nom, téléphone, date de naissance, identifiant d'assuré, adresse du patient | **Jamais** |
| Identifiant patient, même pseudonymisé | **Jamais** (le `patient_pseudo` reste côté serveur, pour compter des patients distincts) |
| Lignes d'une ordonnance individuelle, scans | **Jamais** |
| Nom du prescripteur | Non par défaut : pseudonyme `PRESC-07`, réidentifié côté serveur à l'affichage **[ARBITRAGE D35]** |
| Agrégats par produit, DCI, spécialité, mois | Oui |
| Cellules < *k* patients distincts | Non (masquées) |

Un filtre de sortie côté serveur vérifie chaque charge utile avant envoi (liste blanche de champs) et la journalise ;
un test automatisé l'atteste (§12.2).

---

## 10. Confidentialité et conformité

### 10.1 Mesures

| Exigence | Mesure |
|---|---|
| Accès selon les rôles et habilitations | §5 ; accès contextuel au comptoir ; droits effectifs EXT02 |
| Journalisation des consultations et modifications | `sensitive_access_log` (lectures, recherches, impressions, exports) + `audit_log` (écritures) + `prescriber_history` |
| Protection des données nominatives | Données patient cloisonnées (`patient_profiles`), champs identifiants et scans chiffrés, URL signées de courte durée pour les scans, masquage partiel à l'écran (téléphone) |
| Rapports agrégés par défaut | États A à C et tableau de bord sans patient ; seuil de petits effectifs ; état D séparé et restreint |
| Restriction des exports | Workflow W6 : validation par un tiers, filigrane nominatif, lien 24 h, purge automatique |
| Conservation et archivage | `retention_policies` par catégorie : archivage (lecture seule) puis anonymisation ou purge ; gel juridique possible ; traitement paresseux (dossier §6.5) |
| Revue des accès | Rapport mensuel des accès sensibles au titulaire ; alerte si un volume de consultations est inhabituel pour un collaborateur |
| Droits des personnes | Export des données d'un patient à sa demande ; rectification ; opposition aux traitements facultatifs (suivi, relances) |
| Multi-pays | `compliance_profiles` : chaque pays active ses règles (validité, substitution, conservation, base légale, hébergement) sans modification du code |

### 10.2 Exigences réglementaires à valider (Tunisie)

| # | Sujet | Hypothèse de conception | Interlocuteurs |
|---|---|---|---|
| R1 | Données de santé des patients : régime de déclaration ou d'autorisation | Traitement soumis à l'INPDP (loi organique n° 2004-63) ; base légale = obligation de traçabilité pour la délivrance, consentement pour les usages facultatifs | INPDP, juriste |
| R2 | Mentions obligatoires d'une ordonnance par type | Paramétrées par type ; complétude contrôlée | Ordre des pharmaciens, DPM |
| R3 | Durée de validité des ordonnances (simple, renouvelable, réglementée) | Paramétrable par type | DPM, textes en vigueur |
| R4 | Droit de substitution du pharmacien (générique, conditionnement, dosage) et conditions | Politique paramétrable, prudente par défaut | Ordre, DPM |
| R5 | Durée de conservation des ordonnances, de leurs copies et des registres | Paramétrable ; archivage puis anonymisation | Textes en vigueur |
| R6 | Valeur probante d'une copie numérique (scan) et obligation de conserver l'original | Le scan est un justificatif complémentaire, sans remplacer l'original | Juriste |
| R7 | Mentions à porter sur l'ordonnance après délivrance (cachet, date, quantités) | Impression d'une étiquette ou d'une mention, paramétrable | Ordre |
| R8 | Identifiant professionnel du médecin : existence, format, droit de l'enregistrer | Champ facultatif, désactivable | Conseil de l'Ordre des médecins, INPDP |
| R9 | Statistiques nominatives par prescripteur : licéité de la collecte et des usages | Usage interne de gestion uniquement, aucune cession | Juriste, Ordres |
| R10 | Hébergement des données de santé (localisation, agrément éventuel) | À déterminer selon le choix d'hébergement (dossier D2) | INPDP, ministère de la Santé |
| R11 | Ordonnance électronique (calendrier national) | Champ `support = ELECTRONIQUE` prévu, intégration ultérieure | Ministère de la Santé |
| R12 | Tiers payant CNAM (si retenu) | Hors v1 (D37) | CNAM |
| R13 | Futurs pays (Maroc, Algérie, UE…) | Un profil de conformité par pays ; RGPD pour l'UE (données de santé = catégorie particulière) | Juristes locaux |

---

## 11. Dépendances avec les autres modules

| Module | Nature de la dépendance | Sens |
|---|---|---|
| M00 Plateforme | Séquences (`ORD`), permissions, journal d'audit, notifications | M17 consomme |
| M15 RH (EXT02) | Collaborateur agissant, habilitations `RX_*`, qualification de pharmacien | M17 consomme |
| M04 Catalogue | Produits, DCI, dosage, forme, groupes génériques, `prescription_policy` | M17 consomme (et requiert les champs ajoutés au §3.4) |
| M07 Clients | Fiche client unique, facette `patient_profiles`, consentements | Partagé |
| M02 / M02b Vente & caisse | Mode Ordonnance dans l'écran de vente, lien ligne à ligne, transaction atomique | **Forte, bidirectionnelle** |
| M05 Stock | Réservations, mouvements à la validation, FEFO | M17 consomme |
| M09 Réglementés | Le registre référence l'ordonnance, le prescripteur et le patient | M09 consomme M17 |
| M16 Laboratoire (EXT02) | Préparation magistrale rattachée à une ligne d'ordonnance | M16 consomme M17 |
| M11 Commandes | Demande engagée (reliquats, renouvellements) | M11 consomme M17 |
| M08 Finance | Aucun en v1 ; tiers payant ultérieur | Futur |
| M12 Rapports / métriques | `rx_facts`, `rx_metrics_monthly`, dimension `sale_mode` | M12 consomme |
| M13 IA | Analyseurs P1 à P5 sur agrégats | M13 consomme |
| M14 PWA hors-ligne | Ventes sur ordonnance hors ligne : non autorisées pour les réglementées ; ordonnances simples à synchroniser **[ARBITRAGE D39]** | À arbitrer |

Événements de domaine ajoutés : `prescription.created`, `prescription.dispensed`, `prescription.rectified`,
`prescription.closed`, `prescription.expired`, `prescriber.created`, `prescriber.updated`, `prescriber.merged`,
`patient.sensitive_accessed`, `rx_export.approved`.

---

## 12. Position dans le plan de développement et tests

### 12.1 Découpage proposé

L'extension ne peut pas être un lot tardif isolé : le **modèle** des ordonnances doit exister avant la caisse, sinon
le lien ligne à ligne et le registre des réglementés devraient être refaits. Proposition :

| Lot | Contenu EXT03 | Pourquoi à ce moment |
|---|---|---|
| **2. Catalogue** (ajout) | `prescription_policy`, groupes génériques, `medical_specialties` (référentiel) | Référentiels nécessaires aux contrôles |
| **5b. Ordonnances — cœur** (nouveau, juste après le lot 5) | Répertoire des prescripteurs (création, recherche, doublons, historique), facette patient, ordonnances, lignes, délivrances, substitutions, mode Ordonnance en caisse, reliquats, renouvellements, rectifications, scans, journal des accès sensibles, droits | Le lot 5 conçoit la vente avec le lien `prescription_line_id` dès le départ ; le lot 5b ajoute l'écran et les règles |
| **6. Clients** (ajout) | Onglet Ordonnances de la fiche patient, consentements liés | Fiche unique |
| **10. Réglementés** (ajout) | Registre alimenté par les ordonnances `REGLEMENTEE` | Après 5b et validation juridique |
| **11. Métriques & rapports** (ajout) | `rx_facts`, `rx_metrics_monthly`, états A à D, exports, workflow W6, seuil de petits effectifs | Couche analytique unique |
| **11c. Tableau de bord prescriptions** (nouveau) | E10 | Après 11 |
| **12. Commandes** (ajout) | Demande engagée dans le calcul des besoins | Après 5b et 11 |
| **13. IA** (ajout) | Analyseurs P1 à P5, filtre de sortie vers le LLM | Après 11c |
| **15. SaaS** (ajout) | Profils de conformité multi-pays, politiques de conservation exécutées | Industrialisation |

Pré-requis bloquants avant le lot 5b : validation des exigences R1 à R6 (au minimum les valeurs par défaut) et des arbitrages D27 à D33.

### 12.2 Tests obligatoires

| Domaine | Tests |
|---|---|
| **Prescrit / délivré** | Délivrance partielle puis reliquat ; dépassement refusé ; arrondi au conditionnement ; quantités en boîtes et en unités ; somme des délivrances = délivré affiché |
| **Renouvellements** | Nombre maximal ; délai minimal ; statut recalculé à chaque occurrence ; expiration par traitement paresseux |
| **Stock** | Aucun mouvement avant validation ; mouvements FEFO exacts à la validation ; annulation et retour → contre-mouvements et délivrances négatives ; atomicité (échec simulé au milieu de la transaction → rien n'est écrit) ; idempotence (double clic) |
| **Substitution** | Qualification automatique du type sur un jeu de référence ; ligne non substituable bloquée ; accord du prescripteur exigé selon la politique |
| **Prescripteurs** | Doublons : jeu de paires de référence FR/AR (variantes d'écriture, titres, inversions) avec résultat attendu ; fusion puis défusion sans perte ; instantané de spécialité non modifié par une modification ultérieure |
| **Rapports** | États A à D recalculés à la main sur un jeu de référence ; réconciliation CA ordonnance = Σ `sale_lines` liées ; retours déduits ; seuil de petits effectifs ; mêmes chiffres dans le tableau de bord et les états ; export XLSX/PDF identique à l'écran |
| **Droits** | Matrice §5.2 testée par rôle ; accès contextuel du préparateur ; export patient sans validation refusé ; auto-validation d'un export refusée |
| **Confidentialité** | Chaque consultation nominative crée une ligne dans `sensitive_access_log` ; lien d'export expiré inaccessible ; scans servis uniquement par URL signée |
| **IA** | Test sur les charges utiles envoyées : aucun champ hors liste blanche, aucun identifiant patient ; aucun outil d'écriture sur les ordonnances ; prévisions reproductibles (mêmes entrées → mêmes sorties) |
| **Interface** | Parcours complet au clavier ; ordonnance de 5 lignes saisie en moins de 60 s par un utilisateur entraîné ; FR et AR (RTL) ; tablette avec photo de l'ordonnance |

---

## 13. Arbitrages et exigences réglementaires à valider

### 13.1 Arbitrages demandés au propriétaire

| # | Question | Options | Recommandation |
|---|---|---|---|
| **D27** | Modèle du patient | A. Facette `patient_profiles` de la fiche client unique · B. Table patients séparée | **A** (source unique, conforme au cahier des charges) |
| **D28** | Ordonnance simple sans patient identifié | Autorisée · Interdite | Autorisée pour les ordonnances simples, interdite pour les réglementées (à confirmer R2) |
| **D29** | Validation d'une vente sur ordonnance incomplète (prescripteur à compléter, scan manquant) | Bloquer · Autoriser avec statut `A_COMPLETER` | Autoriser avec statut `A_COMPLETER` et rappel, sauf pour les réglementées |
| **D30** | Niveau de saisie des lignes | Produit délivré seulement · Prescrit + délivré | Prescrit + délivré (exigé pour les reliquats et les statistiques), saisie accélérée quand ils sont identiques |
| **D31** | Spécialité dans les statistiques | Instantané à la date de l'ordonnance · Spécialité actuelle du médecin | Instantané (historique stable) |
| **D32** | Référentiel des spécialités | Liste APEX commune modifiable localement · Liste propre à chaque pharmacie | Liste APEX commune, avec ajouts locaux contrôlés |
| **D33** | Scan des ordonnances | Facultatif · Obligatoire pour certains types | Obligatoire pour les réglementées, facultatif sinon |
| **D34** | Seuil de petits effectifs dans les états agrégés | k = 3 · k = 5 · aucun | k = 3 |
| **D35** | Nom des prescripteurs transmis au LLM | Pseudonymisé · En clair | Pseudonymisé |
| **D36** | Journal des accès sensibles | Table dédiée · Dans le journal d'audit général | Table dédiée (volumétrie, revue) |
| **D37** | Tiers payant (CNAM, mutuelles) | v1 · Ultérieur | Ultérieur, modèle préparé |
| **D38** | Posologie | Non saisie · Facultative · Obligatoire | Facultative, non utilisée par l'IA ni par les rapports |
| **D39** | Ventes sur ordonnance hors ligne | Interdites · Ordonnances simples uniquement | Ordonnances simples uniquement, synchronisées et contrôlées au retour du réseau |
| **D40** | Réservation de stock pour les reliquats `PATIENT_REVIENDRA` | Décision PO du 10/11/2026 : aucune avance ; réservation par défaut de deux jours comptables, configurable par l'administrateur ; échéance notifiée ; aucune annulation ou confiscation silencieuse. La règle PO complète de la matrice v1.3.3 prévaut. | **ARBITRÉE — ne pas reprendre la recommandation historique « réservation courte paramétrable » comme règle suffisante.** |

### 13.2 Exigences réglementaires

Les exigences R1 à R13 du §10.2 doivent être validées avant le développement du lot 5b, au minimum pour fixer les
valeurs par défaut du profil de conformité tunisien. Le module reste entièrement paramétrable : une règle non validée
garde la valeur la plus prudente (blocage ou validation par un pharmacien).

### 13.3 Report dans le dossier principal après validation

Une fois les arbitrages validés, le dossier principal passera en v1.2 : remplacement de `prescriptions` (§4.5),
modifications des tables listées au §3.4, ajout du module M17 dans la cartographie, des écrans E1 à E12 dans
l'arborescence, des analyseurs P1 à P5 dans le moteur IA, et des lots 5b et 11c dans le plan.
