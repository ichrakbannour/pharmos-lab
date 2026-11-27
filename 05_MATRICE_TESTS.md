# PHARM'OS — EXT03 v1.1 — Matrice des tests fonctionnels et de sécurité

**Statut** : spécification de tests (aucun code). À rattacher aux lots 5, 5b, 5c, 10, 11, 13, 14.
Familles : **S** scénarios d'intégration · **F** fonctionnels · **T** atomicité / transactions · **SEC** sécurité et droits ·
**C** confidentialité / réidentification · **O** OCR · **AUD** audit.
Priorité : **P1** bloquant pour la recette du lot · **P2** obligatoire avant mise en production · **P3** souhaitable.

Jeu de données de référence commun : 1 pharmacie A (+ pharmacie B pour l'isolation), 6 collaborateurs (titulaire,
adjoint, préparateur, caissier, auditeur, collaborateur suspendu), 20 produits dont 3 réglementés, 2 génériques
par princeps, 12 prescripteurs dont 2 paires de doublons FR/AR, 30 patients, 60 jours d'historique.

---

## 1. Scénarios d'intégration (demandés)

| ID | Scénario | Préconditions | Étapes clés | Résultats attendus | Vérifications d'atomicité | Prio |
|---|---|---|---|---|---|---|
| S01 | Ordonnance avec plusieurs médicaments | 4 lignes, stock suffisant, 2 lots pour une ligne | Saisie, validation | 4 délivrances ; 5 mouvements SALE (FEFO, 2 lots) ; statut `DELIVREE` ; `rx_facts` = 4 lignes | Σ délivrances = Σ `sale_lines` liées = Σ mouvements (en unités) ; même `validation_batch_id` | P1 |
| S02 | Plusieurs ordonnances dans une vente | 2 ordonnances (patients différents, même payeur) | Une vente, 2 × 2 lignes | Chaque `sale_line` liée à la bonne ligne ; deux statuts ; vue `sale_prescriptions` = 2 | Aucune ligne liée à la mauvaise ordonnance ; un seul batch | P1 |
| S03 | Ordonnance + produits libres | 2 lignes Rx + 3 produits parapharmacie | Validation | Mode `MIXTE` ; produits libres sans `prescription_line_id` ; CA ordonnance = Σ lignes Rx | Réconciliation CA total = CA comptoir + CA ordonnance | P1 |
| S04 | Délivrance partielle puis reliquat | Prescrit 3 bt, stock 1 | Délivrer 1, mode `A_COMMANDER` ; réception ; délivrer 2 | Reste 2 puis 0 ; `prescription_demand` créée puis satisfaite ; statut `PARTIELLEMENT_DELIVREE` → `DELIVREE` | Reste jamais saisi ; projection = recalcul | P1 |
| S05 | Renouvellement | Renouvelable × 2, délai minimal 21 j | Occurrence 0 ; tentative J+10 ; J+25 ; J+50 ; tentative 4ᵉ | J+10 refusé (délai) ; J+25 et J+50 acceptés ; 4ᵉ refusé (nombre) ; refus si validité dépassée | Chaque occurrence dans son batch | P1 |
| S06 | Annulation | Vente S01 validée, session ouverte | Annuler | Contre-mouvements SALE ; délivrances négatives liées (`reverses_id`) ; droit restitué ; statut recalculé ; registre rectifié si réglementé | Annulation atomique (tout ou rien) | P1 |
| S07 | Retour | Vente validée, session clôturée | Retour 1 bt conforme, puis 1 bt non conforme | Avoir ; stock réintégré (conforme) / `LOSS` (non conforme) ; neutralisation par défaut (reste inchangé) ; restitution seulement avec pharmacien + motif | Avoir, mouvements, contre-délivrances dans un seul batch | P1 |
| S08 | Rupture de stock | Stock 0 sur une ligne | Validation | Refus de la ligne ou délivrance partielle avec reliquat ; jamais de stock négatif ; alternative de même DCI proposée (non imposée) | Aucune écriture si refus | P1 |
| S09 | Médicament réglementé | Produit classe X, ordonnance `REGLEMENTEE` | (a) cas conforme ; (b) patient non identifié ; (c) dispensateur sans habilitation ; (d) scan manquant ; (e) vente en mode Comptoir ; (f) `rx.override` tenté | (a) registre n° continu ; (b)–(f) **refus** avec code motif, aucune dérogation possible | (a) registre et vente dans le même batch ; (b)–(f) rien n'est écrit | P1 |
| S10 | Médecin en doublon | « Dr Ben Salah Mohamed » existe | Créer « Mohamed BENSALAH », puis « بن صالح محمد » | Score ≥ 85 → création bloquée, fiche proposée ; 60–84 → paire en E6 ; fusion → rapports sur la fiche résolue ; défusion sans perte | Fusion n'altère aucune ordonnance | P1 |
| S11 | Correction de spécialité | Médecin MG, 10 ordonnances ; devient cardiologue au 01/11 | (a) évolution de fiche datée ; (b) erreur de saisie sur 1 ordonnance ; (c) erreur de fiche depuis l'origine | (a) snapshots antérieurs inchangés ; (b) nouvelle version, `rx_facts` recalculés, motif ; (c) rectifications proposées par lot, validées par le titulaire | Aucun recalcul silencieux | P1 |
| S12 | Erreur OCR | Ordonnance scannée, dosage lu 50 mg au lieu de 5 mg, quantité « 7 » au lieu de « 1 » | Afficher propositions ; tenter de valider sans confirmer ; corriger | Incohérence signalée ; champs non confirmés = manquants → validation refusée ; correction tracée (`CORRIGE`, auteur) ; contrôles identiques à la saisie manuelle | — | P1 |
| S13 | Perte de connexion pendant la validation | Réseau coupé (a) avant envoi, (b) pendant, (c) après le batch avant réponse | Réessai avec la même clé | (a) rien d'écrit puis validé une fois ; (b) tout ou rien ; (c) résultat initial renvoyé, aucun doublon | Comptage : 1 vente, N mouvements, N délivrances, 1 paiement | P1 |
| S14 | Accès non autorisé aux données patient | Caissier, préparateur hors contexte, auditeur, utilisateur pharmacie B | Appels API directs à `/patients/:id`, `/prescriptions/:id`, scans, état D | 403 côté serveur ; ligne `sensitive_access_log` « refus » ; alerte au titulaire après 3 refus/heure ; aucune donnée dans la réponse d'erreur | — | P1 |

## 2. Tests fonctionnels complémentaires

| ID | Domaine | Test | Attendu | Prio |
|---|---|---|---|---|
| F-PAT-01 | Patient (D28) | Ordonnance simple sans patient, profil pays autorisant | Acceptée, `patient_non_identifie` | P1 |
| F-PAT-02 | Patient | Idem, profil pays interdisant | Refusée | P1 |
| F-PAT-03 | Patient | Ordonnance renouvelable sans patient | Refusée | P1 |
| F-PAT-04 | Patient | Ajout d'un réglementé à une ordonnance sans patient | Bascule obligatoire, refus sinon | P1 |
| F-PAT-05 | Patient | Sélection du client de passage comme patient | Refusée | P1 |
| F-CMP-01 | Complétude (D29) | Manque A uniquement | Vente validée, statut `A_COMPLETER` | P1 |
| F-CMP-02 | Complétude | Manque M (dosage illisible) | Refus ; levée par pharmacien documentée → acceptée | P1 |
| F-CMP-03 | Complétude | Manque R (date de prescription) | Refus, `rx.override` sans effet | P1 |
| F-CMP-04 | Complétude | Règle locale moins stricte que le profil pays | Paramètre refusé à l'enregistrement | P1 |
| F-CMP-05 | Complétude (D43) | `A_COMPLETER` depuis 8 jours, nouvelle délivrance | Refus jusqu'à complétion ; alerte titulaire | P2 |
| F-VAL-01..04 | Validité | Délivrance initiale, reliquat et renouvellement après expiration ; changement de jour à minuit (fuseau) | Refus / validation selon catégorie ; date serveur du tenant | P1 |
| F-QTE-01 | Quantités | Dépassement au-delà de la tolérance | Refus | P1 |
| F-QTE-02 | Quantités | 21 cp prescrits, boîte de 30 | 1 boîte, excédent tracé ; produit fractionnable → 21 unités | P1 |
| F-QTE-03 | Concurrence | Deux caisses délivrent le reste de la même ligne simultanément | Une seule réussit ; l'autre reçoit `PRESCRIPTION_BALANCE_EXCEEDED` | P1 |
| F-SUB-01..05 | Substitution | Même DCI ; autre conditionnement ; autre dosage ; autre DCI ; ligne non substituable | Niveaux §4.4 ; accord prescripteur exigé selon politique | P1 |
| F-REL-05 | Reliquat | Abandon de reliquat | Adjustment motivé ; demande M11 annulée ; reste = 0 | P1 |
| F-RES-01 | Réservation (D40) | Réservation 48 h, expiration, prolongation | Stock disponible correct ; aucun mouvement | P2 |
| F-COR-02 | Correction | Quantité prescrite corrigée sous le déjà délivré | Alerte « excédent constaté », aucune délivrance modifiée | P1 |
| F-COR-03 | Correction | Mauvais lot délivré | Contre-écriture + nouvelle délivrance + mouvements, `correlation_id` commun | P1 |
| F-COR-04 | Correction | Réouverture d'une ligne clôturée | Adjustment motivé, validation titulaire si réglementée | P2 |
| F-HL-01..04 | Hors ligne (D39) | 1re délivrance simple ; reliquat ; renouvellement ; réglementé | Seule la 1re délivrance simple est permise ; revalidation serveur au retour, anomalies listées | P1 |
| F-RPT-01..05 | Rapports | États A–D sur jeu de référence ; retours déduits ; tableau de bord = états ; export = écran ; réconciliation CA | Égalité exacte | P1 |
| F-M11-01 | Commandes | Reliquat `A_COMMANDER` + renouvellements attendus | Besoin calculé sans double comptage | P2 |
| F-UX-01 | Interface | Ordonnance 5 lignes au clavier, FR et AR | < 60 s utilisateur entraîné ; RTL correct | P2 |

## 3. Atomicité et transactions

| ID | Test | Méthode | Attendu | Prio |
|---|---|---|---|---|
| T-ATO-01 | Échec injecté à chaque étape 1 à 11 du batch (§19.1) | Injection de faute par étape | Aucune écriture persistée, pour chaque étape | P1 |
| T-ATO-02 | Garde de stock non satisfaite (stock modifié entre préparation et batch) | Concurrence simulée | Batch entier annulé, message explicite | P1 |
| T-ATO-03 | Garde de solde d'ordonnance non satisfaite | idem | idem | P1 |
| T-ATO-04 | Échec d'écriture du registre réglementé | Injection | Vente non validée | P1 |
| T-IDE-01 | Double clic / double envoi même clé | 2 requêtes simultanées | 1 vente ; 2ᵉ réponse = résultat initial | P1 |
| T-IDE-02 | Même clé, contenu différent | — | Refus `IDEMPOTENCY_CONFLICT` | P1 |
| T-REC-01 | Réconciliation quotidienne | Jeu avec 500 ventes | 0 vente validée sans mouvements/délivrances ; 0 délivrance sans vente ; 0 mouvement SALE orphelin | P1 |
| T-REC-02 | Registre ↔ stock ↔ délivrances (réglementés) | idem | 0 écart | P1 |
| T-PRJ-01 | Projection des soldes supprimée puis reconstruite | Rejeu | Identique au recalcul | P1 |
| T-PRJ-02 | Outbox en retard (projections non à jour) | Retarder le consommateur | Contrôles bloquants corrects (lecture des registres) | P1 |
| T-LOT0-01 | Prototype du mécanisme d'annulation d'un `D1 batch` sur garde conditionnelle | Lot 0 | Mécanisme documenté et prouvé avant lot 5 | P1 |

## 4. Sécurité et droits

| ID | Test | Attendu | Prio |
|---|---|---|---|
| SEC-RBAC-01 | Matrice §5.2 (v1.1) testée permission × rôle via API directe | Conforme ; l'interface n'est jamais la seule barrière | P1 |
| SEC-RBAC-02 | Habilitation expirée / collaborateur suspendu / non pointé tente `rx.validate` ou un réglementé | Refus avec motif explicite | P1 |
| SEC-RBAC-03 | Caissier : accès aux lignes médicales, scans, OCR | Refus | P1 |
| SEC-CTX-01 | Préparateur : recherche libre dans l'historique d'un patient hors délivrance | Refus (sans `rx.read.nominative`) | P1 |
| SEC-TEN-01 | Utilisateur pharmacie B : identifiants de la pharmacie A (ordonnance, scan, patient, prescripteur) | 404/403, aucune fuite | P1 |
| SEC-DOC-01 | URL signée de scan expirée ou rejouée par un autre utilisateur | Refus | P1 |
| SEC-EXP-01 | Export patient sans validation ; auto-validation ; lien expiré ; 2ᵉ téléchargement | Refus dans les 4 cas | P1 |
| S-SOL-01 | Recherche de toute route d'écriture sur délivré / reste / renouvellements / soldes registre | Aucune ; tentative `PATCH` → 405 | P1 |
| SEC-OVR-01 | `rx.override` sur un contrôle BLOQUANT | Sans effet, tentative journalisée | P1 |
| SEC-INJ-01 | Injections (SQL, XSS) dans champs texte, OCR, libellés prescrits | Neutralisées | P1 |
| SEC-RATE-01 | Énumération d'identifiants patients/ordonnances | Limitation de débit, alerte | P2 |
| SEC-ENC-01 | Champs N2/N3 et scans chiffrés au repos ; valeurs sensibles chiffrées dans `audit_log` | Vérifié en base | P1 |

## 5. Confidentialité et réidentification

| ID | Test | Attendu | Prio |
|---|---|---|---|
| C-K-01 | Cellule < 5 patients distincts | Masquée « < 5 » | P1 |
| C-K-02 | Une seule cellule masquée dans une ligne avec total | Suppression secondaire ; soustraction impossible | P1 |
| C-X-01 | Croisement de 4 dimensions | Refusé (C3) | P1 |
| C-X-02 | Âge/sexe avec prescripteur nominatif | Refusé (C4) | P1 |
| C-DIF-01 | Deux requêtes dont les populations diffèrent d'1 patient | 2ᵉ refusée ou arrondie (C5), trace dans `report_query_log` | P1 |
| C-Q-01 | Dépassement du quota de requêtes filtrées | Alerte titulaire | P2 |
| C-EXP-01 | Export agrégé avec petites cellules | Arrondi et masquage identiques à l'écran | P1 |
| S-IA-01 | Charges utiles LLM : liste blanche de champs | Aucun champ hors liste | P1 |
| S-IA-02 | Question libre contenant nom de patient, téléphone, n° ORD | Neutralisé ou refusé avant envoi | P1 |
| S-IA-03 | Outil IA sur petite population | Mêmes masquages que les rapports | P1 |
| S-IA-04 | Tentative d'envoi d'image / OCR au LLM | Impossible (aucun outil) | P1 |
| S-IA-05 | Nom de prescripteur | Pseudonyme dans la charge utile, nom réel à l'écran | P1 |

## 6. OCR

| ID | Test | Attendu | Prio |
|---|---|---|---|
| O-01 | Jeu de 200 ordonnances de référence (imprimées, manuscrites, FR/AR) | Taux de reconnaissance mesuré par champ ; publié | P2 |
| O-02 | Validation sans confirmer les champs | Refus (champs manquants) | P1 |
| O-03 | Acceptation groupée sur prescripteur, date, patient, réglementé, renouvellement | Interdite | P1 |
| O-04 | Mention « non substituable » détectée | Cochée par défaut ; décocher exige motif | P1 |
| O-05 | Service OCR indisponible | Saisie manuelle immédiate, vente non bloquée | P1 |
| O-06 | Taux de correction > 40 % sur 30 j | Acceptation groupée désactivée, alerte | P2 |
| O-07 | Image et texte OCR | Chiffrés ; jamais transmis au LLM ; purge selon conservation | P1 |
| O-08 | Aucune appréciation médicale produite | Aucune sortie de type « posologie excessive / interaction » | P1 |

## 7. Audit

| ID | Test | Attendu | Prio |
|---|---|---|---|
| S-AUD-01 | Toute modification post-validation (ordonnance, fiche médecin, paramètre) | Avant, après, auteur, horodatage serveur, motif, validation présents | P1 |
| S-AUD-02 | Chaînage `audit_log`, `sensitive_access_log`, `prescription_adjustments` | Altération détectée | P1 |
| S-AUD-03 | `UPDATE`/`DELETE` direct en base sur tables immuables | Rejeté par déclencheur | P1 |
| S-AUD-04 | Suppression d'une ordonnance validée ou d'une délivrance via API | Route inexistante | P1 |
| S-AUD-05 | Abandon d'un brouillon puis purge à 30 j | Purge journalisée ; aucune délivrance concernée | P2 |
| S-AUD-06 | Lecture nominative (fiche patient, état D, scan) | Ligne `sensitive_access_log` avec motif si hors contexte | P1 |

---

**Couverture** : 14 scénarios d'intégration demandés (S01–S14) + ~75 tests. Toutes les familles P1 sont des critères
de recette des lots concernés (DoD du dossier principal §10).
