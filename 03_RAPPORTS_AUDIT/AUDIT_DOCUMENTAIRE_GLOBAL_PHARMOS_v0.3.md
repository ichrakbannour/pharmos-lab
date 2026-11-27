# PHARM'OS — Audit documentaire global
**Version :** 0.3 — sources récupérées et réconciliation stock/EXT03 (audit en cours)  
**Date de mise à jour :** 2026-11-27  
**Dépôt de référence :** https://github.com/ichrakbannour/pharmos-lab  
**Branche examinée :** `main`  
**Statut :** audit en cours — ne constitue pas une autorisation de développement.

## 1. Mandat et règles de contrôle

Le présent audit est conduit à la demande du Product Owner (PO). Son objectif est de vérifier la cohérence entre le dossier de conception, EXT02 à EXT06, les décisions PO confirmées, les rapports d'audit et les prérequis réglementaires/techniques.

Ordre de priorité retenu :
1. décisions PO explicitement confirmées et les plus récentes ;
2. validations réglementaires ou professionnelles documentées et applicables ;
3. documents de conception et extensions ;
4. recommandations ou propositions non arbitrées.

Une proposition dans un document n'est jamais considérée comme une décision PO par défaut. Les numéros Dxx réutilisés dans plusieurs fils ou documents ne sont pas fusionnés sans preuve de correspondance. Les décisions opérationnelles Pxx/DP-xx restent distinctes des arbitrages Dxx des extensions.

Aucune mission Genspark, aucun développement, aucune migration et aucune modification de données métier ne sont autorisés par le présent audit.

## 2. Périmètre effectivement examiné

| Document | Emplacement actuel | Taille approximative | Statut annoncé dans le document |
|---|---|---:|---|
| Conception PHARM'OS v1.1 | `01_CONCEPTION/CONCEPTION_PHARMOS_v1 (1).md` | 72 Ko / 905 lignes | Conception de référence, aucun module développé |
| EXT02 RH & Team Management | `02_EXTENSIONS/EXT02_RH_TEAM_MANAGEMENT.md` | 63 Ko / 799 lignes | Intégrée à la conception v1.1 |
| EXT03 Ordonnances & prescripteurs | `02_EXTENSIONS/EXT03_ORDONNANCES_PRESCRIPTEURS.md` | 60 Ko / 745 lignes | Proposition soumise à validation |
| EXT04 CNAM, conventions & assurances | `02_EXTENSIONS/EXT04_CNAM_CONVENTIONS_ASSURANCES.md` | 75 Ko / 848 lignes | Proposition soumise à validation |
| EXT05 Annulations, retours & délégations | `02_EXTENSIONS/EXT05_ANNULATIONS_RETOURS_DELEGATIONS.md` | 69 Ko / 826 lignes | Proposition soumise à validation |
| EXT06 Catalogue, gouvernance & finance | `02_EXTENSIONS/EXT06_CATALOGUE_GOUVERNANCE_FINANCE (1).md` | 93 Ko / 1 020 lignes | Proposition soumise à validation |

Les fichiers EXT02 à EXT06 étaient initialement à la racine. Ils ont été déplacés dans `02_EXTENSIONS/`, après copie et vérification de l'identité du contenu, puis suppression des anciens chemins. L'index `02_EXTENSIONS/README.md` a été corrigé pour inclure EXT02. Ces opérations n'ont pas modifié le contenu métier.

## 3. État du dépôt et traçabilité

### 3.1 Éléments présents
- `README.md` à la racine.
- README des dossiers `02_EXTENSIONS`, `03_RAPPORTS_AUDIT`, `04_DECISIONS_PO`, `05_ARCHITECTURE`, `06_PROMPTS_GENSPARK`.
- Le dossier `01_CONCEPTION` contient le dossier de conception principal, mais aucun README n'a été trouvé à son niveau.
- Les cinq extensions et le dossier de conception énumérés ci-dessus.

### 3.2 Éléments annoncés mais non retrouvés dans le dépôt
- Aucun registre de décisions PO exploitable dans `04_DECISIONS_PO` (README seulement).
- Aucun rapport d'audit antérieur retrouvé dans `03_RAPPORTS_AUDIT`.
- Le dossier `03_AUDIT_EXT03_v1.1`, cité comme source de EXT04 et EXT05, n'est pas présent dans le dépôt examiné.
- Aucun document d'architecture séparé retrouvé dans `05_ARCHITECTURE`.
- Aucun prompt ou historique d'exécution Genspark retrouvé dans `06_PROMPTS_GENSPARK`.

Ces constats décrivent le dépôt consulté, pas nécessairement l'ensemble des fichiers qui pourraient exister ailleurs (Genspark, poste local ou autres conversations).

## 4. Référentiel des décisions PO reconstitué

### 4.1 Décisions confirmées pour EXT06 — 2026-11-03
Les confirmations PO retrouvées dans les échanges indiquent notamment :
- D72 et D83 ont été déclarées « absolument » validées.
- D74 : une réintégration corrective par un deuxième collaborateur est permise uniquement si le pharmacien/admin lui a accordé explicitement cette habilitation.
- D76 et D77 acceptées.
- D78 : notification limitée au type d'opération, au nom du client et à la date ; les types de services sont définis par le pharmacien-administrateur ; aucun catalogue de services imposé.
- D80 : la mention « Comptabilité à titre indicatif » est obligatoire.
- D84 : modèle de classification standard interne, modifiable par l'administrateur ; aucune validation préalable par un expert n'est exigée pour le modèle général.
- D79, D81, D82, D85 et D86 acceptées.

**Collision de numérotation à traiter :** le D72 de l'EXT06 décrit une procédure de secours APEX, alors que les échanges PO antérieurs associent aussi D72 à la gouvernance/super-administration et au compte superadmin immuable. De même, D83 de l'EXT06 décrit le plan comptable, et les échanges PO le valident comme modèle standard adaptable sans validation préalable d'expert-comptable. L'audit conserve les décisions PO explicites et signale le besoin d'un registre maître qui attribue un identifiant stable à chaque décision et indique son document d'origine.

### 4.2 État maître des décisions D1–D86 — version la plus récente retrouvée

Le rapport de récupération Genspark du 27/11/2026 confirme que 14 versions de la matrice ont été retrouvées dans `/home/user/webapp/docs/`, de la v1.2 (03/11/2026) à la v1.3.3 (12/11/2026). Les versions v1.2, v1.2.1, v1.2.5 et v1.3.3 ont été comparées à la sauvegarde du 16/11 et déclarées identiques à celle-ci. Les trois versions demandées v1.2.1, v1.2.5 et v1.3.3 contiennent les références D1 à D86 (86/86).

**La v1.3.3 est la plus récente retrouvée et remplace v1.2.1 comme source de travail pour l'audit.** Son tableau complet des statuts et ses modifications successives n'ont toutefois pas encore été relus ici ligne par ligne. Les nombres 16 arbitrées / 63 proposées / 5 à réexaminer / 2 caduques rapportés pour v1.2.1 au 04/11 ne doivent donc plus être présentés comme état courant de v1.3.3. Aucun nouveau décompte n'est affirmé avant lecture de la matrice source.

D3 est arbitrée : une base de données physiquement distincte par pharmacie ; plusieurs bases peuvent résider sur un même serveur ; séparation des bases ne signifie pas séparation obligatoire des serveurs. La trace antérieure v1.1 (base partagée) doit être conservée comme historique, et ARB-02 est fermé/rattaché à D3.

Les décisions D17–D71 provenant d'EXT02 à EXT05 ne sont donc pas globalement approuvées. D37 est à réexaminer ; D61 et D66 sont caduques. Les autres restent proposées selon la matrice, même lorsque les documents affichent une valeur recommandée dans leur tableau final. Les formulations de ces tableaux ne doivent pas être présentées comme des décisions PO.

EXT03 a fait l'objet d'un accord de principe sur l'extension, mais cela ne vaut pas validation individuelle des arbitrages D27–D40 ni validation des exigences réglementaires R1–R22.

### 4.3 Amendements PO postérieurs à la matrice — réconciliation obligatoire

Les échanges PO postérieurs contiennent des précisions qui doivent être comparées à la matrice v1.2.1 avant de réécrire les statuts ou les formulations :
- D78 : les types de service et leurs champs éventuels sont configurés par le pharmacien-administrateur, sans catalogue tunisien imposé par APEX ; la notification est limitée au type d'opération/service, au nom du client et à la date. Les données éventuellement collectées restent soumises à R47 et aux contrôles de confidentialité.
- D80 : le module comptable doit porter clairement la mention **« Comptabilité à titre indicatif »** ; PHARM'OS ne remplace pas la tenue des comptes officiels.
- D83 : le PO a explicitement validé un modèle comptable standard préconfiguré, adaptable, sans validation préalable obligatoire d'un expert-comptable, avec mention « Comptabilité à titre indicatif ». La ligne EXT06 et la définition de `chart_of_accounts` ont été corrigées en conséquence. D84 reste l'arbitrage de classification thérapeutique, à traiter séparément.
- D74 : toute réintégration corrective par un second collaborateur suppose que le pharmacien/admin lui ait explicitement accordé ce droit ; une décision immédiate par un second collaborateur sans contrôle d'habilitation est insuffisante.

Les D72–D86 étaient marquées ARBITRÉES dans la matrice v1.2.1. Les amendements postérieurs précisent ou corrigent certains contenus ; ils ne doivent pas être traités comme une annulation implicite de leur statut sans décision PO explicite.

### 4.4 Décisions opérationnelles récentes — 2026-11-25
Ces décisions sont distinctes de la numérotation Dxx des extensions :
- **DP-1 à DP-6 confirmées :** états de stock DISPONIBLE / EN_CONTROLE / QUARANTAINE / PERTE ; stock calculé à partir des mouvements ; lot obligatoire pour les médicaments ; réservations logiques produit ou lot avec FEFO ; sorties manuelles encadrées et dérogations ADMIN tracées ; entrées de stock limitées aux événements admis et justificatifs requis ; validation de migration uniquement sur copies isolées selon les conditions définies.
- Précisions PO complémentaires : archivage interdit tant qu'une quantité physique subsiste, y compris en QUARANTAINE ou PERTE ; indicateur « en attente de destruction » avec quantité et ancienneté, sans destruction automatique ; les corrections ne doivent pas masquer une destruction ni altérer l'état physique réel.
- **P86 :** aucune API ne doit permettre un passage en PERTE tant que le parcours utilisateur complet et la confirmation explicite de la quantité et des conséquences irréversibles ne sont pas disponibles ; le droit n'est pas accordé par défaut.
- **P89 / reprise P88, option A :** seuil mémoire maintenu à 500 000 kB ; cinq relevés consécutifs strictement au-dessus du seuil avant reprise ; surveillance pendant chaque opération et arrêt immédiat si le seuil est franchi ; aucune opération de nettoyage/libération ; reprise limitée à la phase 1 / contrôles, phase 2 suspendue. Les détails de protocole doivent être recopiés depuis le rapport P89 source avant d'être convertis en exigence de conception.

Ces décisions récentes doivent être répercutées dans les documents de stock, sécurité, archivage et procédure opérationnelle concernés, sans confondre l'état d'audit documentaire avec une autorisation de migration ou de développement.

## 5. Anomalies et écarts identifiés

| ID | Gravité | Fichier(s) | Constat | Action d'audit |
|---|---|---|---|---|
| AUD-001 | Élevée | Conception v1.1 | Les liens vers EXT02 et EXT03 sont restés à la racine après déplacement des extensions ; chemins relatifs cassés. | Corriger les liens vers `../02_EXTENSIONS/...` après vérification. |
| AUD-002 | Élevée | EXT04, EXT05 | Référence à `03_AUDIT_EXT03_v1.1` absent du dépôt ; décisions D42 et exigences citées ne peuvent pas être auditées sur la pièce source. | Retrouver le rapport source ou marquer les éléments dépendants « source manquante ». |
| AUD-003 | Élevée | EXT02–EXT06 / `04_DECISIONS_PO` | Absence de registre maître des décisions PO ; plusieurs statuts documentaires ne reflètent pas les arbitrages confirmés. | Créer un registre maître avec identifiant stable, date, formulation, portée, statut, source et fichiers impactés. |
| AUD-004 | Élevée | EXT06 D78 | Le texte propose mesures/observations structurées et chiffrées ; la décision PO limite la notification au type d'opération, nom du client et date, avec types de service définis par l'administrateur. | Réviser le texte et supprimer les mesures cliniques ou champs non autorisés par l'arbitrage. |
| AUD-005 | Élevée | EXT06 D80 / M20 | Le texte décrit une pré-comptabilité en partie double sans imposer clairement partout la mention obligatoire. | Ajouter « Comptabilité à titre indicatif » aux écrans, exports et documents concernés ; ne pas présenter PHARM'OS comme comptabilité officielle. |
| AUD-006 | Élevée | EXT06 D83 | Le plan comptable est déclaré inactif jusqu'à validation d'un expert-comptable. La décision PO valide un modèle standard adaptable sans validation préalable obligatoire. | Aligner le statut du modèle sur la décision PO, tout en conservant la distinction entre outil indicatif et comptes officiels. |
| AUD-007 | Élevée | EXT05 D61 / EXT06 | EXT05 prévoit l'auto-annulation dans une fenêtre de 15 minutes ; EXT06 remplace ce mécanisme par la règle du jour calendaire et une dérogation administrateur. | Marquer D61 obsolète et propager une règle unique ; vérifier le comportement d'annulation sans retour physique et les contrôles de stock. |
| AUD-008 | Élevée | EXT05 D74 / EXT06 D74 | EXT06 supprime `DISPONIBLE_DIRECT` et propose une décision immédiate par un second collaborateur ; l'arbitrage PO exige une habilitation explicite du pharmacien/admin. | Ajouter le contrôle d'habilitation explicite et distinguer exécutant, contrôleur et réintégrateur. |
| AUD-009 | Moyenne | EXT05 / EXT06 / stock | EXT05 introduit le bucket RESERVE ; EXT06 le supprime au profit d'une réservation logique ; les décisions DP récentes décrivent les états physiques sans RESERVE. | Harmoniser le modèle de stock et les exemples/tests ; aucune quantité physique ne doit être masquée par une réservation logique. |
| AUD-010 | Élevée | EXT02–EXT06 | Les statuts « proposition soumise à validation » et les tableaux « décisions à arbitrer » sont obsolètes pour certaines décisions PO déjà confirmées, mais restent pertinents pour les autres. | Mettre à jour décision par décision, sans déclarer une extension entière validée par extrapolation. |
| AUD-011 | Élevée | EXT04 | Le document dépend de validations réglementaires et de sources CNAM datées du 3 novembre 2026 ; l'état opérationnel des conventions doit être vérifié à la date de mise en œuvre. | Maintenir les règles inactives par défaut et exiger une source officielle/convention signée et validée. |
| AUD-012 | Élevée | Conception / EXT02 / EXT03 / EXT04 | Les exigences réglementaires R1–R51 sont encore marquées à valider dans les documents consultés ; aucun registre de validation réglementaire n'a été trouvé. | Créer une matrice réglementaire avec organisme, texte/source, date de vérification, expert, conclusion et lots bloqués. |
| AUD-013 | Moyenne | Conception v1.1 | Le document se présente comme référence unique, mais les extensions validées ou amendées et les décisions PO récentes ne sont pas consolidées dans son journal. | Clarifier la hiérarchie documentaire et actualiser le journal sans effacer l'historique. |
| AUD-014 | Élevée | EXT05 / conception | Les règles récentes DP-1 à DP-6 et P86 sur PERTE, archivage, destruction et confirmation utilisateur ne sont pas répercutées dans les documents audités. | Mettre à jour les workflows, API, permissions, migrations et tests, après cartographie exhaustive des passages concernés. |
| AUD-015 | Moyenne | `02_EXTENSIONS/README.md` | L'index omettait EXT02 après déplacement. | Corrigé dans le commit `ad9ff844f551188573bd5804d03034db64ee280e`. |

## 5.1 Réconciliation détaillée des statuts Dxx et des exigences réglementaires

### Statuts des décisions documentées

| Extension | Décisions | Statut maître au 04/11/2026 | Conséquence |
|---|---|---|---|
| EXT02 | D17–D26 | Proposées | Aucune recommandation ne doit être présentée comme arbitrage PO. |
| EXT03 | D27–D40 | Proposées, sauf D37 à réexaminer | Accord de principe sur l'extension ≠ validation de ses décisions. |
| EXT04 | D44–D58 | Proposées | Les recommandations D44–D58 ne sont pas des décisions PO. |
| EXT05 | D59–D71 | Proposées, sauf D61 et D66 caduques | D61 (15 minutes) est obsolète ; D66 ne constitue pas une règle de délégation approuvée. |
| EXT06 | D72–D86 | Arbitrées | Appliquer les arbitrages, avec amendements PO ultérieurs explicites (notamment D74, D78, D80). |

La matrice maître indique 16 décisions arbitrées (D3, D72–D86), 63 proposées, 5 à réexaminer (D2, D6, D10, D15, D37) et 2 caduques (D61, D66). Le total est 86. Cette matrice v1.2.1 n'a pas été validée globalement.

### Couverture des exigences réglementaires

Les tableaux formels d'exigences repérés dans les extensions couvrent :
- EXT03 : R1–R13 ;
- EXT04 : R23–R33 ;
- EXT05 : R34–R41 ;
- EXT06 : R42–R51.

**R14–R22 ne figurent pas dans les documents EXT03 actuellement versionnés dans GitHub.** Leur source a été retrouvée dans le dossier d'audit EXT03 v1.1 récupéré hors dépôt, mais son contenu détaillé n'a pas encore été inspecté ici ni intégré dans GitHub. Le registre R1–R51 reste donc incomplet dans le dépôt tant que cette source n'est pas importée et réconciliée.

Autres constats :
- EXT02 ne possède pas de tableau Rxx centralisé, mais comporte des exigences RH et laboratoire marquées [À VALIDER] (droit du travail, repos, heures supplémentaires, congés, présence du pharmacien, conservation RH, protection des données).
- EXT03 bloque le lot 5b sur R1–R6 et D27–D33.
- EXT04 bloque le lot 7b sur R23–R27, R29, R31 (sources) et R32, ainsi que sur des décisions D44, D46, D48 et D53 encore proposées dans la matrice.
- EXT05 bloque le lot 5d sur R34, R37, R38 et R40, ainsi que D59, D63, D70 et D71 encore proposées. La référence D61 a été retirée des prérequis car elle est caduque ; la règle de jour calendaire est portée par D75 dans EXT06.
- EXT06 maintient R42–R51 en attente de sources/validation ; les arbitrages D72–D86 sont distinctement marqués arbitrés.

### Amendements PO propagés dans EXT06

Les corrections suivantes ont été appliquées au document EXT06 et vérifiées après commit :
- **D74** : la décision immédiate simplifiée d'un second collaborateur exige une habilitation explicite accordée par le pharmacien-administrateur ; sans ce droit, pas de réintégration.
- **D78** : types de service et champs éventuels configurés par le pharmacien-administrateur, sans catalogue tunisien imposé par APEX ; notification limitée au type d'opération/service, au nom du client et à la date ; les données éventuellement collectées restent soumises à R47 et aux contrôles de confidentialité.
- **D80** : ajout de l'exigence de mention « Comptabilité à titre indicatif » pour les écrans, états et exports comptables.
- **D83** : correction du modèle de comptes : modèle standard APEX préconfiguré et adaptable, sans validation préalable obligatoire d'un expert-comptable ; il reste indicatif et ne remplace pas les comptes officiels.

Les documents EXT02 à EXT06 ont également reçu un rappel de leur statut PO réel. EXT05 marque D61 et D66 caduques. EXT06 ne présente plus D66 comme une décision arbitrée.

### Écarts additionnels à suivre

| ID | Gravité | Constat | Action |
|---|---|---|---|
| AUD-016 | Élevée | La matrice D1–D86 la plus récente, v1.3.3, a été retrouvée hors dépôt ; GitHub ne contient toujours pas le fichier maître. | Intégrer les versions sources pertinentes et conserver l'historique, sans reconstitution ni réécriture. |
| AUD-017 | Élevée | R14–R22 ne sont pas présentes dans la version EXT03 actuellement déposée ; elles figurent dans le périmètre de la source EXT03 v1.1 récupérée hors dépôt. | Intégrer la source EXT03 v1.1 et son audit, puis contrôler les exigences exactes R14–R22. |
| AUD-018 | Élevée | EXT06 présentait D66 comme « tranché » alors que D66 est caduque dans la matrice maître. | Corrigé : le texte signale désormais D66 caduque et la règle de délégation reste à réconcilier. |
| AUD-019 | Élevée | EXT05 conservait D61 (15 minutes) dans son tableau et ses prérequis malgré son statut caduque et la règle D75. | Corrigé : D61 marqué caduque ; retiré des prérequis du lot 5d. |
| AUD-020 | Élevée | Plusieurs extensions présentent leurs recommandations comme choix retenus sans refléter le statut maître. | Corrigé dans les en-têtes EXT02–EXT06 ; le détail des décisions reste à consolider. |

## 5.2 Audit d'architecture et de gouvernance globale

### Isolation des pharmacies — D3

Le dossier principal v1.1 proposait encore en §6.4 l'option A (base partagée) et en §11.2 D3 une base partagée au départ. Cette formulation contredisait l'arbitrage PO D3 (base physiquement distincte par pharmacie). **Correction appliquée** : §6.4 et la ligne D3 du tableau des décisions attendues indiquent maintenant l'option B arbitrée, avec bases multiples possibles sur un même serveur, base plateforme distincte, sauvegardes/restaurations indépendantes et `tenant_id` conservé en défense en profondeur.

### Superadministration APEX — exigence absente du modèle

La recherche dans le dossier principal, EXT02 et EXT06 ne retrouve aucune entité ni section décrivant le **SUPERADMIN APEX global**. EXT06 définit seulement `tenant_administrators`, c'est-à-dire l'administrateur pharmacien propre à chaque officine. Ce modèle ne couvre pas le besoin PO d'un superadmin global immuable et d'un centre de contrôle couvrant SaaS et On-Premises, les pharmacies, comptes administrateurs, licences/abonnements, supervision et paramètres globaux, sans accès métier indu aux bases des pharmacies.

Le PO a pourtant confirmé une exigence de SUPERADMIN APEX immuable (identifiant `apexproib@gmail.com`) avec contrôle global des pharmacies, comptes administrateurs, licences/abonnements, supervision et paramètres globaux. Cette exigence doit être modélisée séparément du pharmacien administrateur de chaque officine. Il manque au minimum un modèle de contrôle global séparé de la base métier de chaque pharmacie, avec identité superadmin, authentification renforcée, cycle de vie des tenants, gestion des licences/abonnements, journal d'administration global et limites explicites d'accès aux données. **Aucune table ou permission ne doit être ajoutée par supposition** : l'architecture doit être conçue à partir des décisions PO déjà confirmées.

### SaaS et On-Premises — couverture documentaire insuffisante

Le dossier principal décrit surtout une pile SaaS Cloudflare (D1, Workers, R2) et ne formalise pas un mode de déploiement On-Premises équivalent. Les documents ne définissent pas les variantes de packaging, base locale, sauvegardes/restaurations, mises à jour, licences, supervision et fonctionnement hors connexion pour une installation chez le client. La présence de mentions « local » ou « hors ligne » ne constitue pas une architecture On-Premises.

### Anomalies d'architecture supplémentaires

| ID | Gravité | Constat | Action |
|---|---|---|---|
| AUD-021 | Critique | Aucune définition du SUPERADMIN APEX global ni de son centre de contrôle dans les documents de conception consultés ; seul l'administrateur pharmacien par officine est décrit. | Créer une architecture de contrôle global séparée, en respectant l'isolation des bases pharmacies. |
| AUD-022 | Critique | Le dossier principal proposait une base SaaS partagée, en contradiction avec D3 arbitrée. | Corrigé dans §6.4 et le tableau D3 du dossier principal. |
| AUD-023 | Élevée | Architecture On-Premises absente : le dossier est centré sur Cloudflare SaaS. | Définir l'architecture de déploiement local et sa parité fonctionnelle avant développement commercial. |
| AUD-024 | Élevée | Aucun schéma de base « plateforme » ni registre des opérations globales n'est décrit malgré D3 et le besoin de superadministration. | Documenter le plan de contrôle, ses tables, ses API et les limites de séparation des données. |
| AUD-025 | Critique | Le PO a confirmé un SUPERADMIN APEX immuable (`apexproib@gmail.com`) et un centre de contrôle global, mais aucun modèle de données/API/permission correspondant n'existe dans les documents audités. | À intégrer dans l'architecture de contrôle global. |
| AUD-026 | Élevée | Collision de numérotation D72 : EXT06 D72 décrit le secours du pharmacien administrateur, alors que l'arbitrage PO D72 retrouvé décrit le SUPERADMIN APEX immuable. | Comparer la matrice maître et l'addendum PO ; ne pas fusionner les deux formulations sans source. |
| AUD-027 | Élevée | EXT06 D83 exigeait une validation préalable de l'expert-comptable, contrairement à la décision PO confirmée. | Corrigé : modèle comptable standard APEX préconfiguré, adaptable, sans validation préalable obligatoire ; mention indicative conservée. |

## 5.3 Réconciliation du stock — décisions DP-1 à DP-6

Les décisions PO DP-1 à DP-6, confirmées le 25/11/2026, sont plus récentes que les modèles stock des extensions. Elles sont désormais traitées comme référence prioritaire.

| Décision | Règle PO connue | État de propagation |
|---|---|---|
| DP-1 | Quatre états physiques : `DISPONIBLE`, `EN_CONTROLE`, `QUARANTAINE`, `PERTE` ; quantités par état calculées à partir des mouvements. | Propagée dans le modèle de données principal et EXT05/EXT06. |
| DP-2 | Lot obligatoire pour les médicaments. | Propagée dans la table `lots` du dossier principal. |
| DP-3 | Réservations logiques au niveau produit ; FEFO à l'allocation/vente. | Propagée dans le dossier principal et EXT06. |
| DP-4 | Réservations bloquantes ; dérogation ADMIN tracée ; écarts FEFO tracés. | Propagée dans le dossier principal et EXT06. |
| DP-5 | Quatre opérations d'entrée autorisées ; les régularisations exigent des justificatifs. | Principe connu, mais les quatre opérations exactes ne sont pas disponibles dans les fichiers du dépôt. Ne pas les reconstituer. |
| DP-6 | Préparation et essais de la migration 0022 uniquement sur des copies isolées, sous huit conditions. Aucune migration DEMO ni base réelle autorisée. | Les huit conditions exactes sont maintenant consultables dans le registre P81 transmis ; elles n'ont pas encore été intégrées au dépôt GitHub. Aucune migration lancée. |

Précisions PO associées :
- Archivage interdit tant qu'une quantité physique subsiste, y compris en QUARANTAINE ou PERTE.
- PERTE doit rester visible dans un indicateur « en attente de destruction » avec quantité et ancienneté ; aucune destruction automatique.
- Toute correction conserve l'état physique réel et ne peut masquer une destruction.
- La contre-passation doit tenir compte de l'état physique actuel.
- Le passage en PERTE par API reste interdit tant que le parcours utilisateur complet, la confirmation explicite de la quantité et l'acceptation des conséquences irréversibles ne sont pas disponibles ; le droit n'est pas accordé par défaut.

### Source P81 récupérée, mais non encore intégrée au dépôt GitHub

Le fichier `P81_REGISTRE_DECISIONS_PO_STOCK.md` a été transmis dans la conversation et son contenu intégral est maintenant consultable. Il confirme les décisions du PO du 25/11/2026 : DP-1 à DP-6, 37 identifiants DP*-R*, CAT-10/CAT-11, C1 à C4, les points O-5/O-6/O-9/O-10/O-11 et les vigilances V-1/V-2. Le rapport de récupération confirme que l'original existe dans `/home/user/webapp/docs/` (17 719 octets, 226 lignes) et que son SHA-256 correspond au manifeste P88 du 25/11.

Le contenu est donc récupéré et exploitable pour l'audit ; le constat « source introuvable » est clos. En revanche, le fichier source n'a pas encore été ajouté au dépôt GitHub `pharmos-lab`. Les analyses ci-dessous se fondent sur le registre transmis, sans reconstitution.

### Corrections propagées

- Dossier principal : états physiques, projection de stock calculée par mouvements, réservations produit, FEFO, lot obligatoire médicament, règles d'archivage et indicateur PERTE.
- EXT05 : suppression de `DISPONIBLE_DIRECT` et `PERTE_DIRECTE`, fin de l'exception 15 minutes, passage en `EN_CONTROLE`, contrôle D74 et confirmation utilisateur obligatoire avant PERTE ; suppression du bucket `RESERVE`.
- EXT06 : réservation logique au niveau produit, bucket `RESERVE` absent, FEFO et dérogations tracées.

Ces modifications sont documentaires uniquement. Aucune migration, aucun code applicatif, aucune base DEMO et aucune donnée réelle n'ont été touchés.

### Anomalie additionnelle

| ID | Gravité | Constat | Action |
|---|---|---|---|
| AUD-028 | Moyenne — suivi documentaire | Le registre P81 a été retrouvé dans `/home/user/webapp/docs/` et transmis intégralement, mais n'est pas encore intégré à GitHub. | Intégrer une copie fidèle et son manifeste dans le dépôt documentaire ; aucune préparation de migration n'en découle. |
| AUD-029 | Faible | Les liens Markdown internes des six documents de référence ont été contrôlés après déplacement et correction des chemins ; aucun lien local cassé n'a été détecté dans les liens cliquables examinés. | Contrôle effectué ; à rejouer après toute réorganisation ultérieure. |

### 5.4 Réconciliation complémentaire à partir du registre P81 récupéré

| Réf. | Constat vérifié | Classement / suite |
|---|---|---|
| DP5-R1 à R4 | P81 ferme les entrées physiques à quatre opérations : réception physique ; régularisation hors commande avec justificatif et droit distinct ; correction positive d'inventaire justifiée et contrôlée ; retour/annulation revendable limité aux quantités vendues et aux lots d'origine. | Référence PO exacte disponible ; propager la liste fermée dans le modèle de mouvements et les contrats de service. |
| DP5-R5/R6 | Commande fournisseur, catalogue national, adoption locale, réservation et changement d'état n'augmentent jamais le stock physique. Le retour client « contrôle d'abord » est reporté à un lot ultérieur. | À vérifier dans tous les workflows de réception, adoption et retours. |
| DP1-R9 / CAT-11 | Formule PO : « reçu + corrections nettes = disponible + en contrôle + quarantaine + perte + sorties nettes ». « Reçu » désigne les réceptions et régularisations seulement ; retours clients et contre-passations de réception restent dans les mouvements nets, pas dans « reçu ». | Corrigé dans EXT06 §5.4 et dans le scénario CAT-11 ; contrôle de cohérence documentaire effectué après mise à jour. |
| DP1-R7 / C2 | Sorties de QUARANTAINE et destruction des quantités en PERTE reportées à un lot ultérieur. PERTE reste visible avec un indicateur « en attente de destruction », quantité et ancienneté ; aucune destruction automatique. | EXT06 §5.4 clarifié : passage en PERTE distinct de la destruction physique différée. EXT05 conserve un mouvement `RETURN_LOSS` vers PERTE ; sa séparation d'avec un mouvement ultérieur de destruction reste à vérifier dans le parcours complet. |
| C1 | Archivage interdit tant qu'une quantité physique subsiste, y compris QUARANTAINE et PERTE. | EXT06 GOV-13 refuse aussi l'archivage d'un produit ayant des mouvements ; règle plus restrictive que le seul critère de quantité physique. À qualifier comme contrainte technique distincte, non comme reformulation de C1. |
| C3 | Corrections et contre-passations doivent préserver l'état physique réel ; une correction négative ne contourne pas la destruction ; une contre-passation tient compte de l'état actuel des quantités. | EXT05 et le modèle de conception ne décrivent pas complètement l'état cible des corrections négatives et contre-passations (V-2 reste ouvert). Aucun comportement précis ne peut être déduit. |
| C4 | Scénarios de cohérence CAT-11 préparés avant le code et exécutés uniquement sur copies fictives autorisées. | Exigence de préparation de tests à rendre explicite dans le plan de tests documentaire, sans exécution autorisée par P81. |
| DP-5 / M16 | Le modèle de conception énumère `LAB_OUTPUT` parmi les types de mouvements de stock, alors que DP-5 fixe une liste fermée de quatre opérations d'augmentation physique. EXT02 décrit M16 et les préparations, mais ne précise pas ici comment un produit préparé devient un stock vendable. | Ambiguïté à arbitrer avant toute réalisation : déterminer si `LAB_OUTPUT` augmente le stock physique, représente une transformation interne à bilan nul ou relève d'un flux distinct. Ne pas le traiter comme une cinquième entrée autorisée sans décision PO. |

### 5.5 Sources récupérées hors du dépôt et écarts de version

Le rapport de récupération Genspark confirme également :
- Matrice D1–D86 : 14 versions présentes dans `/home/user/webapp/docs/`, dernière version v1.3.3 (12/11/2026). La copie de récupération est dans `docs/RECUPERATION_SOURCES_AUDIT/1_MATRICE_DECISIONS_D1_D86/`.
- P82 et P86 : les sources `P82_SPECIFICATIONS_FINALES_STOCK.md` (41 488 octets) et `P86_ARBITRAGE_CONSOLIDE_13_POINTS.md` (63 462 octets) ont été retrouvées dans la Bibliothèque PHARM'OS et copiées dans `04_DECISIONS_PO/`. Leur statut propre est **PROPOSITION**, pas décision PO. P82 renvoie à P81 et P82_PLAN_REALISATION_STOCK_0022.md, non encore intégrés ; P86 renvoie à P85, au plan unique P86 et au rapport d'exécution, non encore intégrés.
- P81 : le rapport Genspark annonce 17 719 octets et SHA-256 `dec4d642…c2232c`. La pièce jointe actuellement consultable fait 28 221 octets et son SHA-256 calculé est `f0ada0d4…a5980bebc`. Il s'agit donc d'un fichier différent ; il n'a pas été publié sous le nom de la source maîtresse.
- EXT03 v1.1 et son audit : huit fichiers dans `docs/archives/AUDIT_EXT03_v1.1/`, datés du 03/11/2026 ; la v1.1 fait 107 433 octets. La v1.1 et les six documents d'audit ont été comparés au ZIP source `PHARMOS_PROJET_COMPLET.zip` et déclarés identiques octet à octet.
- Le fichier EXT03 actuellement dans GitHub est toujours version 1.0 (proposition du 03/11). EXT04 indique explicitement que ses références à EXT03 §15/§16/§19, D37 et D42 renvoient à la v1.1. Le dépôt présente donc un écart de version : les documents qui dépendent de la v1.1 ne disposent pas de cette source dans le dépôt.

La récupération confirme l'existence et l'intégrité des sources, mais ne vaut pas validation de leur contenu métier. La v1.1 et ses annexes doivent être intégrées fidèlement avant de déclarer la chaîne R1–R22 et D27–D43 complètement auditée.

| ID | Gravité | Constat | Action |
|---|---|---|---|
| AUD-030 | Élevée | La matrice la plus récente retrouvée est v1.3.3 (12/11/2026), tandis que le dépôt et le rapport s'appuient encore sur v1.2.1 et son état du 04/11. | Lire la v1.3.3 intégralement, établir le différentiel de versions et mettre à jour les statuts sans extrapolation. |
| AUD-031 | Élevée | EXT03 v1.1 et son audit ont été récupérés dans l'environnement de travail, mais le dépôt GitHub ne contient que EXT03 v1.0 ; EXT04 dépend explicitement de la v1.1 et cite D42. | Intégrer les sources originales v1.1 et l'audit, puis réconcilier D27–D43 et R1–R22. |
| AUD-032 | Élevée — arbitrage PO requis | DP-5 fixe une liste fermée de quatre entrées physiques, mais le modèle de conception comporte `LAB_OUTPUT` dans les types de mouvements sans règle de qualification au regard de DP-5. | Le point est maintenant explicitement signalé dans la conception ; le PO doit qualifier le flux M16 avant toute réalisation. |
| AUD-033 | Clos — correction documentaire | La formule CAT-11 d'EXT06 §5.4 et le scénario CAT-11 ont été alignés sur DP1-R9 : réceptions/régularisations dans « reçu », retours clients et contre-passations dans les mouvements nets. | Vérification effectuée après commit `ea6dd62`. |
| AUD-034 | Partiellement corrigé | EXT06 §5.4 indique désormais que PERTE reste visible avec quantité et ancienneté « en attente de destruction » et distingue la destruction physique. EXT05 utilise `RETURN_LOSS` pour le transfert vers PERTE, mais le mouvement ultérieur de destruction et son autorisation restent à vérifier. | Poursuivre le contrôle du workflow M10/M19 et des scénarios de destruction ; aucune destruction réelle autorisée. |
| AUD-035 | Moyenne — correction documentaire | La formulation DP-2 de la conception était trop générale et ne précisait pas les cas des dispositifs médicaux, des autres types et des médicaments déjà existants. | Corrigé dans la ligne `lots` et les précisions P81 de la conception : médicaments obligatoires à la création/adoption, dispositifs optionnels désactivés par défaut, autres types optionnels ; médicaments existants « à régulariser », sans conversion automatique de l'historique. |
| AUD-038 | Clos — garanties documentaires D75 | La v1.0 d'EXT06 mentionnait l'option « jour d'exploitation » sans rappeler toutes ses garanties. La décision PO antérieure l'accepte uniquement avec activation explicite par l'administrateur, motif, MFA, absence d'effet rétroactif et journalisation de chaque changement. | Corrigé dans EXT06 v1.0 : les cinq garanties sont maintenant écrites dans la ligne D75. La v1.1 récupérée et son rapport de revue conservent également ces conditions. R49 reste à valider juridiquement, notamment l'annulation après minuit pendant une garde et la clôture journalière. |
| AUD-036 | Élevée — règle de permission ambiguë | EXT06 AN4 attribuait les droits d'annulation exclusivement à l'administrateur en se référant à D66, alors que D66 est marqué caduque dans EXT05. | Corrigé dans EXT06 : AN4 est maintenant explicitement une proposition non arbitrée ; aucune autorité de délégation n'est déduite de D66. |
| AUD-037 | Élevée — collision d'identifiant | EXT06 utilise D72 pour la procédure de secours en cas de perte d'accès du pharmacien administrateur ; les décisions PO antérieures associent également D72 à l'immutabilité du SUPERADMIN APEX. Le document signale la collision mais conserve encore la procédure sous D72. | Ne pas fusionner ni implémenter ces deux sens ; vérifier la matrice v1.3.3 et les addenda pour rattacher la procédure de secours à un identifiant non ambigu. |

## 6. Blocages transverses avant développement

1. Absence de registre maître PO et de lien de traçabilité décision → règle → table/API/écran/test.
2. Sources EXT03 v1.1 et son audit récupérées hors dépôt mais non intégrées à GitHub ; réconciliation D27–D43 et R1–R22 encore incomplète.
3. Exigences réglementaires R1–R51 non centralisées et non prouvées comme validées.
4. Modèle de stock et parcours PERTE/destruction à réconcilier avec DP-1 à DP-6 et P86.
5. Règles d'annulation/réintégration à unifier entre EXT05, EXT06 et les décisions PO.
6. Architecture SaaS / On-Premises, superadministration et isolation physique des bases à consolider dans un document d'architecture versionné, puis à vérifier contre les décisions PO.
7. Les documents portent des statuts et dépendances de versions différentes (v1.0, v1.1, EXT03 v1.1 non déposée, EXT06 v1.1 évoquée dans l'historique mais fichier importé nommé v1.0). Les en-têtes et références de statut de EXT05/EXT06 reposaient encore sur la matrice v1.2.1 ; ils ont été annotés pour signaler que la v1.3.3 est la dernière version retrouvée et que les statuts doivent être revalidés après lecture intégrale.

## 7. Plan de travail de l'audit

| Phase | Travaux | Statut |
|---|---|---|
| A | Inventaire du dépôt, vérification des documents, structure et historique | Réalisée |
| B | Reconstitution du référentiel des décisions PO et des statuts | En cours |
| C | Réconciliation exhaustive des règles EXT02–EXT06 et du dossier principal | À réaliser |
| D | Contrôle du modèle de données, des permissions, API, workflows, états et tests | À réaliser |
| E | Matrice réglementaire et contrôle des sources | À réaliser — validation externe requise |
| F | Corrections documentaires non ambiguës et liens internes | À réaliser |
| G | Rapport final, liste des arbitrages PO résiduels et décision de préparation au développement | À réaliser |

## 8. Règles de clôture

- Aucun développement ou prompt Genspark n'est lancé dans le cadre de cet audit.
- Les décisions métier non confirmées restent en attente PO.
- Les exigences réglementaires restent bloquantes tant qu'une source et une validation compétente ne sont pas enregistrées.
- Les corrections documentaires seront réalisées sans modifier les arbitrages PO et seront vérifiées après commit.
- Le rapport final devra distinguer : conforme, non conforme, ambigu, source manquante, décision PO requise, validation réglementaire requise et hors périmètre.

---
**Fin de la version 0.3 — audit en cours.**
