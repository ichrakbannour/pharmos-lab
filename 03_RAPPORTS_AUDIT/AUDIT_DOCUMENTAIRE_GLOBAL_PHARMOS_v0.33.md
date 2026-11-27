# PHARM'OS — Audit documentaire global v0.33

**Date :** 27/11/2026  
**Périmètre :** réconciliation des références à la matrice PO courante dans les extensions et le référentiel de conception ; inventaire de la documentation d'architecture et des prompts.  
**Nature :** audit documentaire et corrections de références uniquement. Aucun code, schéma, base de données, test, migration, déploiement ou environnement DEMO modifié.

## 1. Anomalie détectée — références de matrice périmées

Après publication de la matrice PO D1–D86 v1.3.5, plusieurs documents actifs continuaient à désigner v1.3.4 comme matrice courante :

- EXT02 — RH & Team Management ;
- EXT03 — Ordonnances et prescripteurs ;
- EXT04 — CNAM, conventions et assurances ;
- EXT05 — Annulations, retours et délégations ;
- EXT06 — Catalogue, gouvernance et finance ;
- README du référentiel de conception, qui mentionnait v1.3.4 dans sa liste des réconciliations à effectuer et ne présentait pas encore l'addendum v1.3.5.

Ces références n'indiquaient pas toutes une contradiction métier : elles créaient néanmoins un risque documentaire, car un lecteur ou un intervenant pouvait appliquer une version PO qui n'est plus la référence courante.

## 2. Corrections effectuées

Les références de matrice courante ont été alignées sur **D1–D86 v1.3.5** dans EXT02, EXT03, EXT04, EXT05 et EXT06.

Le README `01_CONCEPTION/README.md` a été actualisé :
- ajout de l'addendum de réconciliation v1.3.5 ;
- référence à la matrice PO v1.3.5 dans les étapes de réconciliation ;
- maintien du statut historique et non validé dans son ensemble de la conception v1.2.1.

L'addendum `ADDENDUM_CONCEPTION_v1.2.1_MATRICE_PO_v1.3.5_20261127.md` a été créé pour préciser que :
- v1.3.5 corrige le contrôle de cohérence des compteurs de v1.3.4 sans modifier les statuts individuels D1–D86 ;
- l'arbitrage INC-06 option B du 27/11/2026 reste inchangé ;
- EXT06 reste la version canonique v1.0, avec ses références PO courantes alignées ;
- la clarification TVA et la réserve R44 restent distinctes ;
- aucun développement ni aucune opération technique ne sont autorisés par ces corrections.

Les documents historiques datés, notamment l'addendum INC-06 initial qui cite v1.3.4 et l'addendum antérieur v1.3.3, sont conservés sans réécriture.

## 3. Contrôles de cohérence des extensions

Les documents actifs EXT02–EXT06 ont été relus sur leurs statuts principaux :

| Extension | Contrôle documentaire |
|---|---|
| EXT02 | D17–D26 restent proposés/non arbitrés ; D26 explicitement ouverte. |
| EXT03 | D37 arbitrée option B ; D40 arbitrée ; remboursement par l'assuré et conventions privées en v1 ; tiers payant CNAM/mutuelles hors v1. D27–D39 ne sont pas globalement approuvées par l'arbitrage D37. |
| EXT04 | D6, D37 et D44 arbitrées option B ; D45–D58 restent proposées ; R23–R33 et prérequis officiels/conventionnels restent à instruire. |
| EXT05 | D61 et D66 caduques ; D59, D60, D62–D65 et D67–D71 restent proposées ; D74/D75 propagées, dont l'octroi individuel explicite de `stock.return.decide`. |
| EXT06 | Référence PO actualisée ; réconciliation D72–D86 maintenue ; clarification TVA présente ; R44 demeure une réserve réglementaire. |

Ce contrôle est documentaire. Il ne valide pas les exigences réglementaires et ne constitue pas une preuve d'implémentation applicative.

## 4. Lacunes structurelles du dépôt constatées

L'inventaire GitHub du 27/11/2026 montre :

1. `05_ARCHITECTURE/` contient uniquement son README, qui énumère pourtant des sujets attendus : architecture générale, SaaS/On-Premises, isolation des bases, sécurité, modèles de données, sauvegarde/restauration et infrastructure. Aucun livrable détaillé n'est actuellement présent dans ce dossier.
2. `06_PROMPTS_GENSPARK/` contient uniquement son README ; aucun prompt ou historique de mission n'est actuellement présent dans ce dossier.
3. `01_CONCEPTION/` contient un fichier nommé `Documents de conception générale PHARM'OS` de 1 octet, sans contenu textuel exploitable. Il est laissé en place ; aucune suppression n'est effectuée.

Ces constats signifient **absence de livrable dans ces emplacements du dépôt**, et non preuve que ces informations n'existent nulle part ailleurs. Aucun document n'est reconstitué à partir de son seul titre.

## 5. Contrôles et limites

| Contrôle | Résultat |
|---|---|
| Références v1.3.4 restantes dans EXT02–EXT06 actifs | 0 |
| Références courantes v1.3.5 dans EXT02–EXT06 | Présentes |
| README conception aligné sur v1.3.5 | Oui |
| Addendum de réconciliation v1.3.5 publié | Oui |
| Documents historiques réécrits | Non |
| Architecture détaillée présente dans `05_ARCHITECTURE/` | Non, README seul |
| Prompts présents dans `06_PROMPTS_GENSPARK/` | Non, README seul |
| Fichier de 1 octet supprimé | Non |
| Code / schéma / base de données modifiés | Non |
| Tests / migrations exécutés | Non |
| Déploiement / DEMO / système touché | Non |

## 6. Suites documentaires recommandées

- Constituer le référentiel d'architecture à partir de sources identifiées et vérifiables, en distinguant décisions PO, propositions techniques et éléments encore à arbitrer.
- Reconstituer l'index des prompts et missions Genspark uniquement à partir des prompts/rapports réellement disponibles ; ne pas inventer d'historique.
- Continuer la réconciliation des exigences réglementaires R23–R51 avec les sources officielles et les décisions PO, sans transformer une recherche documentaire en validation juridique.

**Conclusion :** les références actives aux décisions PO sont désormais alignées sur v1.3.5 dans EXT02–EXT06 et le README de conception. Les lacunes d'architecture et de prompts sont inventoriées, mais non comblées artificiellement. Les limites de gouvernance et d'autorisation technique restent inchangées.

---
**Fin du rapport v0.33.**
