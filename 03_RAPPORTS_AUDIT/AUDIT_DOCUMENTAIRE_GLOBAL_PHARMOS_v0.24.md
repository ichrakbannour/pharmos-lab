# PHARM'OS — Audit documentaire global v0.24

**Date :** 27/11/2026  
**Périmètre :** enregistrement et contrôle de l'arbitrage PO de l'option B sur INC-06 (D6 / D37 / D44).  
**Nature :** audit documentaire et mise à jour de la traçabilité PO ; aucune modification applicative.

## 1. Résultat exécutif

Le PO a confirmé explicitement retenir **l'option B** pour INC-06. Le conflit de périmètre entre D6, D37 et D44 est donc arbitré au niveau métier :

- modèle complet organismes/conventions prévu par l'option B, à réaliser dans le périmètre v1 limité au remboursement par l'assuré et aux conventions privées ;
- aucune convention active par défaut ;
- remboursement par l'assuré inclus ;
- tiers payant CNAM et mutuelles exclu du périmètre v1 ;
- toute activation CNAM ultérieure nécessite la vérification des textes, conventions, avenants et modalités officielles applicables, puis une validation explicite et distincte du PO.

**INC-06 est clos côté arbitrage du périmètre.** Il n'est pas clos sur les preuves réglementaires/conventionnelles ni sur la réalisation technique.

## 2. Livrables documentaires

1. Addendum de décision PO : 04_DECISIONS_PO/INC-06_ARBITRAGE_PO_OPTION_B_20261127.md.
2. Nouvelle matrice courante : 04_DECISIONS_PO/MATRICE_DECISIONS_D1_D86_v1.3.4.md.
3. En-tête de gouvernance d'EXT04 mis à jour pour refléter l'option B arbitrée, sans réécriture de son corps technique de proposition.
4. Index des décisions PO, index des rapports et README racine mis à jour pour pointer vers les nouveaux documents.

La matrice source v1.3.3 n'est pas écrasée. Le miroir des 42 sources originales n'est pas modifié.

## 3. Effet sur la matrice D1–D86

- D6 passe de **À RÉEXAMINER** à **ARBITRÉE**.
- D37 passe de **À RÉEXAMINER** à **ARBITRÉE**.
- D44 passe de **PROPOSÉE** à **ARBITRÉE — option B**.
- D45 à D58 restent proposées : l'option B ne vaut pas approbation globale de toutes les propositions d'EXT04.

Compteurs contrôlés : **20 arbitrées, 61 proposées, 3 à réexaminer, 2 caduques** ; total 86.

## 4. Portée fonctionnelle retenue

Le modèle cible couvre les organismes payeurs, les conventions versionnées et suspendables, les règles de prise en charge documentées et les justificatifs ; sa réalisation technique n'est pas effectuée ni autorisée. Le périmètre v1 est limité au remboursement par l'assuré et aux conventions privées. Aucune convention n'est active par défaut.

Le tiers payant CNAM et mutuelles est exclu du périmètre v1 ; le tiers payant CNAM demeure verrouillé. Les deux conditions d'une activation ultérieure sont cumulatives :
1. vérification des documents officiels réellement applicables à la date envisagée ;
2. validation explicite et distincte du PO après cette vérification.

Les annonces du SPOT et les articles de presse ne constituent pas une décision officielle de la CNAM. R23–R33 et les autres points réglementaires d'EXT04 restent à instruire.

## 5. Contrôles de cohérence

- D6, D37 et D44 ont été mis à jour ensemble ; aucune de ces lignes ne maintient le conflit antérieur.
- D45–D58 restent proposés et ne sont pas promus implicitement.
- La règle « aucune convention active par défaut » est maintenue.
- Le remboursement par l'assuré ne crée pas de créance tiers payant pour la pharmacie.
- Aucune activation CNAM n'est permise sur la seule base de la présente décision.
- La matrice source v1.3.3 et les sources archivées ne sont pas écrasées.
- Seul l'en-tête de gouvernance d'EXT04 est actualisé ; le corps de la proposition technique reste non validé et non autorisé à la réalisation.

## 6. Points restant ouverts

1. **R23–R33 et exigences réglementaires/conventionnelles :** obtenir et vérifier les documents officiels applicables (CNAM, textes, conventions, avenants, procédures et modalités).
2. **D45–D58 :** arbitrages détaillés restant à effectuer ; ne pas les considérer validés du fait de l'option B.
3. **Réalisation technique :** spécifications détaillées, tests et preuves d'application à établir dans une mission distincte, après autorisation du PO.
4. **Activation CNAM :** interdite jusqu'à satisfaction des deux conditions cumulatives et décision explicite distincte du PO.

## 7. Garanties de périmètre

- Aucune modification de code, schéma, base de données ou test applicatif.
- Aucune migration, aucun déploiement, aucune modification de service ou d'environnement DEMO.
- Aucun flux tiers payant CNAM activé ou autorisé.
- Aucun avis juridique ou réglementaire externe présenté comme acquis.

---
**Fin du rapport v0.24.**
