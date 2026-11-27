# PHARM'OS — Audit documentaire global v0.23
**Date :** 27/11/2026  
**Périmètre :** réconciliation de la conception v1.2.1 avec la matrice PO v1.3.3 ; contrôle des mentions D40 et du conflit D37/D44.  
**Nature :** addendum documentaire et contrôle de cohérence ; aucune modification applicative.

## 1. Résultat exécutif

Un décalage historique subsistait dans `CONCEPTION_PHARMOS_v1.2.1.md` : le dossier conservait « 48 h proposés » pour D40, alors que le PO a arbitré D40 le 10/11/2026. Les compteurs de décisions de la v1.2.1 (16 arbitrées / 63 proposées) étaient également antérieurs à l'arbitrage D40.

Pour ne pas réécrire une version historique, un addendum daté a été créé et indexé dans `01_CONCEPTION/README.md`. Il donne la lecture courante de D40, rappelle la priorité de la matrice PO v1.3.3 et explicite le statut toujours ouvert de D37/D44.

**AUD-057 est clos pour la clarification de la lecture courante** : le texte historique reste intact, mais son décalage est désormais explicitement réconcilié par un addendum accessible depuis l'index de conception.

## 2. D40 — durée des réservations de reliquats

La matrice PO v1.3.3 établit :
- réservation sans avance ;
- expiration par défaut après **deux jours comptables**, et non 48 heures calendaires ;
- durée configurable par l'administrateur ;
- échéance individuelle modifiable par un utilisateur autorisé, avec traçabilité ;
- notification avant expiration ;
- aucune annulation silencieuse ni confiscation automatique ;
- réservation logique, cohérente avec D76.

Les mentions « 48 h proposés » du §9.2 et du §17.1 de la v1.2.1 sont donc historiques et supplantées. L'addendum ne définit pas de calcul supplémentaire du « jour comptable ».

## 3. D37 / D44 — conflit conservé ouvert

Le contrôle croisé confirme :
- D37 reste **À RÉEXAMINER (INC-06)** dans la matrice PO v1.3.3 ;
- D44 reste **PROPOSÉE (INC-06)** ; l'option B est une proposition de périmètre, pas un arbitrage ;
- EXT03 interdit de déduire une activation du tiers payant CNAM, mutuelle ou convention tant que D37 n'est pas arbitrée ;
- l'en-tête d'EXT04 et sa ligne D44 indiquent également que l'option B ne permet aucune activation CNAM avant arbitrage PO.

Les documents sont cohérents sur la restriction opérationnelle actuelle, mais le conflit de périmètre entre D37 et D44 demeure non tranché. **INC-06 reste ouvert.** Aucune option n'est choisie à la place du PO et aucune activation n'est autorisée.

## 4. Fraîcheur des compteurs

Les chiffres de la v1.2.1 sont historiques au 04/11/2026. La matrice PO v1.3.3 demeure la source courante :
- 17 arbitrées (D3, D40, D72 à D86) ;
- 62 proposées ;
- 5 à réexaminer ;
- 2 caduques.

Aucun compteur historique n'a été remplacé dans la v1.2.1.

## 5. Livrable et contrôles

- Addendum créé : `01_CONCEPTION/ADDENDUM_CONCEPTION_v1.2.1_MATRICE_PO_v1.3.3_20261127.md`.
- Index `01_CONCEPTION/README.md` mis à jour pour pointer vers l'addendum.
- Matrice PO v1.3.3 relue ; ligne D40, D37, D44 et compteurs confirmés.
- EXT03 et EXT04 relues sur leurs déclarations D37/D44 et sur l'absence d'autorisation d'activation CNAM.
- Aucune réécriture de la conception v1.2.1 ou des sources archivées.

## 6. Points encore ouverts / besoin du PO

1. **INC-06 / D37–D44 :** un arbitrage PO ciblé reste nécessaire pour trancher le périmètre futur du tiers payant. Aucun besoin d'intervention immédiate tant que le périmètre reste bloqué.
2. Les règles réglementaires et conventionnelles R23–R33 d'EXT04 doivent être validées sur sources officielles avant toute mise en service du lot concerné.
3. La v1.2.1 reste un document historique non promu comme conception active ; sa réconciliation complète avec P81–P91 et EXT02–EXT06 reste à effectuer.

## 7. Garanties de périmètre

- Matrice PO v1.3.3 inchangée ; aucun arbitrage créé, modifié ou rouvert.
- D37, D44 et INC-06 conservés ouverts.
- Conception v1.2.1 et archive originale non modifiées.
- Aucun code, schéma déployé, test, migration, déploiement, compte, droit applicatif, DEMO ou service système inspecté ou modifié.
- Aucun développement ni aucune intervention technique autorisés par ce rapport.

---
**Fin du rapport v0.23.**
