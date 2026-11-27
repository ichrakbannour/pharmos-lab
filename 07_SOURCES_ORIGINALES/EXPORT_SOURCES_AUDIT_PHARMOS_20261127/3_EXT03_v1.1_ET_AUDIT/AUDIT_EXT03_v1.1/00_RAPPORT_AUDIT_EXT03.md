# PHARM'OS — Rapport d'audit complémentaire de l'extension EXT03 (Ordonnances & prescripteurs)

**Date** : 2026-11-03 · **Objet** : préparation de la validation définitive avant intégration dans CONCEPTION_PHARMOS v1.2
**Base auditée** : `docs/EXT03_ORDONNANCES_PRESCRIPTEURS.md` v1.0 (commit `6cf90aa`), `CONCEPTION_PHARMOS_v1.md` v1.1,
`EXT02_RH_TEAM_MANAGEMENT.md` — sauvegarde `webapp_backup_ir3ywwou.tar.gz`.
**Périmètre respecté** : aucun code, aucun développement, aucun module existant modifié, rien n'est intégré au dépôt.
Les livrables sont placés dans ce dossier de revue, hors du dépôt `webapp/`.

## 1. Livrables

| # | Fichier | Contenu |
|---|---|---|
| 1 | `EXT03_ORDONNANCES_PRESCRIPTEURS_v1.1.md` | Version révisée (modifications repérées **[v1.1]** ; nouvelles sections 14 à 19) |
| 2 | `02_GRILLE_DECISIONS_D27_D40.md` | Grille complète D27–D40 + D41–D43 proposées |
| 3 | `03_MODIFICATIONS_CONCEPTION_v1.2.md` | ~60 modifications à reporter, section par section |
| 4 | `04_MATRICE_DEPENDANCES.md` | Dépendances, couplage transactionnel, événements, ordre des lots |
| 5 | `05_MATRICE_TESTS.md` | 14 scénarios d'intégration + ~75 tests (fonctionnels, atomicité, sécurité, confidentialité, OCR, audit) |
| 6 | `06_POINTS_REGLEMENTAIRES.md` | R1–R13 mis à jour + R14–R22 nouveaux, classés par échéance |

## 2. Appréciation générale

La v1.0 est solide sur les points structurants : ordonnance distincte de la vente, lien ligne à ligne, registre
immuable des délivrances, snapshots, dédoublonnage, rapports agrégés, interdits IA. L'audit n'a révélé aucune erreur
d'architecture obligeant à revoir le modèle. Il fait en revanche ressortir **des imprécisions qui auraient pu
permettre de contourner des contrôles**, ainsi que des sujets absents (OCR, régularisations, réidentification par
croisement).

## 3. Constats et corrections

| # | Thème | Constat sur la v1.0 | Gravité | Correction v1.1 |
|---|---|---|---|---|
| 1 | Obligations légales | `A_COMPLETER` et `rx.override` ne distinguent pas l'administratif du réglementaire | Élevée | Classes A / M / R (§4.8) ; BLOQUANT non levable ; principe n° 8 |
| 2 | Patient non identifié | Rien n'empêche d'ajouter un réglementé ou de créer une ordonnance renouvelable sans patient | Élevée | Règles BLOQUANTES §4.1 ; dépendance au profil pays (D28) |
| 3 | Soldes | Les annulations/retours sont prévus, mais pas les régularisations hors délivrance (abandon de reliquat, clôture, correction de quantité prescrite) ; risque de « bricolage » manuel | Élevée | `prescription_adjustments` immuable ; formule du solde ; projection reconstruisible ; aucune route d'écriture (§15) |
| 4 | Retour | Un retour restituait implicitement le droit à délivrance (contournement possible de la quantité prescrite) | Moyenne | Neutralisation par défaut (D42) |
| 5 | Réglementés | Contrôles décrits dans deux modules ; registre non explicitement dans la même transaction ; vente Comptoir d'un réglementé non interdite explicitement ; retour réintégré ; hors ligne contournable | Élevée | Audit A1–A10, service unique, séquence obligatoire, réconciliations (§16) |
| 6 | Atomicité | Principe énoncé, mais garde conditionnelle de solde absente, idempotence non reliée aux délivrances, comportement en coupure non spécifié | Élevée | Batch détaillé, `validation_batch_id`, garde finale, protocole de reprise (§19) ; **point technique D1 à prototyper au lot 0** |
| 7 | Spécialité | Snapshot « à la date de saisie » au lieu de la date de prescription ; correction de fiche et erreur de saisie confondues | Moyenne | Date de prescription ; trois cas de correction distingués (W4, D31) |
| 8 | Audit | Contenu d'une trace non normalisé (validation, corrélation) ; valeurs sensibles en clair dans l'audit | Moyenne | §17 ; chiffrement des champs sensibles dans `audit_log` ; déclencheurs d'immuabilité |
| 9 | Réidentification | Seul le seuil k = 3 ; vulnérable au croisement, à la soustraction, aux requêtes différentielles, à l'IA | Élevée | k = 5 + C2–C10 ; `report_query_log` (§18) |
| 10 | IA | Texte libre de l'utilisateur non filtré | Moyenne | Détection et neutralisation avant envoi (§9.4, §18.3) |
| 11 | OCR | Absent (seulement « scan / photo ») | — (demande nouvelle) | §14 : propositions, confiance, confirmation obligatoire, aucune décision médicale, moteur hors LLM (D41), lot 5c |
| 12 | Hors ligne | « Ordonnances simples » trop large (reliquats et renouvellements dépendent d'un solde non fiable hors ligne) | Moyenne | D39 durcie |

## 4. Points de vigilance non résolus par la conception seule

1. **Mécanisme d'annulation d'un `D1 batch` sur garde conditionnelle** : à prouver par prototype au lot 0 (T-LOT0-01).
   Si le mécanisme s'avérait insuffisant, l'alternative (verrouillage applicatif par ordonnance ou sérialisation par
   Durable Object) devrait être arbitrée avant le lot 5.
2. **Qualité réelle de l'OCR sur ordonnances manuscrites tunisiennes (FR/AR)** : à mesurer sur un jeu de 200 ordonnances
   (O-01) avant de fixer les seuils de confiance ; l'OCR reste un accélérateur, jamais un prérequis.
3. **Validations réglementaires** : 11 points bloquent le lot 5b (voir `06_POINTS_REGLEMENTAIRES.md`).

## 5. Décisions attendues

Valider ou amender les 17 décisions de la grille (D27–D43), puis autoriser :
- le remplacement de `docs/EXT03_ORDONNANCES_PRESCRIPTEURS.md` par la v1.1 ;
- le report des modifications listées dans `03_MODIFICATIONS_CONCEPTION_v1.2.md` (passage du dossier principal en v1.2).
