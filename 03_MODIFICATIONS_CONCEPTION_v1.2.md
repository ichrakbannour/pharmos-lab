# PHARM'OS — Modifications à reporter dans CONCEPTION_PHARMOS (v1.1 → v1.2)

**Statut** : **liste proposée, non appliquée**. Le fichier `docs/CONCEPTION_PHARMOS_v1.md` n'a pas été modifié.
Le report sera fait après validation des décisions D27 à D43 et des valeurs par défaut des exigences R1 à R6, R14, R15.
Références de sections : dossier principal v1.1 (fichier `CONCEPTION_PHARMOS_v1.md`).

Légende — Type : **A** ajout · **R** remplacement · **M** modification · **S** suppression d'un élément du dossier.
Condition : décision(s) dont dépend la modification.

---

## 1. En-tête, sommaire, statut

| # | Section | Type | Modification | Condition |
|---|---|---|---|---|
| 1.1 | En-tête | M | Version v1.2 ; « intégration de l'extension n° 03 Ordonnances & prescripteurs (v1.1) » ; EXT03 marquée **intégrée** | Validation globale |
| 1.2 | Sommaire / annexes | M | Annexe EXT03 : « proposition » → « partie intégrante de la conception » | idem |
| 1.3 | README | M | Lien vers EXT03 v1.1, statut intégré ; « Validation des décisions (D1–D43) » | idem |

## 2. Analyse fonctionnelle (§2)

| # | Section | Type | Modification | Condition |
|---|---|---|---|---|
| 2.1 | §2.1 Acteurs | A | « Service OCR » : acteur système, propositions uniquement, aucune décision | D41 |
| 2.2 | §2.2 Liste des modules | A | **M17 — Ordonnances & prescripteurs** (extension n° 03) | — |
| 2.3 | §2.2 Tableau M02 | M | Règle « produit réglementé → saisie ordonnance obligatoire » remplacée par « contrôles M17/M09 obligatoires sur tous les chemins de vente (EXT03 §16.2) ; mode Ordonnance ; lien ligne à ligne » | D29 |
| 2.4 | §2.2 Tableau M07 | M | Fiche unique + volet patient cloisonné N1/N2/N3 | D27 |
| 2.5 | §2.2 Tableau M09 | M | Registre écrit dans la transaction de validation ; service unique `ControlledDispensingPolicy` | — |
| 2.6 | §2.2 Tableau M11 | M | Demande engagée (reliquats, renouvellements) | — |
| 2.7 | §2.2 Tableau | A | Ligne M17 : exigences clés et règles critiques (prescrit ≠ délivré, soldes calculés, aucune obligation légale contournable, OCR sous confirmation) | D29, D30 |
| 2.8 | §2.3 Exigences non fonctionnelles | A | « Traçabilité : valeur avant/après, auteur, horodatage serveur, motif, validation » ; « Confidentialité statistique : k = 5 et contrôles anti-croisement » ; « Ordonnance de 5 lignes < 60 s » | D34 |

## 3. Architecture et cartographie (§3)

| # | Section | Type | Modification | Condition |
|---|---|---|---|---|
| 3.1 | §3.1 Vue en couches | A | Service OCR dans « INTÉGRATIONS » (distinct du LLM) | D41 |
| 3.2 | §3.2 Cartographie | A | M17 entre M02 et M05/M09 ; flèches M17→M09, M17→M11, M17→M12 ; M07 partagé | — |
| 3.3 | §3.3 Arborescence | A | `/prescriptions`, `/prescriptions/:id`, `/prescribers`, `/prescribers/:id`, `/prescribers/duplicates`, `/settings/specialties`, `/reports/prescriptions/*`, `/reports/exports`, `/analytics/prescriptions`, `/audit/sensitive-access` ; onglet Ordonnances de `/customers/:id` | — |

## 4. Modèle de données (§4)

| # | Section | Type | Modification | Condition |
|---|---|---|---|---|
| 4.1 | §4.1 Conventions | A | Tables immuables protégées aussi par déclencheurs `UPDATE/DELETE` ; soldes = projections recalculables, jamais saisies | — |
| 4.2 | §4.2 `audit_log` | M | + `categorie`, `approved_by_employee_id`, `champs_modifies`, `correlation_id` ; champs sensibles chiffrés dans `avant/apres` | — |
| 4.3 | §4.2 | A | `sensitive_access_log`, `report_query_log`, `export_requests`, `retention_policies` (catégories + OCR, REPORT_QUERY_LOG), `compliance_profiles`, `idempotency_records` | D34, D36 |
| 4.4 | §4.2 `document_sequences` | M | type `ORD` | — |
| 4.5 | §4.3 `products` | R | `ordonnance_requise` → `prescription_policy` (`LIBRE`, `ORDONNANCE_OBLIGATOIRE`, `ORDONNANCE_RENOUVELABLE`, `REGLEMENTE`) + `substitution_group_id` | — |
| 4.6 | §4.3 `ref_products` | M | + `generic_group_code`, `is_generic`, `prescription_status_ref` | R4 |
| 4.7 | §4.3 | A | `medical_specialties` (référentiel APEX + local) | D32 |
| 4.8 | §4.4 `stock_movements` | M | + `validation_batch_id` | — |
| 4.9 | §4.4 `stock_reservations` | M | origine `RELIQUAT_ORDONNANCE`, prolongation tracée | D40 |
| 4.10 | §4.5 `sales` | M | **S** `prescription_id` ; + `mode` (`COMPTOIR`, `ORDONNANCE`, `MIXTE`), `validation_batch_id`, `idempotency_key` | — |
| 4.11 | §4.5 `sale_lines` | M | + `prescription_line_id`, `is_substitution`, `dispensation_type` | D30 |
| 4.12 | §4.5 `sale_return_lines` | M | + `prescription_line_id`, + `restitue_droit` | D42 |
| 4.13 | §4.5 `prescriptions` | **R** | Remplacée par le modèle EXT03 §3.2 (numéro interne, snapshot prescripteur/spécialité, statuts dont `A_COMPLETER`, `ABANDONNE`) | D31 |
| 4.14 | §4.5 (nouvelle sous-section « Ordonnances M17 ») | A | `prescribers`, `prescriber_specialties`, `prescriber_history`, `prescriber_duplicate_candidates`, `prescriber_merges`, `prescription_lines`, `prescription_dispensations`, `prescription_substitutions`, `prescription_documents`, `prescription_versions`, **`prescription_adjustments`**, **`prescription_line_balances`**, **`prescription_completeness_items`**, `prescription_demand`, `prescription_rules`, **`ocr_jobs`**, **`ocr_extractions`** | D29, D41, D42 |
| 4.15 | §4.7 `customers` | M | + `has_patient_profile` ; `cin_optionnel` déplacé vers `patient_profiles` (chiffré) | D27 |
| 4.16 | §4.7 | A | `patient_profiles` (cloisonnée), `patient_duplicate_candidates` | D27 |
| 4.17 | §4.7 `customer_treatments` | M | + `source_prescription_line_id`, `prescriber_id` | — |
| 4.18 | §4.9 `controlled_register` | M | `prescripteur` texte → `prescriber_id` + snapshot ; `patient_ref` → `patient_customer_id` ; + `prescription_line_id`, `dispensation_id`, `validation_batch_id` | D28 |
| 4.19 | §4.11 | A | `rx_facts`, `rx_metrics_monthly` ; `metrics_daily` dimensions `prescriber`, `specialty`, `sale_mode` ; `product_demand_stats` + `rx_share`, `pending_rx_demand` ; `ai_recommendations` catégories `RX_*` | — |
| 4.20 | §4.6 `po_suggestions` | M | Justification inclut la demande engagée | — |
| 4.21 | §4.12 ERD | M | Remplacer `sales n─0..1 prescriptions` par le lien ligne à ligne ; ajouter les relations EXT03 §3.3 | — |
| 4.22 | EXT02 (référence) | M | `lab_preparations` + `prescription_line_id` ; permissions `rx.*`, `prescriber.*`, `patient.*` ; habilitations `RX_VALIDATE`, `RX_SUBSTITUTE`, `RX_OVERRIDE` ; KPI conformité | — |

## 5. Flux (§5)

| # | Section | Type | Modification | Condition |
|---|---|---|---|---|
| 5.1 | §5.1 Vente au comptoir, étape 5 | M | Batch détaillé (EXT03 §19.1) : + `prescription_dispensations`, garde conditionnelle de solde, `validation_batch_id`, idempotence ; registre réglementé dans le même batch | — |
| 5.2 | §5 | A | Nouveau §5.x « Flux Vente sur ordonnance » (W1, W2) | — |
| 5.3 | §5.2 Retour / annulation | M | Contre-écritures de délivrances ; neutralisation par défaut (D42) ; réglementés en quarantaine | D42 |
| 5.4 | §5 | A | §5.x « Flux numérisation OCR » (capture → propositions → confirmation → contrôles) | D41 |
| 5.5 | §5.7 Matrice d'impact | A | Lignes « Délivrance sur ordonnance », « Retour sur ordonnance », « Rectification d'ordonnance », « Régularisation (adjustment) » | — |

## 6. Architecture technique (§6)

| # | Section | Type | Modification | Condition |
|---|---|---|---|---|
| 6.1 | §6.1 Choix technologiques | A | Ligne « OCR » : service hébergé APEX, repli sur l'appareil, LLM exclu | D41 |
| 6.2 | §6.2 Organisation du code | A | `modules/prescriptions/`, `modules/prescribers/`, `modules/ocr/` | — |
| 6.3 | §6.3 API | A | Exemples `POST /api/v1/prescriptions`, `/prescriptions/:id/rectify`, `/prescriptions/:id/adjustments`, `/prescriptions/:id/ocr`, `GET /sales/:id/status?key=` ; **aucune** route d'écriture sur les soldes | — |
| 6.4 | §6.3 Concurrence | A | Garde conditionnelle de solde dans le batch ; mécanisme d'échec de batch à prototyper au lot 0 | — |
| 6.5 | §6.7 Hors ligne | M | Restrictions D39 révisées ; produits `REGLEMENTE` non vendables dans le cache | D39 |

## 7. Moteur IA (§7)

| # | Section | Type | Modification | Condition |
|---|---|---|---|---|
| 7.1 | §7.3 Analyseurs | A | P1 à P5 (EXT03 §9.2) | — |
| 7.2 | §7.5 Règles de sécurité | A | Interdits EXT03 §9.3 ; filtre des questions libres ; aucune image/OCR ; contrôles C1–C8 appliqués aux outils ; pseudonymisation des prescripteurs | D34, D35 |

## 8. Sécurité et conformité (§9)

| # | Section | Type | Modification | Condition |
|---|---|---|---|---|
| 8.1 | §9.1 Rôles | M | Ligne « Données patients » détaillée selon EXT03 §5.2 ; lignes ordonnances (dispenser, valider, rectifier, régulariser, OCR, rapports, exports) | — |
| 8.2 | §9.3 Audit | M | Contenu obligatoire d'une trace (EXT03 §17.1) ; liste des objets sans suppression physique (§17.3) | — |
| 8.3 | §9.4 Données personnelles | A | Cloisonnement N1/N2/N3, réidentification (EXT03 §18), journal des accès sensibles | D27, D34, D36 |
| 8.4 | §9.5 Conformité | A | Renvoi vers R1–R22 (EXT03 + `06_POINTS_REGLEMENTAIRES.md`) | — |

## 9. Plan de développement (§10)

| # | Section | Type | Modification | Condition |
|---|---|---|---|---|
| 9.1 | Lot 0 | A | Prototype et test du mécanisme d'échec de batch conditionnel ; déclencheurs d'immuabilité | — |
| 9.2 | Lot 2 | A | `prescription_policy`, groupes génériques, `medical_specialties` | D32 |
| 9.3 | Lot 5 | M | Vente conçue avec `prescription_line_id`, `validation_batch_id`, idempotence | — |
| 9.4 | **Lot 5b** (nouveau) | A | Ordonnances — cœur (EXT03 §12.1 + soldes §15 + complétude §4.8) ; prérequis R1–R6, R14, R15 ; D27–D33, D42, D43 | — |
| 9.5 | **Lot 5c** (nouveau) | A | Numérisation OCR ; prérequis R16, D41 | D41 |
| 9.6 | Lots 6, 10, 11, 12, 13, 14, 15 | M | Ajouts EXT03 §12.1 ; lot 11 : moteur de masquage et `report_query_log` | — |
| 9.7 | **Lot 11c** (nouveau) | A | Tableau de bord prescriptions | — |
| 9.8 | Lot 10 | M | Critère de recette : réconciliations registre ↔ stock ↔ délivrances à zéro écart | — |

## 10. Risques et décisions (§11)

| # | Section | Type | Modification | Condition |
|---|---|---|---|---|
| 10.1 | §11.1 Risques | A | Réidentification statistique ; erreur OCR acceptée sans lecture ; divergence des contrôles réglementés entre chemins de vente ; échec partiel de batch | — |
| 10.2 | §11.2 Décisions | M | Ligne D27–D40 → **D27–D43** avec renvoi à la grille ; D6 (CNAM) renvoie à D37 | — |
| 10.3 | §11.4 Extensions en attente | S | Retirer EXT03 de la liste (intégrée) | Validation |
| 10.4 | §11.3 Journal des décisions | A | « 2026-11-xx — v1.2 : intégration EXT03 v1.1 (M17), décisions D27–D43 » | Validation |

---

**Volume estimé** : ~60 modifications ; aucune migration de données (aucun module développé).
