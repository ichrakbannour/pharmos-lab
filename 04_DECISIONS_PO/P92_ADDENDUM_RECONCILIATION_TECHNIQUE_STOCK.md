# PHARM'OS — P92
## Addendum PO de réconciliation technique STOCK — RES-1 / RES-2

**Date :** 27/11/2026  
**Autorité :** Product Owner PHARM'OS  
**Sources de référence :** P81, P83, P85, P86, P87 ; audit documentaire global v0.35.  
**Nature :** décisions PO de réconciliation documentaire et séquencement cible.  
**Statut :** décisions de résultat et de séquencement confirmées ; réalisation technique non autorisée par le présent addendum.

---

## 1. Objet

Le présent addendum traduit les décisions déjà prises dans P87 (précautions A et B) en règles de conception et en adaptations du plan unique P86.

Il ne rouvre pas les décisions métier P81/P83 ni les 13 arbitrages P86-1 à P86-13. Il complète le plan P86 sans réécrire les pièces historiques.

## 2. Décision PO — RES-1 : passage en PERTE et API

La précaution A de P87 est confirmée dans sa lecture stricte :

1. **Aucun passage en PERTE ne doit être possible par API tant que le parcours utilisateur complet de passage en PERTE n'est pas disponible.**
2. Un simple paramètre technique de confirmation transmis à l'API ne remplace pas le parcours utilisateur ni sa confirmation explicite.
3. Le parcours doit présenter clairement la quantité concernée, le caractère définitif/irréversible de la PERTE et demander une confirmation explicite de l'utilisateur autorisé.
4. Le serveur demeure l'autorité : il vérifie les droits, la quantité et la confirmation attendue ; un appel direct ou détourné à l'API ne peut contourner les contrôles du parcours.
5. Tant que le parcours complet n'est pas intégré et disponible, le traitement serveur doit refuser l'opération PERTE. Aucun endpoint, route ou chemin alternatif ne doit permettre de la réaliser.
6. La PERTE demeure un état définitif ; aucune sortie ni remise à disposition depuis PERTE n'est introduite.

**Conséquences sur P86 :**
- R4b doit conserver les contrôles serveur et le refus par défaut, mais ne peut pas rendre le passage PERTE opérable avant disponibilité du parcours complet.
- Le parcours utilisateur PERTE est un lot d'interface dédié V1 et V3, hors du périmètre exclusif R7a.
- R7a reste limité aux messages déjà définis ; il ne reçoit aucun point d'entrée de passage en PERTE.
- Les scénarios PER-04, PER-05 et R7a-E6 doivent distinguer explicitement le refus tant que le parcours n'est pas disponible, puis le succès uniquement après intégration du parcours complet et vérification de la confirmation explicite. Aucun PASS ne peut être attribué sans exécution.
- Les tests doivent couvrir l'appel API direct, l'absence de parcours, l'utilisateur sans droit, une quantité modifiée et une confirmation absente ou incohérente.

## 3. Décision PO — RES-2 : suivi obligatoire par lot à la création/adoption

Le choix explicite B de P87 est confirmé :

1. Toute création ou adoption d'un médicament incompatible avec le suivi obligatoire par lot est refusée.
2. Le refus est compréhensible et explique le motif métier.
3. Le parcours utilisateur permet de corriger les informations nécessaires avant de recommencer.
4. Aucune suspension générale des créations/adoptions n'est appliquée lorsque la conformité peut être corrigée.
5. Aucun simple message technique générique ne remplace l'explication ni la possibilité de correction.
6. Les règles DP2-R1 à DP2-R4 restent inchangées ; les médicaments déjà existants non conformes suivent le traitement « à régulariser » prévu, sans conversion automatique.

**Conséquences sur P86 :**
- Le comportement transitoire P86-V1 (refus générique du service avec formulaire non adapté) est abandonné.
- Les écrans V1 et V3 cités dans P87 doivent permettre de renseigner/corriger explicitement le suivi par lot et de comprendre tout refus.
- La valeur par défaut « non » relevée dans les écrans sources ne doit pas neutraliser l'obligation métier pour le type MÉDICAMENT. La valeur proposée à l'écran doit être cohérente avec cette obligation et ne doit pas créer une adoption non conforme.
- Le service reste le contrôle final et refuse toute opération encore non conforme, avec une erreur métier exploitable par l'interface.
- Le traitement d'interface doit être terminé et testé **avant R4a**, donc avant le début du gel de livraison.

## 4. Séquencement documentaire à intégrer au plan P86

Le lot d'interface de conformité doit être inséré avant R4a. Le séquencement cible devient :

`R0 → R1 → R2 → R3 → R3b (interfaces de conformité et parcours PERTE V1/V3) → R4a [début du gel] → R4b → R4c → R4d → R4e → R7a → R5 → PC-1 → R6 → G6 (décision PO distincte)`

### R3b — Interfaces de conformité STOCK (nouveau lot documentaire)

**Périmètre obligatoire :**
- V1 : écran de création/adoption catalogue concerné, notamment `catalogue.js` ;
- V3 : `CatalogForms.jsx`, `reference.js`, `ReferenceCatalog.jsx`, sous réserve de confirmer les points d'entrée et composants effectivement utilisés au relevé R0 ;
- saisie/correction du suivi obligatoire par lot ;
- refus compréhensible et action de correction ;
- parcours complet PERTE V1/V3, quantité affichée, avertissement de caractère définitif et confirmation explicite ;
- contrôles de droits et de cohérence côté serveur, sans possibilité de contournement par appel API direct ;
- traductions FR/EN/AR et cohérence RTL ;
- tests unitaires, API et interface correspondant aux règles ci-dessus.

**Préparation et critères de sortie :**
1. R1 fixe les contrats, erreurs métier, données affichées, cas de test et fichiers réellement concernés.
2. R3 prépare les tests rouges avant toute modification fonctionnelle.
3. R3b réalise les interfaces et le parcours PERTE ainsi que leurs tests, uniquement dans le cadre d'une mission expressément autorisée.
4. Vérifier que la création/adoption non conforme est refusée avec un motif corrigeable, et qu'une opération conforme reste possible.
5. Vérifier que l'API PERTE refuse tant que le parcours complet n'est pas présent ; après intégration, vérifier les droits et la confirmation explicite sur le serveur.
6. Contrôler V1, V3, FR/EN/AR, RTL et les régressions des parcours catalogue existants.
7. Livrer la liste exacte des fichiers, les preuves, les statuts PASS/FAIL/NON EXÉCUTÉ/NON VÉRIFIÉ et les empreintes.

**Porte PR-3b :** tous les tests bloquants sont exécutés et verts ; aucun parcours catalogue conforme n'est bloqué sans motif ; aucun parcours non conforme ne passe ; aucune opération PERTE ne peut contourner le parcours utilisateur ; les contrôles d'intégrité et de non-régression sont documentés.

### Effets sur le gel et le manifeste

- R3b est placé **avant R4a** : les fichiers V1/V3 concernés sont intégrés au manifeste M0 et au périmètre gelé.
- Aucun fichier de ces écrans ne doit être modifié après le début du gel R4a.
- Si une correction postérieure au gel devient indispensable, arrêt au premier écart, signalement au PO et révision formelle du plan/manifeste ; pas de modification silencieuse.
- R7a conserve son périmètre exclusif P85/P86 ; R3b ne l'élargit pas rétroactivement.
- La livraison reste un ensemble unique non promouvable partiellement ; aucune promotion avant la décision G6 distincte du PO.

## 5. Maintien des décisions et réserves

P92 ne modifie pas :
- les quatre états de stock et le caractère définitif de PERTE ;
- le suivi par lot obligatoire pour les médicaments ;
- Q-1 à Q-12, H-1 à H-6 et C-P82-1 à C-P82-3 ;
- P86-1 à P86-13 ;
- C7a obligatoire et C7b en réserve/non vérifié ;
- phase 2 suspendue et PC-1 non exécuté ;
- O-5, O-6, O-9 et O-10 ouverts ;
- V-3 NON APPROUVÉ ;
- les conditions, seuils et limites A-T1/A-T2/A-T3 de P85 ;
- l'interdiction de G6 sans décision PO distincte et l'interdiction de G7.

## 6. Autorisations : distinction impérative

**P92 confirme des règles métier et le séquencement documentaire cible. Il n'autorise aucune réalisation.**

En particulier, P87 N-1 à N-13 restent applicables. Le nouveau lot R3b (écrans catalogue et parcours utilisateur PERTE) est une extension hors du périmètre A-T3/R7a et requiert une autorisation technique distincte du PO avant toute modification de code.

Aucune migration 0022, aucun test, build, benchmark, copie de version, changement de configuration, intervention système, opération DEMO, promotion G6/G7, suppression ou nettoyage n'est autorisé par P92.

## 7. Actions documentaires suivantes

1. Établir une nouvelle version du plan unique P86 intégrant R3b, les nouvelles dépendances, les portes PR-3b/PR-4b/PR-4e/R5 et les scénarios révisés.
2. Mettre à jour la matrice de traçabilité pour chaque règle RES-1/RES-2, chaque fichier et chaque preuve attendue.
3. Revoir la liste des fichiers et empreintes M0 avant gel ; aucune empreinte réelle ne peut être inventée dans le présent document.
4. Maintenir P86 comme document historique et conserver P87/P92 comme addenda d'autorité.

---
**Fin de P92.**
