# PHARM'OS — Audit documentaire global

**Version :** 0.8 — revue approfondie EXT03 v1.1, arbitrages D27–D43 et prérequis de conformité  
**Date :** 27/11/2026  
**Dépôt :** https://github.com/ichrakbannour/pharmos-lab  
**Branche :** `main`  
**Statut :** audit documentaire en cours. Ce rapport n'autorise aucune réalisation technique.

## 1. Périmètre et sources relues

Revue en lecture seule des pièces EXT03 publiées dans le miroir vérifié :

- `EXT03_ORDONNANCES_PRESCRIPTEURS_v1.1.md` ;
- `00_RAPPORT_AUDIT_EXT03.md` ;
- `02_GRILLE_DECISIONS_D27_D40.md` ;
- `03_MODIFICATIONS_CONCEPTION_v1.2.md` ;
- `04_MATRICE_DEPENDANCES.md` ;
- `05_MATRICE_TESTS.md` ;
- `06_POINTS_REGLEMENTAIRES.md` ;
- matrice globale `MATRICE_DECISIONS_D1_D86_v1.3.3.md) ;
- arbitrage PO D40 et décision PO D76, tels que repris dans la matrice globale et les sources stock P82–P91.

Les fichiers du miroir sont des copies exactes des octets de l'archive. Cette identité de copie ne vaut pas approbation de leur contenu.

## 2. Verdict documentaire EXT03

**EXT03 v1.1 reste une proposition documentaire non intégrée et non validée globalement.**

Le rapport d'audit EXT03 du 03/11/2026 décrit une version révisée et une série de corrections importantes, mais sa section « Décisions attendues » demande encore la validation ou l'amendement des décisions D27–D43, puis l'autorisation de remplacer la conception existante et de reporter les modifications vers v1.2.

Le document `03_MODIFICATIONS_CONCEPTION_v1.2.md` confirme explicitement :
- statut : liste proposée, non appliquée ;
- `CONCEPTION_PHARMOS_v1.md` non modifié ;
- report conditionné à la validation des décisions D27–D43 et de plusieurs valeurs réglementaires.

Le dépôt contient EXT03 v1.0 dans `02_EXTENSIONS`, mais la v1.1 et son audit sont publiés dans le miroir des sources originales. Aucun élément consulté ne démontre que la v1.1 a été adoptée comme extension canonique ou intégrée à la conception principale. Il ne faut donc pas la présenter comme « intégrée », « validée » ou « prête à développer ».

## 3. Réconciliation D27–D43 avec la matrice globale v1.3.3

La matrice globale v1.3.3 constitue la référence de statut la plus récente retrouvée pour les décisions D1–D86. Pour le périmètre D27–D43, elle indique :

| Statut | Décisions | Nombre |
|---|---|---:|
| ARBITRÉE | D40 | 1 |
| À RÉEXAMINER | D37 | 1 |
| PROPOSÉES | D27–D36, D38–D39, D41–D43 | 15 |
| **Total** | **D27–D43** | **17** |

La grille EXT03 historique conserve des cases « Décision retenue » vides et indique que rien n'est appliqué au dossier principal tant que ces cases ne sont pas renseignées. Cette présentation est désormais incomplète pour D40 : la décision PO postérieure enregistrée dans la matrice v1.3.3 prévaut. Elle ne permet toutefois pas de considérer les 16 autres décisions comme arbitrées.

### 3.1 D40 — divergence historique à neutraliser

La grille EXT03 propose historiquement une réservation de 48 heures par défaut et une intervention d'un pharmacien pour les produits réglementés.

La matrice v1.3.3 documente une décision PO explicite du 10/11/2026, issue de la v1.2.3 :
- réservation **sans avance** ;
- expiration par défaut après **2 jours comptables**, et non 48 heures calendaires ;
- durée configurable par l'ADMIN ;
- échéance individuelle modifiable par un utilisateur autorisé, avec traçabilité ;
- notification avant expiration ;
- aucune annulation silencieuse ni confiscation automatique ;
- réservation **logique**, cohérente avec D76.

La matrice précise que la clause historique « pharmacien pour les réglementés » n'est pas traitée par cette décision et reste historique. Elle ne doit donc pas être présentée comme une règle PO confirmée. Le comportement exact des réservations de reliquats réglementés demeure à clarifier dans le cadre d'une décision métier, sans réactiver la règle historique par défaut.

Le même écart est présent dans EXT03 v1.1 elle-même : sa section 13.1 résume encore D40 comme « réservation courte (48 h par défaut), jamais pour réglementé sans pharmacien ». La grille D27–D40 reprend également cette proposition. Ces deux formulations sont antérieures à l'arbitrage D40 du 10/11/2026 et ne sont pas synchronisées avec lui. La référence courante est la matrice D1–D86 v1.3.3 ; les textes EXT03 v1.1 et la grille doivent rester identifiés comme historiques tant qu'une consolidation documentaire ultérieure n'est pas autorisée.

**Action documentaire :** toute future consolidation EXT03 devra remplacer la proposition de 48 h par la formulation D40 arbitrée, conserver la distinction jours comptables / heures calendaires et signaler séparément le point réglementé non arbitré. Aucun document source historique n'est réécrit dans le cadre du présent audit.

### 3.2 D37 — dépendance au conflit D44

La matrice v1.3.3 classe D37 « À RÉEXAMINER (INC-06) » en raison du conflit avec D44 dans EXT04 : périmètre du module tiers payant livré et conventions inactives. D37 ne peut donc pas être considérée comme définitivement tranchée au seul motif qu'EXT03 la présente comme « reportée ».

### 3.3 D41–D43 et autres propositions

D41 (OCR), D42 (effet d'un retour sur le droit à délivrance) et D43 (délai de complétion) restent PROPOSÉES dans la matrice v1.3.3. Les recommandations de la grille EXT03 ne sont pas des décisions PO. Même traitement pour D27–D36, D38 et D39.

## 4. Dépendances réglementaires : blocages déclarés par les sources

Le fichier `06_POINTS_REGLEMENTAIRES.md` avertit expressément que PHARM'OS ne présume pas des règles de droit et que ses valeurs de conception sont à confirmer auprès des autorités et conseils compétents, Tunisie d'abord.

Les 22 points R1–R22 sont des questions à confirmer, pas des validations juridiques acquises. Les échéances documentées sont :

| Échéance | Points | Conséquence documentaire |
|---|---|---|
| Avant lot 5b — cœur Ordonnances | R1–R7, R10, R14, R15, R19 | 11 points classés bloquants par la source |
| Avant lot 5c — OCR | R16 | validation requise avant le lot OCR |
| Avant lot 10 — registre réglementés | R17–R18 (et D5 du dossier principal, selon la source) | validation requise avant le registre |
| Avant production | R8, R9, R20–R22 | points à résoudre avant production |
| Futur | R11–R13 | évolutions futures / autres pays |

Aucune preuve de réponse officielle, d'avis juridique signé, d'autorisation INPDP, de validation de l'Ordre des pharmaciens ou de validation DPM n'a été identifiée dans les pièces EXT03 examinées. Le présent audit ne transforme pas l'absence de preuve dans ce corpus en preuve qu'aucune démarche n'a jamais eu lieu ; le statut reste **NON DOCUMENTÉ / NON CONFIRMÉ dans les sources disponibles**.

Conséquence : la conception ne peut pas déclarer les prérequis réglementaires des lots 5b, 5c et 10 satisfaits. Les hypothèses prudentes décrites dans EXT03 restent des garde-fous de conception, pas des avis juridiques.

## 5. Architecture et dépendances fonctionnelles

La matrice de dépendances décrit un couplage fort de M17 avec :
- M02 Vente et M02b Caisse dans le batch atomique ;
- M05 Stock pour FEFO, décrément conditionnel et mouvements ;
- M09 Réglementés pour l'écriture du registre dans le même batch ;
- M15 RH pour les habilitations évaluées côté serveur ;
- M07 Clients pour le cloisonnement N1/N2/N3 ;
- M12 Rapports et M13 IA pour les agrégats, masquages et pseudonymisation ;
- M14 Hors-ligne pour les restrictions et la revalidation serveur.

Cette architecture impose que la validation d'une ordonnance ne soit pas un écran ou un module isolé. Les règles de sécurité et d'atomicité doivent être communes à tous les chemins de vente. La matrice de dépendances décrit un ordre de réalisation, mais ne constitue ni preuve d'implémentation ni autorisation de lancer ces lots.

## 6. Couverture de tests : spécification, pas preuve d'exécution

La matrice EXT03 comporte 14 scénarios d'intégration S01–S14, environ 75 tests complémentaires et des familles dédiées à l'atomicité, l'idempotence, la sécurité, la confidentialité, l'OCR et l'audit.

Constats importants :
- les tests sont au statut de **spécification** ;
- aucun résultat d'exécution, journal CI, rapport de recette ou preuve de test n'est inclus dans ces pièces ;
- `T-LOT0-01` exige un prototype démontrant le mécanisme d'annulation du batch conditionnel avant le lot 5 ;
- l'OCR manuscrit tunisien FR/AR doit être mesuré sur un jeu de 200 ordonnances (O-01), sans en faire un prérequis à la délivrance ;
- les scénarios de sécurité et d'isolation inter-pharmacies sont des critères de recette, pas des contrôles dont la réussite est démontrée.

**Statut de toutes ces vérifications dans le présent audit : NON EXÉCUTÉ / NON PROUVÉ.** Aucun test n'a été lancé.

## 7. Écarts documentaires et actions de consolidation

| ID | Constat | Statut / action |
|---|---|---|
| EXT03-A01 | EXT03 v1.1 et ses modifications restent proposées ; conception principale non mise à jour | Maintenir « NON INTÉGRÉE » jusqu'à arbitrage global et intégration autorisée |
| EXT03-A02 | Grille D27–D43 historique incomplète sur D40 | Ajouter une note de renvoi vers D40 arbitrée dans la matrice v1.3.3 ; ne pas réécrire la source historique |
| EXT03-A03 | D37 reste en conflit avec D44 / INC-06 | Arbitrage PO inter-extensions requis |
| EXT03-A04 | R1–R22 sans preuve de validation externe dans le corpus | Obtenir et archiver les réponses officielles / avis compétents avant les lots concernés |
| EXT03-A05 | T-LOT0-01 et matrice de tests sans preuves d'exécution | Exiger les preuves dans une mission technique ultérieure, après autorisation PO |
| EXT03-A06 | D40 : clause historique « pharmacien pour réglementés » non traitée par l'arbitrage D40 | Ne pas l'appliquer comme décision confirmée ; soumettre une clarification métier ciblée |
| EXT03-A07 | Le rapport EXT03 du 03/11/2026 et la grille n'intègrent pas les arbitrages ultérieurs de la matrice v1.3.3 | Conserver ces pièces comme sources historiques ; utiliser la matrice v1.3.3 pour le statut courant |

## 8. Conclusion

Le corpus permet de confirmer la qualité et l'étendue de la proposition EXT03 v1.1, mais pas son adoption. Son intégration reste suspendue à la validation des décisions encore proposées, au traitement du conflit D37/D44 et à la confirmation des prérequis réglementaires. D40 est la seule décision D27–D43 arbitrée dans la matrice globale v1.3.3 ; sa formulation PO remplace les valeurs historiques de la grille.

La couverture de tests est détaillée mais aucune preuve d'exécution n'est fournie. Le mécanisme de batch conditionnel, les contrôles de sécurité, les parcours OCR et les réconciliations ne sont donc pas considérés comme réalisés ou vérifiés.

## 9. Limites et interdictions

Cette revue est documentaire et en lecture seule. Elle n'autorise :
- aucune modification de code ou de conception principale ;
- aucune migration, aucun test, build, benchmark ou déploiement ;
- aucune activation du module Ordonnances, de l'OCR ou du registre réglementé ;
- aucune modification DEMO, PM2, service ou système ;
- aucune décision PO implicite sur D27–D39, D41–D43, D37/D44 ou sur la clause pharmacien/réglementés de D40.

Toute intégration ou réalisation nécessite les arbitrages et autorisations PO distincts applicables.

---
**Fin du rapport v0.8 — audit documentaire en cours.**
