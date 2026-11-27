# PHARM'OS — Matrice des décisions D1 à D86 (traçabilité)

**Version** : 1.2 (consolidation) — 2026-11-03
**Statut** : document de travail soumis à validation. Il ne crée **aucune décision nouvelle** et ne réattribue aucun numéro.
**Document maître associé** : `CONCEPTION_PHARMOS_v1.2.md` (§18).

## Légendes

**Statut de la décision**

| Code | Signification |
|---|---|
| **ARBITRÉE** | Tranchée explicitement par le propriétaire (APEX AI DEV) |
| **ARBITRÉE (principe)** | Principe validé par le propriétaire ; modalités encore à valider |
| **PROPOSÉE** | Recommandation documentée, **non arbitrée** — ne doit pas être traitée comme validée |
| **À RÉEXAMINER** | Recommandation documentée, remise en cause par une exigence ou un arbitrage plus récent |
| **CADUQUE** | Remplacée par une décision arbitrée plus récente (l'historique est conservé) |

**Fiabilité de la source**

| Code | Signification |
|---|---|
| **CD** | Confirmé documentairement : formulation lue dans un document source accessible |
| **AP** | Arbitré par le propriétaire : formulation donnée par le propriétaire (revue EXT06 ou prompt de consolidation v1.2) |
| **RC** | Reconstitué à partir des éléments disponibles |
| **AV** | À vérifier : interprétation ou portée incertaine |
| **ND** | Non documenté |

**Sources** : `CONCEPTION_PHARMOS_v1.md` (v1.1, §11.2) · `EXT02_RH_TEAM_MANAGEMENT.md` (§13.2) ·
`archives/AUDIT_EXT03_v1.1/02_GRILLE_DECISIONS_D27_D40.md` et `EXT03_ORDONNANCES_PRESCRIPTEURS_v1.1.md` (EXT03 v1.1) ·
`EXT04_CNAM_CONVENTIONS_ASSURANCES.md` (§11) · `EXT05_ANNULATIONS_RETOURS_DELEGATIONS.md` (§11) ·
`EXT06_CATALOGUE_GOUVERNANCE_FINANCE_v1.1.md` (§11) · prompt de consolidation v1.2 (arbitrages D72 à D86).

**Constat d'inventaire** : les six documents sources sont **accessibles et ont été lus** (y compris EXT02, EXT03 v1.1,
EXT04 et EXT05, contrairement à ce qu'indiquait le prompt de mission). Aucune décision n'a donc dû être reconstituée :
la colonne « Fiabilité » ne contient aucun code RC ni ND.

## Synthèse

| Plage | Domaine | Nb | ARBITRÉE | PROPOSÉE | À RÉEXAMINER | CADUQUE |
|---|---|---|---|---|---|---|
| D1–D16 | Plateforme, hébergement, produit | 16 | 0 | 11 | 5 (D2, D3, D6, D10, D15) | 0 |
| D17–D26 | RH (EXT02) | 10 | 0 | 10 | 0 | 0 |
| D27–D43 | Ordonnances (EXT03 v1.1) | 17 | 0 | 16 | 1 (D37) | 0 |
| D44–D58 | Tiers payant (EXT04) | 15 | 0 | 15 | 0 | 0 |
| D59–D71 | Corrections, délégations (EXT05) | 13 | 0 | 11 | 0 | 2 (D61, D66) |
| D72–D86 | Gouvernance, catalogue, finance (EXT06) | 15 | 15 | 0 | 0 | 0 |
| **Total** | | **86** | **15** | **63** | **6** | **2** |

**Lecture** : seules D72 à D86 sont arbitrées. **71 décisions** restent proposées, à réexaminer ou caduques. La v1.2 ne
peut donc pas être déclarée entièrement validée (voir `RAPPORT_AUDIT_COHERENCE_v1.2.md` §1).

---

## 1. D1 à D16 — Plateforme et produit (dossier principal §11.2)

| # | Domaine | Règle / question | Décision retenue ou recommandation | Statut | Fiab. | Source | Modules | Dépendances techniques | Tests d'acceptation |
|---|---|---|---|---|---|---|---|---|---|
| D1 | Marché | Marché et devise de lancement | Tunisie ; devise paramétrable (TND à 3 décimales, montants en millimes entiers) | PROPOSÉE | CD | Dossier §11.2 | M00, tous | Type monétaire entier, arrondis centralisés | Tests du socle (Lot 0) : arrondis, aucune valeur flottante |
| D2 | Hébergement | Mode d'hébergement | Dossier : compte Cloudflare propre d'APEX. **v1.2** : base du **mode A (SaaS)** uniquement ; le **mode B (local)** est désormais obligatoire et ne peut pas reposer sur Cloudflare | À RÉEXAMINER | CD | Dossier §11.2, §6.1, §6.5 ; exigence v1.2 n° 4 | Socle | Architecture portable (ports et adaptateurs) | DEP-01 à DEP-12 |
| D3 | Isolation | Base partagée + `tenant_id` ou base par pharmacie | Dossier : base partagée au départ. **v1.2 recommande une base par pharmacie dans les deux modes** (cohérent avec D72 « base de données propre » et avec le mode B) — à arbitrer | À RÉEXAMINER | CD / AV | Dossier §6.4 ; D72 | M00, tous | Routage de connexion par officine, migrations sur N bases, sauvegarde par officine | MOF-01 à MOF-07, DEP-03, BKP-01 |
| D4 | Référentiel | Source du référentiel médicaments | À identifier (source officielle des AMM et des prix) | PROPOSÉE (ouverte) | CD | Dossier §11.2 | M04 | Import contrôlé avec aperçu | Lot 2 (import sans doublon) ; R43, R51 |
| D5 | Réglementaire | Registres électroniques, facturation électronique | Consultation juridique avant le Lot 10 | PROPOSÉE (ouverte) | CD | Dossier §11.2 | M09, M12 | — | R18, R38, R44 |
| D6 | Tiers payant | CNAM dans la v1 ou plus tard | Dossier : plus tard. **Concurrencée** par D44 (EXT04 : module livré, conventions inactives par défaut) | À RÉEXAMINER (INC-06) | CD | Dossier §11.2 ; EXT04 D44 | M18 | — | TP-20, TP-21, TP-22 |
| D7 | IA | Fournisseur LLM, hébergement des données IA | Fournisseur configurable, données pseudonymisées ; **v1.2** : service distant **optionnel**, désactivable en mode B | PROPOSÉE | CD | Dossier §11.2, §7 | M13 | Connecteur sortant optionnel, file d'attente | S-IA-01 à S-IA-05 (EXT03 v1.1), TP-40, LOC-06 |
| D8 | Messagerie | WhatsApp Cloud API ou liens `wa.me` | `wa.me` en v1, Cloud API ensuite ; optionnel en mode B | PROPOSÉE | CD | Dossier §11.2 | M11, notifications | Connecteur optionnel | LOC-06 |
| D9 | Matériel | Navigateur seul ou pont local (tiroir, ESC/POS) | Navigateur en v1 ; en mode B le serveur local peut porter le pont | PROPOSÉE | CD | Dossier §11.2 | M02, M02b | Pont d'impression | RSP-03 (impression) |
| D10 | Hors-ligne | Dès le lancement ou au Lot 14 | Prévu dès le socle, livré au Lot 14. **v1.2** : distinguer le **poste déconnecté** (D10) de l'**installation déconnectée d'APEX** (mode B) | À RÉEXAMINER (portée) | CD | Dossier §11.2, §6.7 | M14 | ULID, idempotence | LOC-01 à LOC-08 |
| D11 | Avatar | Style, rendu | Pré-calculé, désactivable | PROPOSÉE | CD | Dossier §11.2, §8 | M13 | — | — |
| D12 | Frontend | Preact ou React | Preact | PROPOSÉE | CD | Dossier §11.2 | Interface | — | — |
| D13 | Commercial | Modèle d'abonnement | À définir ; **v1.2** : impacte aussi les licences du mode B | PROPOSÉE (ouverte) | CD | Dossier §11.2 | M00 | `subscriptions` complétée (licence signée) | LIC-01 à LIC-06 |
| D14 | Reprise | Import depuis les logiciels existants | Importeurs Excel génériques au Lot 2 | PROPOSÉE | CD | Dossier §11.2 | M04, M06, M07 | — | Lot 2 |
| D15 | Multi-officines | v1 ou plus tard | Dossier : modèle prêt (`sites`). **EXT06 v1.1 MO1** : une officine = un tenant ; `sites` = dépôts et annexes d'une même officine | À RÉEXAMINER (précision) | CD | Dossier §11.2 ; EXT06 v1.1 §1.4 | M00 | Isolation par officine | MOF-01 à MOF-07 |
| D16 | Affichage | Chiffres en arabe | Chiffres latins par défaut, paramétrable | PROPOSÉE | CD | Dossier §11.2 | Interface | `Intl` | RSP-05 |

## 2. D17 à D26 — Ressources humaines (EXT02 §13.2)

EXT02 est intégrée au dossier v1.1, mais ses décisions restent en attente selon le journal du projet (§4).

| # | Domaine | Règle / question | Recommandation | Statut | Fiab. | Source | Modules | Dépendances techniques | Tests d'acceptation |
|---|---|---|---|---|---|---|---|---|---|
| D17 | Droit du travail | Paramètres (durées, majorations, congés, repos, jours fériés) | Valeurs à valider par un expert avant le Lot 10b ; aucune valeur présumée | PROPOSÉE | CD | EXT02 §13.2 | M15 | `labor_rules` | EXT02 §12.2 « Temps », « Congés » |
| D18 | Pointage | Mode de pointage | PIN au poste + QR mobile ; ni biométrie ni géolocalisation | PROPOSÉE | CD | EXT02 | M15 | Appairage QR | EXT02 §12.2 « Interface » ; RSP-07 |
| D19 | Caisse | Tiroirs | Plateaux amovibles individuels quand c'est possible | PROPOSÉE | CD | EXT02 | M02b | `cash_registers.mode_tiroir` | EXT02 §12.2 « Caisse » |
| D20 | Caisse | Imputation des écarts | Information et décision humaine uniquement | PROPOSÉE | CD | EXT02 | M02b, M15 | `quality_events` | EXT02 §12.2 |
| D21 | Performance | Score global | Désactivé par défaut, axes séparés | PROPOSÉE | CD | EXT02 | M15 | `kpi_profiles` | EXT02 §12.2 « KPI » |
| D22 | Paie | Intégration paie | Export CSV des variables en v1 ; **complété** par D81 (import des montants) | PROPOSÉE | CD | EXT02 ; D81 | M15, M08 | Export / import de fichiers | FIN-08 |
| D23 | Qualité | Mesure de la qualité du service | Réclamations et compliments en v1 | PROPOSÉE | CD | EXT02 | M15 | — | — |
| D24 | Présence | Présence obligatoire d'un pharmacien | Règle bloquante, à confirmer (R19) ; la validation à distance (D73) **ne remplace pas** cette présence | PROPOSÉE | CD | EXT02 ; EXT03 v1.1 R19 | M15, M02, M09 | Moteur de droits effectifs | SEC-RBAC-02 (EXT03 v1.1), GOV-25 |
| D25 | Laboratoire | Périmètre | Module minimal M16 en v1 | PROPOSÉE | CD | EXT02 | M16 | — | EXT02 §12.2 « Laboratoire » |
| D26 | Conservation | Durées de conservation RH | À fixer avec un juriste | PROPOSÉE (ouverte) | CD | EXT02 | M15 | `retention_policies` | — ; R50 |

## 3. D27 à D43 — Ordonnances et prescripteurs (EXT03 v1.1)

Source : grille `archives/AUDIT_EXT03_v1.1/02_GRILLE_DECISIONS_D27_D40.md` (colonne « Décision retenue » **non cochée**
pour les 17 décisions). Les recommandations ci-dessous sont celles de la v1.1, qui remplacent celles de la v1.0.

| # | Domaine | Règle / question | Recommandation (v1.1) | Statut | Fiab. | Modules | Dépendances techniques | Tests d'acceptation (EXT03 v1.1 sauf mention) |
|---|---|---|---|---|---|---|---|---|
| D27 | Patient | Modèle du patient | Fiche client unique + volet `patient_profiles` cloisonné N1 / N2 / N3, accès journalisé | PROPOSÉE | CD | M07, M17 | Cloisonnement, chiffrement des champs N2 / N3 | F-PAT-01 à 05, SEC-RBAC-03, SEC-ENC-01 |
| D28 | Patient | Ordonnance sans patient identifié | Seulement si le profil pays le permet ; jamais pour une ordonnance réglementée ou renouvelable | PROPOSÉE | CD | M17, M09 | `compliance_profiles` | F-PAT-01 |
| D29 | Ordonnance | Ordonnance incomplète | Éléments classés A / M / R ; seuls les éléments A sont différables | PROPOSÉE | CD | M17 | `prescription_completeness_items` | F-CMP-01 à 04 |
| D30 | Ordonnance | Niveau de saisie | Prescrit + délivré, avec saisie accélérée | PROPOSÉE | CD | M17, M02 | — | S01, F-UX-01 |
| D31 | Statistiques | Spécialité dans les statistiques | Instantané à la date de prescription | PROPOSÉE | CD | M17, M12 | `prescriber_specialties` historisée | S11 |
| D32 | Référentiel | Référentiel des spécialités | Liste APEX + ajouts locaux rattachés à un groupe APEX | PROPOSÉE | CD | M17 | Référentiel partagé (voir INC-27) | S10, S11 |
| D33 | Documents | Scan des ordonnances | Obligatoire seulement si le profil pays l'exige ; le scan ne remplace pas l'original (R6) | PROPOSÉE | CD | M17 | Stockage de fichiers (port portable) | SEC-DOC-01 |
| D34 | Confidentialité | Seuil de petits effectifs | k = 5 + contrôles anti-croisement (la v1.0 proposait k = 3 : remplacé, INC-28) | PROPOSÉE | CD | M12, M13 | Moteur de masquage commun | C-K-01, C-K-02, C-X-01, C-X-02, C-DIF-01, C-Q-01, C-EXP-01 |
| D35 | IA | Prescripteur transmis au LLM | Pseudonymisé | PROPOSÉE | CD | M13 | Filtre de sortie | S-IA-05 |
| D36 | Audit | Journal des accès sensibles | Table dédiée, immuable, chaînée | PROPOSÉE | CD | M00, M17 | `sensitive_access_log` | S-AUD-01 à 06 |
| D37 | Tiers payant | CNAM, mutuelles | Reporté, compatibilité préservée. **En conflit avec D44** (EXT04 : module livré, conventions inactives) | À RÉEXAMINER (INC-06) | CD | M17, M18 | — | TP-20 à TP-22 (EXT04) |
| D38 | Ordonnance | Posologie | Facultative, chiffrée, hors IA et hors rapports | PROPOSÉE | CD | M17 | Chiffrement applicatif | S-IA-01 |
| D39 | Hors-ligne | Ventes sur ordonnance hors ligne | Première délivrance d'une ordonnance simple uniquement | PROPOSÉE | CD | M14, M17 | File locale, idempotence | F-HL-01 à 04 |
| D40 | Stock | Réservation pour reliquat | Courte (48 h par défaut), pharmacien pour les réglementés ; **réservation logique** (cohérent avec D76) | PROPOSÉE | CD | M05, M17 | `stock_reservations` | F-RES-01 |
| D41 | OCR | Moteur OCR | Service hébergé APEX, repli sur l'appareil ; LLM exclu. **v1.2** : service distant **optionnel** en mode B | PROPOSÉE | CD | M17 | Connecteur optionnel | O-01 à O-07, LOC-06 |
| D42 | Retours | Effet d'un retour sur le droit à délivrance | Neutralisation par défaut ; l'annulation dans la session restitue le droit | PROPOSÉE | CD | M17, M19 | `prescription_adjustments` | S07, RE-07, RE-08 (EXT05) |
| D43 | Ordonnance | Délai de complétion `A_COMPLETER` | 7 jours par défaut, puis blocage des délivrances suivantes | PROPOSÉE | CD | M17 | Traitement différé | F-CMP-05 |

## 4. D44 à D58 — Tiers payant (EXT04 §11)

| # | Domaine | Règle / question | Recommandation | Statut | Fiab. | Modules | Dépendances techniques | Tests (EXT04) |
|---|---|---|---|---|---|---|---|---|
| D44 | Périmètre | Module à la mise en service (tiers payant CNAM suspendu, R23) | B : modèle complet livré, aucune convention active par défaut ; remboursement par l'assuré utilisable. **En conflit avec D6 et D37** | PROPOSÉE (INC-06) | CD | M18 | — | TP-20, TP-21, TP-22 |
| D45 | Paramétrage | Organismes prioritaires | Tous génériques ; priorités selon les pharmacies pilotes | PROPOSÉE | CD | M18 | — | TP-01 à TP-04 |
| D46 | Couverture | Niveau de vérification minimal | `VERIFIEE` ; `JUSTIFIEE` si la convention le prévoit | PROPOSÉE | CD | M18 | — | TP-06, TP-07 |
| D47 | Calcul | Saisie manuelle d'une part organisme | Interdite par défaut, activable par convention | PROPOSÉE | CD | M18, M02 | — | TP-15 |
| D48 | Référentiel | Source des listes et tarifs | Fichiers sourcés fournis par APEX, activés et validés par la pharmacie. **v1.2** : en mode B, distribution par paquets signés | PROPOSÉE | CD | M18 | Distribution des référentiels (INC-27) | TP-01, TP-05 |
| D49 | Données de santé | Stockage du diagnostic (APCI) | Ne pas stocker de diagnostic | PROPOSÉE | CD | M18 | — | TP-39 |
| D50 | Créances | Refacturation au patient d'une part rejetée | Désactivée par défaut (R28) | PROPOSÉE | CD | M18 | — | TP-29 |
| D51 | Télétransmission | v1 ou ultérieure | Ultérieure ; exports en v1 ; tiers payant interdit hors ligne | PROPOSÉE | CD | M18, M14 | — | TP-41, TP-42 |
| D52 | Créances | Seuil de double validation des pertes | Montant paramétrable | PROPOSÉE | CD | M18 | — | TP-28 |
| D53 | Comptabilité | Naissance de la créance | À la vente (droits constatés), à confirmer (R32) | PROPOSÉE | CD | M18, M20 | — | TP-34, TP-35 |
| D54 | Fiscalité | TVA et timbre sur factures aux organismes | Aucun choix avant R26 | PROPOSÉE (ouverte) | CD | M18 | — | — |
| D55 | Règlements | Lettrage | Proposé puis confirmé (cohérent avec D85) | PROPOSÉE | CD | M18, M08 | — | TP-30, TP-31 |
| D56 | Suspension | Mode de repli | Remboursement par l'assuré | PROPOSÉE | CD | M18 | — | TP-22 |
| D57 | Rôles | Rôle « Responsable tiers payant » | Nouveau rôle, cumulable | PROPOSÉE | CD | M18, M00 | — | TP-38 |
| D58 | IA | IA sur le tiers payant | Agrégats par organisme seulement | PROPOSÉE | CD | M13 | — | TP-40 |

## 5. D59 à D71 — Annulations, retours, délégations (EXT05 §11)

| # | Domaine | Règle / question | Recommandation | Statut | Fiab. | Modules | Dépendances techniques | Tests (EXT05 sauf mention) |
|---|---|---|---|---|---|---|---|---|
| D59 | Restitution | Moyens de restitution | Même moyen que le paiement ; espèces pour une vente par carte interdites sauf « titulaire » (lire : administrateur, INC-18) | PROPOSÉE | CD | M19, M02b | — | PA-01, PA-02, FI-01 |
| D60 | Restitution | Remboursement par carte | Confirmation manuelle en v1 | PROPOSÉE | CD | M19 | — | PA-02 |
| D61 | Annulation | Fenêtre d'auto-annulation (15 min) | **Remplacée** par la règle du jour calendaire (EXT06 AN2) et par D75 | **CADUQUE** (INC-07) | CD | M19 | — | AN-02 à réécrire → ANN-02 à ANN-05 (EXT06) |
| D62 | Retours | Retours sans ticket | Autorisés avec droit spécifique, interdits pour les réglementés | PROPOSÉE | CD | M19 | — | RE-06 |
| D63 | Annulation | Annulation sans retour physique | Exceptionnelle, administrateur, motif ; interdite pour les réglementés | PROPOSÉE | CD | M19 | — | AN-05 |
| D64 | Hors-ligne | Corrections hors ligne | Demande locale seulement | PROPOSÉE | CD | M19, M14 | — | HL-01 |
| D65 | Plafonds | Plafonds par défaut par rôle | À fixer avec les pharmacies pilotes | PROPOSÉE (ouverte) | CD | M19, M00 | — | DR-01 à DR-03 |
| D66 | Délégations | Qui gère les délégations | Titulaire seul + délégant désigné. **Remplacée** par l'administrateur unique (EXT06 GV2, D72) | **CADUQUE** (INC-08) | CD | M00 | — | DL-01 → GOV-04 (EXT06) |
| D67 | Délégations | Durée maximale | 30 jours, renouvelable | PROPOSÉE | CD | M00 | — | DL-01, DL-04 |
| D68 | Stock | Délai de décision `EN_CONTROLE` | 48 h, puis alerte | PROPOSÉE | CD | M05, M19 | Traitement différé | ST-03 |
| D69 | Avoirs | Durée de validité | Après R39 | PROPOSÉE (ouverte) | CD | M19 | — | FI-02 |
| D70 | Modèle | Remplacement de `sale_returns` | Remplacer par le modèle correctif unifié | PROPOSÉE | CD | M19, M02 | — | RE-01, RE-02 |
| D71 | Stock | Buckets dans `stock_levels` dès le lot 3 | Dès le lot 3 (`EN_CONTROLE`, `QUARANTAINE`, `PERTE` ; sans `RESERVE`, D76) | PROPOSÉE | CD | M05 | — | ST-01, ST-04 |

## 6. D72 à D86 — Gouvernance, catalogue, finance (EXT06 v1.1 + arbitrages du propriétaire)

Les arbitrages proviennent du **prompt de consolidation v1.2** (D72, D74, D76 à D86) et de la **revue d'EXT06 v1.0**
(D73, D75). Lorsqu'un arbitrage diffère de la proposition d'EXT06 v1.1, la colonne « Écart avec EXT06 v1.1 » le signale ;
l'écart est analysé dans `RAPPORT_AUDIT_COHERENCE_v1.2.md`.

| # | Domaine | Décision retenue | Statut | Fiab. | Écart avec EXT06 v1.1 | Modules | Dépendances techniques | Tests d'acceptation |
|---|---|---|---|---|---|---|---|---|
| **D72** | Comptes et gouvernance | Compte **Super Admin APEX** immuable (`apexproib@gmail.com`) ; il crée, bloque, supprime et recrée les comptes administrateurs des pharmacies ; identifiants d'administration propres à chaque pharmacie ; code initial généré automatiquement ; changement du mot de passe à la première connexion ; base de données propre à chaque pharmacie, conservée après régénération du mot de passe ; **suppression d'un accès ≠ suppression des données** ; aucune destruction automatique de l'historique ; procédure sécurisée de récupération du Super Admin sans le rendre modifiable ni supprimable | **ARBITRÉE** | AP | **Oui** : EXT06 proposait une procédure de secours sans compte APEX permanent (INC-13) ; mode B non traité (INC-14) | M00 (plateforme), toutes les officines | Base plateforme distincte des bases d'officine ; `users.platform_role` (aucune table nouvelle pour le compte) ; `platform_audit_log` ; `tenant_data_deletion_requests` ; MFA renforcée | ADM-01 à ADM-10 |
| **D73** | Dérogations | Validation à distance par l'administrateur seul : MFA au moment de la décision, confirmation explicite, motif, opération identifiée, usage unique, journalisation, aucune substitution ; exclue pour les réglementés | **ARBITRÉE** (revue EXT06) | AP | Non | M00, M19 | MFA step-up, appareil enrôlé ; **mode B : accès distant au serveur local** (INC-16) | GOV-16 à GOV-26 |
| **D74** | Réintégration corrective | Un second collaborateur peut effectuer la réintégration d'une opération corrective **uniquement** si l'administrateur lui a **explicitement** accordé cette autorisation | **ARBITRÉE** | AP | **Partiel** : EXT06 proposait une décision immédiate « par un second collaborateur habilité » ; EXT05 accorde `stock.return.decide` par défaut à plusieurs rôles (INC-10) | M19, M05, M00 | Permission dédiée, non attribuée par défaut à un rôle | ANN-18 à ANN-21, ST-02 |
| **D75** | Annulations | Jour calendaire par défaut ; option « jour d'exploitation » activée explicitement par l'administrateur, documentée, versionnée, jamais silencieuse | **ARBITRÉE** (revue EXT06) | AP | Non | M19 | `settings`, `duty_periods` | ANN-10 à ANN-17 |
| **D76** | Stock | Supprimer le compartiment physique `RESERVE` ; réservation **logique** distincte du stock physique disponible | **ARBITRÉE** | AP | Non (identique à la proposition EXT06) | M05 | `stock_reservations` ; `stock_buckets` sans `RESERVE` | CAT-10, ST-04 |
| **D77** | Interface | Accueil adapté au rôle **et aux permissions effectives** | **ARBITRÉE** | AP | Non (précision : permissions effectives, pas seulement le rôle) | Interface, M00 | Moteur de droits effectifs | NAV-01, NAV-05 à NAV-07 |
| **D78** | Services | Page de gestion des services ; l'administrateur définit ses services ; **aucune liste universelle** ; enregistrement simple : type d'opération, nom du client, date ; **pas** de dossier clinique ni de mesures structurées non demandées | **ARBITRÉE** | AP | **Oui** : EXT06 prévoyait des modèles livrés désactivés, une autorisation réglementaire bloquante, des mesures chiffrées et des observations (INC-11) | M04 (services), M02 | Modèle de données simplifié | SRV-08 à SRV-12 (SRV-01, SRV-02, SRV-07 à réviser) |
| **D79** | Confidentialité | Permission spécifique de consultation des marges, appliquée aussi à G25 | **ARBITRÉE** | AP | Non | M12, M01 | `analytics.margin.read` | RNT-08 |
| **D80** | Comptabilité | Partie double M20 validée ; mention affichée « **Comptabilité à titre indicatif** » ; écritures validées immuables ; corrections par contre-passation et nouvelle écriture | **ARBITRÉE** | AP | Non (libellé imposé) | M20 | Immutabilité (déclencheurs), batch atomique | ACC-01 à ACC-19, FIN-18 |
| **D81** | Paie | Import des montants mensuels uniquement ; pas de moteur de paie | **ARBITRÉE** | AP | Non | M08, M15 | Import de fichiers | FIN-08, FIN-17 |
| **D82** | Périodes | Réouverture sur décision de l'administrateur, motif obligatoire ; interdite après export final | **ARBITRÉE** | AP | Non | M20 | — | FIN-13, ACC-19 |
| **D83** | Plan comptable | Plan standard proposé comme modèle initial ; adaptable par l'administrateur ; **pas de validation préalable obligatoire par un expert-comptable** ; traçabilité des modifications et cohérence des règles de passation | **ARBITRÉE** | AP | **Oui** : EXT06 proposait un modèle inactif avant validation de l'expert-comptable et posait le principe « aucun plan de comptes fourni par défaut » (INC-12) | M20 | Historisation (audit), contrôle de cohérence comptes ↔ règles | FIN-21 à FIN-24 |
| **D84** | Classification | Classification thérapeutique standard initiale, modifiable par l'administrateur ; distincte des données réglementaires officielles | **ARBITRÉE** | AP | Non (précision de la proposition) | M04 | `classification_nodes.source` ; champs réglementaires séparés | CAT-14 à CAT-16 |
| **D85** | Banque | Rapprochement proposé automatiquement, confirmation humaine obligatoire ; écarts = exceptions tracées | **ARBITRÉE** | AP | Non | M08, M20 | `bank_reconciliations`, `accounting_exceptions` | FIN-07, EXC-08 |
| **D86** | Dépenses | Seuil de validation configurable par l'administrateur | **ARBITRÉE** | AP | Non | M08 | `settings` | FIN-25 |

---

## 7. Décisions caduques et historique

| Décision | Remplacée par | Raison | Conservation |
|---|---|---|---|
| D61 (15 minutes) | EXT06 AN2, D75 | Règle du jour calendaire arbitrée | Texte conservé dans EXT05 (non modifié) ; signalé caduc ici |
| D66 (titulaire + délégant désigné) | D72, EXT06 GV2 | Administrateur unique de l'officine | Idem |
| D34 version v1.0 (k = 3) | D34 version v1.1 (k = 5) | Révision EXT03 v1.1 | EXT03 v1.0 conservée |

## 8. Questions d'arbitrage nouvelles (non numérotées)

La consolidation fait apparaître des questions qui ne correspondent à aucune décision D1–D86. Conformément à la règle
« ne jamais transformer une hypothèse en décision », elles ne reçoivent **pas** de numéro D : elles sont suivies sous les
identifiants ARB-01 à ARB-14 dans `REGISTRE_POINTS_OUVERTS_PHARMOS.md` §3, et recevront un numéro D lors de leur
arbitrage.

## 9. Contrôles de cette matrice

| Contrôle | Résultat |
|---|---|
| Numéros présents | D1 à D86, 86 décisions, aucune lacune, aucun doublon |
| Décisions arbitrées | 15 (D72 à D86) ; aucune autre décision n'est présentée comme arbitrée |
| Décisions dont la source n'a pas pu être lue | 0 |
| Écarts entre arbitrage et proposition antérieure | 5 (D72, D74, D78, D83 ; D77 précisée) — tous renvoyés à un INC du rapport d'audit |
| Tests cités et définis | Séries existantes : voir les documents sources ; séries nouvelles (ADM, ANN-18 à 21, NAV-05 à 07, SRV-08 à 12, FIN-21 à 25, CAT-14 à 16, DEP, LOC, LIC, BKP, UPD, RSP) définies dans `CONCEPTION_PHARMOS_v1.2.md` §15 et `MATRICE_DEPLOIEMENT_SAAS_ONPREMISE.md` §8 |
