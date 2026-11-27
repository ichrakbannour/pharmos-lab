# PHARM'OS — Audit documentaire global v0.18
**Date :** 27/11/2026  
**Périmètre :** EXT02 — moteur de droits effectifs, fraîcheur des références PO ; cohérence avec EXT05 corrigée et matrice D1–D86 v1.3.3.  
**Nature :** audit documentaire et corrections ciblées de cohérence. Aucun changement applicatif.

## 1. Résultat exécutif

Deux écarts documentaires ont été corrigés dans EXT02 :

1. **Référence PO obsolète :** l'en-tête citait encore la matrice v1.2.1. Il référence désormais la matrice canonique v1.3.3 et indique correctement que D17–D26 demeurent proposées, D26 explicitement ouverte.
2. **Modèle incomplet des droits effectifs :** l'ancien moteur ne testait que l'attribution par rôle, alors que les extensions prévoient également des octrois individuels (`permission_overrides`) et des délégations temporaires. Il est désormais décrit comme une source de droit distincte, suivie de contrôles cumulatifs d'habilitation, qualification, statut, présence, portée, plafonds, séparation des tâches et validations métier.

Cette correction aligne le principe général d'EXT02 avec la règle D74 et la correction d'EXT05 : une permission réservée à l'octroi individuel explicite ne peut pas être accordée par une valeur par défaut de rôle. Un octroi individuel ne contourne aucune condition obligatoire.

## 2. Sources contrôlées

- `02_EXTENSIONS/EXT02_RH_TEAM_MANAGEMENT.md`, en-tête, §1.1–1.2, §2.2 et intégrations.
- `02_EXTENSIONS/EXT05_ANNULATIONS_RETOURS_DELEGATIONS.md`, §§2.6, 5.1–5.3, matrice par rôle.
- `04_DECISIONS_PO/MATRICE_DECISIONS_D1_D86_v1.3.3.md`, D17–D26 et D74–D75.
- `02_EXTENSIONS/README.md` et rapports v0.16–v0.17.

## 3. Corrections apportées à EXT02

### 3.1 En-tête et fraîcheur de la gouvernance

L'en-tête ne renvoie plus à la matrice D1–D86 v1.2.1. Il référence désormais v1.3.3 et précise :
- D17 à D26 restent proposées/non arbitrées ;
- D26 est explicitement ouverte ;
- les valeurs réglementaires et paramètres `[À VALIDER]` restent à confirmer ;
- l'extension de conception n'autorise aucun développement.

Le statut de conception est également référencé à la conception canonique v1.2.1, avec intégration à réconcilier.

### 3.2 Moteur de droits effectifs

Le §1.2 distingue désormais :
- permission accordée par rôle (`role_permissions`) ;
- octroi individuel explicite (`permission_overrides`) ;
- délégation temporaire valide selon les règles de l'extension concernée.

Le moteur applique ensuite les conditions cumulatives : habilitation active, qualifications prérequises, statut actif, politique de présence, portée, plafonds, séparation des tâches et validations requises.

Le texte précise que :
- les permissions réservées à un octroi individuel ne peuvent jamais être accordées par rôle ;
- un octroi individuel ne contourne pas les habilitations, qualifications ou autres contrôles obligatoires ;
- les changements d'octrois individuels et de délégations invalident le cache de droits.

### 3.3 Référentiel des permissions

Le §2.2 précise désormais que `role_permissions` reste le référentiel des permissions par rôle, tandis que les octrois/retraits individuels relèvent du mécanisme `permission_overrides` défini par les extensions qui l'utilisent. Les délégations temporaires restent régies par leur extension.

## 4. Cohérence avec D74 et EXT05

EXT05 a été corrigée en v0.17 :
- `stock.return.decide` n'est attribuée par défaut à aucun rôle ;
- l'octroi individuel explicite par le pharmacien-administrateur est requis ;
- l'habilitation métier seule ne suffit pas ;
- la matrice des rôles ne confère plus le droit aux rôles Titulaire, Adjoint ou Achats/stock par défaut.

EXT02 §1.2 fournit maintenant le modèle général permettant de représenter ce droit individuel sans le réintroduire implicitement par le rôle.

**AUD-062 est clos pour les documents EXT02/EXT05 contrôlés.** Cela ne vaut pas vérification des permissions réellement appliquées dans l'application.

## 5. Limites et points non arbitrés

- D17–D26 restent proposés selon la matrice PO v1.3.3 ; aucune décision n'est créée par ce rapport.
- Les paramètres réglementaires et droit du travail [À VALIDER] restent à confirmer.
- La cohérence du schéma technique réel, des API et des droits effectifs en production n'a pas été vérifiée.
- Les délégations temporaires restent soumises aux règles spécifiques de leurs extensions ; aucune règle nouvelle n'est créée ici.
- INC-06 / D37–D44, AUD-055 et AUD-056 restent ouverts.
- AUD-063 et AUD-064 restent à traiter lors de la prochaine consolidation canonique d'EXT06.

## 6. Registre de suivi

| Référence | État |
|---|---|
| AUD-062 — permission D74 attribuée par défaut | **Clos documentairement** : EXT05 corrigée et moteur EXT02 réconcilié. |
| EXT02 — référence de matrice v1.2.1 obsolète | **Corrigée** : référence v1.3.3 vérifiée. |
| EXT02 — modèle des sources de droits incomplet | **Corrigé dans le document** : rôle, octroi individuel, délégation et contrôles cumulatifs explicités. |
| AUD-063 — D74 encore « proposée » dans EXT06 v1.1 historique | **Ouvert** : à corriger dans la prochaine version canonique d'EXT06. |
| AUD-064 — renvoi D75 | **À harmoniser** lors de la consolidation EXT05/EXT06. |
| INC-06 / AUD-055 / AUD-056 | **Ouverts**. |

## 7. Garanties

- Seuls les documents canoniques EXT02 et EXT05 ont été corrigés.
- La matrice PO v1.3.3, les sources originales, les archives, EXT06 v1.1 historique et son rapport de revue n'ont pas été modifiés.
- Aucun code, API, schéma de base de données, compte, droit effectif applicatif, test, état DEMO ou service système n'a été inspecté ou modifié.
- Aucune migration, opération système ou déploiement n'a été exécuté.
- Aucun arbitrage PO nouveau n'a été inventé.

---
**Fin du rapport v0.18.**
