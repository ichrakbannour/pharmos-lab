# PHARM'OS — Audit documentaire global v0.19
**Date :** 27/11/2026  
**Périmètre :** EXT03 / EXT04 — actualisation de la référence PO, D37 tiers payant, D40 réservation des reliquats et dépendance D44.  
**Nature :** audit documentaire et corrections ciblées des documents canoniques. Aucun changement applicatif.

## 1. Résultat exécutif

Deux extensions ont été corrigées après confrontation à la matrice PO canonique v1.3.3 :

- **EXT03 :** l'en-tête ne présente plus D40 comme une décision encore proposée et ne cite plus la matrice v1.2.1. Il reflète l'arbitrage D40 (aucune avance ; deux jours comptables par défaut ; durée configurable par l'administrateur ; échéance notifiée ; aucune annulation/confiscation silencieuse). D37 est explicitement maintenue « À RÉEXAMINER ».
- **EXT04 :** l'option B de D44 est explicitement marquée comme une proposition non arbitrée. Le texte précise qu'elle ne permet pas d'activer le tiers payant CNAM : D37 doit d'abord être réexaminée et arbitrée par le PO.

Les deux corrections empêchent qu'une proposition technique ou un paramètre configurable soit interprété comme une autorisation métier.

## 2. Références PO contrôlées

La matrice `04_DECISIONS_PO/MATRICE_DECISIONS_D1_D86_v1.3.3.md` reste la référence de statut :
- **D37 : À RÉEXAMINER** ; elle n'autorise pas à considérer le tiers payant comme un périmètre v1 approuvé.
- **D40 : ARBITRÉE** ; réservation de reliquat sans avance, durée par défaut de deux jours comptables, configurable par l'administrateur, avec échéance notifiée et sans annulation ou confiscation silencieuse.
- **D44–D58 : propositions non globalement approuvées** ; l'option B de D44 n'est pas un arbitrage PO.

Aucun statut de la matrice n'a été modifié.

## 3. EXT03 — corrections publiées

Fichier : `02_EXTENSIONS/EXT03_ORDONNANCES_PRESCRIPTEURS.md`.

### 3.1 En-tête
- Référence actualisée vers la matrice v1.3.3.
- D27–D39 restent non globalement approuvées ; D37 est spécifiquement identifiée « À RÉEXAMINER ».
- D40 est explicitement indiquée comme arbitrée, avec renvoi à la règle PO complète.

### 3.2 Finance / tiers payant
Le §6.3 indique maintenant qu'aucun flux de tiers payant CNAM, mutuelle ou convention ne peut être activé en v1 tant que le périmètre D37 n'est pas arbitré. Le modèle peut conserver des points d'extension futurs sans préjuger de la décision.

### 3.3 Tableau d'arbitrages
La ligne D40 n'affiche plus la recommandation historique « réservation courte paramétrable » comme règle suffisante. Elle reproduit les paramètres fonctionnels de l'arbitrage PO et précise que la matrice v1.3.3 prévaut.

## 4. EXT04 — verrou documentaire sur D37 / D44

Fichier : `02_EXTENSIONS/EXT04_CNAM_CONVENTIONS_ASSURANCES.md`.

- L'en-tête renvoie à la matrice PO v1.3.3 et rappelle D37 « À RÉEXAMINER ».
- La ligne D44 identifie l'option B comme **PROPOSITION NON ARBITRÉE**.
- Une formulation explicite empêche de lire « activable par paramétrage » comme une autorisation : le tiers payant CNAM ne peut pas être considéré comme approuvé ou activable avant arbitrage de D37.
- Les D44–D58 restent des propositions ; aucune n'est présentée comme arbitrée.

La conception générique des organismes, conventions et justificatifs n'est pas supprimée. Le verrou porte sur l'activation du tiers payant, pas sur l'existence d'un modèle d'extension.

## 5. Contrôles après publication

Les deux fichiers EXT03 et EXT04 ont été relus depuis la branche `main`. Les passages d'en-tête, §6.3, ligne D40 et ligne D44 contiennent les formulations corrigées.

## 6. Registre de suivi

| Référence | État |
|---|---|
| EXT03 — référence obsolète à la matrice v1.2.1 | **Corrigée** : v1.3.3 |
| EXT03 — statut de D40 | **Corrigé** : arbitrage PO intégré |
| EXT03 — D37 et activation tiers payant | **Clarifié** : D37 reste à réexaminer, aucun flux activable |
| EXT04 — ambiguïté D44 « activable par paramétrage » | **Corrigée documentairement** : D44 explicitement non arbitrée et conditionnée à D37 |
| INC-06 / D37–D44 | **Ouvert** : aucun arbitrage métier inventé |
| AUD-063 / AUD-064 — consolidation EXT06 / D75 | **Ouverts** : inchangés par ce rapport |

## 7. Limites et garanties

- Corrections limitées aux documents EXT03 et EXT04.
- Matrice PO v1.3.3 et sources originales inchangées.
- Aucune validation réglementaire externe n'a été effectuée ; les exigences R1–R22 restent soumises à leurs statuts propres.
- Aucun code, API, schéma de données, compte, droit effectif applicatif, test, état DEMO ou service système n'a été inspecté ou modifié.
- Aucune migration, opération système ou déploiement n'a été exécuté.
- Aucun arbitrage PO supplémentaire n'a été créé.

---
**Fin du rapport v0.19.**
