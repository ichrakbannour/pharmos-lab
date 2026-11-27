# PHARM'OS — Audit documentaire global
**Version :** 0.7 — vérification directe de l’archive et approfondissement P83/P85/P87–P91  
**Date :** 27/11/2026  
**Dépôt :** https://github.com/ichrakbannour/pharmos-lab  
**Branche :** `main`  
**Statut :** audit documentaire en cours. Ce rapport n’autorise aucune réalisation technique.

## 1. Contrôle direct de l’archive jointe

L’archive a été ouverte et contrôlée directement dans cette session, indépendamment du seul manifeste :

- fichier : `EXPORT_SOURCES_AUDIT_PHARMOS_20261127.zip` ;
- taille : **498 326 octets** ;
- SHA-256 : `b3a4d228927300c6c12b18b5082fb4c797599fc762102a9e08a97fd47d0f9fa5` ;
- entrées : **42** ;
- taille totale décompressée : **1 455 582 octets** ;
- test ZIP : aucune entrée corrompue ;
- les 42 fichiers sont décodables en UTF-8 et leur réencodage UTF-8 reproduit exactement leurs octets.

P81 est présent à **17 719 octets**, SHA-256 `dec4d6420c5e5ecf54d4e31410c4776a45b3f63e160dae7193a958cd41c2232c`.

Le manifeste fourni indique que les 42 entrées ont été comparées aux originaux par taille et SHA-256, sans écart. Ce contrôle de comparaison des 42 originaux est rapporté par le manifeste ; le contrôle direct effectué ici confirme l’archive, ses 42 entrées, les tailles et l’intégrité de P81. Le croisement individuel P82–P86 avec les manifestes M0 P88/P87 reste NON VÉRIFIÉ.

## 2. Décisions et arbitrages P83, P85 et P87

### P83
P83 est bien l’addendum PO confirmant Q-1 à Q-12, H-1 à H-6 et les précautions C-P82-1 à C-P82-3. Q-12 établit une table de référence de motifs et traite O-11 pour la forme. O-5, O-6, O-9 et O-10 restent ouverts. V-3 demeure NON APPROUVÉ. DP1-R7 reste reporté ; aucune sortie de QUARANTAINE ni destruction des quantités en PERTE n’est autorisée par cet addendum.

La levée de QUARANTAINE est réservée à l’ADMIN/pharmacien titulaire, avec authentification forte ; elle n’est pas délégable. Les corrections et contre-passations enregistrent la réalité physique et ne sont pas bloquées par une réservation.

### P85
P85 confirme :
- A-T1 APPROUVÉ : quatre mesures de sécurisation des livraisons et autorisation future, strictement bornée, de modifier `compose.ts` pour la garde de démarrage ;
- A-T2 : phase 1 autorisée sous conditions, phase 2 suspendue ;
- A-T3 : Option A approuvée pour le lot R7a borné, limité aux deux messages définis.

Ces arbitrages ne lancent pas une mission de réalisation. O-5, O-6, O-9 et O-10 restent ouverts ; migration 0022 non créée/non exécutée.

### P87 — référence officielle P86
Le document `P87_ADDENDUM_DECISIONS_PO_P86.md` déclare explicitement être la référence officielle des arbitrages PO P86-1 à P86-13 et des précautions A/B. Il confirme notamment :
- C1–C8 révisés, C7 scindé en C7a/C7b, ajout de C9 ;
- les seuils P84 sont des planchers de départ, durcissables mais jamais assouplis ;
- phase 2 reportée par défaut, avec porte de capacité PC-1 avant G6 ;
- passage en PERTE non exposé dans V1/V3 tant que le formulaire complet n’est pas livré ;
- O-5, O-6, O-9 et O-10 maintenus ouverts ;
- V-3 maintenu NON APPROUVÉ ;
- précaution A : aucune opération PERTE directement par API sans parcours utilisateur complet et confirmation liée à l’opération, la quantité et ses conséquences irréversibles ;
- précaution B : P86-V1 est à traiter avant R4e ; son lot, sa place exacte et son autorisation restent non fixés (RES-2).

P87 est bien la source à citer pour les décisions PO P86. Cela confirme le choix documentaire décrit dans le rapport d’export ; il n’existe pas de fichier distinct intitulé « réponse PO P86 ».

## 3. P88, P89, P90 et P91 : clarification de la chaîne

### P88
P88 est BLOQUÉ au préflight R0 par PRQ-3 : MemAvailable sous le plancher de lancement de 500 000 kB. Le rapport indique 497 432–497 900 kB aux relevés finaux concernés. Aucun PASS n’est déclaré pour C1–C9 ; migration 0022 non créée ; G6 non proposée ; phase 2 suspendue.

### P90 : absence de rapport autonome, mais trace substantielle dans P88
Il faut distinguer deux constats :
- **Lacune documentaire confirmée :** aucun fichier autonome officiel `P90_*` n’est présent dans l’export.
- **Trace opérationnelle existante :** P88 contient une section complète « MISE À JOUR P90 » qui décrit l’autorisation Option A, les cinq relevés préalables, les étapes exécutées, la surveillance et l’arrêt.

Cette section indique que P90 a repris P88 en phase 1 seulement, sans nettoyage de `/tmp`, sans arrêt de processus, sans modification système, sans intervention DEMO et sans migration officielle 0022. Le premier test R0d a franchi le seuil de 500 000 kB et l’opération a été arrêtée. Pour le fichier testé, la marge disponible était insuffisante ; aucun autre fichier n’a été testé. C1–C9 restent NON ÉVALUÉS, PR-0 non franchie et phase 2 suspendue.

Il est donc inexact de dire que P90 n’a aucune trace documentaire. La formulation correcte est : **pas de rapport P90 autonome ; compte rendu détaillé intégré à P88**. Aucun rapport P90 ne doit être reconstitué.

### P89
P89 corrige l’hypothèse P88 : Shmem et le contenu de `/tmp` sont constants sur les relevés comparés et n’expliquent pas les variations de MemAvailable. La contrainte structurelle du tmpfs est confirmée, mais la cause des variations (489 648 à 606 724 kB) reste NON IDENTIFIÉE. P89 est un diagnostic en lecture seule ; aucune action corrective n’a été effectuée. La reprise P88 reste suspendue jusqu’à décision PO explicite.

### P91
P91 est une préparation de l’Option B, pas son exécution. Aucun palier n’a été réalisé ; aucune suppression, copie ou libération mémoire n’a été effectuée. Le résidu P90 est examiné au niveau des métadonnées seulement et n’est pas un candidat à la libération mémoire. Toute action sur les archives, le résidu, les services ou la reprise P88 exige une décision PO distincte.

### Réserves de cohérence P87 à ne pas perdre dans la synthèse

La relecture détaillée de P87 fait apparaître deux réserves qui doivent rester explicitement ouvertes :

- **RES-1 — PERTE/API :** la précaution A interdit le passage en PERTE par API tant que le parcours utilisateur complet et sa confirmation explicite ne sont pas disponibles. Le plan P86 prévoit pourtant un passage par service/route HTTP avec confirmation de quantité. P87 indique que la lecture littérale de la précaution A est plus restrictive, que le PO n'a pas précisé la lecture à appliquer, et que P86 §3 n'a pas été révisé. Les scénarios de succès PER-04, PER-05 et R7a-E6 sont concernés. Arbitrage PO requis avant R4b ; aucune des deux lectures ne doit être choisie par déduction.

- **RES-2 — P86-V1 :** le résultat métier (refus explicite, motif, possibilité de correction avant R4e) est décidé, mais le lot, les fichiers, la position exacte par rapport au gel R4a→G6 et l'autorisation technique ne le sont pas. P87 constate qu'aucune autorisation F-1 à F-4 ne couvre clairement cet écran hors R7a ; une mission d'interface distincte ou une révision du plan P86 est évoquée. « Avant R4e » ne vaut donc ni autorisation d'exécution ni définition du lot.

Ces réserves ne sont pas des contradictions entre décisions PO ; P87 les décrit comme des tensions entre les précautions PO et le plan technique. Elles doivent être levées avant les étapes concernées, sans élargir A-T1/A-T2/A-T3.

## 4. État documentaire des sources dans GitHub

L’archive est vérifiée dans la session, mais **les 42 fichiers originaux ne sont pas encore tous publiés individuellement dans le dépôt**. La présence du ZIP dans la conversation, du manifeste ou de ses empreintes dans un rapport ne vaut pas archivage GitHub.

Les écarts de taille déjà constatés entre le manifeste et trois fichiers présents dans GitHub restent à résoudre par comparaison octet à octet avec les originaux :
- matrice v1.2 : 25 541 octets dans l’archive contre 25 540 dans GitHub ;
- P82 spécifications finales : 41 488 contre 41 487 ;
- P86 arbitrage consolidé : 63 462 contre 63 461.

Ne pas corriger ces fichiers par simple ajout supposé d’un saut de ligne. Les octets exacts doivent être transférés depuis les sources et vérifiés après publication. Les sources absentes à publier comprennent notamment la matrice v1.3.3, P81, P83, P85, P87, P88, P89, P91 et EXT03 v1.1 avec ses six pièces d’audit. `LISEZMOI.md` doit rester identifié comme ajout de copie, pas comme pièce de l’archive initiale EXT03.

## 5. Registre des points ouverts

| Point | Statut au 27/11/2026 |
|---|---|
| P90 | Pas de rapport autonome ; trace détaillée intégrée à P88 |
| P82–P86 vs manifestes M0 | Empreintes individuelles non croisées |
| O-5, O-6, O-9, O-10 | Ouverts |
| O-11 | Traité pour la forme par P83 Q-12 |
| V-3 | NON APPROUVÉ |
| EXT03 R1–R22 | Questions réglementaires ; aucune validation externe démontrée |
| EXT03 D27–D43 | Grille historique soumise à validation ; D40 couvert par arbitrage ultérieur |
| Sources originales GitHub | Publication individuelle exacte incomplète |
| Cause des variations mémoire | NON IDENTIFIÉE |
| Option B P91 | Préparée uniquement ; aucun palier exécuté |

## 6. Interdictions et limites

Aucune conclusion de cet audit n’autorise le développement, les tests, la migration 0022, la promotion G6, l’application G7, le déploiement, le nettoyage de `/tmp`, la suppression ou le déplacement de fichiers, une modification système, PM2, ou une intervention DEMO.

Les décisions métier, les autorisations futures conditionnelles et l’autorisation d’exécuter une mission restent trois niveaux distincts. Toute réalisation exige une autorisation explicite distincte du PO.

---
**Fin du rapport v0.7 — audit documentaire en cours.**
