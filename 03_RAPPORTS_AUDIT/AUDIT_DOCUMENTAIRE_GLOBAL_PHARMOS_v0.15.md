# PHARM'OS — Audit documentaire global v0.15
**Date :** 27/11/2026  
**Périmètre :** EXT04 / tiers payant — D37, D44, INC-06 ; cohérence avec la conception v1.2.1 et la matrice PO v1.3.3.  
**Nature :** contrôle documentaire en lecture seule.

## 1. Conclusion exécutive

**INC-06 reste ouvert. Aucun arbitrage PO de clôture n'est identifié dans les sources contrôlées.** La matrice D1–D86 v1.3.3 maintient D37 « À RÉEXAMINER (INC-06) » et D44 « PROPOSÉE (INC-06) ». Le tiers payant ne peut donc pas être déclaré arbitré, autorisé ou activable sur la seule base d'EXT04.

Le conflit est désormais précisé : D37 recommande explicitement l'absence de fonctionnalité de tiers payant en v1, avec compatibilité architecturale préservée ; l'option B proposée pour D44 décrit un périmètre v1 limité au remboursement par l'assuré et aux conventions privées, mais ajoute que le tiers payant CNAM serait « activable par paramétrage ». Cette dernière possibilité n'est pas conciliée avec le report exprimé par D37.

## 2. Sources primaires contrôlées

1. `07_SOURCES_ORIGINALES/EXPORT_SOURCES_AUDIT_PHARMOS_20261127/3_EXT03_v1.1_ET_AUDIT/AUDIT_EXT03_v1.1/02_GRILLE_DECISIONS_D27_D40.md`, § D37.
2. `02_EXTENSIONS/EXT04_CNAM_CONVENTIONS_ASSURANCES.md`, en-tête, §§ 0, 1.1, 10 et 11.
3. `04_DECISIONS_PO/MATRICE_DECISIONS_D1_D86_v1.3.3.md`, synthèse et lignes D6, D37, D44.
4. `01_CONCEPTION/CONCEPTION_PHARMOS_v1.2.1.md`, §§ 9.3, 16.2 et 18.
5. Rapports d'audit v0.10 et v0.11, pour l'historique du constat AUD-055/AUD-056.

## 3. Comparaison précise D37 / D44

### 3.1 D37 — proposition issue d'EXT03 v1.1

La grille d'arbitrage EXT03 décrit D37 « Tiers payant (CNAM, mutuelles) ». Sa recommandation B est : **reporté, architecture compatible**. Elle précise « aucune fonctionnalité en v1 », avec conservation de points de compatibilité (lien vente/ordonnance, identifiant assuré chiffré, organisme comme tiers distinct, grand livre multi-tiers et migration additive ultérieure).

La grille porte encore la mention « soumis à validation du propriétaire » et la case « Décision retenue » de D37 n'est pas cochée. La matrice v1.3.3 conserve D37 « À RÉEXAMINER (INC-06) ».

### 3.2 D44 — proposition issue d'EXT04 v1.0

EXT04 §10 pose la question du périmètre du module à la mise en service, compte tenu de la suspension du tiers payant CNAM. L'option B recommandée est formulée ainsi : v1 limitée au remboursement par l'assuré et aux conventions privées, **tiers payant CNAM activable par paramétrage** ; le modèle complet est livré, sans convention active par défaut.

La matrice v1.3.3 maintient D44 « PROPOSÉE (INC-06) ». D44 n'est donc pas une autorisation PO. La désactivation initiale des conventions ne répond pas, à elle seule, à la question de savoir si une activation CNAM par simple paramétrage est permise dans le périmètre v1.

### 3.3 D6 — conflit de contexte

La matrice v1.3.3 maintient également D6 « À RÉEXAMINER (INC-06) » et indique explicitement son conflit avec D44. La résolution ne peut donc pas être limitée à une lecture isolée de D37 et D44 : le périmètre de mise en service doit rester cohérent avec D6.

## 4. Portée réelle d'EXT04

EXT04 est explicitement versionnée « 1.0 (proposition) » et son en-tête précise que les arbitrages D44–D58 ne sont pas globalement approuvés et que D37 reste à réexaminer. Le principe de sécurité fonctionnelle (§§ 0 et 1.1) impose qu'aucune règle de prise en charge ne soit active sans source officielle ou convention signée et validation du titulaire.

Ce garde-fou est nécessaire, mais **il ne tranche pas le périmètre fonctionnel autorisé en v1**. Une convention inactive par défaut peut être compatible avec plusieurs options de périmètre ; elle ne résout pas le désaccord entre « aucune fonctionnalité en v1 » (D37) et « CNAM activable par paramétrage » (option B de D44).

Le §11 d'EXT04 liste les prérequis du lot 7b, notamment les décisions D44, D46, D48 et D53 ainsi que des exigences réglementaires. Cette liste de prérequis ne vaut ni arbitrage de ces décisions, ni autorisation d'exécuter le lot.

## 5. Contradiction persistante dans la conception v1.2.1

La conception v1.2.1 contient toujours deux indications incompatibles :
- §16.2, ligne « 7b. Tiers payant » : dépendance « INC-06 tranché » ;
- §§9.3 et 18 : conflit avec D6/D37, D37 à réexaminer, D44 proposée ; la conception qualifie le sujet de non arbitré.

La matrice PO v1.3.3 confirme les statuts ouverts. La mention « INC-06 tranché » au §16.2 demeure donc erronée et correspond au constat **AUD-056** déjà enregistré dans le rapport v0.11. Aucune correction de la conception n'a été effectuée dans cette mission.

## 6. Arbitrage PO nécessaire pour fermer INC-06

L'arbitrage devra préciser explicitement, sans laisser la portée implicite :

1. **Périmètre v1 :** le tiers payant est-il totalement absent en v1, ou le module M18 est-il livré avec remboursement par l'assuré et conventions privées uniquement ?
2. **CNAM :** l'activation par paramétrage proposée dans D44 est-elle interdite en v1 conformément à D37, ou autorisée sous conditions précises ?
3. **Mutuelles :** leur périmètre suit-il le report de D37 ou une règle distincte ?
4. **Portée architecturale :** quels éléments de compatibilité peuvent être préparés sans constituer une fonctionnalité de tiers payant disponible en v1 ?

Après décision, la matrice PO devra être mise à jour par le PO ou selon son arbitrage explicite ; EXT03, EXT04 et la conception devront ensuite être alignées. D'ici là, D37 reste à réexaminer, D44 reste proposée, INC-06 reste ouvert et le lot 7b reste conditionné.

## 7. Registre des constats

| Référence | Constat | Statut après ce contrôle |
|---|---|---|
| AUD-055 | D37 et D44 non arbitrées ; périmètre tiers payant non résolu | **Ouvert — précisé** : opposition entre absence de fonctionnalité en v1 et activation CNAM par paramétrage |
| AUD-056 | La conception v1.2.1 §16.2 indique « INC-06 tranché » alors que §§9.3/18 et la matrice le maintiennent ouvert | **Ouvert — confirmé** |

Aucun nouveau numéro d'anomalie n'est créé : les deux constats existants couvrent le problème et sa contradiction documentaire.

## 8. Limites et garanties

- Contrôle limité aux documents et versions listés ci-dessus, présents dans le dépôt au 27/11/2026.
- Aucun arbitrage PO nouveau n'a été créé ou supposé.
- Aucune extension, conception ou matrice source n'a été modifiée.
- Aucun code, test exécuté, comportement applicatif ou état DEMO n'a été inspecté.
- Aucune convention n'a été activée ; aucun lot, migration ou déploiement n'a été lancé.
- Ce rapport est un constat documentaire, pas une autorisation d'implémentation.

---
**Fin du rapport v0.15.**