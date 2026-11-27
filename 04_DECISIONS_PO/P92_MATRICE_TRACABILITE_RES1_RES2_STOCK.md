# PHARM'OS — P92
## Matrice de traçabilité RES-1 / RES-2 — STOCK

**Date :** 27/11/2026  
**Statut :** matrice documentaire de préparation ; aucun test n'a été exécuté.  
**Autorité :** décisions P87 et P92 ; plan révisé P92 v2.  
**Règle :** les identifiants de tests ci-dessous sont des identifiants de conception proposés, pas des tests existants ni des preuves PASS. Ils devront être rapprochés des suites réelles en R1/R3.

---

## 1. RES-1 — Passage en PERTE

| ID exigence | Règle à démontrer | Source | Zone technique à examiner | Test/preuve à préparer | Porte |
|---|---|---|---|---|---|
| RES1-01 | API refuse PERTE tant que le parcours complet n'est pas disponible | P87 précaution A ; P92 §2 | route/service STOCK, point d'entrée PERTE | P92-RES1-T01 : appel direct avant disponibilité du parcours → refus métier | R3/R4b |
| RES1-02 | Aucun chemin alternatif ne permet de contourner le refus | P87 précaution A ; P92 §2 | toutes routes/services de transition vers PERTE | P92-RES1-T02 : inventaire et tests des routes alternatives → aucune transition possible | R1/R4b |
| RES1-03 | Le parcours affiche la quantité exacte concernée | P86 R7a-AC2 ; P87 précaution A ; P92 §3.2 | écrans PERTE V1/V3 ; réponse service | P92-RES1-T03 : quantité affichée = quantité reçue du service, sans recalcul | R3b/R4b |
| RES1-04 | Le caractère définitif/irréversible est clairement expliqué | P87 précaution A ; P92 §3.2 | écrans PERTE V1/V3, clés FR/EN/AR | P92-RES1-T04 : présence et exactitude du message dans les trois langues | R3b |
| RES1-05 | Une confirmation explicite est recueillie | P87 précaution A ; P92 §3.2 | parcours PERTE V1/V3 et contrat API | P92-RES1-T05 : absence de confirmation → refus ; confirmation explicite → parcours autorisé sous contrôles serveur | R3b/R4b |
| RES1-06 | Un paramètre technique seul ne suffit pas | P87 précaution A ; P92 §2 | service et route PERTE | P92-RES1-T06 : appel API avec simple indicateur/paramètre sans parcours de confirmation → refus | R4b |
| RES1-07 | Le serveur vérifie les droits de l'acteur | P86 R4b, Q-11 ; P87 ; P92 §2 | middleware/permission/service STOCK | P92-RES1-T07 : utilisateur sans droit → refus ; droit non accordé par défaut | R4b |
| RES1-08 | La confirmation correspond à la quantité concernée | P86 R1/R4b ; P87 ; P92 §2 | contrat de commande et service | P92-RES1-T08 : quantité modifiée/incohérente → refus | R4b |
| RES1-09 | L'appel direct ne contourne pas les contrôles | P87 précaution A ; P92 §2 | API et parcours complet | P92-RES1-T09 : appels directs sans éléments attendus → refus identique au contrôle métier | R4b |
| RES1-10 | Aucun passage de sortie depuis PERTE n'est possible | P81/P82 ; P86 R4b/R7a-E9 ; P92 §3.2 | transitions d'état, routes et interface | P92-RES1-T10 : toute transition sortante depuis PERTE → refus/absence d'action | R4b/R3b |
| RES1-11 | FR/EN/AR et RTL restent cohérents | P92 §3.2 | interfaces V1/V3 et ressources i18n | P92-RES1-T11 : contrôle visuel et fonctionnel par langue/sens d'écriture | R3b |
| RES1-12 | Aucun PASS n'est attribué sans exécution | P86, P87, P92 | rapport R6 et registre de preuves | preuve d'exécution datée, commande, résultat, artefact | R5/R6 |

## 2. RES-2 — Suivi obligatoire par lot à la création/adoption

| ID exigence | Règle à démontrer | Source | Zone technique à examiner | Test/preuve à préparer | Porte |
|---|---|---|---|---|---|
| RES2-01 | Médicament créé/adopté avec suivi par lot conforme | P81 DP2-R1 ; P87 précaution B ; P92 §3.1 | écrans V1/V3, service catalogue | P92-RES2-T01 : opération conforme possible | R3b/R4e |
| RES2-02 | Médicament sans suivi obligatoire refusé | P81 DP2-R1 ; P87 précaution B ; P92 §3.1 | validation catalogue côté service | P92-RES2-T02 : création/adoption non conforme → refus | R4e |
| RES2-03 | Le refus explique le motif métier | P87 précaution B ; P92 §3.1 | erreurs métier et messages V1/V3 | P92-RES2-T03 : motif explicite, non technique et compréhensible | R3b/R4e |
| RES2-04 | L'utilisateur peut corriger les informations | P87 précaution B ; P92 §3.1 | formulaires catalogue V1/V3 | P92-RES2-T04 : correction puis reprise de l'opération | R3b |
| RES2-05 | Pas de suspension générale si l'opération est corrigeable | P87 précaution B ; P92 §3.1 | service catalogue et parcours | P92-RES2-T05 : opération corrigible reste réalisable après correction | R3b/R4e |
| RES2-06 | Pas de message technique générique comme substitut | P87 précaution B ; P92 §3.1 | erreurs et traductions | P92-RES2-T06 : aucun message générique sans motif/correction | R3b/R4e |
| RES2-07 | La valeur par défaut ne neutralise pas l'obligation métier | P87 §4.2 ; P92 §3.1 | `catalogue.js`, `CatalogForms.jsx`, `reference.js`, `ReferenceCatalog.jsx` | P92-RES2-T07 : défaut cohérent pour MÉDICAMENT ; service reste autoritaire | R3b/R4e |
| RES2-08 | Les médicaments existants non conformes sont « à régulariser », sans conversion automatique | P86 R4e ; P87 ; P92 §3.1 | service catalogue et état des références | P92-RES2-T08 : aucune conversion automatique ; signalement à régulariser | R4e |
| RES2-09 | V1 et V3 sont traités | P87 §4.2 ; P92 §3.1 | fichiers actifs déterminés au relevé R0 | P92-RES2-T09 : preuve sur les deux portails | R3b |
| RES2-10 | FR/EN/AR et RTL restent cohérents | P92 §3.1 | interfaces et ressources i18n | P92-RES2-T10 : contrôle par langue et sens d'écriture | R3b |
| RES2-11 | Les parcours catalogue existants ne régressent pas | P86 R0/R5 ; P92 §3.3 | suites de non-régression catalogue | P92-RES2-T11 : mêmes résultats de base et nouveaux cas verts | R3b/R5 |

## 3. Fichiers et zones à confirmer — ne pas présumer

**Cités explicitement par P87 :**
- V1 : `catalogue.js`
- V3 : `CatalogForms.jsx`, `reference.js`, `ReferenceCatalog.jsx`

**Cité par le plan P86 pour les routes de transition :**
- `stockRoutes.ts`

**À identifier au relevé R0/R1 avant toute intervention :**
- fichier(s) de service réellement responsable(s) des transitions vers PERTE ;
- fichier(s) de validation et de service de création/adoption catalogue ;
- schémas de requête/réponse et erreurs métier existants ;
- ressources i18n effectivement utilisées par V1 et V3 ;
- suites de tests unitaires/API/UI réellement disponibles.

Aucun autre chemin de fichier n'est affirmé ici. Les fichiers actifs, leurs empreintes et leurs dépendances doivent être constatés sur le dépôt de code pendant une mission expressément autorisée.

## 4. Preuves obligatoires pour chaque cas

Pour chaque test :
1. identifiant stable ;
2. environnement et version exacte ;
3. préconditions et acteur/droits ;
4. commande ou parcours réellement exécuté ;
5. résultat attendu et résultat observé ;
6. statut exclusif : PASS / FAIL / NON EXÉCUTÉ / NON VÉRIFIÉ ;
7. journal ou capture exploitable ;
8. lien au fichier et à son empreinte dans M0/M1/M2.

Un scénario préparé, une revue documentaire, un test non lancé ou un résultat simulé ne constitue pas un PASS.

## 5. Garde-fous

- Cette matrice ne vaut ni autorisation de code ni autorisation de test.
- R3b demeure hors A-T3/R7a et nécessite une autorisation technique distincte.
- R3b précède R4a ; les fichiers concernés sont inclus dans M0 avant le gel.
- Aucune migration 0022, aucun changement DEMO/PM2/service, aucune promotion G6/G7, aucun nettoyage ou intervention système.
- Phase 2 suspendue ; PC-1 non exécuté ; C7b non vérifié ; O-5/O-6/O-9/O-10 ouverts ; V-3 NON APPROUVÉ.

---
**Fin de la matrice P92 RES-1/RES-2.**
