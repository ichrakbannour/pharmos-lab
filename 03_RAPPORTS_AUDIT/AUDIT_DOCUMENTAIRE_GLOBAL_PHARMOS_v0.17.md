# PHARM'OS — Audit documentaire global v0.17
**Date :** 27/11/2026  
**Périmètre :** clôture documentaire de la propagation D74 dans EXT05 ; statut historique EXT06 v1.1 ; vérification de D75.  
**Nature :** audit documentaire et correction ciblée de la source canonique EXT05. Aucun changement applicatif.

## 1. Résultat exécutif

**La contradiction opérationnelle D74 identifiée en v0.16 a été corrigée dans EXT05 et la correction a été relue sur GitHub.** La permission `stock.return.decide` n'est désormais attribuée par défaut à aucun rôle. Son octroi doit être explicite, individuel, motivé et tracé par le pharmacien-administrateur. La permission distincte applicable aux produits réglementés ne peut pas être obtenue par simple appartenance à un rôle ; les exigences d'habilitation et de contrôle à quatre yeux restent applicables.

EXT06 v1.1 et son rapport de revue du 03/11/2026 sont des documents historiques. Ils ne sont pas réécrits : leur statut ancien « D74 proposée / à arbitrer » est conservé comme trace historique, mais ne peut plus servir de statut courant face à la matrice PO v1.3.3. Le présent rapport fait foi pour le contrôle de propagation documentaire effectué le 27/11/2026.

D75 est cohérente sur le fond dans EXT06 v1.1 : jour calendaire par défaut ; option jour d'exploitation activée explicitement par l'administrateur, avec motif, MFA, date d'effet non rétroactive, versionnement et journalisation. EXT05 renvoie à D75 ; le détail opérationnel reste décrit dans EXT06 W16 / AN9–AN13. Aucun conflit fonctionnel nouveau n'a été constaté sur les extraits contrôlés.

## 2. Référence PO applicable

La matrice canonique `04_DECISIONS_PO/MATRICE_DECISIONS_D1_D86_v1.3.3.md` est la référence :
- **D74 — ARBITRÉE :** un second collaborateur ne peut effectuer la réintégration corrective que si l'administrateur lui a explicitement accordé cette autorisation ; permission dédiée, non attribuée par défaut à un rôle.
- **D75 — ARBITRÉE :** jour calendaire par défaut ; option « jour d'exploitation » explicitement activée par l'administrateur, documentée, versionnée et jamais silencieuse.

Aucun changement de la matrice PO n'a été effectué.

## 3. Correction apportée à EXT05

Fichier canonique modifié : `02_EXTENSIONS/EXT05_ANNULATIONS_RETOURS_DELEGATIONS.md`.

Corrections publiées :
1. §2.6 : le décideur du workflow D est désormais décrit comme un collaborateur identifié auquel le pharmacien-administrateur a explicitement accordé `stock.return.decide`, conformément à D74.
2. §5.1 : la permission est indiquée comme non attribuée par défaut ; son octroi individuel explicite relève exclusivement du pharmacien-administrateur.
3. §5.2 : toutes les cases de la ligne `stock.return.decide` sont retirées de la matrice des valeurs par défaut (Titulaire, Adjoint, Achats/stock inclus).
4. §5.2 : le droit distinct pour les décisions relatives aux produits réglementés n'est pas attribué par défaut à l'Adjoint ; habilitation, autorisation explicite et contrôle à quatre yeux restent requis.
5. §5.3 : la règle d'octroi individuel précise que le droit ne peut être déduit du rôle, d'une habilitation métier ou d'une délégation générique ; l'octroi est motivé et tracé.

La correction ne supprime pas les autres permissions d'annulation, de remboursement ou de consultation et ne modifie pas les plafonds métier. Elle ne change pas le parcours de PERTE défini par P86.

**Vérification après publication :** le fichier a été relu depuis la branche `main`. Les occurrences de `stock.return.decide`, la ligne de matrice par rôle et la règle d'octroi individuel reflètent désormais D74. La matrice PO et le miroir des sources originales n'ont pas été modifiés.

## 4. Traitement d'EXT06 v1.1

Le dépôt identifie `EXT06_CATALOGUE_GOUVERNANCE_FINANCE_v1.1.md` comme une copie historique récupérée, qui ne remplace pas automatiquement la version EXT06 de référence. Son rapport de revue date du 03/11/2026 et précise lui-même que D74 était alors proposée.

Les passages suivants sont donc historiques et obsolètes pour le statut courant :
- §5.2 / AN8 : « Point à arbitrer (D74) » ;
- §8.2 : D74 encore décrite comme « supprimée ou remplacée » ;
- §11 : D74 « PROPOSÉE » ;
- plan des lots : D74 mentionnée parmi les décisions préalables au Lot 5.

Ils ne sont pas réécrits dans la copie historique. Lors de la prochaine consolidation d'EXT06, ils devront être remplacés par une référence à D74 arbitrée dans la matrice PO et à la règle corrigée d'EXT05. La décision D74 n'est pas un nouveau prérequis à arbitrer.

Le rapport de revue EXT06 v1.1 reste lui aussi un document historique daté du 03/11/2026 ; il n'est pas modifié rétroactivement.

## 5. Contrôle D75

Les sections W16 et AN9–AN13 d'EXT06 v1.1 détaillent :
- jour calendaire comme valeur par défaut ;
- option GARDES ou PERMANENTE, explicitement activée par l'administrateur ;
- motif, MFA et date d'effet non rétroactive ;
- paramètre `settings.cancellation.reference_day` versionné ;
- journalisation avant/après, notification et affichage ;
- impossibilité pour un import, une migration, un autre rôle ou une délégation de modifier silencieusement le paramètre ;
- conservation des règles spécifiques aux produits sensibles/réglementés et de l'interdiction de réintégration automatique.

EXT05 renvoie à D75 et enregistre la règle appliquée avec l'opération corrective. Le détail W16 / AN9–AN13 demeure la spécification complémentaire dans EXT06 v1.1. La prochaine consolidation devra préserver ce renvoi et éviter deux définitions concurrentes.

Les références réglementaires R49 et les autres points réglementaires restent à confirmer selon leur propre statut ; l'arbitrage fonctionnel D75 ne vaut pas validation juridique.

## 6. Registre des constats

| Référence | État |
|---|---|
| AUD-062 — valeurs par défaut `stock.return.decide` contraires à D74 dans EXT05 | **Clos pour le périmètre contrôlé** : matrice de rôles corrigée, octroi individuel explicite exigé et vérifié. |
| AUD-063 — statut D74 obsolète dans EXT06 v1.1 historique | **Ouvert pour consolidation** : aucun texte historique réécrit ; mise à jour requise dans la prochaine version canonique d'EXT06. |
| AUD-064 — renvoi D75 EXT05 / détail W16 et AN9–AN13 EXT06 | **À harmoniser lors de la consolidation** ; cohérence fonctionnelle de fond confirmée dans les extraits contrôlés. |
| INC-06 / AUD-055 / AUD-056 | **Toujours ouverts**, indépendants de D74/D75. |

La clôture d'AUD-062 ne signifie pas que le code ou les permissions effectives de l'application ont été vérifiés. Elle concerne exclusivement le document EXT05.

## 7. Suite documentaire

La prochaine consolidation d'EXT06 devra :
1. reprendre la règle D74 arbitrée et le modèle d'octroi explicite désormais inscrit dans EXT05 ;
2. supprimer les statuts « proposée / à arbitrer » de D74 dans la nouvelle version canonique ;
3. conserver la référence complémentaire à W16 et AN9–AN13 pour D75 ;
4. vérifier les autres décisions D72–D86 contre la matrice PO v1.3.3 avant toute présentation comme alignées.

## 8. Limites et garanties

- La correction porte uniquement sur le document canonique EXT05.
- La matrice PO v1.3.3, les archives sources, EXT06 v1.1 historique et son rapport de revue n'ont pas été modifiés.
- Aucun code, API, schéma de base de données, test applicatif, compte, droit effectif ou état DEMO n'a été inspecté ou modifié.
- Aucune migration, opération système ou déploiement n'a été exécuté.
- Aucun arbitrage PO supplémentaire n'a été inventé.

---
**Fin du rapport v0.17.**
