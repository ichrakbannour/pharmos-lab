# PHARM'OS — Audit documentaire global v0.11

**Date :** 27/11/2026  
**Complément de :** audit documentaire global v0.10.  
**Périmètre :** conception consolidée v1.2.1, EXT02, EXT04, EXT05, EXT06 v1.1 et matrice PO D1–D86 v1.3.3.  
**Nature :** vérification de cohérence interdocuments. Aucun code, aucune migration, aucun test, aucune opération système.

## 1. Résultat principal

La conception consolidée v1.2.1 contient déjà une traduction détaillée de D74 et reprend les règles D75. Elle ne doit donc pas être décrite comme dépourvue de solution documentaire sur ces deux décisions.

En revanche, cette consolidation n'a pas été propagée dans les extensions sources :
- EXT05 conserve la permission et la matrice de rôles historiques ;
- EXT06 v1.1 conserve une mention « Point à arbitrer (D74) » devenue obsolète ;
- EXT05 ne reprend pas le dispositif détaillé D75, qui figure dans EXT06 et la conception consolidée.

Un nouveau défaut interne est également établi : la conception v1.2.1 présente INC-06 comme « tranché » dans son plan du lot 7b, alors que ses sections 9.3 et 18 maintiennent D37 à réexaminer et D44 proposée. La matrice PO v1.3.3 confirme que l'arbitrage n'est pas clos.

## 2. D74 — Solution de conception déjà présente, extensions sources non alignées

### 2.1 Conception v1.2.1 §6.8 et §6.10

La section 6.8 porte le titre « Réintégration corrective [DÉCIDÉ D74] » et reprend la règle PO : un second collaborateur ne peut réintégrer que si le pharmacien administrateur lui a explicitement accordé cette autorisation.

La traduction proposée dans cette section :
- crée la permission dédiée stock.return.reintegrate pour la décision REINTEGRE ;
- interdit son attribution par défaut à un rôle ;
- permet un octroi nominatif par réglage individuel ou délégation de l'administrateur ;
- exige que le collaborateur qui réintègre soit différent de celui qui a exécuté l'opération corrective ;
- distingue cette permission de stock.return.decide, conservée pour QUARANTAINE et PERTE ;
- maintient l'exclusion des produits réglementés.

La section 6.10 confirme que stock.return.reintegrate n'est attribuée à aucun rôle par défaut. La section 7.3 reprend cette permission dans le modèle consolidé et prévoit la traçabilité de l'autorisation et de la personne ayant réintégré.

### 2.2 Réserve sur le niveau de validation

La règle PO D74 est arbitrée. Toutefois, la « traduction proposée » de la section 6.8 est explicitement marquée RECO / interprétation AV / INC-10. Le principe est donc repris, mais certains choix de modélisation restent présentés comme une interprétation de conception. Il ne faut pas transformer cette traduction en décision PO supplémentaire.

### 2.3 Conséquence sur l'audit v0.10

AUD-052 et AUD-053 sont précisés :
- le conflit est réel dans EXT05 et EXT06 ;
- la conception v1.2.1 contient déjà la correction documentaire cible ;
- le travail restant est l'alignement contrôlé des extensions sources sur cette conception, sans réintroduire d'attribution par rôle ;
- les tests ANN-18 à ANN-21 et ST-02 sont à vérifier contre la permission dédiée et les règles de séparation des personnes.

Aucune modification de EXT05 ou EXT06 n'a été effectuée.

## 3. D75 — Consolidation présente dans EXT06 et la conception v1.2.1

La conception v1.2.1 reprend D75 à plusieurs endroits :
- §8.5 : le flux correctif distingue le jour de référence et la dérogation administrateur ;
- §9.4 : jour calendaire par défaut et option jour d'exploitation activée explicitement par l'administrateur, statut DÉCIDÉ ;
- §12 : MFA au moment des décisions sensibles, dont l'option jour d'exploitation ;
- EXT06 v1.1 §3.16 et AN9–AN13 : fuseau de l'officine, horodatage serveur, variantes GARDES/PERMANENTE, motif, MFA, date d'effet non rétroactive, versionnement, journalisation, notification et conservation de la règle appliquée.

La réserve AUD-054 du v0.10 est donc précisée : la conception consolidée et EXT06 portent déjà les règles détaillées ; c'est EXT05 qui reste à aligner. La date de référence d'annulation ne doit pas être confondue avec la date comptable ou le rattachement des métriques.

## 4. INC-06 — Contradiction interne dans la conception v1.2.1

### 4.1 Mentions contradictoires

- **§9.3** : le tiers payant est décrit comme modèle complet livré, conventions inactives par défaut, mais le statut demeure « proposé » avec conflit D6/D37 et INC-06.
- **§18** : D37 reste dans la liste « À RÉEXAMINER » ; D44 reste dans la liste « PROPOSÉE ».
- **§16.2, ligne du lot 7b** : M18 est conditionné à la validation d'EXT04, mais la dépendance indique « INC-06 tranché ».

La matrice PO v1.3.3 maintient D37 à réexaminer (INC-06) et D44 proposée (INC-06). Aucun arbitrage de clôture n'a été identifié dans les sources vérifiées.

### 4.2 Conclusion

La mention « INC-06 tranché » au §16.2 est incompatible avec les statuts D37/D44 affichés dans la même conception et dans la matrice PO de référence.

Correction documentaire recommandée : remplacer cette mention par « INC-06 ouvert — lot 7b conditionné à l'arbitrage de D37/D44 », jusqu'à décision PO explicite. Cette correction ne préjuge pas du choix final et n'autorise aucune activation de convention.

## 5. Registre des constats mis à jour

| ID | Constat | Mise à jour v0.11 |
|---|---|---|
| AUD-052 | EXT05 attribue stock.return.decide par défaut à plusieurs rôles, contrairement à D74 | La conception v1.2.1 fournit déjà une cible documentaire : stock.return.reintegrate, nominative, jamais par défaut |
| AUD-053 | EXT06 conserve « Point à arbitrer (D74) » | Statut périmé confirmé ; la conception v1.2.1 porte déjà D74 comme décidé |
| AUD-054 | D75 insuffisamment propagée dans EXT05 | EXT06 et conception v1.2.1 contiennent les garde-fous ; alignement EXT05 restant |
| AUD-055 | D37 et D44 non arbitrées | INC-06 demeure ouvert dans la matrice v1.3.3 |
| **AUD-056** | La conception v1.2.1 indique « INC-06 tranché » au §16.2 alors que §9.3, §18 et la matrice v1.3.3 le maintiennent ouvert | **Nouvelle contradiction interne ; correction documentaire recommandée, sans arbitrage présumé** |

## 6. Actions documentaires recommandées

1. Aligner EXT05 §5.1–5.2 sur la permission stock.return.reintegrate et sur l'absence d'attribution par défaut, conformément à la conception v1.2.1 §6.8, §6.10 et §7.3.
2. Remplacer dans EXT06 v1.1 la mention « Point à arbitrer (D74) » par une référence à D74 arbitrée ; conserver le caractère proposé de la traduction technique lorsqu'il s'agit d'un choix non arbitré.
3. Répercuter dans EXT05 les règles D75 d'EXT06 §3.16 et AN9–AN13, sans dupliquer ni affaiblir les garde-fous.
4. Corriger dans la conception v1.2.1 §16.2 la mention « INC-06 tranché » en « INC-06 ouvert », en attendant l'arbitrage PO.
5. Conserver le lot 7b conditionné à la validation d'EXT04 et à la clôture explicite d'INC-06.

Ces actions sont des recommandations d'alignement documentaire. Elles ne sont pas exécutées dans ce rapport.

## 7. Limites

- Vérification documentaire uniquement, sur les versions présentes dans le dépôt au 27/11/2026.
- La matrice source v1.3.3 est utilisée pour les statuts PO.
- Les formulations RECO / AV de la conception ne sont pas élevées au rang d'arbitrages.
- Aucun code, test exécuté, prototype, migration, déploiement ou comportement DEMO n'a été vérifié.
- Aucun fichier source n'a été modifié ; aucun arbitrage nouveau n'a été créé.

## 8. Sources vérifiées

- 01_CONCEPTION/CONCEPTION_PHARMOS_v1.2.1.md — §§6.5–6.10, 7.3, 8.5, 9.3–9.4, 12, 16.2, 18.
- 02_EXTENSIONS/EXT02_RH_TEAM_MANAGEMENT.md — §§1.1–1.2 et catalogue de permissions.
- 02_EXTENSIONS/EXT04_CNAM_CONVENTIONS_ASSURANCES.md — statut et §10.
- 02_EXTENSIONS/EXT05_ANNULATIONS_RETOURS_DELEGATIONS.md — §§2.3, 4.1–4.3, 5.1–5.3.
- 02_EXTENSIONS/EXT06_CATALOGUE_GOUVERNANCE_FINANCE_v1.1.md — §§0, 3.5, 3.16, 5.2, 8.2.
- 07_SOURCES_ORIGINALES/EXPORT_SOURCES_AUDIT_PHARMOS_20261127/1_MATRICE_D1_D86/MATRICE_DECISIONS_D1_D86_v1.3.3.md — D37, D44, D74, D75.
