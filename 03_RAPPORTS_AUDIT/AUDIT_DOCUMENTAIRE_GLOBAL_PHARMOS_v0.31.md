# PHARM'OS — Audit documentaire global v0.31

**Date :** 27/11/2026  
**Périmètre :** clarification PO TVA et propagation documentaire dans EXT06 et les index du dépôt.  
**Nature :** audit et mise à jour documentaire uniquement. Aucun code, schéma, base de données, test, migration, déploiement ou environnement DEMO modifié.

## 1. Clarification PO TVA publiée

Le PO a confirmé le 27/11/2026 que :

- le champ TVA doit exister dès l'origine sur chaque fiche produit et dans le mécanisme de vente ;
- la valeur initiale préremplie est **7 %**, pour faciliter les opérations ;
- l'ADMIN-PHARMACIEN titulaire peut modifier le taux produit par produit selon la nature fiscale du produit ;
- **0 % est une valeur valide** ;
- les changements réglementaires, notamment issus d'une Loi de finances, peuvent conduire à actualiser les taux ;
- les changements sont historisés et le taux effectivement appliqué est figé sur chaque ligne de vente.

Cette règle est une valeur initiale fonctionnelle et un droit de paramétrage, pas une affirmation que 7 % s'applique légalement à tous les médicaments ou produits. Aucun autre rôle ne peut modifier le taux.

La clarification est publiée séparément dans `04_DECISIONS_PO/CLARIFICATION_PO_TVA_20261127.md`. Elle n'ajoute pas D87 et ne réécrit pas la matrice D1–D86 v1.3.4.

## 2. Propagation dans EXT06

EXT06 §4.5 a été corrigée :
- retrait de la phrase « Aucun taux fourni par défaut » ;
- valeur initiale 7 % (700 points de base) ;
- possibilité d'un taux individuel produit, y compris 0 % ;
- autorité de modification réservée à l'ADMIN-PHARMACIEN titulaire ;
- historisation des périodes, de l'auteur et de la source justificative lorsqu'elle existe ;
- gel du taux appliqué sur chaque ligne de vente ;
- règle de priorité du taux explicite du produit en vigueur, sinon valeur initiale préremplie ;
- clarification qu'une évolution générale ne doit pas écraser silencieusement les ajustements individuels.

Le paragraphe de principes et la ligne R44 ont été alignés. Le modèle proposé de `tax_rates` mentionne désormais `product_id` pour représenter un taux propre au produit. Ce point est une exigence documentaire à vérifier lors d'une réalisation autorisée, pas une migration exécutée.

## 3. Réserve réglementaire R44 maintenue

Le taux initial de 7 % ne détermine pas à lui seul le traitement légal de chaque produit. Les éléments relevés dans v0.30 concernant les textes tunisiens et l'exclusion signalée pour les ventes de médicaments et produits pharmaceutiques au stade du gros et du détail restent à rapprocher de la classification de chaque produit et à confirmer avec un expert-comptable ou l'administration fiscale.

Les produits non médicamenteux, dispositifs, accessoires, parapharmacie, cosmétiques, compléments, services, importations, timbre et facturation électronique ne sont pas assimilés automatiquement. La flexibilité du champ ne vaut pas validation juridique de ces traitements.

## 4. Contrôles et limites

| Contrôle | Résultat |
|---|---|
| Clarification PO distincte publiée | Réalisé |
| EXT06 §4.5 et principe fiscal alignés | Réalisé |
| Réserve R44 maintenue | Réalisé |
| Matrice D1–D86 modifiée ou D87 créé | Non — aucune modification |
| README concernés actualisés | Réalisé dans le cadre de cette publication |
| Code / schéma / base de données modifiés | Non |
| Tests ou migrations exécutés | Non |
| Déploiement / DEMO / système touché | Non |

La conformité applicative, le calcul effectif de 0 %, l'autorisation exclusive côté serveur, l'historisation et le gel du taux sur les ventes restent à démontrer dans une mission technique autorisée.

## 5. Fichiers concernés

- `02_EXTENSIONS/EXT06_CATALOGUE_GOUVERNANCE_FINANCE (1).md`
- `04_DECISIONS_PO/CLARIFICATION_PO_TVA_20261127.md`
- `02_EXTENSIONS/README.md`
- `03_RAPPORTS_AUDIT/README.md`
- `04_DECISIONS_PO/README.md`
- `README.md`

**Conclusion :** la règle fonctionnelle TVA demandée par le PO est désormais formalisée dans EXT06 et accompagnée d'une clarification PO autonome. R44 demeure une réserve fiscale à confirmer ; aucune réalisation logicielle n'a été effectuée ni autorisée par cette intervention.
