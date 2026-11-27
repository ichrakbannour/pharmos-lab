# PHARM'OS — EXT06 v1.1 : rapport de revue et de cohérence

**Objet** : traçabilité des corrections demandées par la revue architecturale et fonctionnelle d'EXT06 v1.0
(décision : *validation de principe sous réserve de corrections ciblées*).
**Date** : 2026-11-03
**Documents** :
- `EXT06_CATALOGUE_GOUVERNANCE_FINANCE.md` — v1.0, **conservée sans modification** (empreinte SHA-256
  `944d8e1312adca028a3d6a50c1a1a7736f4bd005d2cba3334ae223c0878ed36d`, vérifiée après les corrections) ;
- `EXT06_CATALOGUE_GOUVERNANCE_FINANCE_v1.1.md` — v1.1 (1490 lignes ; passages modifiés repérés **[v1.1]**) ;
- présent rapport.

**Restrictions respectées** : aucun code, aucune migration, aucun changement applicatif ; `CONCEPTION_PHARMOS_v1.md`,
EXT02, EXT03, EXT04 et EXT05 non modifiés (aucune différence git) ; aucune règle fiscale ou réglementaire inventée
(les points correspondants sont rattachés à R42–R51) ; structure en 12 sections conservée ; aucune fonctionnalité validée
supprimée.

## Sommaire

1. Synthèse et avis d'intégration
2. Rapport des modifications par section
3. Matrice des décisions D72–D86 mise à jour
4. Matrice des dépendances mise à jour
5. Matrice de tests mise à jour
6. Points restant à valider réglementairement
7. Rapport de cohérence avec le dossier principal et EXT02 à EXT05

---

## 1. Synthèse et avis d'intégration

| Demande de la revue | Traitement v1.1 | Où |
|---|---|---|
| 1. M20 — immutabilité et cycle de vie | Contradiction levée : brouillons séparés (`journal_entry_drafts`), `journal_entries` sans statut et strictement immuable ; contre-passation unique ; clé de génération par événement et version de règle ; batch atomique avec contrôle débit = crédit sur l'ensemble des lignes ; scellement ; contrôles d'export | §4.9, §5.13 (CP1–CP13), W15, tests ACC-01 à ACC-19 |
| 2. M20 — exceptions comptables | `accounting_exceptions` : origine, montants, responsable, échéance, cycle jusqu'à clôture à quatre yeux ; quatre voies de résolution ; l'opération métier n'est jamais touchée | §4.9, §5.14 (EX1–EX11), W14, G26, tests EXC-01 à EXC-11 |
| 3. D73 — validation à distance | **Retenue** : MFA au moment de la décision, confirmation explicite, motif, empreinte de l'opération, autorisation à usage unique et expirante, journalisation complète, aucune substitution | W4-D, GV13, `permission_exceptions`, §6.2, G28, tests GOV-16 à GOV-26 |
| 4. D75 — jour calendaire | **Retenue** : jour calendaire par défaut ; option « jour d'exploitation » activée par l'administrateur, documentée et versionnée, jamais silencieuse ; produits sensibles et absence de réintégration inchangés | §5.2 (AN9–AN13), W16, tests ANN-10 à ANN-17 |
| 5. Couche métriques | Grain, clé unique, dimensions, agrégation, annulations, recalculs, date de rattachement, contrôles ; référentiel `metric_definitions` ; cinq grandeurs distinguées | §4.10, §5.6.1, §5.6 bis (MT1–MT11), tests MET-01 à MET-12 |
| 6. Multi-officines | Une officine = un tenant ; isolation renforcée ; vue consolidée future en lecture seule sur agrégats, avec accord de chaque administrateur ; tables futures réservées, non créées | §1.4, §5.11 (MO1–MO10), tests MOF-01 à MOF-07 |
| 7. Rentabilité commerciale | Huit indicateurs fondés sur les définitions et la couche métriques existantes ; aucune table nouvelle | §5.12, G25, tests RNT-01 à RNT-09 |
| 8. Contrôle des impacts | Effectué : tables, doublons, D, R, dépendances, calendrier, tests | §§4 à 7 du présent rapport |

**Corrections induites, découvertes pendant la revue** (au-delà des huit demandes) :
1. `permission_exceptions` était déclarée « immuable » alors qu'elle change de statut → « transitions contrôlées ».
2. La v1.0 ajoutait les services comme « autres produits d'exploitation » alors qu'ils sont déjà dans le CA → double comptage supprimé.
3. La v1.0 plaçait `lettrage_code` sur des lignes d'écriture immuables → colonne retirée, le lettrage reste dans `party_ledger`.
4. La v1.0 (§8.3) indiquait qu'une écriture en écart « est régénérée » → remplacé par exception + contre-passation.
5. `posting_rules` : versions activées figées, une seule version active par événement et par date.

**Bilan chiffré** : 43 tables nouvelles (40 en v1.0, + `journal_entry_drafts`, `accounting_exceptions`,
`metric_definitions`) ; 1 table existante complétée en plus (`corrective_operations` d'EXT05, 2 colonnes) ; 0 doublon ;
0 nouveau module ; 4 écrans (G25–G28) ; 3 workflows (W14–W16) et 1 sous-workflow (W4-D) ; 51 règles nouvelles
(GV13, AN9–AN13, MT1–MT11, MO1–MO10, CP1–CP13, EX1–EX11 — le §5.12 n'est pas numéroté) ; 80 tests nouveaux (145 au total) ;
D72–D86 inchangées en nombre (2 retenues) ; R42–R51 inchangées en nombre (5 précisées).

### Avis d'intégration

**EXT06 v1.1 est prête à être soumise à la revue documentaire en vue de son intégration dans `CONCEPTION_PHARMOS_v1.2`.**
Les huit corrections demandées sont traitées, sans contradiction interne relevée par les contrôles du §7.

Cet avis est assorti de quatre conditions, qui ne bloquent pas l'intégration du document mais bloquent certains lots :

| # | Condition | Ce qu'elle bloque |
|---|---|---|
| C1 | **Ordre d'intégration** : EXT06 amende EXT05, qui s'appuie sur EXT04 et EXT03 v1.1. La v1.2 doit intégrer EXT03 v1.1 → EXT04 → EXT05 → EXT06, ou les intégrer ensemble | La cohérence de la v1.2 |
| C2 | **13 décisions encore « PROPOSÉES »** (D72, D74, D76–D86) : elles sont suivies comme dans les extensions précédentes | Les lots indiqués au §3 |
| C3 | **Points réglementaires R42–R51** non confirmés (§6) | Les lots 1, 2, 5, 7c, 9c, 11 |
| C4 | **Trois prototypes techniques au Lot 0** : en-tête scellé et contrainte différée dans un batch D1 (CP8) ; déclencheurs d'immutabilité et de transitions ; recalcul d'un jour de métriques dans la limite CPU d'un Worker (MT7) | L'architecture de M20 (Lot 7c) et de la couche métriques (Lot 11) |

La validation finale de l'intégration reste soumise à la revue documentaire du propriétaire.

---

## 2. Rapport des modifications par section

Légende : **A** = ajout · **M** = modification · **C** = correction d'une erreur de la v1.0 · **=** = inchangé.

| Section v1.1 | Nature | Modification | Demande |
|---|---|---|---|
| En-tête | M | Version 1.1, statut, historique des versions, renvoi au rapport, conservation de la v1.0 | 10 |
| §0 Chevauchements | A | 7 lignes : validation à distance, brouillons, exceptions, métriques, multi-officines, jour d'exploitation, rentabilité ; bilan porté à 43 tables | 1–8 |
| §1.2 Principes | M | Principe 6 : immutabilité stricte, contre-passation, erreur comptable sans effet sur l'opération métier | 1, 2 |
| §1.3 Modules | = | Aucun module ajouté | — |
| §1.4 Multi-officines | A | Schéma et principe « une officine = un tenant » ; précision de D15 (`sites` = dépôts et annexes) | 6 |
| §2 Écrans | A | G25 rentabilité, G26 exceptions comptables, G27 brouillons d'écritures, G28 décision à distance | 1, 2, 3, 7 |
| §3.4 W4 | M + A | Dernière ligne réécrite ; sous-workflow **W4-D** (demande, examen, décision avec MFA, exécution à usage unique, cas particuliers, journalisation) | 3 |
| §3.5 W5 | M | « Même jour de référence » : calendaire par défaut, exploitation si l'option est active | 4 |
| §3.13 W13 | M | Clôture : contrôle que chaque événement a une écriture ou une exception ; exceptions bloquantes ; brouillons ; intégrité ; réconciliation des métriques ; cliché | 1, 2, 5 |
| §3.14 W14 | A | Traitement d'une exception comptable (détection, affectation, quatre voies de résolution, clôture, escalade) | 2 |
| §3.15 W15 | A | Cycle de vie d'un brouillon (EN_COURS, SOUMIS, REJETE, ABANDONNE, COMPTABILISE) | 1 |
| §3.16 W16 | A | Activation de l'option « jour d'exploitation » (variantes GARDES et PERMANENTE) | 4 |
| §4 Introduction | A | Nouvelle catégorie « transitions contrôlées » | 1 |
| §4.1 `permission_exceptions` | M + C | Empreinte d'opération, motif de décision, confirmation explicite, MFA, session, appareil, hachage de l'autorisation, consommation, révocation, statut `REVOQUEE` ; « immuable » → « transitions contrôlées » | 3 |
| §4.9 M20 | M + C | `posting_rules` versionnées et figées ; `journal_entry_drafts` (nouvelle) ; `journal_entries` sans statut, avec clé de génération, totaux, empreinte, unicité de contre-passation ; lignes avec clé étrangère différée ; `accounting_exceptions` (nouvelle) ; exports provisoires / définitifs avec totaux ; `lettrage_code` retiré | 1, 2 |
| §4.10 Métriques | M | `metric_definitions` (nouvelle) ; `metrics_daily` : clé unique, `metric_code`, version, type et identifiant de dimension ; `financial_snapshots` alignée sur les mêmes codes | 5 |
| §4.11 Relations | M | Relations des nouvelles tables | 1, 2, 5 |
| §5.1 Gouvernance | A | **GV13** validation à distance | 3 |
| §5.2 Annulations | M | Paragraphe « jour calendaire » remplacé par **AN9 à AN13** | 4 |
| §5.6 Définitions | M + C | §5.6.1 : CA, CA encaissé, marge commerciale, marge réalisée, résultat de gestion ; §5.6.2 : autres grandeurs avec code ; règle 6 sur les taux ; double comptage des services supprimé | 5 |
| §5.6 bis | A | **MT1 à MT11** : grain, clé, dimensions, axes multi-valeurs, agrégation, annulations et retours, idempotence, recalculs, rattachement, contrôles, source unique | 5 |
| §5.7 Tableau de bord financier | M | CA encaissé, marge réalisée, résultat de gestion, exceptions ouvertes | 2, 5 |
| §5.8 Tableau de bord commercial | M | Renvoi à MT10–MT11 et au §5.12 | 5, 7 |
| §5.11 | A | **MO1 à MO10** multi-officines | 6 |
| §5.12 | A | Analyse de rentabilité : 8 indicateurs, 4 règles | 7 |
| §5.13 | A | **CP1 à CP13** immutabilité, idempotence, atomicité | 1 |
| §5.14 | A | **EX1 à EX11** exceptions comptables | 2 |
| §6.2 Permissions | A | 11 permissions : `governance.exceptions.request`, `governance.reference_day.manage`, `accounting.drafts.create` / `.post`, `accounting.reverse`, `accounting.exceptions.read` / `.resolve` / `.close`, `accounting.posting_rules.activate`, `analytics.profitability.read` ; `consolidated.read` réservée ; `governance.exceptions.grant` précisée | 2, 3, 4, 6, 7 |
| §6.3 | A | Permissions de gouvernance évaluées depuis `tenant_administrators`, pas depuis un rôle | 3 |
| §7 Dépendances | A | 7 lignes ; 3 prototypes techniques au Lot 0 | 8 |
| §8.1 Dossier principal | A | §4.11, §4.2 (`settings`, `sites`), §3.2 (outbox), §6 (CI `tenant_id`) | 5, 6, 8 |
| §8.2 Extensions | M | EXT02 (`duty_periods`), EXT03 v1.1 (dimensions, SEC-TEN-01), EXT04 (métriques, CA encaissé), EXT05 (`corrective_operations`, garanties de la validation à distance) | 8 |
| §8.3 Compatibilité | C + A | « L'écriture est régénérée » corrigé ; jour de référence sans effet sur le rattachement | 1, 4 |
| §9 Réglementaire | M | R46, R48, R49, R50 précisées ; aucun numéro ajouté | 8 |
| §10 Tests | A | GOV-16 à 26, ANN-10 à 17, FIN-18 à 20, séries ACC, EXC, MET, MOF, RNT | 8 |
| §11 Décisions | M | Colonne « Statut » ; D73 et D75 retenues ; points tranchés sans numéro | 3, 4 |
| §12 Plan | M | Lots 0, 1, 5, 7c, 11 complétés ; vue consolidée hors plan ; prérequis mis à jour | 8 |
| Récapitulatif des tables | M | 43 tables nouvelles ; tables utilisées sans modification ; tables réservées ; `corrective_operations` complétée | 8 |
| §1.1, §2.1, §3.1–3.3, §3.6–3.12, §4.2–4.8, §5.3–5.5, §5.9, §5.10 | = | Inchangés | — |

---

## 3. Matrice des décisions D72–D86 mise à jour

| # | Sujet | Statut v1.0 | Statut v1.1 | Position | Bloque | Modifiée en v1.1 |
|---|---|---|---|---|---|---|
| D72 | Perte d'accès de l'administrateur | Proposée | **Proposée** | Procédure de secours APEX, sans accès aux données | Lot 1 | Non |
| **D73** | Dérogations en l'absence de l'administrateur | Proposée | **RETENUE** | Validation à distance par l'administrateur seul ; MFA, confirmation explicite, motif, empreinte, usage unique, journalisation ; exclue pour les réglementés | Lot 1 (R48 pour la preuve) | **Oui** |
| D74 | Réintégration directe d'EXT05 | Proposée | **Proposée** | Décision immédiate tracée par un second collaborateur | Lot 5d | Non |
| **D75** | Jour d'annulation | Proposée | **RETENUE** | Jour calendaire par défaut ; option « jour d'exploitation » (GARDES / PERMANENTE) activée explicitement par l'administrateur | Lot 5 (R49) | **Oui** |
| D76 | Bucket `RESERVE` | Proposée | **Proposée** | Retirer | Lot 2–3 | Non |
| D77 | Page après connexion | Proposée | **Proposée** | Selon le rôle | Lot 5 | Non |
| D78 | Données saisies lors des services | Proposée | **Proposée** | Mesures structurées, facultatives, chiffrées | Lot 9c (R47) | Non |
| D79 | Visibilité des marges | Proposée | **Proposée** | Permission dédiée ; aussi appliquée à G25 | Lot 11 | Portée étendue à G25 |
| D80 | Profondeur de M20 | Proposée | **Proposée** | B (pré-comptabilité en partie double), précisée par CP1–CP13 et EX1–EX11 | Lot 7c | Précisée |
| D81 | Moteur de paie | Proposée | **Proposée** | Import des montants | Lot 7c | Non |
| D82 | Réouverture de période | Proposée | **Proposée** | Administrateur avec motif, interdite après export définitif | Lot 7c | Non |
| D83 | Plan de comptes livré | Proposée | **Proposée** | Modèle indicatif inactif avant validation de l'expert-comptable | Lot 7c | Non |
| D84 | Classification thérapeutique | Proposée | **Proposée** | Selon R43 | Lot 2 | Non |
| D85 | Rapprochement bancaire | Proposée | **Proposée** | Proposé puis confirmé ; écarts → exceptions | Lot 7c | Précisée |
| D86 | Seuil de validation des charges | Proposée | **Proposée** | Montant paramétrable | Lot 7c | Non |

**Contrôle de numérotation** : D72 à D86 continues, 15 décisions, aucune lacune, aucun doublon avec D1–D71
(dossier, EXT02 D17–D26, EXT03 D27–D43, EXT04 D44–D58, EXT05 D59–D71). Aucune décision nouvelle : les exigences 1, 2, 5,
6 et 7 de la revue sont des choix du propriétaire, intégrés directement comme règles (CP, EX, MT, MO, §5.12).

**Prérequis par lot (v1.1)** : Lot 0 — prototypes techniques ; Lot 1 — R48, D72 ; Lot 2 — R42, R43, R44, R51, D76, D84 ;
Lot 5 — R49, D74, D77 ; Lot 7c — R45, R46, R50, D80–D83, D85, D86 ; Lot 9c — R47, D78 ; Lot 11 — D79 et validation des
définitions §5.6.1 par l'expert-comptable.

---

## 4. Matrice des dépendances mise à jour

### 4.1 Dépendances fonctionnelles des éléments d'EXT06

| Élément | Dépend de | Est utilisé par | Nouveau v1.1 |
|---|---|---|---|
| Gouvernance (administrateur, matrice, dérogations) | EXT02 (droits effectifs, qualifications), EXT05 (délégations, réglages individuels) | Tous les modules | — |
| Validation à distance (W4-D) | `tenant_administrators`, MFA (`users.mfa_secret`), `devices`, `sessions`, `notifications`, `audit_log` | W4, W5, plafonds de toutes les opérations ; garanties reprises par EXT05 | **Oui** |
| Annulations amendées | EXT05 (M19), M05 (buckets), gouvernance | M02, M08, EXT03, EXT04 | — |
| Option « jour d'exploitation » (W16) | `settings`, `duty_periods` (EXT02, Lot 10b), gouvernance | M19, écran d'annulation, `corrective_operations` (AN13) | **Oui** |
| Catalogue, classification, fiscalité | Référentiel APEX, R42–R44 | M02, M03, M05, M11, M12, EXT03, EXT04, M20, recherche | — |
| Substitution unifiée | Catalogue, validation pharmacien | EXT03, caisse | — |
| Lots et états du stock | M03, M05 | FEFO, M10, M11, EXT05, valorisation, M20, G25 | — |
| Services | Catalogue, EXT02, M02, M05, EXT03 (`sensitive_access_log`) | M12, tableaux de bord, M20 | — |
| Finance étendue | M03, M06, M08, EXT02, EXT04 | M20, tableaux de bord, IA | — |
| Écritures M20 (CP1–CP13) | Outbox `domain_events`, `document_sequences`, `posting_rules`, `accounting_periods`, déclencheurs SQLite, `D1 batch` | Grand livre, balance, exports, exceptions | **Oui** |
| Brouillons (W15) | Plan de comptes, journaux, périodes, permissions `accounting.drafts.*` | Régularisations, résolution d'exceptions | **Oui** |
| Exceptions comptables (W14) | Générateur M20, rapprochement bancaire, réconciliations, `notifications` | Clôture W13, G23, expert-comptable | **Oui** |
| Couche métriques (MT1–MT11) | `metric_definitions`, outbox, registres sources, `tenants.fuseau`, `rx_facts`, projections EXT04 | M01, G23, G24, G25, M12, IA, clichés, contrôles | **Oui** |
| Rentabilité (§5.12) | Couche métriques, `lots`, `stock_levels`, `product_demand_stats`, classification, `brands`, `ref_laboratories` | G25, IA (recommandations) | **Oui** |
| Multi-officines (MO1–MO10) | `tenants`, `memberships`, `sessions`, filtrage `tenant_id`, CI | Vue consolidée future (hors EXT06) | **Oui** |
| Accueil et recherche universelle | Catalogue, FTS, matrice des droits, M17, M18 | Tous les utilisateurs | — |

### 4.2 Dépendances entre lots (v1.1)

```
Lot 0  (prototypes : FTS5, en-tête scellé, déclencheurs, recalcul métriques, CI tenant_id)
  └─► Lot 1  (gouvernance, MFA step-up, W4-D, paramètre jour de référence)
        ├─► Lot 2 (catalogue) ─► Lot 3 (stock & lots) ─► Lot 5 (caisse, annulations, accueil)
        │                                                   ├─► Lot 5d (EXT05 amendé)
        │                         Lot 10b (gardes EXT02) ─ ─┘  (variante GARDES de W16)
        ├─► Lot 7 (finance) ─► Lot 7b ─► Lot 7c (M20 : écritures, brouillons, exceptions, clôture, paie importée)
        ├─► Lot 9c (services, après 5 et 10b)
        └─► Lot 11 (metric_definitions, metrics_daily, clichés, G23, G24, G25) ─► Lot 13 (IA sur agrégats)
Vue consolidée multi-officines : hors plan (extension future)
```

**Point d'attention** : la variante GARDES de l'option « jour d'exploitation » dépend des gardes d'EXT02 (Lot 10b), livrées
après le Lot 5. Tant que le Lot 10b n'est pas livré, seule la variante PERMANENTE est proposée (§12 de la v1.1).

### 4.3 Événements consommés par les nouveaux traitements

| Événement (outbox) | Générateur M20 | Projecteur de métriques | Contrôle d'exceptions |
|---|---|---|---|
| `sale.completed`, `sale.voided`, `sale.returned` | Écritures VT, CA | CA, coût, marge, quantités, dimensions | Écart métriques ↔ journal |
| `payment.recorded`, `payer_remittance.allocated` | Écritures CA / BQ, lettrage tiers | CA encaissé, encaissements | Écart tiers, rapprochement |
| `supplier_invoice.validated`, avoirs, acomptes | Écritures AC | Achats, charges | Écart tiers |
| `cash_session.closed` | Écriture d'écart de caisse | — | Discordance caisse ↔ M20 |
| `loss.recorded` | Écriture de perte | Démarque | Écart stock ↔ valorisation |
| `claim.written_off` | Écriture de perte sur créance | Marge réalisée | — |
| `payroll.validated`, `loan.instalment_paid` | Écritures OD / BQ | Charges, décaissements | — |
| Acte de service facturé (W8) | Aucun événement propre : passe par `sale.completed` (ligne de vente du service) | Services, coût des consommables (mouvements de stock liés) | — |

---

## 5. Matrice de tests mise à jour

### 5.1 Synthèse par série

| Série | Domaine | v1.0 | v1.1 | Nouveaux | Couvre la demande |
|---|---|---|---|---|---|
| S-GOV | Gouvernance | 15 | 26 | GOV-16 à GOV-26 | 3 (D73) |
| S-ANN | Annulations amendées | 9 | 17 | ANN-10 à ANN-17 | 4 (D75) |
| S-CAT | Catalogue et stock | 13 | 13 | — | — |
| S-SRV | Services | 7 | 7 | — | — |
| S-FIN | Finance et comptabilité | 17 | 20 | FIN-18 à FIN-20 | 1, 2, 5 |
| S-NAV | Accueil et navigation | 4 | 4 | — | — |
| **S-ACC** | M20 : immutabilité, idempotence, atomicité | — | 19 | ACC-01 à ACC-19 | 1 |
| **S-EXC** | M20 : exceptions comptables | — | 11 | EXC-01 à EXC-11 | 2 |
| **S-MET** | Couche métriques | — | 12 | MET-01 à MET-12 | 5 |
| **S-MOF** | Multi-officines et isolation | — | 7 | MOF-01 à MOF-07 | 6 |
| **S-RNT** | Rentabilité commerciale | — | 9 | RNT-01 à RNT-09 | 7 |
| **Total** | | **65** | **145** | **80** | |

**Unicité des identifiants** : aucun doublon dans EXT06 v1.1 ; aucune collision avec les préfixes des autres documents
(EXT04 : TP ; EXT05 : AN, AU, CX, DB, DL, DR, FI, HL, OG, PA, RE, RP, SE, ST ; EXT03 v1.1 : AUD, ATO, CMP, COR, PAT, QTE,
SEC-*, etc.). Le préfixe `AN-` d'EXT05 et `ANN-` d'EXT06 sont distincts.

### 5.2 Couverture des exigences de la revue

| Exigence de la revue | Tests |
|---|---|
| Écriture validée strictement immuable | ACC-01, ACC-08, ACC-11 |
| Correction par contre-passation et nouvelle écriture | ACC-11, ACC-12, ACC-13, FIN-11 |
| Cycle de vie des brouillons sans modification d'une écriture validée | ACC-14, ACC-15, ACC-16 |
| Une seule écriture par événement et par version de règle | ACC-04, ACC-17 |
| Traitements idempotents ; relance sans doublon | ACC-05, ACC-06, ACC-07, MET-01, MET-02 |
| Débit = crédit sur l'ensemble des lignes, en transaction atomique | ACC-02, ACC-03, ACC-18 |
| Aucune écriture déséquilibrée validée ou exportée | ACC-02, ACC-09, ACC-10 |
| Exceptions enregistrées, identifiables, chiffrées, affectées, suivies | EXC-01, EXC-02, EXC-07, EXC-08, EXC-10 |
| Aucune perte de traçabilité d'une opération métier validée | EXC-01, FIN-19, FIN-20 |
| Reprise, régularisation, contrôle | EXC-03, EXC-04, EXC-05, EXC-06, EXC-09 |
| D73 : MFA, confirmation, motif, opération identifiée, usage unique, journalisation, pas de substitution | GOV-16 à GOV-26 |
| D75 : jour calendaire par défaut, option explicite, jamais silencieuse, sensibles et réintégration inchangés | ANN-10 à ANN-17 |
| Métriques : grain, clé, dimensions, agrégation, annulations, recalculs, rattachement, contrôles | MET-01 à MET-12 |
| CA, CA encaissé, marge commerciale, marge réalisée, résultat de gestion distincts | FIN-18, MET-05, MET-06, MET-07 |
| Source de calcul unique | MET-11, RNT-09, FIN-15 |
| Multi-officines : isolation, administrateur, utilisateurs, permissions, comptabilité, paramètres | MOF-01 à MOF-07 |
| Rentabilité commerciale | RNT-01 à RNT-09 |

### 5.3 Priorité d'exécution

- **Dès le Lot 0** (prototypes et socle) : ACC-01, ACC-02, ACC-03, ACC-08, MET-01, MET-02, MOF-01, MOF-07.
- **Lot 1** : GOV-16 à GOV-26, MOF-02 à MOF-05.
- **Lot 5** : ANN-10 à ANN-17.
- **Lot 7c** : ACC-04 à ACC-19, EXC-01 à EXC-11, FIN-18 à FIN-20.
- **Lot 11** : MET-03 à MET-12, MOF-06, RNT-01 à RNT-09.

---

## 6. Points restant à valider réglementairement

Aucune valeur réglementaire ou fiscale n'a été présumée en v1.1. Les numéros R42 à R51 sont inchangés ; quatre ont été
précisés (R46, R48, R49, R50).

| # | Sujet | Question à trancher | Interlocuteurs | Bloque | Modifié en v1.1 |
|---|---|---|---|---|---|
| R42 | Catégories réglementaires de produits | Obligations distinctes (traçabilité des dispositifs, matériovigilance, numéros de série, cosmétovigilance) | DPM, ministère de la Santé | Lot 2 | Non |
| R43 | Classification thérapeutique, statut de commercialisation | Référentiel officiel tunisien exploitable ; licence de l'ATC | DPM, PCT | Lot 2 | Non |
| R44 | TVA et fiscalité des produits et services | Taux par catégorie ; droit de timbre ; facture électronique | Expert-comptable, administration fiscale | Lot 2 | Non |
| R45 | Immobilisations et amortissements | Durées, méthodes, seuil d'immobilisation | Expert-comptable | Lot 7c | Non |
| R46 | Pré-comptabilité et plan de comptes | Plan applicable ; mentions des journaux ; acceptation d'une pré-comptabilité logicielle ; conservation des pièces ; **[v1.1]** inaltérabilité et continuité de numérotation ; acceptabilité des notions « marge réalisée » et « résultat de gestion » ; conservation des exceptions comptables | Expert-comptable, Ordre des experts-comptables | Lots 7c et 11 | **Oui** |
| R47 | Services pharmaceutiques autorisés | Prestations autorisées, personnel, traces, tarification | Ordre des pharmaciens, ministère de la Santé | Lot 9c | Non |
| R48 | Responsabilité de l'administrateur, délégation | Désignation d'un autre pharmacien comme administrateur ; limites de délégation ; **[v1.1]** valeur d'une autorisation donnée à distance pour une opération de caisse ou de stock, et preuve exigée | Ordre, juriste | Lot 1 | **Oui** |
| R49 | Délai d'annulation d'une vente | Règles fiscales sur l'annulation d'un ticket ou d'une facture ; **[v1.1]** annulation après minuit pendant une garde, rattachée au jour d'exploitation ; clôture journalière de caisse | Administration fiscale, expert-comptable | Lot 5 | **Oui** |
| R50 | Données de paie et données personnelles | Traitement et conservation des données de paie ; déclaration INPDP ; **[v1.1]** consultation consolidée d'agrégats de plusieurs officines par leur propriétaire | INPDP, juriste | Lot 7c ; vue consolidée future | **Oui** |
| R51 | Prix réglementés et marges | Source officielle des prix, règles de marge, affichage ; **[v1.1]** signalement des produits à prix réglementé dans l'analyse de rentabilité (aucune modification de R51) | PCT, ministère du Commerce | Lot 2 | Usage étendu |

**Paramètres de l'officine sans valeur réglementaire présumée** (à fixer par l'officine, pas par APEX) : durée de validité
d'une autorisation à distance, délai de fraîcheur du MFA, échéances des exceptions par gravité, seuils de rentabilité, seuil
de validation des charges (D86), heure de bascule de la variante PERMANENTE.

---

## 7. Rapport de cohérence avec le dossier principal et EXT02 à EXT05

### 7.1 Contrôles automatiques effectués

| Contrôle | Résultat |
|---|---|
| EXT06 v1.0 inchangée (empreinte SHA-256) | **Conforme** |
| `CONCEPTION_PHARMOS_v1.md`, EXT02, EXT03, EXT04, EXT05 non modifiés (git) | **Conforme** |
| Tables nouvelles déclarées au §4 = tables du récapitulatif | **Conforme** (43 = 43, listes identiques) |
| Tables nouvelles déjà définies dans le dossier, EXT02, EXT03 v1.1, EXT04 ou EXT05 | **Aucune** (0 doublon) |
| Numérotation des décisions | **Conforme** : D72 à D86, continues, sans doublon avec D1–D71 |
| Numérotation des exigences réglementaires | **Conforme** : R42 à R51, continues, sans doublon avec R1–R41 |
| Identifiants de tests | **Conforme** : 145 uniques ; aucune collision entre extensions |
| Dimensions de `metrics_daily` (§4.10) = liste fermée MT3 (§5.6 bis) | **Conforme** |
| Tableaux Markdown (nombre de colonnes) | **Conforme** (0 ligne incohérente) |
| Restes de la v1.0 contredits par la v1.1 (`BROUILLON` dans `journal_entries`, « l'écriture est régénérée », « résultat d'exploitation », double comptage des services, `lettrage_code` sur lignes immuables) | **Aucun** (subsistent seulement dans les notes « Correction v1.0 ») |
| Références internes (W4-D, W14–W16, G25–G28, GV13, AN9–AN13, MT, MO, CP, EX, §5.11–§5.14, §10.7–§10.11) | **Toutes résolues** |

### 7.2 Cohérence avec le dossier principal (v1.1)

| Élément du dossier | Usage par EXT06 v1.1 | Cohérence |
|---|---|---|
| §3.2 Outbox `domain_events`, événements de domaine | Source unique du générateur M20 et du projecteur de métriques, consommateurs idempotents | Cohérent ; aucune modification de table |
| §4.2 `tenants.fuseau` | Jour de référence (AN9) et jour de rattachement des métriques (MT1) | Cohérent |
| §4.2 `settings` | Paramètres `cancellation.reference_day`, `remote_approval_ttl` | Cohérent ; nouvelles clés, aucune nouvelle table |
| §4.2 `users.mfa_secret`, `sessions`, `devices` | MFA step-up et appareil enrôlé de W4-D | Cohérent |
| §4.2 `sites` (D15 « multi-officines / dépôt ») | Réservé aux dépôts et annexes d'une même officine ; chaque officine = un tenant | **Précision à acter en v1.2** (pas de contradiction structurelle) |
| §4.2 `audit_log` chaîné | Journalisation de W4-D, W16, transitions contrôlées | Cohérent |
| §4.2 `document_sequences` | Numérotation continue des écritures (CP11), par officine (MO4) | Cohérent |
| §4.11 `metrics_daily` (tenant_id, date, cle, dimension, valeur) | Colonnes précisées : `metric_code` + version, type et identifiant de dimension, clé unique | **Modification de structure à reporter en v1.2** (déjà annoncée en v1.0, précisée en v1.1) |
| §6 Idempotence (`Idempotency-Key`), batch atomique | Validations manuelles M20 (CP6), consommation de l'autorisation (W4-D) | Cohérent |
| §6.7 Hors ligne | Les écritures et les métriques sont générées côté serveur après synchronisation ; aucune écriture comptable hors ligne | Cohérent |
| « PHARM'OS n'est pas un logiciel de comptabilité » | Résultats indicatifs, pré-comptabilité, expert-comptable responsable (D80 B, R46) | Cohérent, à reformuler en v1.2 |

### 7.3 Cohérence avec les extensions

| Extension | Points vérifiés | Résultat |
|---|---|---|
| **EXT02** (intégrée) | Catalogue `permissions` (module × action) ; qualification de pharmacien ; `duty_periods` lue par W16 sans modification ; `quality_events` `ECART_CAISSE` conservé pour l'écart de comptage, distinct de l'exception comptable `ECART_CAISSE` (discordance caisse ↔ M20) ; paie N4 jamais exposée | **Cohérent**, aucun doublon |
| **EXT03 v1.1** (dossier d'audit) | `substitution_groups` remplace les champs d'EXT03 ; `rx_facts` reste la source des indicateurs d'ordonnances ; dimensions `prescriber`, `specialty`, `sale_mode` reprises ; SEC-TEN-01 complété par MOF-01 ; D42 indépendante de la règle du jour | **Cohérent** ; intégration d'EXT03 v1.1 au dépôt toujours en attente (condition C1) |
| **EXT04** | Règlement d'organisme = CA encaissé, jamais CA ; mesures `ca_part_organismes`, `encours_organismes` reprises dans `metric_definitions` ; rapprochement proposé puis confirmé (D55 / D85) ; écarts de rapprochement → exceptions comptables ; aucune table modifiée | **Cohérent** |
| **EXT05** | D61 remplacé par AN2 / AN9–AN13 ; `DISPONIBLE_DIRECT` (D74) ; bucket `RESERVE` retiré (D76) ; `delegation.manage` remplacé ; `corrective_operations` complétée de 2 colonnes (AN13) ; `DEMANDE_A_DISTANCE` reprend les garanties techniques de W4-D sans changer le valideur prévu par EXT05 (pharmacien), sauf pour les dérogations de plafond et de délai, réservées à l'administrateur | **Cohérent** ; les amendements sont tous listés au §8.2 de la v1.1 |

### 7.4 Points d'attention pour la revue documentaire

1. **Ordre d'intégration** (C1) : EXT03 v1.1, EXT04, EXT05, puis EXT06, car chacune amende ou réutilise la précédente.
2. **`sites` et D15** : acter que l'officine est le tenant et que `sites` désigne un dépôt ou une annexe.
3. **`metrics_daily`** : la structure du dossier §4.11 (`cle`, `dimension`) doit être remplacée par celle de la v1.1.
4. **Deux décisions retenues** (D73, D75) : à reporter comme telles dans la grille des décisions de la v1.2 ; leurs points
   réglementaires associés (R48, R49) restent ouverts.
5. **Prototypes du Lot 0** (C4) : un résultat négatif sur le batch scellé ou sur le recalcul des métriques modifierait les
   règles CP8 ou MT7 (solution de repli prévue), sans remettre en cause les principes.
6. **Notions de gestion** « marge réalisée » et « résultat de gestion » : à confirmer par l'expert-comptable (R46) avant le Lot 11.

---

*Fin du rapport. La validation finale de l'intégration reste soumise à la revue documentaire.*