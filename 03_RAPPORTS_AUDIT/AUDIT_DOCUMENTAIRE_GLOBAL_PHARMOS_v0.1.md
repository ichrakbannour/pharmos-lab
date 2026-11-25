# PHARM'OS — Audit documentaire global
**Version :** 0.1 — état initial / phase de cadrage et de réconciliation  
**Date :** 2026-11-25  
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

### 4.2 Décisions D17–D71
L'historique retrouvé au 4 novembre indique que D17–D71 n'étaient pas globalement arbitrées à cette date : D17–D26 (EXT02), D27–D43 (EXT03), D44–D58 (EXT04) et D59–D71 (EXT05) restaient principalement des propositions. D37 devait être réexaminée ; D61 et D66 étaient signalées comme caduques dans une analyse antérieure. Toute évolution ultérieure devra être rapprochée d'une confirmation PO datée avant de mettre à jour les statuts.

EXT03 a reçu un accord de principe, mais cela ne vaut pas validation intégrale de tous ses arbitrages ni de ses exigences réglementaires.

### 4.3 Décisions opérationnelles récentes — 2026-11-25
Ces décisions sont distinctes de la numérotation Dxx des extensions :
- **DP-1 à DP-6 confirmées :** états de stock DISPONIBLE / EN_CONTROLE / QUARANTAINE / PERTE ; stock calculé à partir des mouvements ; lot obligatoire pour les médicaments ; réservations logiques produit ou lot avec FEFO ; sorties manuelles encadrées et dérogations ADMIN tracées ; entrées de stock limitées aux événements admis et justificatifs requis ; validation de migration uniquement sur copies isolées selon les conditions définies.
- Précisions PO complémentaires : archivage interdit tant qu'une quantité physique subsiste, y compris en QUARANTAINE ou PERTE ; indicateur « en attente de destruction » avec quantité et ancienneté, sans destruction automatique ; les corrections ne doivent pas masquer une destruction ni altérer l'état physique réel.
- **P86 :** aucune API ne doit permettre un passage en PERTE tant que le parcours utilisateur complet et la confirmation explicite de la quantité et des conséquences irréversibles ne sont pas disponibles ; le droit n'est pas accordé par défaut.
- **P89 / reprise P88, option A :** seuil mémoire maintenu à 500 000 kB ; cinq relevés consécutifs au-dessus/au seuil requis avant reprise selon la formulation exacte de l'arbitrage ; surveillance pendant chaque opération et arrêt immédiat si le seuil est franchi ; aucune opération de nettoyage/libération ; reprise limitée à la phase 1 / contrôles, phase 2 suspendue. Les détails de protocole doivent être recopiés depuis le rapport P89 source avant d'être convertis en exigence de conception.

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
**Fin de la version 0.1 — audit en cours.**
