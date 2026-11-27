# PHARM'OS — Audit documentaire global v0.10

**Date :** 27/11/2026  
**Périmètre :** EXT05 (annulations, retours, délégations), EXT06 v1.1 (gouvernance et annulations), EXT04 (tiers payant), matrice PO D1–D86 v1.3.3.  
**Nature :** audit documentaire comparatif. Aucun code, aucune migration, aucun test, aucune opération système.

## 1. Verdict exécutif

**EXT05 et EXT04 ne sont pas prêtes à être considérées comme consolidées.** La comparaison avec les arbitrages PO fait apparaître :
- une contradiction directe sur l'attribution de la permission de réintégration corrective (stock.return.decide) au regard de D74 ;
- un statut documentaire périmé dans EXT06, qui continue à présenter D74 comme un point à arbitrer alors que la matrice v1.3.3 l'enregistre comme arbitrée ;
- une propagation incomplète des règles D75 d'EXT06 vers EXT05 ;
- une divergence de périmètre non résolue entre D37 (EXT03) et D44 (EXT04), toujours identifiée INC-06 dans la matrice.

Aucune de ces observations ne constitue une nouvelle décision PO. Les propositions ci-dessous sont des corrections documentaires à préparer, pas des arbitrages.

## 2. D74 — Réintégration corrective : contradiction à corriger

### 2.1 Arbitrage PO applicable

La matrice v1.3.3 indique que le second collaborateur ne peut effectuer la réintégration d'une opération corrective **que si l'administrateur lui a explicitement accordé cette autorisation**. La permission dédiée doit être **non attribuée par défaut à un rôle**. Le statut D74 est ARBITRÉE.

### 2.2 Contradictions relevées

**EXT05 §5.1 — Catalogue des permissions**
- stock.return.decide est marquée délégable et plafonnable.
- La formulation ne pose pas, à elle seule, l'exigence d'une attribution explicite et nominative par l'administrateur.

**EXT05 §5.2 — Matrice par rôle**
- La permission apparaît accordée par défaut au Titulaire et à l'Adjoint.
- Elle apparaît également accordée au rôle Achats/stock pour les produits non réglementés.
- Cette attribution par rôle contredit l'exigence D74 si elle permet à un collaborateur d'agir sans octroi explicite de l'administrateur.

**EXT06 v1.1 — §0 et §5.2**
- EXT06 identifie elle-même l'attribution par défaut d'EXT05 comme un écart à D74.
- Le §5.2 propose une « décision immédiate simplifiée » par un second collaborateur habilité.
- Le texte conserve ensuite un intitulé « Point à arbitrer (D74) », alors que D74 est déjà arbitrée dans la matrice v1.3.3.

### 2.3 Conclusion et correction documentaire recommandée

- Faire prévaloir le libellé PO D74.
- Retirer toute attribution automatique de la permission de réintégration corrective au seul motif du rôle.
- Décrire une permission dédiée, accordée explicitement par l'administrateur au collaborateur concerné ; conserver une trace de l'octroi, de sa portée et de sa révocation.
- Réexaminer la délégabilité de cette permission : aucune délégation générique ne doit contourner l'exigence d'octroi explicite de D74.
- Remplacer dans EXT06 « Point à arbitrer (D74) » par une référence à l'arbitrage déjà rendu, sans élargir celui-ci.
- Aligner les tests ANN-18 à ANN-21 et ST-02 sur l'absence de droit par défaut et sur l'octroi explicite.

**Réserve :** le PO n'a pas, dans la ligne D74 de la matrice, détaillé le modèle technique d'octroi (override permanent ou autre mécanisme). Ne pas choisir ce mécanisme à sa place.

## 3. D75 — Jour de référence des annulations : décision présente, propagation incomplète

### 3.1 Arbitrage PO et contenu EXT06

La matrice v1.3.3 confirme :
- jour calendaire par défaut ;
- option « jour d'exploitation » activée explicitement par l'administrateur ;
- documentation, versionnement et absence de changement silencieux.

EXT06 v1.1 formalise utilement cette décision :
- fuseau horaire de l'officine et horodatage serveur ;
- variante GARDES ou PERMANENTE ;
- motif obligatoire et référence à la décision interne ;
- MFA de l'administrateur ;
- prise d'effet au plus tôt au début du prochain jour de référence, sans rétroactivité ;
- paramètre versionné, journalisation avant/après et notification des collaborateurs concernés ;
- enregistrement de la règle et de sa version sur chaque opération corrective.

### 3.2 Écart dans EXT05

EXT05 §2.3 et §4.1/§4.3 mentionne le jour calendaire et renvoie à D75, mais ne reprend pas le dispositif complet de paramétrage et de traçabilité défini dans EXT06 v1.1. EXT05 reste une proposition v1.0 et son texte ne doit pas être réputé automatiquement amendé par la seule présence de règles dans EXT06.

### 3.3 Conclusion et correction documentaire recommandée

Ajouter à EXT05 un renvoi normatif explicite vers les règles D75 d'EXT06, puis répercuter dans le workflow et les tests :
- détermination par horodatage serveur et fuseau de l'officine ;
- activation exclusivement par l'administrateur, avec motif et MFA ;
- GARDES ou PERMANENTE, selon la configuration prévue ;
- effet non rétroactif, versionnement et journalisation ;
- conservation de la règle appliquée et de sa version sur l'opération corrective.

Ne pas confondre le jour de référence de l'annulation avec la date comptable ou le rattachement des métriques : EXT06 précise que ces derniers restent déterminés indépendamment.

## 4. D37 / D44 — Périmètre du tiers payant : INC-06 reste ouvert

### 4.1 Sources comparées

- **D37**, dans la matrice v1.3.3 : tiers payant CNAM/mutuelles « reporté, compatibilité préservée », statut **À RÉEXAMINER (INC-06)**.
- **D44**, dans la même matrice : recommandation B — modèle complet livré, aucune convention active par défaut ; statut **PROPOSÉE (INC-06)**.
- **EXT04 v1.0** : décrit un module M18 complet, mais pose le principe qu'aucune règle de prise en charge n'est active sans source officielle ou convention signée et validation du titulaire. Son §10 recommande l'option B.
- L'en-tête d'EXT04 indique encore que les arbitrages D44–D58 ne sont pas globalement approuvés et que D37 reste à réexaminer.

### 4.2 Analyse

Les deux formulations pourraient être compatibles si « reporté » désigne l'activation opérationnelle du tiers payant, tandis que « modèle complet livré » désigne la disponibilité architecturale du module avec toutes les conventions inactives par défaut. Mais cette lecture est une hypothèse de réconciliation, pas une décision documentée.

La matrice maintient explicitement D37 en réexamen et D44 en proposition. Il serait donc incorrect de déclarer le périmètre tiers payant arbitré ou de présenter l'option B comme une décision PO acquise.

### 4.3 Conclusion

- Maintenir INC-06 ouvert.
- Ne pas activer de convention ni déduire une autorisation de tiers payant de la seule présence du modèle M18.
- Faire préciser si D37 signifie report du déploiement opérationnel seulement, ou report du module lui-même, et comment cette portée s'articule avec D44.
- Après arbitrage, mettre à jour la matrice et l'en-tête/statut d'EXT04 de manière cohérente.

## 5. Tableau des écarts et actions documentaires

| ID | Constat | Source principale | État | Action recommandée |
|---|---|---|---|---|
| AUD-052 | stock.return.decide attribuée par défaut à plusieurs rôles, contrairement à D74 | EXT05 §5.1–5.2 ; matrice v1.3.3 D74 | Ouvert — contradiction | Corriger la matrice des rôles et formaliser l'octroi explicite |
| AUD-053 | EXT06 conserve « Point à arbitrer (D74) » malgré D74 arbitrée | EXT06 §5.2 ; matrice v1.3.3 D74 | Ouvert — statut périmé | Remplacer par une référence à l'arbitrage PO |
| AUD-054 | Les règles complètes D75 d'EXT06 ne sont pas répercutées dans EXT05 | EXT05 §2.3, §4.1, §4.3 ; EXT06 §3.16, AN9–AN13 | Ouvert — propagation | Aligner EXT05 et ses tests sur le dispositif D75 |
| AUD-055 | D37 reste à réexaminer tandis que D44 demeure proposée | EXT03/matrice v1.3.3 D37 ; EXT04 §10/D44 | Ouvert — arbitrage requis | Maintenir INC-06 et demander une clarification PO |

## 6. Limites et statut

- Audit limité aux documents versionnés visibles dans le dépôt au 27/11/2026.
- La matrice D1–D86 v1.3.3 du dossier des sources originales a été utilisée comme référence de statut PO ; la copie canonique versionnée à la racine du dossier Décisions PO est plus ancienne et ne doit pas être confondue avec cette source.
- Aucun code ni comportement applicatif n'a été inspecté ou testé dans cette mission.
- Aucun test cité dans les extensions n'est considéré exécuté faute de preuve d'exécution consultée.
- Aucune extension source n'a été modifiée.
- Aucun arbitrage PO nouveau n'a été créé ou supposé.
- Aucune migration, activation du tiers payant, modification DEMO, opération PM2 ou intervention système n'a été effectuée.

## 7. Sources vérifiées

- 02_EXTENSIONS/EXT05_ANNULATIONS_RETOURS_DELEGATIONS.md — §§2.3, 4.1–4.3, 5.1–5.3.
- 02_EXTENSIONS/EXT06_CATALOGUE_GOUVERNANCE_FINANCE_v1.1.md — §§0, 3.5, 3.16, 5.2, 8.2.
- 02_EXTENSIONS/EXT04_CNAM_CONVENTIONS_ASSURANCES.md — en-tête, principe de désactivation par défaut, §10 (D44).
- 07_SOURCES_ORIGINALES/EXPORT_SOURCES_AUDIT_PHARMOS_20261127/1_MATRICE_D1_D86/MATRICE_DECISIONS_D1_D86_v1.3.3.md — D37, D44, D74, D75 et synthèse des statuts.
