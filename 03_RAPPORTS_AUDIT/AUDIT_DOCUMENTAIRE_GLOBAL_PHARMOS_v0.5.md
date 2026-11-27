# PHARM'OS — Audit documentaire global
**Version :** 0.5 — contrôle de l'export des sources et réconciliation P81–P91  
**Date :** 27/11/2026  
**Dépôt :** https://github.com/ichrakbannour/pharmos-lab  
**Branche :** `main`  
**Statut :** audit documentaire en cours. Ce rapport n'autorise aucune réalisation technique.

## 1. Objet et méthode

Cette version complète le rapport v0.4 après réception et inspection de l'archive `EXPORT_SOURCES_AUDIT_PHARMOS_20261127.zip`.

Contrôles réalisés sur l'archive jointe :
- taille : **498 326 octets** ;
- SHA-256 : **b3a4d228927300c6c12b18b5082fb4c797599fc762102a9e08a97fd47d0f9fa5** ;
- **42 entrées** ;
- test ZIP : aucune erreur ;
- contenu total annoncé : 1 455 582 octets ;
- registre P81 : **17 719 octets**, SHA-256 **dec4d6420c5e5ecf54d4e31410c4776a45b3f63e160dae7193a958cd41c2232c**, conforme à l'empreinte exigée ;
- matrice D1–D86 v1.3.3 présente : 100 023 octets, SHA-256 **c3df9bbcf5e3ff3e87eba6362eed5fc55721bb6d13f883699796885bb5c4617e**.

Les 42 fichiers et leurs chemins ont été énumérés depuis l'archive. L'archive correspond à l'empreinte annoncée. Le rapport d'export fourni affirme une comparaison SHA-256 de chaque entrée avec les originaux, sans écart. Le manifeste détaillé a été communiqué dans la conversation ; il n'est pas une entrée de l'archive ZIP.

## 2. Corpus désormais disponible

| Ensemble | Contenu | Conclusion documentaire |
|---|---|---|
| Matrice D1–D86 | 14 versions, v1.2 à v1.3.3 | v1.3.3 est la version la plus récente du corpus exporté (12/11/2026). Les versions précédentes restent historiques. |
| P81 | Registre officiel DP-1 à DP-6, C1 à C4 et points ouverts | Original exact retrouvé ; l'ancienne anomalie de fichier joint de taille/hash divergents ne doit plus être confondue avec cet original. |
| EXT03 v1.1 | Document + six pièces d'audit + LISEZMOI | Source récupérée. LISEZMOI est un ajout de copie, pas une pièce de l'archive initiale. EXT03 v1.1 reste une proposition en attente de validation selon son dossier. |
| P82–P91 | 19 fichiers, incluant addenda PO, plans, rapports et pièces complémentaires | La chaîne documentaire est nettement plus complète. Les propositions techniques restent distinctes des arbitrages PO et des autorisations de réalisation. |
| P90 autonome | Aucun fichier officiel séparé | Lacune de livrable autonome confirmée, avec nuance détaillée en §5. |

## 3. Décisions PO confirmées retrouvées dans les sources

### 3.1 P81 — registre officiel du stock

Le registre P81 porte les décisions DP-1 à DP-6 et les conséquences C1 à C4. Il confirme notamment :
- quatre états physiques : DISPONIBLE, EN_CONTROLE, QUARANTAINE et PERTE ;
- traçabilité des changements d'état avec quantité, motif, auteur et date ;
- PERTE définitive, sans retour vers un autre état ;
- réservations logiques, sans compartiment physique RESERVE ;
- lots obligatoires pour les médicaments et facultatifs pour les autres catégories ;
- stock par état calculé depuis les mouvements ;
- sorties de QUARANTAINE et destruction physique des quantités en PERTE reportées à un lot ultérieur.

Les points O-5, O-6, O-9, O-10 et O-11 demeurent ouverts dans le registre. Aucune réponse ne doit être inventée pour les fermer.

### 3.2 P83 — confirmation des propositions P82

`P83_ADDENDUM_DECISIONS_PO_STOCK.md` déclare confirmés par le PO Q-1 à Q-12, H-1 à H-6 et les précautions C-P82-1 à C-P82-3. Il précise notamment :
- sorties manuelles uniquement depuis DISPONIBLE ;
- aucune sortie directe depuis QUARANTAINE ou PERTE ; la destruction des quantités en PERTE reste reportée ;
- réservations honorées par ancienneté ;
- signalement interne avec audit persistant, sans notification externe ;
- levée de QUARANTAINE réservée à l'ADMIN (pharmacien titulaire), avec authentification forte ;
- levée du blocage d'un lot par la même autorité ;
- corrections et contre-passations non bloquées par les réservations ;
- toute nouvelle entrée naît en DISPONIBLE ;
- motifs normalisés conservés dans une table de référence.

Cet addendum confirme des décisions métier ; il n'autorise ni développement, ni migration 0022, ni test, ni déploiement.

### 3.3 P85 — arbitrages A-T1, A-T2 et A-T3

L'addendum P85 confirme :
- A-T1 approuvé, avec quatre mesures de sécurisation de livraison et une autorisation future limitée de modifier `compose.ts` pour la garde de démarrage, sous conditions ;
- A-T2 : phase 1 autorisée sous conditions, phase 2 suspendue ;
- A-T3 : option A approuvée, lot R7a borné aux deux messages prévus, sans refonte générale de l'interface.

Les autorisations inscrites dans P85 valent pour une future mission de réalisation et ne constituent pas, à elles seules, une autorisation de lancer cette mission.

### 3.4 P86 et P87 — arbitrages PO

Le document `P86_ARBITRAGE_CONSOLIDE_13_POINTS.md` est une proposition soumise au PO. La référence de décision n'est pas ce document, mais `P87_ADDENDUM_DECISIONS_PO_P86.md`.

P87 déclare que le PO a confirmé intégralement P86-1 à P86-13 et deux précautions supplémentaires A et B. Il maintient notamment :
- C1 à C8 révisés, C7 scindé en C7a/C7b et ajout de C9 ;
- les seuils P84 comme planchers de départ durcissables, jamais assouplis sans nouvel accord ;
- phase 2 reportée par défaut et porte de capacité PC-1 avant G6 ;
- passage en PERTE non exposé dans V1/V3 tant que le parcours d'interface complet n'est pas livré ;
- O-5, O-6, O-9 et O-10 maintenus ouverts ;
- V-3 maintenu NON APPROUVÉ.

P87 précise expressément que ces décisions métier n'autorisent aucune réalisation. La précaution A interdit l'opération PERTE déclenchée directement par API sans parcours utilisateur complet et confirmation liée à l'opération, à la quantité et aux conséquences irréversibles. La précaution B impose le traitement P86-V1 dans un lot identifié avant R4e.

## 4. P88, P89 et P91 — état réel de la réalisation STOCK

### 4.1 P88 — GO conditionnel, mais préflight bloqué

P88 rapporte un GO conditionnel PO du 25/11/2026 après arbitrage de RES-1 et RES-2, sous réserve de tous les contrôles, gels et critères d'acceptation. Le préflight R0 est bloqué par PRQ-3 : MemAvailable relevée à 489 648 kB puis 497 432–497 900 kB, sous le plancher de lancement de 500 000 kB.

Aucune suite de test n'a été lancée. R0d, R1, R2, R3, R4a, P86-V1, R4b–R4e, R7a, R5, PC-1 et R6 sont non exécutés. Migration 0022 non créée ; G6 non proposée et non promue.

### 4.2 P90 — absence de fichier autonome, mais contenu partiellement intégré à P88

Aucun fichier distinct `P90_*` n'est inclus dans l'export. Le rapport P88 contient toutefois une section explicitement intitulée **« MISE À JOUR P90 — Reprise conditionnelle de P88 (Option A, GO du PO du 25/11/2026) »**, avec les sous-sections P90-1 à P90-8.

Cette section documente l'autorisation, les cinq relevés préalables, les étapes exécutées, la surveillance mémoire et l'arrêt de R0d huit secondes après le lancement du premier fichier de test, sur franchissement du seuil. Elle déclare qu'aucune étape P88 n'est validée, que la migration 0022 n'est pas créée et que G6 n'est pas proposée/promue.

Conclusion exacte : **P90 n'existe pas comme livrable autonome dans le corpus retrouvé ; son exécution est néanmoins documentée par une mise à jour intégrée au rapport P88.** Les fichiers de travail du dossier `/home/user/work/p90/` ne sont pas des rapports officiels et n'ont pas été inclus. Aucune reconstitution ne doit être entreprise.

### 4.3 P89 — diagnostic mémoire

P89 corrige l'hypothèse avancée dans P88 : le contenu de `/tmp` et Shmem sont constants et n'expliquent pas les variations de MemAvailable. La contrainte structurelle du tmpfs est confirmée, mais la cause des variations (489 648 à 606 724 kB) demeure non identifiée. Le plus gros fichier de `/tmp/backup` a une origine non documentée et son statut reste inconnu ; il n'a pas été ouvert.

Les propositions de reprise P89 ne valent pas autorisation de nettoyage ou de suppression.

### 4.4 P91 — préparation de l'Option B, aucune action exécutée

P91 est un rapport de préparation. Aucun palier n'a été exécuté ; aucun fichier de `/tmp` n'a été supprimé, déplacé ou modifié. Les estimations de gain mémoire ne sont pas testées. Toute action sur les paliers, les archives P73/P76/P77/P78, le fichier `/tmp/backup` ou le résidu P90 requiert une décision PO distincte.

À la clôture de P91 : P88 non relancée, migration 0022 non créée, G6 non proposée, PR-0 non franchie, phase 2 suspendue.

## 5. Matrice D1–D86 v1.3.3 : portée et limites

La v1.3.3 est bien la dernière version retrouvée dans l'export. Son en-tête indique qu'elle ne crée aucune décision nouvelle et ne modifie aucun statut ; elle annote l'arbitrage QO-11 / Décision 3 du PO du 12/11/2026. Le document demeure « soumis à validation ».

Le tableau D72–D86 marque ces décisions ARBITRÉES. Il confirme notamment :
- D72 : compte Super Admin APEX immuable et base physiquement distincte par pharmacie ;
- D74 : réintégration corrective par un second collaborateur uniquement si le droit lui est explicitement accordé ;
- D75 : jour calendaire par défaut, option jour d'exploitation sous activation explicite et garanties ;
- D76 : absence de compartiment physique RESERVE, réservation logique ;
- D78 : aucun catalogue universel de services ; gestion par l'administrateur et notification limitée au type d'opération, nom du client et date ;
- D80 : comptabilité à titre indicatif ;
- D83 : plan comptable standard adaptable, sans validation préalable obligatoire d'un expert-comptable ;
- D84–D86 : classification standard modifiable, rapprochement bancaire avec confirmation humaine, seuil de validation des dépenses configurable.

La matrice ne remplace pas les décisions opérationnelles P81–P91, qui utilisent une numérotation distincte. La lecture exhaustive et le différentiel ligne à ligne des 14 versions restent à achever avant toute consolidation globale D1–D86. Le décompte historique de la matrice ne doit pas être extrapolé à l'état opérationnel du chantier STOCK.

## 6. Anomalies et statuts actualisés

| ID | Statut au 27/11/2026 | Actualisation |
|---|---|---|
| AUD-028 — P81 original | **Source retrouvée / intégrité confirmée** | L'original 17 719 o et SHA-256 attendu est dans l'export. La publication de la source dans GitHub reste à faire. |
| AUD-030 — matrice v1.3.3 | **Source retrouvée / analyse en cours** | v1.3.3 vérifiée et section D72–D86 examinée ; différentiel exhaustif des 14 versions restant à produire. |
| AUD-031 — EXT03 v1.1 et audit | **Sources retrouvées / réconciliation en cours** | Document et six pièces d'audit d'origine retrouvés ; LISEZMOI est un ajout de copie. D27–D43 et R1–R22 restent à auditer. |
| AUD-040 — décision P83 | **Preuve PO retrouvée** | Addendum P83 confirme Q-1 à Q-12, H-1 à H-6 et C-P82-1 à C-P82-3. Vérification détaillée de toutes les dépendances encore à faire. |
| AUD-041 — décision P86 | **Preuve PO retrouvée** | P87 est la référence officielle des arbitrages P86-1 à P86-13 et précautions A/B ; le dossier P86 reste une proposition source. |
| AUD-043 — P0 | **À réexaminer à la lumière de P85–P91** | Les documents P85–P91 décrivent une autorisation conditionnelle distincte, mais P88 est bloqué au préflight et aucune migration/G6 n'a été exécutée. Ne pas confondre GO conditionnel et franchissement des portes. |
| AUD-047 — P90 autonome | **Lacune confirmée, avec contenu embarqué** | Aucun fichier autonome P90 retrouvé ; P88 contient une mise à jour P90 détaillée. |
| AUD-048 — hypothèse tmpfs de P88 | **Hypothèse infirmée par P89** | Shmem et contenu de /tmp constants ; cause des variations de MemAvailable non identifiée. |
| AUD-049 — Option B | **Préparation seulement** | P91 n'a exécuté aucun palier. Toute suppression/libération nécessite les autorisations distinctes listées dans P91. |

## 7. Limites et réserves de l'export

- Les 42 entrées du ZIP sont lisibles et le ZIP passe le test d'intégrité.
- Le rapport d'export affirme que chaque entrée a été comparée à son original par taille et SHA-256, sans écart. Le contrôle indépendant réalisé ici confirme l'empreinte de l'archive, le nombre d'entrées et l'empreinte exacte de P81.
- Le croisement individuel des empreintes P82 à P86 avec les manifestes M0 de P88 et M0 de P87 n'est pas établi par le rapport d'export : il reste NON VÉRIFIÉ.
- Le manifeste détaillé transmis dans la conversation n'est pas inclus comme entrée du ZIP.
- Le corpus exporté ne contient pas P92, P93 ni P94 ; leur contenu n'est donc pas audité ici.
- L'archive ZIP jointe n'est pas elle-même une copie publiée dans le dépôt GitHub. Les sources ne doivent pas être déclarées « intégrées à GitHub » tant que leur publication n'a pas été vérifiée.
- Le fichier P81 n'existe qu'à un seul emplacement source connu ; sa sauvegarde indépendante reste une vigilance.

## 8. Blocages et suites documentaires

1. Publier dans GitHub les originaux vérifiés de la matrice, de P81, d'EXT03 v1.1 et de la chaîne P82–P91, sans écraser les versions historiques ni transformer les propositions en décisions.
2. Effectuer le différentiel D1–D86 v1.2 → v1.3.3, puis rapprocher les décisions Dxx des amendements PO postérieurs.
3. Auditer EXT03 v1.1 et ses six pièces d'audit, en particulier R14–R22, D27–D43 et les réserves réglementaires.
4. Construire une matrice de traçabilité P81 → P82 → P83 → P84 → P85 → P86 → P87 → P88/P90 → P89 → P91, en distinguant décisions métier, propositions techniques, autorisations futures, exécution réelle et preuves.
5. Garder P90 comme lacune de document autonome, sans reconstituer un rapport à partir de P88 ou des journaux de travail.
6. Ne lancer aucune mission de réalisation, migration, test, nettoyage, suppression, modification système, PM2 ou intervention DEMO sans autorisation PO explicite et distincte.

---
**Fin du rapport v0.5 — audit documentaire en cours.**
