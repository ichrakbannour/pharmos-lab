# PHARM'OS — P92
## Plan unique de réalisation STOCK — révision contrôlée du plan P86

**Date :** 27/11/2026  
**Base historique :** `07_SOURCES_ORIGINALES/EXPORT_SOURCES_AUDIT_PHARMOS_20261127/4_DECISIONS_STOCK_P82_P91/P86_PLAN_UNIQUE_REALISATION_STOCK.md`  
**Addenda d'autorité :** P87 et P92 — décisions PO confirmées.  
**Statut :** plan documentaire révisé ; aucune réalisation autorisée.

---

## 1. Règle de lecture et portée

Le plan P86 demeure conservé sans modification comme pièce historique. Le présent document constitue sa révision contrôlée pour les seuls sujets RES-1 et RES-2, et doit être lu avec le plan P86.

- Toutes les règles, portes, contrôles, seuils, conditions d'arrêt, livrables et limites du P86 restent applicables lorsqu'ils ne sont pas explicitement remplacés ou complétés ci-dessous.
- En cas de conflit, le présent plan P92 prévaut pour RES-1, RES-2 et le séquencement R3b.
- P85 et P87 conservent leur autorité sur les autorisations et non-autorisations. P92 ne les élargit pas.
- Les décisions P86-1 à P86-13, Q-1 à Q-12, H-1 à H-6 et C-P82-1 à C-P82-3 restent inchangées.

## 2. Séquencement révisé

Le séquencement P86 est complété par un lot R3b, placé avant R4a et donc avant le gel :

`R0 → R1 → R2 → R3 → R3b → R4a [début du gel] → R4b → R4c → R4d → R4e → R7a → R5 → PC-1 → R6 → G6 (décision PO distincte)`

| Lot | Contenu révisé | Porte |
|---|---|---|
| R0 à R3 | Inchangés selon P86 | PR-0 à PR-3 |
| **R3b — Interfaces de conformité et parcours PERTE** | Écrans catalogue V1/V3, correction du suivi obligatoire par lot, refus compréhensible et corrigeable ; parcours utilisateur complet PERTE V1/V3 et confirmation explicite | **PR-3b** |
| R4a à R4d | Inchangés, sous réserve des règles RES-1 en R4b | PR-4a à PR-4d |
| R4e | DP2-R1 côté service, sans message générique ; cohérence avec parcours correctif R3b | PR-4e |
| R7a | Périmètre exclusif P85/P86 inchangé ; aucun point d'entrée PERTE | PR-7a |
| R5, PC-1, R6, G6, G7 | Inchangés selon P86 et P85 | PR-5, PR-C, PR-6, décision G6 ; G7 hors mission |

Le gel de livraison commence toujours après PR-4a. Les fichiers d'interface R3b doivent donc être achevés, testés et inclus dans M0 avant le début de R4a. Aucune modification de ces fichiers après le début du gel sans arrêt formel, information PO et révision documentée du plan/manifeste.

## 3. R3b — Interfaces de conformité et parcours PERTE

### 3.1 Suivi obligatoire par lot — RES-2

**Résultat métier obligatoire :**
- Une création ou adoption d'un médicament incompatible avec le suivi obligatoire par lot est refusée.
- Le refus explique le motif dans un langage compréhensible.
- Le parcours permet de corriger les informations nécessaires et de reprendre l'opération.
- Aucune suspension générale n'est appliquée lorsqu'une correction permet la conformité.
- Aucun message technique générique ne remplace le motif métier et la possibilité de correction.
- Le service conserve le contrôle final et refuse toute opération encore non conforme.

**Interfaces à intégrer :**
- V1 : écran catalogue concerné, notamment `catalogue.js` ;
- V3 : `CatalogForms.jsx`, `reference.js`, `ReferenceCatalog.jsx`, après confirmation des composants réellement actifs au relevé R0.
- Les champs/valeurs par défaut ne doivent pas permettre de neutraliser l'obligation métier pour le type MÉDICAMENT.
- Les médicaments existants non conformes restent « à régulariser », sans conversion automatique.

### 3.2 Parcours utilisateur PERTE — RES-1

**Résultat métier obligatoire :**
- Aucun passage en PERTE par API avant disponibilité du parcours utilisateur complet.
- Un paramètre technique de confirmation envoyé à l'API ne suffit pas à lui seul.
- Le parcours affiche la quantité concernée, explique le caractère définitif/irréversible et recueille une confirmation explicite de l'utilisateur autorisé.
- Le serveur vérifie les droits, la quantité et les éléments de confirmation attendus ; un appel direct ne peut contourner les contrôles.
- Tant que le parcours complet n'est pas intégré et disponible, le traitement serveur refuse le passage en PERTE.
- PERTE reste définitive ; aucune sortie ni remise à disposition depuis PERTE n'est ajoutée.

**Périmètre interface :**
- V1 et V3 ;
- affichage de la quantité exacte fournie par le service ;
- avertissement neutre et explicite sur le caractère définitif ;
- confirmation explicite et non ambiguë ;
- retours de refus compréhensibles ;
- FR/EN/AR et RTL ;
- aucune action ou écran permettant de sortir un lot de PERTE.

Le parcours PERTE est hors du périmètre exclusif R7a. Son intégration dans R3b ne modifie pas rétroactivement A-T3 : elle requiert une autorisation technique distincte avant tout code.

### 3.3 Tests et porte PR-3b

Les cas doivent être préparés en R1 et écrits en échec attendu en R3, puis exécutés après les réalisations correspondantes :
- création/adoption médicament avec suivi par lot conforme ;
- création/adoption sans suivi obligatoire ;
- correction des informations et reprise ;
- refus compréhensible et absence de suspension globale injustifiée ;
- V1 et V3 ;
- FR/EN/AR et RTL ;
- API PERTE appelée directement avant disponibilité du parcours : refus ;
- utilisateur sans droit : refus ;
- confirmation absente, incohérente ou quantité modifiée : refus ;
- parcours complet et confirmation explicite : résultat attendu conforme aux décisions PO ;
- impossibilité de sortir de PERTE ;
- non-régression des autres parcours catalogue et stock.

**PR-3b n'est franchie que si** les contrôles bloquants sont exécutés et verts, les parcours conformes restent possibles, les parcours non conformes sont refusés avec une correction possible, et aucun appel API ne contourne les contrôles PERTE. Les tests non exécutés ou non vérifiés restent explicitement ainsi ; aucun PASS n'est inféré.

## 4. Adaptations obligatoires des lots P86

### 4.1 R4b — Service, droits et routes

Compléter la description P86 R4b par les exigences RES-1 :
- le service refuse le passage en PERTE tant que le parcours complet n'est pas disponible ;
- le paramètre de confirmation portant la quantité reste nécessaire selon P86, mais n'est pas une preuve suffisante à lui seul ;
- le serveur contrôle les droits, la quantité et la confirmation explicite ;
- aucun chemin API alternatif ne permet le passage en PERTE avant que le parcours complet soit intégré et disponible ;
- les tests API directs et les cas de refus sont obligatoires.

Le reste de R4b (transferts de somme nulle, immutabilité, états, quarantaine, droits par défaut, audit et routes) reste inchangé.

### 4.2 R4e — Catalogue et DP2-R1

Remplacer la mention P86 « comportement transitoire à faire confirmer » par le résultat PO P87/P92 :
- le service applique DP2-R1 comme contrôle final ;
- le refus est métier, explicable et exploitable par les écrans R3b ;
- le service ne renvoie pas un simple message technique générique ;
- une opération corrigible n'entraîne pas une suspension générale ;
- DP2-R2/R3/R4, C1/C2, alertes et garde de démarrage restent inchangés.

### 4.3 R7a — Périmètre exclusif conservé

R7a reste limité aux messages de refus de contre-passation et de confirmation prévus par P85/P86, avec clés FR/EN/AR. Il ne comprend :
- ni les écrans catalogue de suivi par lot ;
- ni le parcours complet de passage en PERTE ;
- ni un point d'entrée PERTE.

Ces éléments relèvent de R3b, soumis à autorisation technique distincte. La séparation évite de déformer A-T3 et le périmètre R7a confirmé.

## 5. Manifeste, gel et dépendances

1. R0 identifie les fichiers V1/V3 réellement utilisés et relève leurs empreintes.
2. R1 fixe contrats, erreurs métier, données affichées, cas de test et fichiers concernés.
3. R3 prépare les tests rouges avant toute modification fonctionnelle.
4. R3b réalise les interfaces ; PR-3b valide les parcours et la non-régression avant R4a.
5. M0 doit intégrer les fichiers R3b dans l'état de référence avant R4a.
6. R4a ouvre le gel. Aucun fichier R3b ne peut être modifié ensuite sans arrêt et révision du plan.
7. R4b fournit les contrôles serveur PERTE et leur intégration ; l'opération ne devient disponible que lorsque le parcours complet et les contrôles serveur sont tous deux présents dans le candidat final.
8. R4e applique le contrôle catalogue DP2-R1 cohérent avec les parcours R3b.
9. R5/R6 couvrent tous les fichiers, tests, empreintes et preuves ajoutés par R3b.
10. L'ensemble reste un livrable unique non promouvable partiellement ; G6 demeure une décision PO distincte.

Aucune empreinte n'est renseignée à l'avance : seules les empreintes réellement calculées pendant une mission autorisée peuvent figurer dans M0/M1/M2.

## 6. Conditions de lancement et autorisations

Les conditions P86 §14 et P85 §8 restent intégralement applicables. En complément :
- une autorisation explicite et distincte doit nommer le plan révisé P92 ;
- elle doit autoriser expressément le lot R3b (écrans catalogue V1/V3 et parcours PERTE complet), qui est hors du périmètre A-T3/R7a ;
- elle doit préciser le périmètre technique autorisé et les environnements de test ;
- aucune intervention DEMO, base réelle, PM2, service, configuration, migration officielle, G6/G7, nettoyage ou opération système n'est autorisée par le présent plan.

**P92 est un plan documentaire, pas un ordre de mission.** Aucun code, test, build, benchmark ou migration n'est lancé par sa publication.

## 7. Éléments inchangés et statuts à préserver

- Quatre états de stock ; PERTE définitive.
- DP1–DP6, Q-1 à Q-12, H-1 à H-6, C-P82-1 à C-P82-3.
- P86-1 à P86-13 ; P86-12 maintient O-5/O-6/O-9/O-10 ouverts ; P86-13 maintient V-3 NON APPROUVÉ.
- C7a obligatoire ; C7b en réserve/non vérifié.
- Phase 2 suspendue ; PC-1 non exécuté.
- A-T1/A-T2/A-T3 et leurs limites P85 inchangées.
- 0022 non créée ; aucune application à la DEMO/base réelle ; G6 et G7 non autorisés.
- Aucun résultat PASS n'est créé par le présent document.

---
**Fin du plan révisé P92.**
