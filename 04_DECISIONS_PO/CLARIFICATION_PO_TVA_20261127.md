# PHARM'OS — Clarification du Product Owner : champ et taux de TVA

**Date :** 27/11/2026  
**Objet :** présence du champ TVA, valeur initiale et modification par produit  
**Portée :** clarification fonctionnelle d'une règle déjà prévue ; aucune nouvelle décision D87.

## Décision PO clarifiée

Le champ TVA doit exister dès l'origine sur la fiche de chaque produit et être pris en compte dans le mécanisme de vente.

Pour faciliter les opérations quotidiennes, la valeur initialement préremplie est **7 %**. Cette valeur est un défaut opérationnel, pas une affirmation selon laquelle tous les médicaments ou produits relèvent légalement de 7 %.

L'**ADMIN-PHARMACIEN titulaire** peut modifier le taux **pour chaque produit**, en fonction de sa nature fiscale et des règles applicables. La valeur **0 % est expressément admise** et ne doit jamais être confondue avec une absence de valeur ou une donnée manquante.

Une modification législative ou réglementaire, notamment une Loi de finances, peut conduire l'administrateur à actualiser les taux concernés. Les modifications doivent être historisées avec leur période d'effet, leur auteur et, lorsqu'elle est disponible, leur source justificative. Le taux effectivement appliqué doit être figé sur chaque ligne de vente afin que les opérations passées ne soient pas recalculées rétroactivement.

Aucun autre rôle ne dispose du droit de modifier les taux TVA.

## Conséquences documentaires

- EXT06 §4.5 formalise le champ présent dès l'origine, la valeur initiale de 7 %, la modification par produit par l'ADMIN-PHARMACIEN titulaire, l'acceptation de 0 %, l'historisation et le gel du taux sur chaque vente.
- Le modèle fiscal doit distinguer une valeur à 0 % d'une valeur absente ; la détermination du taux doit tenir compte de la règle explicite du produit en vigueur.
- La valeur initiale de 7 % ne remplace pas la classification fiscale ni la vérification de l'assujettissement légal de chaque catégorie.
- R44 reste une réserve de validation réglementaire/comptable : cette clarification fonctionnelle ne tranche pas l'interprétation fiscale applicable aux médicaments, dispositifs, produits de parapharmacie, services, importations, timbre ou facturation électronique.
- Cette clarification n'ajoute, ne réouvre et ne modifie aucun identifiant D1–D86. Elle ne crée pas D87.

## Limites d'autorisation

Cette publication est documentaire. Elle n'autorise aucune modification de code, schéma, base de données, migration, test, déploiement ou environnement DEMO. La réalisation devra être autorisée séparément et respecter les contrôles d'accès et l'historisation décrits ci-dessus.
