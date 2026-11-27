# PHARM'OS — Audit documentaire global v0.25

**Date :** 27/11/2026  
**Périmètre :** propagation de l'arbitrage PO INC-06 option B ; réconciliation D74/D75 entre EXT02, EXT05 et EXT06 ; clôture documentaire des constats AUD-055, AUD-056, AUD-063 et AUD-064.  
**Nature :** audit documentaire GitHub en lecture/écriture documentaire uniquement. Aucun code ni environnement applicatif modifié.

## 1. Résultat exécutif

La décision PO option B est désormais propagée dans la matrice, l'addendum PO, les addenda de conception, les extensions et les index du dépôt.

**Portée B confirmée et précisée :**
- modèle complet de gestion des organismes et conventions ;
- périmètre fonctionnel v1 limité au remboursement par l'assuré et aux conventions privées ;
- aucune convention active par défaut ;
- tiers payant CNAM et mutuelles exclu de la v1 ;
- tiers payant CNAM désactivé ; toute activation ultérieure exige vérification des textes, conventions, avenants et modalités officielles applicables, puis validation explicite et distincte du PO.

D6, D37 et D44 sont arbitrées dans la matrice PO v1.3.4. **INC-06 est clos au niveau de l'arbitrage de périmètre**, mais les preuves réglementaires/conventionnelles R23–R33 et les arbitrages D45–D58 ne sont pas clos.

## 2. Réconciliation des documents de conception

Un nouvel addendum a été publié :
01_CONCEPTION/ADDENDUM_CONCEPTION_v1.2.1_INC-06_OPTION_B_20261127.md.

Il précise la lecture courante des mentions historiques de la conception v1.2.1 :
- §9.3 conservait le conflit D6/D37 ;
- §16.2 indiquait « INC-06 tranché » sans préciser l'option ;
- les tableaux de statut présentaient encore D37/D44 comme ouverts ou proposés.

Le dossier v1.2.1 n'a pas été réécrit. L'addendum et la matrice v1.3.4 prévalent pour la lecture courante. Le premier addendum de conception sur D40 (2 jours comptables, sans avance) reste distinct et inchangé.

## 3. Propagation dans EXT02–EXT06

- **EXT02** : référence de matrice actualisée vers v1.3.4 ; le modèle des droits effectifs reste fondé sur les rôles, octrois individuels explicites et délégations, avec contrôles cumulatifs.
- **EXT03** : D37 indiquée arbitrée option B ; tiers payant CNAM/mutuelles exclu de la v1 ; périmètre v1 limité au remboursement par l'assuré et aux conventions privées ; D40 renvoie à la matrice v1.3.4.
- **EXT04** : ligne D44 actualisée en ARBITRÉE — option B ; D45–D58 restent des propositions ; D44 n'est plus listée comme décision PO en attente avant le lot 7b. Les exigences R23–R33 restent ouvertes.
- **EXT05** : en-tête de statut actualisé ; D61 et D66 sont confirmées caduques ; D59, D60, D62–D65 et D67–D71 restent proposées selon la matrice.
- **EXT06 canonique v1.0** : références actualisées vers la matrice v1.3.4 ; §11.1 continue de réconcilier D72–D86 ; contrôle croisé D74/D75 ajouté.
- **EXT06 v1.1** : conservée sans modification comme version historique ; son ancienne ligne D74 « PROPOSÉE » ne prévaut pas sur EXT06 canonique v1.0 et la matrice PO courante.

## 4. D74 / D75 — contrôle croisé et clôture documentaire

### D74 — droit de réintégration corrective

Cohérence confirmée entre :
- EXT02 §1.2 : une permission réservée à un octroi individuel ne peut pas être attribuée par rôle ; les contrôles d'habilitation, qualification, présence, plafonds et séparation des tâches restent cumulatifs ;
- EXT05 §§2.3, 2.4, 5.1–5.3 : stock.return.decide n'est accordée par défaut à aucun rôle ; octroi individuel explicite du pharmacien-administrateur, opération tracée ;
- EXT06 §5.2 et §11.1 : D74 arbitrée, DISPONIBLE_DIRECT supprimé et décision immédiate simplifiée possible uniquement avec le droit explicitement accordé.

Aucune preuve de l'implémentation réelle n'est revendiquée.

### D75 — période de référence des annulations

Cohérence confirmée :
- jour calendaire par défaut, calculé à partir de l'horodatage serveur et du fuseau de l'officine ;
- modes alternatifs GARDES ou PERMANENTE uniquement après activation explicite par l'administrateur ;
- motif obligatoire, MFA, versionnement, journalisation et prise d'effet non rétroactive ;
- EXT05 renvoie à D75 ; EXT06 §5.2 porte les modalités détaillées.

Le calcul temporel et le paramétrage réels restent à tester dans une mission de réalisation autorisée.

## 5. Registre des constats

| Constat | Statut documentaire v0.25 | Portée exacte |
|---|---|---|
| AUD-055 — conflit D37/D44 et périmètre tiers payant | **Clos** | Arbitrage PO D6/D37/D44 enregistré ; CNAM/mutuelles exclues de la v1 ; aucun flux CNAM activé |
| AUD-056 — contradiction de lecture INC-06 dans la conception v1.2.1 | **Clos pour la lecture courante** | Addendum daté et indexé ; dossier historique non réécrit |
| AUD-063 — D74 encore « proposée » dans EXT06 v1.1 | **Clos pour l'extension canonique** | EXT06 v1.0 §11.1 et sections fonctionnelles alignées ; v1.1 historique conservée sans modification |
| AUD-064 — renvoi D75 entre EXT05 et EXT06 | **Clos documentairement** | Renvoi EXT05 et règle détaillée EXT06 recroisés ; preuves applicatives restantes |
| AUD-062 — droit D74 attribué par défaut | **Déjà clos** | EXT02/EXT05 documentaires ; aucune vérification de l'application réelle |

## 6. Points restant ouverts

1. **R23–R33** : obtenir et vérifier les textes, conventions, avenants, pièces, procédures et modalités officielles réellement applicables. Aucune validation réglementaire n'est acquise.
2. **D45–D58** : poursuivre l'arbitrage ciblé ; D44 seule est arbitrée. D46, D48 et D53 restent des décisions à traiter pour les dépendances du lot 7b.
3. **D59–D71** : D61 et D66 sont caduques ; les autres décisions de cette plage restent proposées selon la matrice v1.3.4.
4. **D74/D75 en réalisation** : tests des droits effectifs, de la révocation/cache, du calcul temporel, des fuseaux, du versionnement et de la non-rétroactivité — uniquement après autorisation distincte.
5. **Réalisation du module B** : spécifications détaillées et preuves de conformité à établir dans une mission séparée et autorisée. Ce rapport ne vaut pas autorisation de développement.

## 7. Garanties de périmètre

- Aucun code, schéma ou base de données modifié.
- Aucun test applicatif, build, migration, déploiement ou service système exécuté.
- Aucun environnement DEMO ou PM2 touché.
- Aucune convention activée ; aucun flux de tiers payant CNAM/mutuelles activé.
- Aucune source historique ou archive originale écrasée.

---
**Fin du rapport v0.25.**
