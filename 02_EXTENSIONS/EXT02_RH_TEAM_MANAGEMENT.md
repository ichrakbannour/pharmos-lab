# PHARM'OS — Extension fonctionnelle n° 02 : RH & Team Management

**Produit** : PHARM'OS — APEX AI DEV
**Version** : 1.0 — 2026-11-03
**Statut** : extension de conception ; intégration à réconcilier avec le dossier principal canonique `../01_CONCEPTION/CONCEPTION_PHARMOS_v1.2.1.md`. Aucun développement n'est autorisé par ce document.
**Statut PO des décisions** : selon la matrice maître D1–D86 v1.3.5, D17–D26 demeurent des propositions non arbitrées (D26 explicitement ouverte). Les valeurs réglementaires et paramètres marqués [À VALIDER] restent à confirmer avant toute mise en production.
**Modules créés** : **M15 RH & équipes** · **M16 Laboratoire** (nécessaire à la traçabilité exigée par l'extension)
**Modules modifiés** : M00 Plateforme (permissions), M02/M02b Vente & caisse, M03 Achats, M05 Stock, M09 Réglementés, M11 Commandes, M12 Rapports, M13 IA

> Les paramètres de droit du travail (durées, majorations, congés, repos) sont **paramétrables** et
> leurs valeurs par défaut sont marquées **[À VALIDER]** : elles doivent être validées au regard du
> Code du travail tunisien et des conventions collectives applicables aux officines avant mise en production.

---

## Sommaire

1. Architecture fonctionnelle RH
2. Modèle de données
3. Relations avec les autres modules
4. Workflows
5. Système de planning
6. Gestion des congés et absences
7. Caisses nominatives
8. Système de KPI et d'évaluation
9. Intégration PHARM'OS AI
10. Tableau de bord RH
11. Sécurité, confidentialité et permissions RH
12. Plan de développement et de tests
13. Risques et décisions

---

## 1. Architecture fonctionnelle RH

### 1.1 Principe fondateur : distinguer la personne, le compte et le droit

Le dossier v1.0 rattachait les opérations à un **utilisateur** (compte de connexion). L'extension
introduit trois notions distinctes, articulées par des règles explicites :

| Notion | Table | Rôle |
|---|---|---|
| **Collaborateur** (la personne) | `employees` | Identité, contrat, poste, équipe, qualifications, planning, KPI. Existe même sans compte (ex. agent d'entretien). |
| **Compte** (l'accès) | `users` + `memberships` | Authentification. Un compte actif est rattaché à **un seul** collaborateur par pharmacie. Les comptes partagés sont interdits. |
| **Droit effectif** (ce qu'il peut faire maintenant) | calculé | `rôle ∩ habilitations actives ∩ qualifications valides ∩ statut actif ∩ politique de présence` |

**Conséquence** : toutes les opérations métier enregistrent `employee_id` (qui a agi) et, pour les
opérations validées par un tiers, `approved_by_employee_id`. Le `user_id` reste dans le journal
d'audit pour la traçabilité technique (session, appareil).

### 1.2 Moteur de droits effectifs

```
Permission demandée (ex. controlled.dispense)
  │
  ├─ 1. Source du droit :
  │      rôle (role_permissions), OU octroi individuel explicite (permission_overrides),
  │      OU délégation temporaire valide selon les règles de l'extension concernée
  │      [les permissions réservées à un octroi explicite ne sont jamais accordées par rôle]
  ├─ 2. Conditions obligatoires de la permission :
  │      habilitation requise ? (permissions.requires_habilitation)
  │      └─ habilitation active ? (employee_habilitations, dates)
  │           └─ qualifications prérequises valides ? (employee_qualifications, expiration)
  ├─ 3. Collaborateur au statut actif ? (non suspendu, non sorti)
  ├─ 4. Politique de présence (paramétrable par permission) :
  │      aucune / pointé / en shift planifié / affecté à l'équipe concernée aujourd'hui
  ├─ 5. Portée, plafonds et séparation des tâches :
  │      soi / équipe gérée / toute la pharmacie ; plafonds (remise max, montant)
  └─ 6. Contrôles complémentaires et validations requises par le workflow
       → AUTORISÉ | REFUSÉ (code motif) | AUTORISÉ SOUS VALIDATION (PIN d'un habilité)
```

- Les octrois individuels et délégations sont gérés selon les extensions qui les définissent ; ils ne sont jamais déduits d'une simple affectation ou d'un rôle. Une permission explicitement réservée à l'octroi individuel ne peut pas être ajoutée par `role_permissions`. Les habilitations, qualifications, présence, plafonds, séparation des tâches et validations obligatoires restent des contrôles cumulatifs : un octroi individuel ne les contourne pas.
- Évalué **côté serveur** à chaque requête ; résultat mis en cache par session, invalidé par les événements `habilitation.*`, `qualification.expired`, `employee.status_changed`, `attendance.*`, ainsi que par tout changement d'octroi individuel ou de délégation.
- Le motif de refus est explicite (« habilitation *Dispensation réglementés* expirée le 31/10 »).
- Exemple réglementaire : la dispensation d'un médicament réglementé et la validation d'une
  préparation exigent l'habilitation correspondante, elle-même conditionnée à la qualification de
  pharmacien (diplôme + inscription à l'Ordre) **[À VALIDER : périmètre exact par catégorie de personnel]**.

### 1.3 Sous-modules de M15

| Sous-module | Contenu |
|---|---|
| **M15.1 Dossier collaborateur** | Identité, photo, matricule, coordonnées, fonction, qualification, contrat(s), embauche, statut, responsable, documents, données privées cloisonnées, entrée/sortie |
| **M15.2 Organisation** | Équipes, postes, affectations principales/secondaires/remplacements, responsables d'équipe, compétences, habilitations, historique |
| **M15.3 Planning & shifts** | Modèles de shifts, rotations, besoins de couverture, gardes, planning jour/semaine/mois en glisser-déposer, détection de conflits et de sous-effectif, publication, échanges |
| **M15.4 Temps & présence** | Pointage, retards, départs anticipés, absences, heures supplémentaires, validation journalière, export des variables de paie |
| **M15.5 Congés & absences** | Types, soldes (grand livre), demandes, validation hiérarchique, calendrier collectif, chevauchements |
| **M15.6 Caisses nominatives** | Sessions de caisse personnelles (évolution de M02b), comptage à l'aveugle, écarts, justification, validation |
| **M15.7 Qualité & performance** | Événements qualité, KPI par fonction, profils de notation pondérés, évaluations, commentaires et droit de réponse, formations |
| **M15.8 Tableau de bord RH** | Effectifs, présence, planning du jour, anomalies, KPI, alertes IA |

### 1.4 Équipes et postes par défaut (paramétrables)

| Équipe (`teams.code`) | Postes typiques (`positions`) | Habilitations typiques |
|---|---|---|
| `COUNTER` Vente et comptoir | Pharmacien, préparateur, vendeur conseil | Vente, dispensation réglementés (pharmacien), remise élevée |
| `LAB` Laboratoire | Pharmacien, préparateur | Réalisation préparation, validation préparation (pharmacien) |
| `STOCK` Stock et inventaire | Magasinier, préparateur | Ajustement stock, comptage, validation inventaire |
| `PURCHASING` Achats et réception | Acheteur, magasinier | Réception, saisie facture, validation BC |
| `CASH` Caisse | Caissier, vendeur | Caisse, encaissement chèque, remboursement |
| `ADMIN` Administration | Titulaire, comptable, assistant | Paramètres, RH, finance |

Un collaborateur peut avoir une **affectation principale** et des **affectations secondaires**
datées, éventuellement limitées à certains jours et horaires (ex. « Caisse le samedi après-midi »).

---

## 2. Modèle de données

Conventions du dossier principal (§4.1) : `tenant_id` partout, ULID, montants en millimes, colonnes
d'audit, pas de suppression physique, historisation par **périodes de validité** (`valid_from`,
`valid_to`) plutôt que par mise à jour.

### 2.1 Dossier collaborateur

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `employees` | id, tenant_id, matricule, user_id (nullable), nom, prenom, nom_ar, prenom_ar, photo_key, telephone_pro, email_pro, date_embauche, date_sortie, motif_sortie, statut (`ESSAI`, `ACTIF`, `SUSPENDU`, `ABSENCE_LONGUE`, `SORTI`), manager_employee_id, created_at… | UNIQUE(tenant_id, matricule) ; UNIQUE(tenant_id, user_id) WHERE user_id NOT NULL ; INDEX(tenant_id, statut) ; INDEX(manager_employee_id) |
| `employee_private` (**cloisonnée**) | employee_id (PK), date_naissance, cin_chiffre, cnss_numero_chiffre, adresse, situation_familiale, contact_urgence (JSON), rib_chiffre, notes_rh | Lecture : permission `hr.private.read` ; champs sensibles chiffrés au niveau applicatif |
| `employee_compensation` (**cloisonnée**) | id, employee_id, valid_from, valid_to, salaire_base_millimes, primes (JSON), devise | Lecture : `hr.salary.read` (titulaire par défaut) ; INDEX(employee_id, valid_from) |
| `contracts` | id, tenant_id, employee_id, type (`CDI`, `CDD`, `SIVP`, `STAGE`, `AUTRE` **[À VALIDER]**), debut, fin, fin_periode_essai, duree_hebdo_min, position_id, document_key, statut (`BROUILLON`, `ACTIF`, `TERMINE`, `ROMPU`), avenant_de | INDEX(employee_id, debut) ; un seul contrat `ACTIF` par collaborateur (contrôle applicatif + index partiel) |
| `employee_documents` | id, employee_id, type (contrat, diplôme, attestation, justificatif), fichier_key, confidentialite (`STANDARD`, `PRIVE`, `MEDICAL`), expire_le | Accès selon niveau de confidentialité |

### 2.2 Organisation, compétences, habilitations

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `teams` | id, tenant_id, code, libelle_fr, libelle_ar, couleur, actif | UNIQUE(tenant_id, code) |
| `team_leads` | id, team_id, employee_id, valid_from, valid_to | historisé |
| `positions` | id, tenant_id, code, libelle_fr, libelle_ar, team_id_defaut, role_id_defaut, kpi_profile_id, qualifications_requises (JSON) | UNIQUE(tenant_id, code) |
| `employee_assignments` | id, tenant_id, employee_id, team_id, position_id, type (`PRINCIPALE`, `SECONDAIRE`, `REMPLACEMENT`, `INTERIM`), valid_from, valid_to, jours_mask (bits lun→dim), heure_debut, heure_fin, remplace_employee_id, motif, created_by | INDEX(employee_id, valid_from) ; INDEX(team_id, valid_from) ; une seule `PRINCIPALE` active par date (contrôle) |
| `qualifications` | id, tenant_id (NULL = référentiel national), code (`DIPLOME_PHARMACIEN`, `INSCRIPTION_ORDRE`, `DIPLOME_PREPARATEUR`…), type (diplôme, inscription, certification, formation), validite_mois, justificatif_requis | UNIQUE(COALESCE(tenant_id,''), code) |
| `employee_qualifications` | id, employee_id, qualification_id, numero, obtenue_le, expire_le, justificatif_key, statut (`DECLAREE`, `VERIFIEE`, `EXPIREE`, `REVOQUEE`), verifiee_par, verifiee_le | INDEX(employee_id) ; INDEX(expire_le) |
| `skills` / `employee_skills` | référentiel de compétences (conseil dermo, orthopédie, préparation…) ; employee_id, skill_id, niveau 1-4, evalue_par, evalue_le | — |
| `habilitations` | id, tenant_id, code (`CASH`, `CONTROLLED_DISPENSE`, `LAB_PREPARE`, `LAB_VALIDATE`, `STOCK_ADJUST`, `INVENTORY_VALIDATE`, `RECEIPT_VALIDATE`, `PO_APPROVE`, `HIGH_DISCOUNT`, `REFUND`…), libelle, qualifications_prerequises (JSON), formation_prerequise_id, accordee_par_role (ex. titulaire seul) | UNIQUE(tenant_id, code) |
| `employee_habilitations` | id, employee_id, habilitation_id, valid_from, valid_to, accordee_par_employee_id, statut (`ACTIVE`, `SUSPENDUE`, `RETIREE`, `EXPIREE`), motif | INDEX(employee_id, statut) |
| `permissions` (catalogue) | code (`sales.create`, `controlled.dispense`, `hr.private.read`…), module, sensibilite (1-3), requires_habilitation_code, presence_policy (`NONE`, `CLOCKED_IN`, `ON_SHIFT`, `ASSIGNED_TEAM`) | PK(code) |

`role_permissions` (dossier principal §4.2) reste la source des permissions par rôle ; les octrois et retraits individuels sont portés par le mécanisme `permission_overrides` défini dans les extensions qui l'utilisent (notamment EXT05). Les délégations temporaires restent régies par leur extension. `permissions` demeure le **catalogue** décrivant chaque permission et ses conditions supplémentaires. Une permission réservée à un octroi individuel explicite ne doit pas être activée dans `role_permissions`.

### 2.3 Planning, temps et présence

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `opening_hours` | tenant_id, jour_semaine, ouverture, fermeture, valid_from | — |
| `public_holidays` | id, tenant_id (NULL = national), date, libelle, chome | — |
| `duty_periods` (gardes) | id, tenant_id, debut, fin, type (`NUIT`, `WEEKEND`, `FERIE`), source (`MANUEL`, `IMPORT`), reference | INDEX(tenant_id, debut) |
| `shift_templates` | id, tenant_id, code (`MATIN`, `APREM`, `JOURNEE`, `GARDE_NUIT`), heure_debut, heure_fin (peut dépasser minuit), pause_min, team_id, position_id, couleur | — |
| `rotation_patterns` | id, tenant_id, libelle, cycle_jours, sequence (JSON : jour → template), membres (JSON ordonné) | — |
| `coverage_rules` | id, tenant_id, team_id, position_id (option), qualification_id (option), jours_mask, heure_debut, heure_fin, effectif_min, effectif_cible, severite (`BLOQUANT`, `ALERTE`) | Ex. « 1 pharmacien en permanence pendant l'ouverture » **[À VALIDER]** |
| `schedules` | id, tenant_id, periode_debut, periode_fin, statut (`BROUILLON`, `PUBLIE`, `VERROUILLE`), version, publie_par, publie_le | UNIQUE(tenant_id, periode_debut, version) |
| `shifts` | id, tenant_id, schedule_id, employee_id, team_id, position_id, template_id, start_at, end_at (UTC + fuseau du tenant), pause_min, type (`NORMAL`, `GARDE`, `ASTREINTE`, `FORMATION`), statut (`PLANIFIE`, `CONFIRME`, `ANNULE`), source (`MANUEL`, `ROTATION`, `IA_PROPOSE`), notes, version | INDEX(tenant_id, start_at) ; INDEX(employee_id, start_at) ; CHECK(end_at > start_at) |
| `shift_swap_requests` | id, shift_id, demandeur_id, cible_employee_id, statut, valide_par | — |
| `attendance_records` (**immuable**) | id, tenant_id, employee_id, shift_id (nullable), type (`IN`, `OUT`, `BREAK_START`, `BREAK_END`), at, source (`POS_PIN`, `QR_MOBILE`, `LOGIN`, `MANUEL`), device_id, corrige_record_id, motif, saisi_par, valide_par | INDEX(employee_id, at) — correction = nouvel enregistrement lié |
| `attendance_days` (projection) | tenant_id, employee_id, date, prevu_min, travaille_min, pause_min, retard_min, depart_anticipe_min, heures_sup_min, nuit_min, ferie_min, absence_type, anomalies (JSON), statut (`CALCULE`, `A_VERIFIER`, `VALIDE`), valide_par | PK(employee_id, date) — recalculable |
| `overtime_records` | id, employee_id, date, minutes, categorie (taux paramétrable **[À VALIDER]**), statut (`CALCULE`, `APPROUVE`, `REFUSE`, `RECUPERE`, `PAYE`), approuve_par | INDEX(employee_id, date) |
| `labor_rules` | tenant_id, cle, valeur (JSON) — durée hebdo, repos minimal entre shifts, repos hebdomadaire, plafond heures sup, tolérance de retard | **Toutes valeurs [À VALIDER]** |

### 2.4 Congés et absences

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `leave_types` | id, tenant_id, code (`ANNUEL`, `EXCEPTIONNEL`, `MALADIE`, `MATERNITE`, `ABS_JUSTIFIEE`, `ABS_INJUSTIFIEE`, `AUTORISATION_SORTIE`, `SANS_SOLDE`, `RECUPERATION`), unite (`JOUR_OUVRABLE`, `JOUR_OUVRE`, `HEURE`), avec_solde (bool), remunere, justificatif_requis, confidentiel (bool), regle_acquisition (JSON), circuit_validation (JSON), max_par_demande | UNIQUE(tenant_id, code) |
| `leave_requests` | id, tenant_id, employee_id, leave_type_id, debut, debut_demi_journee, fin, fin_demi_journee, duree_calculee, motif (texte libre non médical), justificatif_key, statut (`BROUILLON`, `SOUMISE`, `VALIDEE_N1`, `APPROUVEE`, `REFUSEE`, `ANNULEE`, `PRISE`), impact_couverture (JSON), saisie_par (soi ou RH) | INDEX(employee_id, debut) ; INDEX(tenant_id, statut) |
| `leave_approvals` | id, request_id, niveau, approbateur_employee_id, decision, commentaire, at | historique complet |
| `leave_ledger` (**immuable**) | id, tenant_id, employee_id, leave_type_id, exercice, date, delta (signé, en minutes ou demi-jours), origine (`ACQUISITION`, `PRISE`, `AJUSTEMENT`, `REPORT`, `EXPIRATION`, `ANNULATION`), ref_id, motif, saisi_par | INDEX(employee_id, leave_type_id, exercice) |
| `leave_balances` (projection) | employee_id, leave_type_id, exercice, acquis, pris, en_attente, reporte, disponible | recalculable depuis `leave_ledger` |

### 2.5 Caisses nominatives (remplace et complète §4.5 du dossier principal)

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `cash_registers` | id, tenant_id, device_id, nom, mode_tiroir (`INDIVIDUEL`, `PARTAGE_SEQUENTIEL`) | — |
| `cash_drawers` | id, register_id, libelle (plateau amovible A/B…), actif | — |
| `cash_sessions` | id, tenant_id, register_id, drawer_id, **employee_id** (titulaire de la session), user_id, ouverte_at, fond_initial, fond_compte_ouverture, cloturee_at, montant_theorique, montant_compte, ecart, statut (`OUVERTE`, `SUSPENDUE`, `EN_COMPTAGE`, `A_VALIDER`, `VALIDEE`, `CONTESTEE`), ecart_statut (`AUCUN`, `JUSTIFIE`, `ACCEPTE`, `A_REGULARISER`, `REGULARISE`), justification, valide_par_employee_id, valide_le, rapport_key | **UNIQUE(employee_id) WHERE statut IN (OUVERTE, SUSPENDUE, EN_COMPTAGE)** ; **UNIQUE(drawer_id) WHERE statut actif** |
| `cash_counts` | id, session_id, moment (`OUVERTURE`, `CLOTURE`, `CONTROLE_INOPINE`), compte_par, temoin_employee_id, total, at | — |
| `cash_count_lines` | count_id, mode (`ESPECES`, `CHEQUE`, `CARTE_TICKET`), denomination_millimes, quantite, total | coupures paramétrables (billets et pièces TND) |
| `cash_movements` | id, session_id, employee_id, type (`VENTE`, `ENCAISSEMENT_DETTE`, `REMBOURSEMENT`, `DEPENSE`, `APPORT`, `RETRAIT`, `TRANSFERT_ENTRANT`, `TRANSFERT_SORTANT`, `REGULARISATION`), mode, montant, ref_doc_type, ref_doc_id, motif, approved_by_employee_id, at | **immuable** ; INDEX(session_id) |
| `cash_transfers` | id, session_source_id, session_cible_id (ou coffre), montant, compte_par, recu_par, at | double signature |
| `drawer_events` | id, register_id, employee_id, type (`OUVERTURE_MANUELLE`, `OUVERTURE_VENTE`), motif, at | alimente l'antifraude |

### 2.6 Laboratoire (M16, minimal)

| Table | Colonnes principales |
|---|---|
| `lab_preparations` | id, tenant_id, numero (séquence, registre des préparations **[À VALIDER]**), type (`MAGISTRALE`, `OFFICINALE`), prescription_id, customer_id, formule (JSON), quantite_produite, statut (`A_PREPARER`, `EN_COURS`, `A_CONTROLER`, `VALIDEE`, `REJETEE`, `DELIVREE`), preparer_employee_id, controle_par_employee_id, date_preparation, date_peremption, observations |
| `lab_preparation_inputs` | preparation_id, product_id, lot_id, quantite_theorique, quantite_pesee, pese_par, stock_movement_id |
| `lab_controls` | preparation_id, type (aspect, pesée, étiquetage), resultat, controle_par, at |

Règles : `preparer_employee_id` doit détenir `LAB_PREPARE` ; `controle_par_employee_id` doit détenir
`LAB_VALIDATE` et être **différent** du préparateur (principe des quatre yeux, paramétrable).
Les matières premières consommées génèrent des mouvements de stock `LAB_CONSUMPTION`.

### 2.7 Qualité, performance, formation

| Table | Colonnes principales | Contraintes / index |
|---|---|---|
| `quality_events` | id, tenant_id, employee_id (concerné, nullable), team_id, type (`ERREUR_DISPENSATION`, `ERREUR_RECEPTION`, `ECART_CAISSE`, `PROCEDURE_NON_RESPECTEE`, `RECLAMATION_CLIENT`, `COMPLIMENT_CLIENT`, `NON_CONFORMITE_LAB`, `PEREMPTION_NON_RETIREE`), gravite (1-4), source (`AUTO`, `MANUEL`), source_ref (doc lié), constate_par, description, statut (`OUVERT`, `ANALYSE`, `CLOS`, `CONTESTE`, `ANNULE`), action_corrective, at | INDEX(employee_id, at) ; contestation par le collaborateur concerné |
| `kpi_definitions` | code, famille (`ACTIVITE`, `QUALITE`, `CONFORMITE`, `FIABILITE`), libelle_fr/ar, unite, sens (`HAUT`, `BAS`), calcul (code d'un calculateur versionné), fenetre, volume_min, explication_template, positions_applicables | PK(code) |
| `kpi_profiles` | id, tenant_id, position_id, version, valid_from, valid_to, poids_axes (JSON), afficher_score_global (bool) | une version active par poste |
| `kpi_profile_items` | profile_id, kpi_code, poids_bp, cible, seuil_bas, seuil_haut, mode (`LINEAIRE`, `PALIERS`) | Σ poids par axe = 10 000 bp |
| `performance_indicators` (valeurs) | id, tenant_id, scope (`EMPLOYEE`, `TEAM`, `PHARMACY`), scope_id, kpi_code, periode_debut, periode_fin, valeur, numerateur, denominateur, volume, heures_travaillees, score_0_100, significatif (bool), sources (JSON : requêtes et ids), version_calcul, calcule_le | UNIQUE(scope, scope_id, kpi_code, periode_debut, version_calcul) |
| `performance_reviews` | id, tenant_id, employee_id, periode_debut, periode_fin, reviewer_employee_id, profile_id (version figée), score_activite, score_qualite, score_conformite, score_fiabilite, score_global (nullable), synthese, objectifs (JSON), statut (`BROUILLON`, `PARTAGEE`, `REPONDUE`, `FINALISEE`, `CONTESTEE`), partagee_le, delai_reponse_le, finalisee_le | INDEX(employee_id, periode_debut) ; finalisée = immuable |
| `employee_comments` (**immuable**) | id, tenant_id, employee_id, review_id (nullable), quality_event_id (nullable), auteur_employee_id, type (`OBSERVATION`, `REPONSE_COLLABORATEUR`, `FELICITATION`, `POINT_AMELIORATION`), visibilite (`COLLABORATEUR_ET_HIERARCHIE`, `HIERARCHIE`), contenu, at | une réponse collaborateur est toujours visible par sa hiérarchie et lui-même |
| `training_catalog` | id, tenant_id, intitule, type (`INTERNE`, `EXTERNE`, `OBLIGATOIRE`), duree_h, validite_mois, habilitation_id (si elle conditionne une habilitation) | — |
| `training_records` | id, employee_id, training_id, statut (`PROPOSEE`, `PLANIFIEE`, `REALISEE`, `VALIDEE`, `ANNULEE`), date, organisme, certificat_key, expire_le, origine (`MANAGER`, `IA`, `OBLIGATION`, `DEMANDE_COLLABORATEUR`) | INDEX(employee_id) ; INDEX(expire_le) |

### 2.8 Colonnes ajoutées aux tables existantes

| Table existante | Ajout | Raison |
|---|---|---|
| `memberships` | `employee_id` (NOT NULL pour un compte opérationnel) | Un compte = un collaborateur |
| `sales` | `seller_employee_id`, `cashier_employee_id`, `cash_session_id`, `approved_by_employee_id` | Vendeur-conseil et encaissant peuvent différer |
| `sale_returns` | `employee_id`, `approved_by_employee_id` | Retours contrôlés |
| `stock_movements` | `employee_id`, `approved_by_employee_id` | Mouvements sensibles attribués |
| `goods_receipts`, `supplier_invoices`, `purchase_orders` | `prepared_by_employee_id`, `validated_by_employee_id` | KPI achats et traçabilité |
| `inventory_counts` | `counted_by_employee_id` ; `inventory_sessions.validated_by_employee_id` | Exactitude par compteur |
| `controlled_register` | `employee_id` (dispensateur habilité) | Réglementaire |
| `loss_records`, `destruction_records` | `employee_id`, `validated_by_employee_id` | Pertes |
| `audit_log` | `employee_id` | Lecture humaine de l'audit |
| `metrics_daily` | dimension `employee` et `team` | KPI issus de la même couche métriques |
| `ai_recommendations` | `audience` (`TITULAIRE`, `MANAGER`, `COLLABORATEUR`), `sensibilite` | Recommandations RH confidentielles |

### 2.9 Résumé des relations (ERD)

```
users 1─n memberships n─1 employees            (UNIQUE employee par tenant/compte)
employees 1─1 employee_private ; 1─n employee_compensation ; 1─n contracts ; 1─n employee_documents
employees n─1 employees (manager) ; employees 1─n employee_assignments n─1 teams / positions
employees 1─n employee_qualifications n─1 qualifications
employees 1─n employee_habilitations n─1 habilitations ─(prérequis)→ qualifications, training_catalog
permissions ─(requires)→ habilitations ; roles 1─n role_permissions n─1 permissions
schedules 1─n shifts n─1 employees / teams / positions / shift_templates
shifts 1─n attendance_records ; employees 1─n attendance_days, overtime_records
employees 1─n leave_requests 1─n leave_approvals ; leave_ledger → leave_balances
cash_registers 1─n cash_drawers 1─n cash_sessions n─1 employees ; cash_sessions 1─n cash_movements, cash_counts
sales / stock_movements / goods_receipts / controlled_register / lab_preparations → employees
quality_events n─1 employees ; kpi_profiles n─1 positions 1─n kpi_profile_items n─1 kpi_definitions
performance_indicators → employees | teams ; performance_reviews 1─n employee_comments
employees 1─n training_records n─1 training_catalog
```

---

## 3. Relations avec les autres modules

### 3.1 Matrice d'intégration

| Module | Ce que RH fournit | Ce que RH reçoit | Règle d'intégration |
|---|---|---|---|
| **M00 Plateforme / permissions** | Collaborateur rattaché au compte, habilitations, qualifications, statut, présence | Création de compte, rôles | Le moteur de droits effectifs (§1.2) remplace le simple contrôle par rôle |
| **M02 Vente** | Vendeur identifié (`seller_employee_id`), plafond de remise selon habilitation | Transactions, durée de traitement, retours, annulations | Changement d'opérateur par PIN ; chaque ligne attribuée |
| **M02b / M15.6 Caisse** | Titulaire de la session, validation par responsable habilité | Mouvements, écarts, ouvertures de tiroir | Aucun encaissement hors session nominative ouverte |
| **M03 Achats** | Préparateur et valideur des réceptions et factures | Délais de traitement, écarts de réception | Validation d'une réception soumise à `RECEIPT_VALIDATE` |
| **M05 Stock / inventaires** | Auteur et valideur des ajustements, compteurs | Écarts d'inventaire par compteur, ajustements | Ajustement au-delà d'un seuil : validation par un second habilité |
| **M09 Réglementés** | Dispensateur habilité | Opérations du registre | Blocage si habilitation absente ou expirée |
| **M10 Péremptions** | Responsable du suivi par zone ou équipe | Lots périmés non retirés, retraits effectués | KPI « suivi des péremptions » de l'équipe Stock |
| **M11 Commandes** | Approbateur habilité (`PO_APPROVE`) | Délais entre suggestion et validation | Aucun envoi sans approbation nominative |
| **M16 Laboratoire** | Préparateur et contrôleur habilités, distincts | Préparations, contrôles, non-conformités | Contrôle par une personne différente |
| **M12 Rapports / couche métriques** | Dimensions collaborateur et équipe | Agrégats | Les KPI RH utilisent la même couche métriques que le tableau de bord |
| **M13 PHARM'OS AI** | Données de planning, présence, KPI (avec audience restreinte) | Recommandations et brouillons de planning | Aucune décision RH exécutée sans validation humaine |
| **M01 Tableau de bord** | Widgets « présents », « shifts en cours », « anomalies RH » | — | Visible selon permissions |

### 3.2 Événements de domaine émis et consommés

| Événement | Émis par | Consommé par |
|---|---|---|
| `employee.created`, `employee.status_changed`, `employee.left` | M15.1 | M00 (désactivation du compte à la sortie), M15.3 (retrait des shifts futurs) |
| `habilitation.granted`, `habilitation.revoked`, `qualification.expired` | M15.2 | M00 (invalidation du cache de droits), M13 (alerte formation) |
| `schedule.published`, `shift.changed` | M15.3 | Notifications aux collaborateurs, M13 |
| `attendance.clocked_in/out` | M15.4 | Droits liés à la présence, M15.6 (ouverture de caisse proposée) |
| `leave.approved`, `leave.cancelled` | M15.5 | M15.3 (shifts en conflit marqués), `leave_ledger` |
| `cash_session.closed`, `cash_session.variance_detected` | M15.6 | M15.7 (KPI caisse), M13 (anomalies), notifications au titulaire |
| `sale.completed`, `sale.voided`, `stock.adjusted`, `receipt.validated`, `inventory.validated`, `lab.preparation.validated` | Modules métier | M15.7 (calcul des KPI), M13 |
| `quality_event.created`, `quality_event.contested` | M15.7 et automatiques | Collaborateur concerné, manager |

### 3.3 Règles de cohérence transversales

1. **Aucune opération sans auteur** : les colonnes `employee_id` des tables métier sont `NOT NULL`
   pour les opérations humaines. Les opérations système portent l'acteur `SYSTEM` et les brouillons de l'IA l'acteur `AI`.
2. **Sortie d'un collaborateur** : compte désactivé, sessions révoquées, habilitations clôturées,
   shifts futurs retirés (avec alerte de sous-effectif). Session de caisse ouverte : la sortie est bloquée tant qu'elle n'est pas clôturée.
   Historique intégralement conservé.
3. **Changement d'affectation** : il ne modifie jamais l'attribution des opérations passées. Les KPI
   historiques restent rattachés au poste occupé au moment des faits.
4. **Une seule source par donnée** : le nom d'un collaborateur n'est saisi que dans `employees`. Les
   documents (tickets, registres) affichent un instantané figé au moment de l'opération.

---

## 4. Workflows

### 4.1 Embauche (onboarding)

```
RH crée le collaborateur (BROUILLON) → contrat (BROUILLON) → affectation principale + poste
  → qualifications déclarées + justificatifs → vérification par le titulaire (VERIFIEE)
  → habilitations accordées (le système contrôle les prérequis)
  → création du compte (invitation email/SMS) + PIN de caisse
  → contrat ACTIF + collaborateur ESSAI/ACTIF → formations obligatoires planifiées
  → intégration au planning
```

### 4.2 Changement d'affectation ou remplacement

```
Manager crée l'affectation (type, période, jours, horaires, personne remplacée)
  → contrôle : qualifications du poste, habilitations requises, conflit avec affectation principale
  → si une habilitation manque : demande d'habilitation temporaire (validation du titulaire)
  → enregistrement historisé → planning mis à jour → notification au collaborateur
```

### 4.3 Planning

```
Besoins de couverture + horaires d'ouverture + gardes + congés approuvés
  → génération (rotation ou proposition IA) ou saisie glisser-déposer → BROUILLON
  → contrôles en continu (conflits, repos, sous-effectif, qualifications) → corrections
  → publication par le manager (version n) → notification aux collaborateurs
  → modifications après publication : nouvelle version, motif obligatoire, notification aux personnes concernées
  → période écoulée : VERROUILLE (seules des corrections tracées restent possibles)
```

### 4.4 Pointage et validation du temps

```
Pointage (PIN au poste, QR mobile, ou à la connexion si paramétré) → attendance_records
  → calcul quotidien de attendance_days (prévu vs réel)
  → anomalies : retard, oubli de pointage, départ anticipé, heures sup, absence sans demande
  → collaborateur : justification ou demande de correction
  → manager : valide la journée, la correction, et les heures sup (APPROUVE / REFUSE / RECUPERE)
  → clôture mensuelle → export des variables de paie (CSV)
```

### 4.5 Demande de congé

```
Collaborateur (ou RH pour son compte) saisit la demande → calcul de la durée (jours ouvrables,
  jours fériés exclus) et du solde prévisionnel → analyse de l'impact sur la couverture
  → SOUMISE → N1 (responsable d'équipe) → N2 (titulaire) si règle (durée, période sensible, solde négatif)
  → APPROUVEE : écriture leave_ledger (PRISE), shifts en conflit libérés, alerte de sous-effectif si nécessaire
  → REFUSEE : motif obligatoire et visible par le collaborateur
  → annulation après approbation : contre-écriture dans le ledger
```

### 4.6 Session de caisse nominative

Détaillée au §7.

### 4.7 Cycle d'évaluation

```
Période (mensuelle pour les KPI, semestrielle ou annuelle pour l'évaluation) → calcul des KPI figés
  → le manager prépare l'évaluation (BROUILLON) : scores par axe, observations, objectifs
  → PARTAGEE avec le collaborateur → délai de réponse (paramétrable, ex. 10 jours)
  → réponse du collaborateur (REPONDUE), possibilité de contester un KPI ou un événement qualité
  → entretien → FINALISEE (immuable ; toute correction ultérieure passe par un addendum)
  → actions : formations proposées, objectifs, évolution d'habilitation
```

### 4.8 Événement qualité

```
Détection automatique (écart de caisse, écart de réception, lot périmé non retiré, annulation anormale)
  ou déclaration manuelle → OUVERT → notification au collaborateur concerné et au manager
  → analyse (cause, contexte) → action corrective → CLOS
  → contestation possible par le collaborateur → décision motivée du titulaire
  → seuls les événements CLOS et non annulés entrent dans les KPI
```

---

## 5. Système de planning

### 5.1 Vues

| Vue | Contenu | Usage |
|---|---|---|
| **Jour** | Frise horaire par collaborateur (lignes) et par équipe ; courbe de couverture prévue vs requise ; présences réelles en surimpression | Pilotage du jour |
| **Semaine** | Grille collaborateurs × jours ; cases de shifts colorées par équipe ; compteurs d'heures hebdomadaires | Construction du planning |
| **Mois** | Calendrier condensé avec congés, gardes, jours fériés, repos | Vision d'ensemble, congés |
| **Équipe / poste** | Couverture par équipe et par créneau | Contrôle du sous-effectif |
| **Mon planning** (mobile) | Shifts personnels, échanges, demandes | Collaborateur |

### 5.2 Interactions

- **Glisser-déposer** : déplacer un shift (heure ou personne), étirer sa durée, dupliquer (Ctrl+glisser),
  déposer un modèle de shift depuis la palette, copier une semaine, appliquer une rotation.
- Mise à jour optimiste avec contrôle serveur : une violation **bloquante** annule le déplacement et
  affiche le motif ; une **alerte** laisse le shift en place avec un badge.
- Sur tablette et mobile : appui long pour déplacer, et alternative au clavier ou par formulaire pour l'accessibilité.
- Mises en page RTL : la frise horaire s'inverse en arabe, les heures restent lisibles.
- Implémentation : Pointer Events natifs (pas de dépendance lourde), virtualisation des lignes au-delà de 30 collaborateurs.

### 5.3 Contrôles automatiques

| Contrôle | Type | Paramètre |
|---|---|---|
| Chevauchement de deux shifts d'un même collaborateur | Bloquant | — |
| Shift pendant un congé approuvé | Bloquant | — |
| Collaborateur sorti, suspendu, ou contrat inactif | Bloquant | — |
| Qualification ou habilitation requise par le poste absente | Bloquant (paramétrable) | `coverage_rules`, `positions` |
| Repos minimal entre deux shifts | Alerte ou bloquant | `labor_rules.repos_min_h` **[À VALIDER]** |
| Repos hebdomadaire | Alerte | **[À VALIDER]** |
| Durée maximale quotidienne / hebdomadaire | Alerte | **[À VALIDER]** |
| Heures sup prévisionnelles au-delà du plafond | Alerte | **[À VALIDER]** |
| **Sous-effectif** : effectif < `effectif_min` sur un créneau | Alerte (bloquant pour publier si la règle est `BLOQUANT`) | `coverage_rules` |
| **Absence de pharmacien** pendant l'ouverture ou une garde | Bloquant pour publier | **[À VALIDER réglementairement]** |
| Sureffectif (> cible + tolérance) | Information | `coverage_rules` |
| Inéquité (gardes, week-ends) sur 3 mois | Information | — |

L'algorithme de couverture découpe la journée en tranches de 15 minutes et compare, pour chaque
règle, le nombre de shifts éligibles à l'effectif requis. Le résultat est un tableau
`créneau → (requis, planifié, présent)` réutilisé par l'écran, l'IA et le tableau de bord.

### 5.4 Heures supplémentaires, retards, absences

- **Retard** = premier `IN` − début du shift, au-delà d'une tolérance paramétrable (ex. 5 min).
- **Départ anticipé** = fin du shift − dernier `OUT`.
- **Absence** = shift planifié sans pointage, ni congé, ni justification à J+1 → `ABS_INJUSTIFIEE` provisoire, à qualifier par le manager.
- **Heures supplémentaires** = travaillé validé au-delà de la durée contractuelle (quotidienne ou hebdomadaire selon la règle), ventilé par catégorie (jour, nuit, jour férié, repos) avec des taux de majoration paramétrables **[À VALIDER]**. Elles ne sont comptées qu'après approbation.
- Les gardes sont des shifts de type `GARDE` ; leur rémunération spécifique est hors périmètre (export pour la paie).

### 5.5 Paie

PHARM'OS **ne calcule pas la paie** dans cette version. Il produit un export mensuel des variables
(jours travaillés, absences par type, heures sup par catégorie, gardes, congés pris) au format CSV
paramétrable pour le logiciel de paie ou le comptable. **[À DÉCIDER : intégration paie ultérieure]**

---

## 6. Gestion des congés et absences

### 6.1 Types et règles

| Type | Solde | Justificatif | Confidentialité | Validation par défaut |
|---|---|---|---|---|
| Congé annuel | Oui (acquisition mensuelle paramétrable **[À VALIDER]**) | Non | Standard | N1 puis N2 au-delà de N jours |
| Congé exceptionnel (mariage, naissance, décès…) | Forfait par motif **[À VALIDER]** | Oui | Standard | N1 |
| Congé maladie | Non (compteur) | Certificat | **Médical** : seul le type « absence pour raison de santé » est visible du manager, le certificat est accessible au titulaire et à la RH uniquement | Enregistrement par la RH |
| Absence justifiée | Non | Selon motif | Standard | N1 |
| Absence injustifiée | Non | — | Standard | Qualification par le manager |
| Autorisation de sortie | Compteur en heures | Non | Standard | N1, possibilité de validation instantanée |
| Récupération | Alimentée par les heures sup en mode `RECUPERE` | Non | Standard | N1 |
| Sans solde | Non | Non | Standard | N2 |

### 6.2 Calcul et soldes

- Les soldes sont **toujours** la somme du `leave_ledger` (acquisitions, prises, ajustements motivés,
  reports, expirations). Aucune saisie directe d'un solde.
- Le calcul de la durée tient compte du calendrier (jours ouvrables ou ouvrés, jours fériés, demi-journées) ;
  le résultat est enregistré dans la demande pour être auditable.
- Le solde négatif est interdit par défaut, autorisé par exception par le titulaire.
- Un traitement en fin d'exercice gère le report et l'expiration selon les règles paramétrées.

### 6.3 Calendrier collectif et chevauchements

- Calendrier mensuel par équipe : congés approuvés, demandes en attente (en hachuré), jours fériés, gardes.
- À la soumission et à la validation, le système calcule l'impact : « Si cette demande est approuvée,
  l'équipe Comptoir passe sous l'effectif minimal le 14/12 de 14 h à 18 h (1 présent / 2 requis) ;
  aucun pharmacien n'est disponible le 15/12 matin ».
- Règles paramétrables : nombre maximal d'absents simultanés par équipe, périodes de restriction
  (ex. fin d'année, inventaire annuel), délai de prévenance.
- Le système **alerte**, le manager **décide** : l'approbation reste possible avec un motif tracé.

---

## 7. Caisses nominatives

### 7.1 Principes

1. **Une session = un collaborateur = un tiroir (ou plateau)**. Un collaborateur ne peut avoir qu'une
   session active ; un tiroir ne peut porter qu'une session active (contraintes d'unicité, §2.5).
2. **Aucun encaissement hors session**. Une vente peut être *préparée* par un vendeur-conseil sans session,
   mais son *encaissement* exige la session nominative de l'encaissant.
3. **Poste partagé** : si plusieurs collaborateurs utilisent la même caisse physique, deux modes sont proposés :
   - `INDIVIDUEL` (recommandé) : plateaux de tiroir amovibles, une session par plateau ;
   - `PARTAGE_SEQUENTIEL` : la session en cours doit être clôturée (ou **suspendue avec comptage**) avant
     qu'un autre collaborateur ouvre la sienne. Le changement d'opérateur par PIN n'autorise pas
     l'encaissement dans la session d'un autre.
4. **Mouvements immuables** : toute erreur se corrige par un mouvement de régularisation motivé et approuvé.
5. **Comptage à l'aveugle** : à la clôture, le collaborateur saisit son comptage **avant** de voir le
   montant théorique (paramétrable, activé par défaut). L'écart est calculé ensuite.

### 7.2 Cycle de vie

```
OUVERTURE
  collaborateur habilité CASH, pointé (si politique), contrat actif
  → choix du tiroir/plateau → comptage du fond par coupures (cash_counts OUVERTURE)
  → écart avec le fond attendu (fond remis par le responsable ou laissé par la session précédente)
     → si écart : signalement immédiat au responsable
  → statut OUVERTE

EN COURS
  ventes, encaissements de dettes, remboursements (habilitation REFUND + plafond),
  dépenses (catégorie + justificatif), apports/retraits (double signature),
  transferts vers coffre, contrôles inopinés (comptage par un responsable avec témoin)
  ouverture manuelle du tiroir → motif obligatoire → drawer_events
  pause → SUSPENDUE (verrouillage du poste, aucune opération possible)

CLÔTURE
  → EN_COMPTAGE : saisie par coupures (espèces), liste des chèques (rapprochée des instruments
    en portefeuille), tickets carte (rapprochés des paiements carte)
  → calcul : théorique = fond + Σ entrées − Σ sorties, par mode de paiement
  → écart par mode = compté − théorique
  → |écart| ≤ tolérance (ex. 0,500 TND) : VALIDEE automatiquement, si paramétré
  → sinon A_VALIDER : justification obligatoire par le collaborateur
  → responsable habilité (≠ titulaire de la session) : ACCEPTE / A_REGULARISER / CONTESTEE
  → rapport de session (PDF figé, R2) + événement cash_session.closed
  → fond pour la session suivante / remise au coffre
```

### 7.3 Formule du solde théorique

Pour chaque mode `m` (espèces, chèque, carte…) :

```
théorique(m) = fond_initial(m)
             + Σ ventes encaissées(m) + Σ encaissements de dettes(m) + Σ apports(m) + Σ transferts entrants(m)
             − Σ remboursements(m) − Σ dépenses(m) − Σ retraits(m) − Σ transferts sortants(m)
écart(m)     = compté(m) − théorique(m)
```

Les ventes à crédit n'entrent pas dans le théorique (aucun encaissement), mais figurent au rapport.
Tous les montants sont en millimes : aucun arrondi n'intervient dans le calcul.

### 7.4 Contrôles et droits

| Opération | Droit requis | Validation supplémentaire |
|---|---|---|
| Ouvrir une session | `cash.session.open` + habilitation `CASH` | — |
| Annuler une vente de sa session non clôturée | `sales.void.own` | PIN responsable au-delà d'un montant ou d'un délai |
| Annuler la vente d'un autre collaborateur | `sales.void.any` | Toujours tracée, motif obligatoire |
| Remboursement | `sales.refund` + habilitation `REFUND` | PIN responsable au-delà du plafond |
| Dépense depuis la caisse | `cash.expense` | Justificatif ; plafond |
| Ouverture manuelle du tiroir | `cash.drawer.open` | Motif obligatoire |
| Valider un écart | `cash.session.validate` | Ne peut pas valider sa propre session |
| Régulariser un écart | `cash.session.regularize` | Titulaire ; mouvement `REGULARISATION` ; information du collaborateur |
| Rouvrir une session clôturée | **Interdit** | Correction uniquement par régularisation |

### 7.5 Traitement des écarts

- Un écart n'est **jamais** automatiquement imputé au salaire. PHARM'OS enregistre, documente et
  laisse la décision au responsable, conformément au droit applicable **[À VALIDER]**.
- Chaque écart supérieur à la tolérance crée un `quality_event` `ECART_CAISSE`, qui n'entre dans les KPI
  qu'une fois clos (éventuellement après contestation).
- Écarts répétés, même faibles, et ouvertures manuelles du tiroir sans vente : signaux transmis à l'IA (§9).

### 7.6 Journal d'audit non modifiable

Toutes les opérations de caisse sont écrites dans `cash_movements` (immuable) **et** dans `audit_log`
(chaîné par hachage, dossier principal §9.3). Le rapport de session est figé (PDF + hachage enregistré).
Aucune API ne permet la modification ou la suppression d'un mouvement, y compris pour le titulaire.

---

## 8. Système de KPI et d'évaluation

### 8.1 Principes

1. **Quatre axes séparés, jamais fusionnés par défaut** : **Activité** (volume, productivité),
   **Qualité** (erreurs, réclamations, non-conformités), **Conformité** (respect des procédures,
   traçabilité), **Fiabilité** (ponctualité, exactitude des clôtures). Un score global n'est affiché
   que si le titulaire l'active explicitement dans le profil de notation.
2. **Indicateurs mesurables uniquement** : chaque KPI correspond à un calculateur versionné qui
   enregistre numérateur, dénominateur, volume et références des données sources.
3. **Normalisation par le temps de présence et le contexte** : les volumes sont rapportés aux heures
   travaillées et comparés aux collègues **du même poste, sur les mêmes créneaux** (un samedi matin n'a pas le même flux qu'un mardi après-midi).
4. **Significativité** : en dessous d'un volume minimal (ex. moins de 30 transactions ou moins de 20 heures sur la période), le KPI est
   affiché « non significatif » et exclu du score.
5. **Absences légitimes neutres** : congés, maladie et formation ne pénalisent aucun indicateur.
6. **Explicabilité** : chaque score s'affiche avec sa formule, ses données, sa cible, son poids et sa
   contribution, ainsi qu'un lien vers les opérations sources (selon les permissions).
7. **Aucune sanction automatique** : les KPI sont des aides au dialogue, et les décisions relèvent du responsable.

### 8.2 Catalogue des KPI par fonction (valeurs par défaut paramétrables)

| Fonction | Axe | KPI | Formule | Source |
|---|---|---|---|---|
| **Vente** | Activité | Transactions par heure travaillée | nb ventes validées / heures travaillées | `sales`, `attendance_days` |
| | Activité | Temps moyen de traitement | médiane (validée_at − première ligne ajoutée), hors ventes en attente | horodatages de vente |
| | Qualité | Taux de retours imputables | retours pour erreur de dispensation / ventes | `sale_returns` (motif), `quality_events` |
| | Qualité | Qualité du service | réclamations et compliments clos, enquête facultative **[À DÉCIDER]** | `quality_events` |
| | Conformité | Respect des procédures | ventes réglementées conformes (ordonnance saisie) / ventes réglementées ; remises dans le plafond / remises | `controlled_register`, `sale_lines` |
| **Laboratoire** | Activité | Préparations réalisées | nb préparations validées | `lab_preparations` |
| | Qualité | Taux de non-conformité | préparations rejetées ou avec écart de pesée / préparations | `lab_controls` |
| | Conformité | Traçabilité complète | préparations avec lots, pesées et contrôle renseignés / préparations | `lab_preparation_inputs` |
| | Conformité | Contrôle indépendant | préparations contrôlées par une autre personne habilitée / préparations | `lab_preparations` |
| **Stock** | Qualité | Exactitude des inventaires | 1 − Σ\|écart\| / Σ quantités comptées (par compteur, après recomptage) | `inventory_counts` |
| | Qualité | Erreurs de réception | lignes avec écart détecté a posteriori / lignes réceptionnées | `goods_receipt_lines`, `quality_events` |
| | Conformité | Ajustements justifiés | ajustements avec motif et validation / ajustements | `stock_movements` |
| | Conformité | Suivi des péremptions | lots périmés retirés dans le délai / lots arrivés à péremption | `lots`, `loss_records` |
| **Caisse** | Fiabilité | Exactitude des clôtures | sessions sans écart au-delà de la tolérance / sessions | `cash_sessions` |
| | Fiabilité | Écart moyen absolu | moyenne de \|écart\| par session (millimes) | `cash_sessions` |
| | Conformité | Traçabilité | opérations sensibles motivées (tiroir, annulation, remboursement) / opérations sensibles | `drawer_events`, `audit_log` |
| | Conformité | Conformité des opérations | sessions sans anomalie de procédure / sessions | `quality_events` |
| **Achats** | Activité | Délai de traitement | médiane (réception validée − arrivée du BL) ; (facture saisie − réception) | `goods_receipts`, `supplier_invoices` |
| | Conformité | Respect des procédures | réceptions rapprochées BC ↔ BL ↔ facture / réceptions | `purchase_orders`, `goods_receipts` |
| | Qualité | Suivi fournisseurs | écarts de prix détectés avant validation / écarts de prix ; litiges clos dans le délai | `goods_receipt_lines` |
| **Tous postes** | Fiabilité | Ponctualité | shifts sans retard au-delà de la tolérance / shifts | `attendance_days` |
| | Conformité | Formations obligatoires à jour | formations valides / formations requises | `training_records` |

### 8.3 Notation paramétrable

- Chaque poste possède un **profil de notation versionné** (`kpi_profiles`) : KPI retenus, poids par axe
  (en points de base, somme égale à 100 %), cible, seuils, mode de conversion (linéaire ou par paliers).
- Conversion d'un KPI en score de 0 à 100 :
  `score = clamp((valeur − seuil_bas) / (cible − seuil_bas), 0, 1) × 100` (inversée si un KPI « plus bas est meilleur »).
- Score d'un axe = moyenne pondérée des KPI **significatifs** ; les poids sont renormalisés si un KPI est exclu, et l'interface l'indique.
- Score global (optionnel) = moyenne pondérée des axes, présentée **toujours** à côté du détail par axe.
- Toute modification d'un profil crée une nouvelle version, qui ne s'applique pas rétroactivement aux évaluations finalisées.

Exemple d'explication affichée :
> **Qualité — 82/100** · Taux de retours imputables 0,6 % (cible ≤ 0,5 %, seuil 2 %) → 93/100, poids 60 % ·
> Réclamations : 1 close sur 412 ventes → 66/100, poids 40 % · Période 01/10–31/10 · 98 h travaillées ·
> Données : 412 ventes, 3 retours, 1 réclamation (voir le détail).

### 8.4 Observations et droit de réponse

- Le responsable rédige des observations (`employee_comments`) liées à une évaluation ou à un événement qualité.
- Le collaborateur est notifié, consulte ses KPI et l'évaluation, et dispose d'un **droit de réponse** écrit
  dans un délai paramétrable. Sa réponse est immuable et conservée avec l'évaluation.
- Le collaborateur peut **contester** un événement qualité ou une valeur de KPI ; la contestation suspend
  la prise en compte de l'élément jusqu'à la décision motivée du titulaire.
- Le collaborateur voit toujours ses propres indicateurs ; il ne voit pas ceux de ses collègues
  (sauf indicateurs collectifs d'équipe, anonymisés en dessous de 3 personnes).

### 8.5 KPI collectifs

Les mêmes calculateurs s'appliquent au niveau de l'équipe et de la pharmacie (`scope = TEAM / PHARMACY`),
ce qui garantit que la somme des indicateurs individuels et les KPI collectifs restent cohérents.

---

## 9. Intégration PHARM'OS AI

### 9.1 Nouveaux analyseurs (extension du dossier principal §7.3)

| Analyseur | Méthode déterministe | Exemple de recommandation | Audience |
|---|---|---|---|
| **I. Couverture des shifts** | Tableau de couverture par tranche de 15 min vs `coverage_rules` ; prévision de charge issue des ventes horaires historiques (même jour de semaine, 8 semaines) | « Mardi 14 h–17 h : affluence prévue 38 ventes/h, 2 vendeurs planifiés pour 3 nécessaires » | Manager |
| **J. Conflits de planning** | Contrôles du §5.3 sur le brouillon et le publié | « 3 conflits dans le planning de la semaine 47 » | Manager |
| **K. Sous-effectif et absences** | Congés approuvés et en attente, absences en cours, gardes → simulation de couverture J+30 | « Le 24/12, 2 congés en attente rendraient impossible la présence d'un pharmacien de 18 h à 20 h » | Manager, titulaire |
| **L. Anomalies de caisse** | Écarts répétés, écarts signés systématiques, tiroir ouvert sans vente, annulations après encaissement, remboursements atypiques (z-score par rapport aux pairs, même créneau) | « 5 ouvertures manuelles du tiroir sans vente cette semaine sur la caisse 2 (moyenne 0,4) » | **Titulaire uniquement** |
| **M. Tendances de productivité** | Évolution sur 8 à 12 semaines des KPI d'activité, normalisés par la charge | « Temps moyen de traitement en hausse de 25 % sur 4 semaines pour l'équipe Comptoir, en parallèle d'une hausse de 30 % des ventes à ordonnance » | Manager |
| **N. Besoins de formation** | KPI qualité ou conformité sous le seuil, habilitations ou formations expirant sous 60 j, nouvelles procédures | « 2 collaborateurs : formation *Procédure des réglementés* expirant le 15/12 » | Manager, collaborateur concerné |
| **O. Répartition de la charge** | Heures, gardes, week-ends, heures sup par collaborateur sur 3 mois ; indice d'équité | « Ali a effectué 6 gardes sur 3 mois, l'équipe 2,5 en moyenne » | Manager |

### 9.2 Propositions d'ajustement de planning

- L'IA peut générer un **brouillon de planning** ou des **propositions unitaires** (échanger deux shifts,
  ajouter un renfort, déplacer un créneau), calculés par un solveur sous contraintes (contraintes dures :
  §5.3 bloquants ; contraintes souples pondérées : couverture cible, équité, préférences, heures sup minimales).
- Les propositions sont créées avec `source = IA_PROPOSE` dans un planning `BROUILLON` et chacune indique
  le problème résolu, les contraintes respectées et l'impact (heures sup, équité).
- **Seul un manager peut publier.** L'IA n'approuve ni ne refuse aucun congé, ne crée ni ne modifie aucune
  habilitation, ne valide aucun écart de caisse, ne finalise aucune évaluation et ne crée aucun événement
  qualité nominatif sans validation humaine.

### 9.3 Garde-fous spécifiques aux données RH

1. **Aucune donnée privée ou salariale** (`employee_private`, `employee_compensation`) ni aucun motif médical n'est transmis au LLM.
2. Les collaborateurs sont **pseudonymisés** (identifiant temporaire) dans les échanges avec le LLM ; la réidentification se fait côté serveur.
3. Les recommandations nominatives portent une `audience` : une anomalie de caisse n'est visible que du titulaire, et une
   recommandation de formation est visible du manager et du collaborateur concerné.
4. Aucune recommandation ne porte de jugement de valeur sur une personne : elle décrit des faits mesurés et le contexte
   (« ces écarts peuvent aussi provenir d'un problème de procédure ou de rendu monnaie »).
5. L'avatar ne présente jamais une alerte RH nominative sur un écran visible du public (caisse) : ces alertes s'affichent uniquement dans
   l'espace manager.

---

## 10. Tableau de bord RH

| Bloc | Contenu | Source | Visibilité |
|---|---|---|---|
| Effectifs | Effectif total actif, entrées/sorties du mois, répartition par contrat | `employees`, `contracts` | Manager, titulaire |
| Présence du jour | Présents (pointés), attendus non pointés, absents, en congé, en retard | `attendance_records`, `shifts`, `leave_requests` | Manager |
| Répartition par équipe | Présents / planifiés / requis par équipe, maintenant et sur la journée | Couverture §5.3 | Manager |
| Planning du jour | Frise compacte des shifts, gardes | `shifts` | Tous (soi) / manager (tous) |
| Shifts en cours | Qui est en poste, sur quel poste, sessions de caisse ouvertes | `shifts`, `cash_sessions` | Manager |
| Heures supplémentaires | Mois en cours : calculées, approuvées, en attente, par équipe | `overtime_records` | Manager |
| Anomalies | Pointages manquants, retards, absences non qualifiées, écarts de caisse à valider, conflits de planning, qualifications expirant | Projections | Manager |
| Congés | Demandes en attente, absents de la semaine, chevauchements à venir | `leave_requests` | Manager |
| KPI collectifs | Quatre axes par équipe, tendance sur 3 mois | `performance_indicators` | Manager, titulaire |
| KPI individuels | Accès à la fiche de chaque collaborateur (avec permission) ; « Mes indicateurs » pour chacun | `performance_indicators` | Selon permissions |
| Alertes IA | Recommandations RH filtrées par audience | `ai_recommendations` | Selon audience |

Chaque bloc renvoie vers l'écran détaillé correspondant avec les filtres pré-appliqués.

---

## 11. Sécurité, confidentialité et permissions RH

### 11.1 Niveaux de confidentialité

| Niveau | Données | Accès par défaut |
|---|---|---|
| **N0 Public interne** | Nom d'usage, photo, équipe, poste, planning publié | Tous les collaborateurs |
| **N1 Professionnel** | Contrat (type, dates), affectations, qualifications, habilitations, présence, congés (type non médical) | Manager de l'équipe, RH, titulaire, le collaborateur |
| **N2 Évaluation** | KPI individuels, évaluations, commentaires, événements qualité | Manager direct, titulaire, le collaborateur |
| **N3 Personnel** | Date de naissance, CIN, CNSS, adresse, situation familiale, RIB | RH désignée, titulaire |
| **N4 Salarial et médical** | Rémunération, certificats médicaux | Titulaire (et RH si délégation explicite) |

N3 et N4 sont stockés dans des tables séparées, avec chiffrement applicatif des champs identifiants, et
**chaque consultation** est inscrite au journal d'audit (lecture comprise).

### 11.2 Permissions RH (extrait du catalogue)

`hr.employee.read` · `hr.employee.write` · `hr.private.read` · `hr.private.write` · `hr.salary.read` ·
`hr.salary.write` · `hr.contract.write` · `hr.assignment.write` · `hr.qualification.verify` ·
`hr.habilitation.grant` · `hr.schedule.edit` · `hr.schedule.publish` · `hr.attendance.correct` ·
`hr.attendance.validate` · `hr.overtime.approve` · `hr.leave.request` · `hr.leave.approve.n1` ·
`hr.leave.approve.n2` · `hr.leave.adjust_balance` · `hr.kpi.read.own` · `hr.kpi.read.team` ·
`hr.kpi.read.all` · `hr.review.write` · `hr.review.finalize` · `hr.quality.declare` · `hr.quality.decide` ·
`hr.training.manage` · `hr.export.payroll` · `cash.session.*` (§7.4)

Les portées `own` / `team` / `all` sont résolues à partir des affectations et de la hiérarchie à la date de
la requête. Un manager ne peut ni valider sa propre demande, ni ses propres heures supplémentaires, ni sa
propre session de caisse.

### 11.3 Conformité

- Les données RH relèvent de la loi organique n° 2004-63 (déclaration INPDP) **[À VALIDER]** ; les
  certificats médicaux sont des données de santé.
- Durées de conservation paramétrables par catégorie (contrats, pointages, évaluations, documents),
  avec archivage puis anonymisation à l'échéance **[À VALIDER]**.
- Le pointage par géolocalisation ou par biométrie est **exclu** de la v1 (proportionnalité, autorisations spécifiques).
- Le collaborateur peut exporter ses propres données (droit d'accès).
- La surveillance de l'activité est transparente : les KPI collectés sont listés et consultables par chaque collaborateur.

---

## 12. Plan de développement et de tests

### 12.1 Insertion dans le plan global

L'extension est répartie entre des lots existants (ce qui est structurel doit exister **avant** les
modules métier) et deux nouveaux lots :

| Lot | Contenu RH ajouté | Justification |
|---|---|---|
| **1. Plateforme** (étendu) | `employees` (identité, statut, manager), lien compte ↔ collaborateur, catalogue `permissions`, `teams`, `positions`, `employee_assignments`, `qualifications`, `habilitations`, **moteur de droits effectifs** | Tous les modules métier en dépendent pour attribuer les opérations |
| **5. Caisse & ventes** (étendu) | Caisses nominatives complètes (§7) : tiroirs, comptage par coupures, comptage à l'aveugle, écarts, validation, transferts, `drawer_events` | La caisse doit naître nominative |
| **9b. Laboratoire** (nouveau) | M16 : préparations, pesées, contrôles, consommation de stock, habilitations | Après stock, avant réglementés |
| **10b. RH opérationnelle** (nouveau) | Dossier complet (contrats, documents, N3/N4), planning glisser-déposer, rotations, couverture, gardes, pointage, heures sup, congés et soldes, calendrier collectif, export paie, tableau de bord RH (partie opérationnelle) | Après les modules métier, dont il consomme la charge |
| **11. Métriques & rapports** (étendu) | Dimensions collaborateur et équipe, rapports RH | Couche unique |
| **11b. Performance & formation** (nouveau) | Événements qualité, calculateurs KPI, profils de notation, évaluations, commentaires et droit de réponse, formations | Nécessite des données métier réelles |
| **13. PHARM'OS AI** (étendu) | Analyseurs I à O, solveur de planning (brouillons), audiences et pseudonymisation RH | Après 10b et 11b |

### 12.2 Stratégie de tests

| Domaine | Tests obligatoires (exemples) |
|---|---|
| **Droits effectifs** | Table de vérité rôle × habilitation × qualification × statut × présence ; habilitation expirée la veille → refus avec motif ; changement d'affectation pris en compte immédiatement (cache invalidé) ; un collaborateur sorti ne peut plus se connecter |
| **Isolation** | Aucun accès inter-pharmacies aux tables RH ; un manager ne voit que son équipe ; un collaborateur ne voit que ses KPI ; chaque lecture N3/N4 est auditée |
| **Planning** | Shifts traversant minuit ; changements d'heure (si applicables) ; chevauchement refusé ; congé approuvé → shift en conflit signalé ; calcul de couverture vérifié sur un jeu de données de référence calculé à la main ; publication bloquée sans pharmacien si la règle est bloquante ; glisser-déposer concurrent (verrou optimiste) |
| **Temps** | Retard, oubli de pointage, correction par nouvel enregistrement (l'original reste) ; heures sup par catégorie sur un jeu de référence |
| **Congés** | Durée hors jours fériés et demi-journées ; solde = Σ ledger ; annulation après approbation → contre-écriture ; solde négatif refusé ; circuit N1 → N2 ; un manager ne valide pas sa propre demande |
| **Caisse** | Formule du théorique par mode sur des scénarios de référence (vente mixte, remboursement, dépense, transfert) ; deux sessions simultanées pour un même collaborateur refusées ; même tiroir refusé ; comptage à l'aveugle (le théorique n'est pas renvoyé avant la saisie) ; auto-validation interdite ; aucune route de modification de mouvement ; double clic sur la clôture (idempotence) |
| **KPI** | Chaque calculateur testé sur un jeu de référence ; exclusion des KPI non significatifs ; renormalisation des poids ; neutralité des absences légitimes ; cohérence entre la somme individuelle et le KPI collectif ; profil versionné non rétroactif |
| **Évaluations** | Finalisée = immuable ; la réponse du collaborateur est conservée ; la contestation suspend l'élément |
| **Laboratoire** | Le préparateur ne peut pas être le contrôleur ; habilitation requise ; consommation de stock par lot |
| **IA** | Aucune écriture hors brouillon ; aucune donnée N3/N4 dans les prompts (test sur les charges utiles envoyées) ; audience respectée ; le solveur ne viole aucune contrainte bloquante |
| **Interface** | Planning en FR et AR (frise inversée), tablette (appui long), mobile (« Mon planning », demande de congé, pointage QR) |

### 12.3 Definition of Done spécifique

En plus de la DoD générale : jeu de données de référence RH (pharmacie fictive **réservée aux tests**,
jamais en production) avec résultats attendus calculés à la main ; revue de confidentialité des
écrans et des API ; vérification que chaque opération métier du lot porte un `employee_id`.

---

## 13. Risques et décisions

### 13.1 Risques

| Risque | Impact | Mitigation |
|---|---|---|
| Règles du droit du travail mal paramétrées (durées, majorations, congés) | Élevé (légal) | Paramétrage complet, valeurs **[À VALIDER]**, validation par un juriste ou un expert-comptable avant le Lot 10b |
| Perception de surveillance par les équipes | Élevé (adoption) | Axes séparés, transparence des KPI, droit de réponse, pas de sanction automatique, KPI personnels visibles |
| KPI biaisés (créneaux, mix d'ordonnances) | Moyen | Normalisation par la charge et le créneau, seuils de significativité, comparaison au même poste |
| Caisse partagée sans plateaux amovibles | Moyen | Mode séquentiel avec comptage intermédiaire |
| Complexité du glisser-déposer (tactile, RTL, concurrence) | Moyen | Pointer Events, verrou optimiste, alternative par formulaire |
| Fuite de données personnelles ou salariales | Critique | Tables séparées, chiffrement, audit des lectures, exclusion de l'IA |
| Données saisies hors système (pointage papier) | Moyen | Saisie manuelle tracée et validée par le manager |

### 13.2 Décisions à prendre (ajoutées à la liste D1-D16 du dossier principal)

| # | Décision | Options | Recommandation |
|---|---|---|---|
| D17 | Paramètres du droit du travail (durée hebdomadaire, majorations, congés annuels, repos, jours fériés) | Valeurs du Code du travail / convention collective | Validation par un expert avant le Lot 10b |
| D18 | Mode de pointage | PIN au poste / QR mobile / connexion / pointeuse externe | PIN au poste + QR mobile ; pas de biométrie ni de géolocalisation |
| D19 | Tiroirs de caisse | Plateaux amovibles individuels / mode séquentiel | Plateaux individuels quand c'est possible |
| D20 | Imputation des écarts de caisse | Information seule / procédure interne | Information et décision humaine uniquement |
| D21 | Score global de performance | Désactivé / activé | Désactivé par défaut, axes séparés |
| D22 | Intégration paie | Export CSV / module paie ultérieur / connecteur logiciel de paie | Export CSV en v1 |
| D23 | Mesure de la qualité du service | Réclamations et compliments seuls / enquête client | Réclamations et compliments en v1 |
| D24 | Présence obligatoire d'un pharmacien | Règle bloquante / alerte | Règle bloquante, à confirmer réglementairement |
| D25 | Périmètre du laboratoire | Préparations magistrales et officinales / hors v1 | Module minimal M16 en v1 |
| D26 | Durées de conservation des données RH | Par catégorie | À fixer avec un juriste |
