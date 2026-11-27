# PHARM'OS — Audit documentaire global v0.32

**Date :** 27/11/2026  
**Périmètre :** contrôle de cohérence interne de la matrice PO D1–D86 après arbitrage INC-06 option B et correction de son tableau de contrôle.  
**Nature :** audit documentaire uniquement. Aucun code, schéma, base de données, test, migration, déploiement ou environnement DEMO modifié.

## 1. Anomalie détectée

La matrice PO publiée après l'arbitrage du 27/11/2026 présentait deux états incompatibles :

- la synthèse, le tableau des décisions D1–D86 et l'annotation de l'arbitrage INC-06 indiquaient **20 arbitrées, 61 proposées, 3 à réexaminer et 2 caduques** ;
- le tableau « 9. Contrôles de cette matrice » conservait une ligne héritée de la version antérieure : **17 arbitrées**, limitée à D3, D40 et D72 à D86, et indiquant qu'aucune autre décision n'était arbitrée.

Cette ligne n'avait pas été actualisée après l'arbitrage PO de D6, D37 et D44. Elle rendait la matrice contradictoire avec ses propres lignes et sa synthèse.

## 2. Vérification du décompte courant

Le décompte est cohérent avec les statuts détaillés :

- D3 et D40 : arbitrages antérieurs maintenus ;
- D6, D37 et D44 : arbitrées par le PO le 27/11/2026 dans le cadre de l'option B INC-06 ;
- D72 à D86 : 15 décisions arbitrées.

Soit **20 décisions arbitrées**. Les autres compteurs restent ceux de la synthèse : **61 proposées, 3 à réexaminer, 2 caduques**, total 86.

Aucun statut individuel D1–D86 n'a été changé par ce correctif. Aucun numéro D87 n'a été créé.

## 3. Correctif documentaire

Une version corrective **MATRICE_DECISIONS_D1_D86_v1.3.5.md** a été publiée.

Le §9 « Contrôles de cette matrice » indique désormais :
- les 20 décisions arbitrées : D3, D6, D37, D40, D44 et D72 à D86 ;
- la concordance avec la synthèse et l'arbitrage INC-06 consigné au §8 quaterdecies ;
- le maintien des dates d'arbitrage antérieures de D3 et D40.

Les index suivants ont été actualisés pour pointer vers la matrice v1.3.5 :
- README racine ;
- `04_DECISIONS_PO/README.md` ;
- `02_EXTENSIONS/README.md`.

Les décomptes présents dans les annotations historiques des versions antérieures n'ont pas été réécrits : ils décrivent leurs états datés respectifs.

## 4. Contrôles et limites

| Contrôle | Résultat |
|---|---|
| Concordance des compteurs courants | Corrigée : 20 / 61 / 3 / 2 |
| Liste des décisions arbitrées dans le §9 | Corrigée |
| Statuts individuels D1–D86 modifiés | Non |
| Numéro D87 créé | Non |
| Références des index principaux | Actualisées vers v1.3.5 |
| Code / schéma / base de données modifiés | Non |
| Tests / migrations exécutés | Non |
| Déploiement / DEMO / système touché | Non |

**Conclusion :** l'incohérence de décompte dans le contrôle de la matrice est corrigée au niveau documentaire et les index principaux pointent vers la version corrective. Les décisions métier et les réserves réglementaires existantes ne sont pas modifiées par cet audit.
