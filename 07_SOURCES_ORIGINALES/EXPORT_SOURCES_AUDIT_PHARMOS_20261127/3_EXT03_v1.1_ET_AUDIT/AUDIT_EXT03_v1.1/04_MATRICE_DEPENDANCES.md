# PHARM'OS — EXT03 v1.1 — Matrice des dépendances de M17 avec les autres modules

Sens : **→** M17 consomme le module · **←** le module consomme M17 · **↔** bidirectionnel.
Couplage : **Tx** = participe à la transaction atomique de validation · **Sync** = appel de service synchrone hors
transaction · **Evt** = événement de domaine asynchrone (outbox) · **Lect** = lecture seule.

## 1. Matrice principale

| Module | Sens | Données / services échangés | Couplage | Règle d'intégration critique | Lot | Risque si non respecté |
|---|---|---|---|---|---|---|
| **M00 Plateforme** | → | Séquence `ORD`, catalogue des permissions, `audit_log`, `sensitive_access_log`, `idempotency_records`, notifications, paramètres | Tx (audit, idempotence) | Aucune écriture sans `tenant_id` ni `employee_id` | 0, 1 | Fuite inter-tenant, trace incomplète |
| **M15 RH (EXT02)** | → | Collaborateur agissant, habilitations `RX_*`, `CONTROLLED_DISPENSE`, qualification de pharmacien, présence (`CLOCKED_IN`) | Sync (moteur de droits) | Droits évalués côté serveur à la validation, pas seulement à l'ouverture de l'écran | 1, 10b | Délivrance réglementée par un non-habilité |
| **M04 Catalogue** | → | Produit, DCI, dosage, forme, conditionnement, `prescription_policy`, groupe générique, classe réglementaire, `medical_specialties` | Lect | Version du produit en vigueur à la date de validation | 2 | Mauvais niveau de contrôle |
| **M07 Clients** | ↔ | Fiche client unique, volet `patient_profiles`, consentements, onglet Ordonnances | Sync / Lect | Patient ≠ payeur possible ; client de passage jamais nominatif ; N2/N3 cloisonnés | 5b, 6 | Doublons, fuite de données de santé |
| **M02 Vente** | ↔ | Mode Ordonnance, `sale_lines.prescription_line_id`, contrôles de validation, ticket (n° ORD sans donnée médicale) | **Tx** | Un seul service de validation pour tous les chemins ; délivrances écrites dans le batch de la vente | 5, 5b | Vente validée sans délivrance, ou inverse |
| **M02b Caisse nominative** | → | Session ouverte du collaborateur, paiements, mouvements de caisse | **Tx** | Aucun encaissement hors session ; dispensateur ≠ encaissant possible | 5 | Écart de caisse non attribuable |
| **M05 Stock** | → | FEFO, `stock_levels` (décrément conditionnel), `stock_movements` SALE / SALE_RETURN, `stock_reservations` (reliquats) | **Tx** | Aucun mouvement avant validation ; jamais de stock négatif ; réservation ≠ mouvement | 3, 5b | Stock faux, double sortie |
| **M09 Réglementés** | ↔ | `ControlledDispensingPolicy` (contrôles), `controlled_register` (prescripteur, patient, ordonnance, délivrance), rectifications | **Tx** | Registre dans le même batch ; contrôles non désactivables ; retour en quarantaine | 10 | Infraction réglementaire |
| **M16 Laboratoire** | ← | Préparation magistrale rattachée à une ligne d'ordonnance | Sync | La délivrance de la préparation passe par M02/M17 comme un produit | 9b | Préparation sans ordonnance tracée |
| **M11 Commandes** | ← | `prescription_demand` (reliquats `A_COMMANDER` certains, renouvellements pondérés) | Evt / Lect | Pas de double comptage avec la demande historique ; demande annulée à l'abandon du reliquat | 12 | Sur- ou sous-commande |
| **M08 Finance** | — (futur ↔) | Aucun en v1 ; futur tiers payant (part organisme, `party_ledger` type `ORGANISME`) | — | Points de compatibilité D37 uniquement | ext. future | Refonte si points réservés ignorés |
| **M12 Rapports / métriques** | ← | `rx_facts`, `rx_metrics_monthly`, `metrics_daily` (dimensions), `report_query_log`, moteur de masquage | Evt / Lect | Source unique = délivrances validées ; contrôles C1–C10 ; réconciliation CA | 11, 11c | Chiffres incohérents, réidentification |
| **M13 IA** | ← | Agrégats P1–P5 via outils en lecture seule, prescripteurs pseudonymisés | Lect | Liste blanche, aucun nominatif ni image ; seuil ≥ rapports | 13 | Fuite vers le LLM externe |
| **M14 Hors-ligne** | ↔ | Cache filtré (réglementés non vendables), file de synchronisation, revalidation serveur | Sync (au retour) | D39 révisée ; aucune écriture registre hors ligne | 14 | Contournement des contrôles |
| **Service OCR** | → | Image → propositions (`ocr_extractions`) | Sync (hors transaction de vente) | Propositions jamais enregistrées sans confirmation ; panne non bloquante | 5c | Erreur de délivrance |
| **M10 Péremptions** | → (indirect) | Lots bloqués / périmés exclus du FEFO | Lect | Lot périmé jamais délivré, même sur ordonnance | 8 | Délivrance d'un périmé |
| **M01 Tableau de bord** | ← | Widgets ordonnances (CA ordonnance, reliquats ouverts) | Lect | Même couche métriques | 11 | KPI divergents |

## 2. Contenu de la transaction atomique (qui écrit quoi)

| Écriture | Module propriétaire | Appelée par |
|---|---|---|
| `sales` statut validé, `sale_lines` | M02 | Service de validation de vente |
| `stock_levels`, `stock_movements` | M05 (`StockService.allocateAndIssue`) | idem |
| `prescription_dispensations`, `prescription_substitutions`, `prescriptions.statut` | M17 | idem |
| `controlled_register` | M09 | idem |
| `payments`, `cash_movements`, `party_ledger` | M02b / M08 | idem |
| `audit_log`, `domain_events`, `idempotency_records` | M00 | idem |

Principe du dossier §3.2 respecté : aucun module n'écrit dans les tables d'un autre ; chaque service **fournit ses
instructions** au batch assemblé par le service de validation de vente.

## 3. Événements de domaine

| Événement | Émetteur | Consommateurs |
|---|---|---|
| `prescription.created` / `rectified` / `closed` / `expired` | M17 | M12, M13, audit |
| `prescription.dispensed` | M17 (dans le batch de vente) | M12 (`rx_facts`), M11 (demande), M07 (traitements si consentement), projection des soldes |
| `prescription.adjusted` *(v1.1)* | M17 | Projection des soldes, M11 |
| `prescriber.created` / `updated` / `merged` | M17 | M12 (fiche résolue), M13 |
| `patient.sensitive_accessed` | M17 / M07 | Revue des accès, alertes de volume |
| `rx_export.approved` | M12 | Notification, audit |
| `ocr.completed` *(v1.1)* | Service OCR | Écran E1 (affichage des propositions) |
| `sale.voided`, `sale.returned` | M02 | M17 (contre-écritures déjà dans le batch ; projections) |
| `habilitation.revoked`, `qualification.expired` | M15 | Invalidation des droits `rx.*` |

## 4. Ordre de développement imposé par les dépendances

```
Lot 0 (batch conditionnel, immuabilité) → 1 (droits) → 2 (catalogue, spécialités) → 3 (stock) → 5 (vente avec lien
ligne d'ordonnance) → 5b (cœur M17) → 5c (OCR) ; 6 (clients) ; 10 (réglementés, après validation juridique) ;
11 → 11c ; 12 ; 13 ; 14
```
