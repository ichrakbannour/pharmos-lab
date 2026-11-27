# PHARM'OS — Audit documentaire global v0.16
**Date :** 27/11/2026  
**Périmètre :** EXT05 / EXT06 — D74 (réintégration corrective), D75 (jour de référence des annulations), cohérence avec la matrice PO D1–D86 v1.3.3.  
**Nature :** contrôle documentaire en lecture seule.

## 1. Conclusion exécutive

**D74 est arbitrée par le PO, mais sa propagation documentaire reste contradictoire et incomplète.** La matrice PO v1.3.3 fixe une règle sans ambiguïté : un second collaborateur ne peut effectuer la réintégration corrective que si l'administrateur lui a explicitement accordé cette autorisation. Le droit dédié ne doit pas être attribué par défaut à un rôle.

Or EXT05 conserve encore, dans sa matrice de permissions par défaut, `stock.return.decide` pour le titulaire, l'adjoint et le rôle Achats/stock. EXT06 v1.1 ne résout pas cette contradiction : il décrit une décision immédiate par un second collaborateur habilité, conserve D74 comme « point à arbitrer » dans le corps du texte, et garde D74 parmi les décisions préalables au Lot 5. Son tableau de compatibilité qualifie pourtant les amendements EXT05 de « cohérents ».

**D75 est mieux propagée dans EXT06 v1.1** : règle calendaire par défaut, option explicite GARDES/PERMANENTE, motif, MFA, date d'effet non rétroactive, versionnement et journalisation sont documentés. La propagation vers EXT05 reste toutefois répartie entre deux extensions : EXT05 décrit surtout la règle calendaire, tandis que W16 et AN9–AN13 d'EXT06 portent l'option. Cette répartition doit être rendue explicite lors de la consolidation, sans modifier l'arbitrage.

## 2. Sources primaires contrôlées

1. `04_DECISIONS_PO/MATRICE_DECISIONS_D1_D86_v1.3.3.md`, lignes D74 et D75.
2. `02_EXTENSIONS/EXT05_ANNULATIONS_RETOURS_DELEGATIONS.md`, §§ 2.6, 4.1, 5.1 et 5.2.
3. `02_EXTENSIONS/EXT06_CATALOGUE_GOUVERNANCE_FINANCE_v1.1.md`, §§ 3.16, 6, AN3–AN13, §8.2, plan des lots et tableau des décisions.
4. `03_RAPPORTS_AUDIT/EXT06_v1.1_RAPPORT_DE_REVUE.md`, §§ 3, 8.2, 11 et 12.
5. Rapports d'audit globaux v0.10 et v0.11, pour l'historique des réserves D74/D75.

## 3. D74 — arbitrage PO contre matrice de permissions EXT05

### 3.1 Décision PO en vigueur

La matrice v1.3.3, D74, indique : « Un second collaborateur peut effectuer la réintégration d'une opération corrective uniquement si l'administrateur lui a explicitement accordé cette autorisation ». La même ligne précise que la proposition EXT06 d'une décision par second collaborateur était seulement partiellement compatible et que le droit doit être dédié, sans attribution par défaut à un rôle.

Cette décision est arbitrée. Elle prévaut sur les propositions antérieures des extensions.

### 3.2 Contradiction encore présente dans EXT05

EXT05 §5.1 décrit `stock.return.decide` comme délégable et plafonnable, sans préciser que l'octroi doit être explicite par l'administrateur.

Surtout, EXT05 §5.2 annonce une matrice de « valeurs par défaut » et attribue `stock.return.decide` :
- au Titulaire ;
- à l'Adjoint ;
- au rôle Achats/stock pour les produits non réglementés.

Cette attribution par rôle est incompatible avec D74. Le fait que les valeurs soient dites « configurables » ne corrige pas le défaut : la permission apparaît déjà accordée par la matrice standard, sans décision individuelle explicite de l'administrateur.

EXT05 §2.6 parle d'un « collaborateur habilité » ; cette formulation doit également être reliée explicitement à l'autorisation dédiée accordée par l'administrateur, et non à la seule appartenance à un rôle ou à une habilitation métier.

### 3.3 EXT06 v1.1 n'a pas fermé l'écart

Trois éléments empêchent de considérer l'alignement comme acquis :

- §3.16 et AN6 suppriment la réintégration directe et imposent un contrôle, mais le texte de D74 (§ AN8 et commentaire qui suit) continue de proposer une décision immédiate par un second collaborateur « habilité », sans exiger explicitement l'octroi nominatif du droit par l'administrateur.
- Le tableau de décisions d'EXT06 conserve D74 au statut « PROPOSÉE » ; le plan des prérequis indique encore « décisions D74, D77 » avant le Lot 5.
- Le §8.2 affirme que `DISPONIBLE_DIRECT` est « supprimé ou remplacé (D74) » et conclut à la cohérence des amendements EXT05, alors que la matrice EXT05 §5.2 conserve les valeurs par défaut incompatibles.

Le rapport de revue EXT06 v1.1 conserve lui aussi D74 comme « Proposée » et décrit une décision par un second collaborateur. Ce statut est désormais obsolète au regard de la matrice PO v1.3.3.

### 3.4 Conséquence documentaire

Il faut aligner EXT05, EXT06 et le rapport de revue sur la règle PO, sans réinterpréter D74 :
1. retirer toute attribution par défaut de `stock.return.decide` liée aux rôles ;
2. définir l'octroi explicite par l'administrateur, au collaborateur identifié, avec traçabilité ;
3. conserver les conditions distinctes de qualification, de séparation des tâches, de produits réglementés et de plafonds ;
4. remplacer les mentions « D74 à arbitrer / proposée » par une référence à la décision PO arbitrée ;
5. reprendre le statut de cohérence EXT05 dans EXT06 après correction effective, et non avant.

Aucune de ces corrections n'a été effectuée dans cette mission.

## 4. D75 — propagation de la règle du jour de référence

La matrice PO v1.3.3, D75, arbitre :
- jour calendaire par défaut ;
- option « jour d'exploitation » activée explicitement par l'administrateur ;
- documentation et versionnement ;
- aucune modification silencieuse.

EXT06 v1.1 apporte une spécification opérationnelle substantielle :
- W16 : paramètres de l'officine, variantes GARDES et PERMANENTE ;
- motif et référence de décision interne ;
- MFA de l'administrateur ;
- date d'effet au plus tôt au début du prochain jour de référence ;
- paramètre versionné dans `settings`, journalisation avant/après et notification ;
- AN9–AN13 : calcul à partir de l'horodatage serveur et du fuseau de l'officine, non-rétroactivité, interdiction de modification par import, migration, autre rôle ou délégation.

EXT05 §2.3 et ses tableaux continuent à présenter principalement le jour calendaire. Ils renvoient à D75 mais n'exposent pas eux-mêmes le détail de l'option d'exploitation. Ce n'est pas une décision contradictoire si EXT06 est explicitement la spécification complémentaire ; c'est toutefois une dépendance documentaire à rendre visible dans la version consolidée d'EXT05.

Le rapport de revue EXT06 v1.1 classe D75 « RETENUE », ce qui correspond à la matrice. Les prérequis réglementaires R49 restent distincts et ouverts : la décision fonctionnelle D75 ne vaut pas validation juridique ou fiscale.

## 5. Registre des constats

| Référence | Constat | Statut |
|---|---|---|
| AUD-062 | EXT05 §5.2 attribue par défaut `stock.return.decide` à plusieurs rôles, contrairement à D74 arbitrée | **Ouvert — contradiction confirmée** |
| AUD-063 | EXT06 v1.1 maintient D74 « proposée / à arbitrer » et la liste comme prérequis du Lot 5, malgré l'arbitrage PO v1.3.3 | **Ouvert — statut documentaire obsolète** |
| AUD-064 | Le détail opérationnel D75 est dans EXT06/W16/AN9–AN13 mais sa référence dans EXT05 reste partielle | **À harmoniser lors de la consolidation** |

Les constats antérieurs AUD-047 à AUD-051, AUD-055 et AUD-056 ne sont pas clos par ce rapport. INC-06 (tiers payant) reste indépendant et ouvert, conformément au rapport v0.15.

## 6. Actions documentaires recommandées

1. Corriger EXT05 §5.1–§5.2 et §2.6 pour supprimer l'attribution par défaut du droit de réintégration et exiger l'octroi explicite par l'administrateur.
2. Corriger EXT06 v1.1 : D74 doit être présenté comme arbitrée selon la matrice PO ; supprimer les mentions « point à arbitrer », « proposée » et « décision préalable » qui laissent croire qu'un nouvel arbitrage est nécessaire.
3. Réviser la conclusion de cohérence EXT06 §8.2 après alignement effectif d'EXT05.
4. Ajouter dans EXT05 un renvoi normatif clair vers W16 et AN9–AN13 d'EXT06 pour le détail de D75, sans recopier deux règles concurrentes.
5. Mettre à jour le rapport de revue EXT06 v1.1 comme document historique ou publier un addendum daté ; ne pas réécrire silencieusement son constat d'origine.

Ces actions sont des recommandations documentaires. Elles ne constituent pas une autorisation d'implémenter le droit, de modifier une API, une base de données ou le comportement applicatif.

## 7. Limites et garanties

- Contrôle limité aux documents et versions listés, présents dans le dépôt au 27/11/2026.
- La matrice PO v1.3.3 est la référence de statut et de contenu pour D74/D75.
- Aucun arbitrage PO nouveau n'a été créé ou supposé.
- Aucune extension, conception, matrice ou source originale n'a été modifiée.
- Aucun code, test exécuté, comportement applicatif ou état DEMO n'a été inspecté.
- Aucune migration, opération système, activation de permission ou déploiement n'a été lancé.

---
**Fin du rapport v0.16.**
