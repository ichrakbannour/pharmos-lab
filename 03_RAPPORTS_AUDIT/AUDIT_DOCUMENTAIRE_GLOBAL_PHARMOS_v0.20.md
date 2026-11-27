# PHARM'OS — Audit documentaire global v0.20
**Date :** 27/11/2026  
**Périmètre :** EXT06 v1.0 canonique — réconciliation de D72 à D86 avec la matrice PO v1.3.3.  
**Nature :** audit documentaire et mise à jour de la conception canonique. Aucun changement applicatif.

## 1. Résultat exécutif

La version canonique EXT06 v1.0 (`02_EXTENSIONS/EXT06_CATALOGUE_GOUVERNANCE_FINANCE (1).md`) contenait encore un avertissement indiquant que la matrice PO v1.3.3 n'avait pas été relue. Son registre §11 conservait plusieurs formulations historiques qui ne reflétaient pas les arbitrages PO actuels.

La matrice v1.3.3 a été relue et une nouvelle section **§11.1 — Réconciliation avec la matrice PO v1.3.3** a été ajoutée à EXT06 v1.0. Le registre historique §11 n'a pas été effacé ni réécrit : il reste conservé comme trace de la proposition du 03/11/2026. La section §11.1 indique explicitement que la matrice PO prévaut.

Trois écarts de contenu sont particulièrement importants :
- **D72 :** le registre historique décrit une procédure de secours du pharmacien-administrateur, alors que l'arbitrage PO porte sur le SUPERADMIN APEX immuable et sa gouvernance globale.
- **D77 :** le registre historique fonde l'accueil sur le rôle seul, alors que le PO exige le rôle et les permissions effectives.
- **D84 :** la formulation historique ne reprend pas complètement la classification thérapeutique standard initiale, modifiable par l'administrateur et distincte des données réglementaires officielles.

## 2. Sources contrôlées

- `02_EXTENSIONS/EXT06_CATALOGUE_GOUVERNANCE_FINANCE (1).md` — version 1.0 canonique, §§0, 5.2, 8.2, 11 et plan des lots.
- `02_EXTENSIONS/EXT06_CATALOGUE_GOUVERNANCE_FINANCE_v1.1.md` — copie historique récupérée, non substituée à la v1.0.
- `04_DECISIONS_PO/MATRICE_DECISIONS_D1_D86_v1.3.3.md` — décisions D72 à D86.
- `02_EXTENSIONS/EXT02_RH_TEAM_MANAGEMENT.md` et `02_EXTENSIONS/EXT05_ANNULATIONS_RETOURS_DELEGATIONS.md` — modèle des droits effectifs et D74 corrigée.
- Rapports globaux v0.16 à v0.19.

## 3. Mise à jour effectuée dans EXT06 v1.0

### 3.1 En-tête
L'avertissement disant que la matrice v1.3.3 n'avait pas été relue a été remplacé. L'en-tête indique désormais que :
- le registre §11 reste historique ;
- les statuts et arbitrages courants sont repris au §11.1 ;
- la matrice PO v1.3.3 prévaut en cas d'écart ;
- les validations réglementaires R42–R51 restent à traiter indépendamment.

### 3.2 Réconciliation D72–D86
La nouvelle section §11.1 reprend le statut PO et confronte chaque décision à EXT06 :
- D72 : contradiction historique identifiée ;
- D73 : garanties complètes à propager ;
- D74 : alignement avec l'octroi explicite, en cohérence avec EXT05 corrigée ;
- D75 : garanties de paramétrage à préserver intégralement ;
- D76 : réservation logique distincte du stock physique ;
- D77 : contradiction historique sur le rôle seul ;
- D78 : portée fonctionnelle et confidentialité à préserver ;
- D79 : permission marge à vérifier jusqu'à G25 ;
- D80 : libellé exact, immutabilité et contre-passation à préserver ;
- D81, D82, D83, D85, D86 : principes de l'arbitrage à conserver ;
- D84 : propagation incomplète de la classification standard et de sa séparation du réglementaire.

Le tableau historique §11 n'a pas été supprimé : la correction préserve la traçabilité et évite de faire passer une modification récente pour le contenu original du 03/11.

## 4. Statuts des constats

| Sujet | État |
|---|---|
| EXT06 — avertissement matrice v1.3.3 non relue | **Clos** : lecture effectuée et §11.1 ajouté |
| D72 — confusion secours pharmacien / SUPERADMIN APEX | **Écart explicité** ; consolidation du texte principal encore requise |
| D77 — rôle seul au lieu de rôle + permissions effectives | **Écart explicité** ; consolidation requise |
| D84 — classification thérapeutique | **Écart explicité** ; consolidation requise |
| D73/D75/D79/D80 — propagation de garanties | **À vérifier en consolidation** |
| EXT06 v1.1 historique | Conservée comme copie historique ; non réécrite |
| R42–R51 | Validations réglementaires toujours à établir |

## 5. Suite documentaire recommandée

La prochaine version consolidée d'EXT06 devra intégrer les formulations PO actuelles dans les sections fonctionnelles et le plan des lots, pas seulement dans le registre de réconciliation :
1. reconstruire D72 à partir de la matrice PO, en séparant toute procédure de secours locale non arbitrée ;
2. corriger l'accueil D77 pour rôle + permissions effectives ;
3. compléter D84 ;
4. vérifier les garanties complètes D73, D75, D79 et D80 ;
5. réconcilier les prérequis de lots avec les décisions et validations réglementaires réellement ouvertes.

Cette suite est documentaire. Aucun développement n'est autorisé par ce rapport.

## 6. Garanties et limites

- Seule EXT06 v1.0 canonique a été mise à jour, par ajout d'une section de réconciliation et correction de son en-tête.
- La matrice PO v1.3.3, EXT06 v1.1 historique et les sources originales n'ont pas été modifiées.
- Aucun arbitrage PO n'a été créé, réouvert ou modifié.
- Aucun code, API, schéma de données, compte, permission effective applicative, test, état DEMO ou service système n'a été inspecté ou modifié.
- Aucune migration, opération système ou déploiement n'a été exécuté.

---
**Fin du rapport v0.20.**
