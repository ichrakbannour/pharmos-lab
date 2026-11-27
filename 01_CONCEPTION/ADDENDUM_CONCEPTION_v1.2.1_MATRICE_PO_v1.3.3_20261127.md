# Addendum de réconciliation — conception v1.2.1 et matrice PO v1.3.3

**Date :** 27/11/2026  
**Référence historique :** `CONCEPTION_PHARMOS_v1.2.1.md` (04/11/2026)  
**Référence PO courante :** `04_DECISIONS_PO/MATRICE_DECISIONS_D1_D86_v1.3.3.md` (12/11/2026)  
**Nature :** addendum documentaire de traçabilité ; aucune réécriture de la v1.2.1 et aucun arbitrage nouveau.

## 1. Portée et règle de priorité

La conception v1.2.1 précède plusieurs arbitrages PO ultérieurs. Elle reste une source historique de conception consolidée, non validée dans son ensemble et non promue automatiquement comme conception active.

Pour les décisions D1–D86, la matrice PO v1.3.3 prévaut sur toute formulation antérieure de la v1.2.1. Le présent addendum explicite les écarts datés afin qu'ils ne soient pas repris comme exigences courantes.

## 2. D40 — réservation des reliquats : correction de lecture

### Formulations historiques à ne plus appliquer
La v1.2.1 comporte notamment :
- §9.2 : « 48 h proposés, D40 » et durée marquée « [PROPOSÉ] » ;
- §17.1 : exemple de recommandation « réservation de reliquat de 48 h (D40) ».

Ces formulations reflètent un état antérieur à l'arbitrage PO du 10/11/2026. Elles sont conservées dans le document historique mais ne décrivent plus la règle applicable.

### Règle PO courante
Selon la ligne D40 de la matrice PO v1.3.3 :
- réservation **sans avance** ;
- expiration par défaut après **deux jours comptables**, et non 48 heures calendaires ;
- durée configurable par l'administrateur ;
- échéance individuelle modifiable par un utilisateur autorisé, avec traçabilité ;
- notification avant expiration ;
- aucune annulation silencieuse ni confiscation automatique ;
- réservation logique, cohérente avec D76.

Aucune définition supplémentaire de « jour comptable » n'est inventée par cet addendum : son calcul détaillé ne doit pas être extrapolé au-delà des sources et arbitrages disponibles.

**Statut : D40 ARBITRÉE.** La matrice v1.3.3 est la référence normative ; les mentions « 48 h » de la v1.2.1 sont historiques et supplantées.

## 3. D37 / D44 — tiers payant : conflit conservé ouvert

La matrice PO v1.3.3 confirme :
- **D37 : À RÉEXAMINER (INC-06)** — report du tiers payant CNAM/mutuelles avec compatibilité future ;
- **D44 : PROPOSÉE (INC-06)** — option B proposée : modèle de module complet, conventions inactives par défaut, remboursement par l'assuré utilisable.

La proposition D44 ne vaut pas arbitrage et ne permet pas d'activer le tiers payant CNAM. EXT03 rappelle qu'aucun flux de tiers payant CNAM, mutuelle ou convention ne doit être activé tant que D37 n'a pas été réexaminée et arbitrée. EXT04 porte la même réserve dans son statut et dans la description de D44.

**Aucun choix n'est fait ici entre D37 et D44.** INC-06 reste ouvert et doit être soumis au PO ; aucune activation ni mise en service n'est autorisée par cet addendum.

## 4. Fraîcheur des compteurs de décisions

Les compteurs de la v1.2.1 (§18 : 16 arbitrées, 63 proposées) correspondent à l'état historique du 04/11/2026.

La matrice PO v1.3.3 donne l'état courant suivant :
- **17 arbitrées** : D3, D40 et D72 à D86 ;
- **62 proposées** ;
- **5 à réexaminer** ;
- **2 caduques**.

La v1.2.1 n'est pas réécrite pour remplacer ses compteurs historiques. Pour tout état courant, utiliser exclusivement la matrice PO v1.3.3.

## 5. Garanties

- Aucun contenu de `CONCEPTION_PHARMOS_v1.2.1.md` n'a été modifié.
- Aucun fichier de l'archive originale n'a été modifié.
- Aucun statut PO n'a été créé, modifié ou rouvert.
- D37 / D44 / INC-06 restent ouverts ; aucune décision n'est présumée.
- Aucun code, schéma déployé, test, migration, déploiement ou environnement DEMO n'a été inspecté ou modifié.
- Cet addendum ne constitue pas une validation globale de la conception v1.2.1 et n'autorise aucun développement.

---
**Fin de l'addendum.**
