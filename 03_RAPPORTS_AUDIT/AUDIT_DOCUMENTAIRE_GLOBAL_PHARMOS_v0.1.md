# PHARM'OS — Audit documentaire global
**Version :** 0.2 — réconciliation PO, architecture et stock (audit en cours)  
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

### 4.2 État maître des décisions D1–D86 (matrice v1.2.1 retrouvée dans l'historique PO)

Le PO a précédemment désigné `MATRICE_DECISIONS_D1_D86_v1.2.1.md` comme document maître de statut des décisions D1–D86. Cette matrice n'est pas présente dans le dépôt GitHub actuellement audité ; son état connu doit être restauré dans `04_DECISIONS_PO` à partir de la source Genspark ou du fichier original, sans reconstitution approximative.

État consolidé confirmé au 4 novembre 2026 :
- **16 arbitrées :** D3 et D72 à D86 inclusivement.
- **63 proposées.**
- **5 à réexaminer :** D2, D6, D10, D15 et D37.
- **2 caduques :** D61 et D66.
- La matrice v1.2.1 n'avait pas été validée globalement ; son existence ne signifie donc pas que les 86 décisions sont toutes approuvées.

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

**R14–R22 ne figurent dans aucun des documents actuellement présents dans le dépôt.** EXT04 et EXT05 citent explicitement une version auditée EXT03 v1.1 et/ou un rapport `03_AUDIT_EXT03_v1.1` absent du dépôt ; EXT05 cite notamment R14. Le registre complet R1–R51 est donc incomplet dans GitHub et ne peut pas être déclaré exhaustif tant que la source EXT03 v1.1 et ses exigences R14–R22 n'ont pas été récupérées.

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
| AUD-016 | Élevée | La matrice D1–D86 v1.2.1, pourtant désignée par le PO comme document maître, est absente du dépôt GitHub. | Retrouver le fichier source et l'ajouter sans reconstitution ni réécriture. |
| AUD-017 | Élevée | R14–R22 sont absentes des fichiers du dépôt alors que les extensions suivantes y font référence. | Récupérer EXT03 v1.1 / rapport d'audit et restaurer la traçabilité. |
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
| DP-6 | Préparation et essais de la migration 0022 uniquement sur des copies isolées, sous huit conditions. Aucune migration DEMO ni base réelle autorisée. | Aucune migration lancée ; les huit conditions exactes sont absentes du dépôt. Source P81 à récupérer. |

Précisions PO associées :
- Archivage interdit tant qu'une quantité physique subsiste, y compris en QUARANTAINE ou PERTE.
- PERTE doit rester visible dans un indicateur « en attente de destruction » avec quantité et ancienneté ; aucune destruction automatique.
- Toute correction conserve l'état physique réel et ne peut masquer une destruction.
- La contre-passation doit tenir compte de l'état physique actuel.
- Le passage en PERTE par API reste interdit tant que le parcours utilisateur complet, la confirmation explicite de la quantité et l'acceptation des conséquences irréversibles ne sont pas disponibles ; le droit n'est pas accordé par défaut.

### Source PO manquante

Le PO a identifié le fichier source officiel `docs/P81_REGISTRE_DECISIONS_PO_STOCK.md`, qui contient DP-1 à DP-6, 37 décisions numérotées, CAT-10/CAT-11 et les huit conditions DP-6. Ce fichier n'est pas présent dans le dépôt GitHub examiné. Il est indispensable pour rétablir les formulations exactes de DP-5, DP-6, CAT-10/CAT-11 et les précautions complémentaires ; aucun détail ne sera inventé.

### Corrections propagées

- Dossier principal : états physiques, projection de stock calculée par mouvements, réservations produit, FEFO, lot obligatoire médicament, règles d'archivage et indicateur PERTE.
- EXT05 : suppression de `DISPONIBLE_DIRECT` et `PERTE_DIRECTE`, fin de l'exception 15 minutes, passage en `EN_CONTROLE`, contrôle D74 et confirmation utilisateur obligatoire avant PERTE ; suppression du bucket `RESERVE`.
- EXT06 : réservation logique au niveau produit, bucket `RESERVE` absent, FEFO et dérogations tracées.

Ces modifications sont documentaires uniquement. Aucune migration, aucun code applicatif, aucune base DEMO et aucune donnée réelle n'ont été touchés.

### Anomalie additionnelle

| ID | Gravité | Constat | Action |
|---|---|---|---|
| AUD-028 | Critique | Le registre source `docs/P81_REGISTRE_DECISIONS_PO_STOCK.md` manque dans GitHub ; les quatre entrées DP-5 et les huit conditions DP-6 ne sont donc pas vérifiables mot pour mot. | Récupérer le document source avant de déclarer le registre stock complet ou de préparer la migration 0022. |
| AUD-029 | Faible | Les liens Markdown internes des six documents de référence ont été contrôlés après déplacement et correction des chemins ; aucun lien local cassé n'a été détecté dans les liens cliquables examinés. | Contrôle effectué ; à rejouer après toute réorganisation ultérieure. |

## 6. Blocages transverses avant développement

1. Absence de registre maître PO et de lien de traçabilité décision → règle → table/API/écran/test.
2. Sources manquantes pour EXT03 v1.1 / D42 / rapport d'audit cité.
3. Exigences réglementaires R1–R51 non centralisées et non prouvées comme validées.
4. Modèle de stock et parcours PERTE/destruction à réconcilier avec DP-1 à DP-6 et P86.
5. Règles d'annulation/réintégration à unifier entre EXT05, EXT06 et les décisions PO.
6. Architecture SaaS / On-Premises, superadministration et isolation physique des bases à consolider dans un document d'architecture versionné, puis à vérifier contre les décisions PO.
7. Les documents portent des statuts et dépendances de versions différentes (v1.0, v1.1, EXT03 v1.1 non déposée, EXT06 v1.1 évoquée dans l'historique mais fichier importé nommé v1.0).

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
**Fin de la version 0.2 — audit en cours.**
