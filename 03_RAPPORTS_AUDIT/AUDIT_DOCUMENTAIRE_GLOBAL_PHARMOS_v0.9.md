# PHARM'OS — Audit documentaire global

**Version :** 0.9 — réconciliation EXT06 v1.1 avec les arbitrages PO D72–D86  
**Date :** 27/11/2026  
**Dépôt :** https://github.com/ichrakbannour/pharmos-lab  
**Branche :** main  
**Statut :** audit documentaire en cours. Revue en lecture seule ; aucune réalisation autorisée.

## 1. Sources examinées

- 02_EXTENSIONS/EXT06_CATALOGUE_GOUVERNANCE_FINANCE_v1.1.md ;
- 03_RAPPORTS_AUDIT/EXT06_v1.1_RAPPORT_DE_REVUE.md ;
- 04_DECISIONS_PO/MATRICE_DECISIONS_D1_D86_v1.2.md ;
- source originale MATRICE_DECISIONS_D1_D86_v1.3.3.md dans le miroir des 42 fichiers ;
- décisions PO et contraintes PHARM'OS déjà consignées.

Le rapport EXT06 est daté du 03/11/2026. Les arbitrages D72–D86 ont été consignés ensuite dans la matrice PO v1.2 et repris dans la matrice globale v1.3.3. Les statuts et formulations PO ultérieurs prévalent sur les propositions historiques d'EXT06.

## 2. Constat principal : EXT06 v1.1 n'est pas réconciliée avec les décisions PO postérieures

Le rapport de revue EXT06 v1.1 indique que seules D73 et D75 sont « RETENUES » et que les 13 autres décisions D72–D86 restent « PROPOSÉES ». La matrice globale v1.3.3 indique au contraire 17 décisions arbitrées au total, dont D3, D40 et toutes les décisions D72 à D86.

La matrice PO D1–D86 v1.2 fournit le détail des arbitrages D72–D86. L'état documentaire actuel est donc :
- D72–D86 : **ARBITRÉES** ;
- les propositions EXT06 v1.1 doivent être relues à la lumière de ces arbitrages ;
- la revue du 03/11/2026 reste utile comme analyse technique, mais son tableau de statut est historiquement dépassé ;
- aucun arbitrage PO ne vaut validation réglementaire, comptable, fiscale ou juridique externe ;
- aucun arbitrage métier n'autorise, à lui seul, le développement ou le déploiement.

## 3. Écarts matériels par décision

| Décision | Proposition EXT06 v1.1 | Arbitrage PO documenté | Résultat de la réconciliation |
|---|---|---|---|
| **D72 — gouvernance / secours administrateur** | Procédure de secours APEX hors application ; rapport EXT06 mentionne une proposition sans compte APEX permanent | Super Admin APEX immuable ; contrôle global ; création, blocage, suppression et recréation des comptes administrateurs de pharmacies ; données de pharmacie conservées indépendamment de l'accès ; procédure de récupération sécurisée du Super Admin | **Écart majeur.** Le scénario de secours EXT06 ne décrit pas l'architecture et les pouvoirs globaux décidés par le PO. Revoir le texte et les contrôles sans réduire l'isolation des pharmacies. |
| **D73 — dérogation à distance** | MFA au moment de la décision, confirmation, motif, empreinte de l'opération, usage unique, journalisation ; réglementés exclus | Même principe retenu par le PO | **Alignement de principe.** Conserver les garde-fous, notamment l'exclusion des réglementés et l'autorisation à usage unique. |
| **D74 — réintégration corrective** | Décision immédiate par un second collaborateur habilité ; EXT05 accordait par défaut le droit stock.return.decide à plusieurs rôles (INC-10 signalé dans la matrice PO) | Un second collaborateur ne peut réintégrer une opération corrective que si l'administrateur lui a explicitement accordé ce droit | **Écart de permission.** L'habilitation ne doit pas découler automatiquement d'un rôle ni être présumée. La permission dédiée est explicitement accordée par l'administrateur. |
| **D75 — période d'annulation** | Jour calendaire par défaut ; option « jour d'exploitation » activée explicitement, documentée et versionnée, jamais silencieuse | Jour calendaire par défaut ; option GARDES ou PERMANENTE seulement sur activation explicite de l'administrateur, avec motif et MFA, documentée/versionnée, sans effet rétroactif et avec journalisation de chaque changement | **Alignement avec précisions à répercuter.** Les garanties complémentaires PO doivent apparaître dans toute consolidation. |
| **D76 — bucket RESERVE** | Retrait du compartiment physique RESERVE proposé | Suppression du bucket physique RESERVE ; réservation logique distincte du stock physique disponible | **Alignement.** Ne pas recréer un bucket physique RESERVE. |
| **D77 — page après connexion** | Page déterminée selon le rôle (ex. caissier, préparateur, titulaire) | Accueil adapté au rôle **et aux permissions effectives** | **Écart de portée.** Le rôle seul ne suffit pas ; les permissions effectives doivent déterminer l'accès et la navigation. |
| **D78 — services** | Modèles de services livrés désactivés ; activation liée à l'autorisation réglementaire ; saisie possible de mesures et observations selon le service, données chiffrées | Aucun catalogue universel imposé ; l'administrateur de pharmacie définit ses services ; saisie limitée au type d'opération, nom du client et date ; pas de dossier clinique ni de mesures structurées non demandées | **Écart majeur.** Retirer les modèles/catalogues imposés par APEX et les champs cliniques ou mesures prévus par défaut. Les services et champs facultatifs relèvent du paramétrage de la pharmacie ; ne pas préremplir un catalogue tunisien universel. |
| **D79 — visibilité des marges** | Permission dédiée analytics.margin.read, étendue à G25 | Permission spécifique de consultation des marges, appliquée aussi à G25 | **Alignement.** |
| **D80 — comptabilité M20** | Pré-comptabilité en partie double ; le comptable tient les comptes officiels | Partie double M20 ; libellé visible obligatoire « Comptabilité à titre indicatif » ; écritures validées immuables ; correction par contre-passation et nouvelle écriture | **Alignement de fond, précision d'interface obligatoire.** Ne jamais présenter M20 comme comptabilité officielle ou comme remplacement du comptable. Le libellé PO doit être affiché. |
| **D81 — paie** | Import des montants, sans moteur intégré | Import des montants mensuels uniquement ; pas de moteur de paie | **Alignement.** |
| **D82 — réouverture de période** | Administrateur avec motif ; interdite après export définitif | Même règle ; décision de l'administrateur et motif obligatoires ; interdite après export final | **Alignement.** |
| **D83 — plan comptable** | Modèle indicatif inactif tant que l'expert-comptable ne l'a pas validé ; aucun plan fourni par défaut | Plan standard proposé comme modèle initial, adaptable par l'administrateur ; aucune validation préalable obligatoire par un expert-comptable ; modifications tracées et cohérence comptes/règles contrôlée | **Contradiction majeure.** La condition préalable de validation par l'expert-comptable doit être retirée. Le modèle standard peut être livré et adapté par l'administrateur, sans être présenté comme un plan officiel certifié. |
| **D84 — classification thérapeutique** | Classification dépendante de R43 ; classification APEX maintenue et sourcée à défaut | Classification thérapeutique standard initiale, modifiable par l'administrateur et distincte des données réglementaires officielles | **Clarification PO à répercuter.** La classification fonctionnelle initiale ne doit pas être confondue avec un référentiel réglementaire officiel ; les validations réglementaires applicables restent séparées. |
| **D85 — rapprochement bancaire** | Rapprochement proposé puis confirmé par un humain ; écarts tracés en exceptions | Même principe | **Alignement.** |
| **D86 — seuil de validation des charges** | Seuil paramétrable par l'administrateur | Même principe | **Alignement.** |

## 4. Conséquences pour le statut d'intégration EXT06

L'avis EXT06 du 03/11/2026 dit que la v1.1 est « prête à être soumise à la revue documentaire en vue de son intégration ». Cet avis ne vaut pas intégration effective. Les pièces consultées montrent que plusieurs formulations doivent encore être harmonisées avec les décisions PO ultérieures, en particulier D72, D74, D77, D78 et D83.

**Verdict : EXT06 v1.1 est une proposition technique révisée, mais pas une version intégrée et alignée sur les décisions PO actuelles.** La conception principale ne doit pas être déclarée consolidée sur la seule base du rapport de revue EXT06.

Les décisions PO à répercuter dans une future consolidation sont au minimum :
- D72 : gouvernance Super Admin APEX et récupération ;
- D74 : droit explicite administrateur, sans héritage automatique ;
- D75 : raison, MFA, versionnement, non-rétroactivité et journalisation ;
- D77 : permissions effectives, pas uniquement rôle ;
- D78 : données de service minimales et configurables, sans catalogue clinique imposé ;
- D80 : libellé « Comptabilité à titre indicatif » ;
- D83 : plan standard adaptable sans validation préalable obligatoire d'un expert-comptable ;
- D84 : classification standard fonctionnelle distincte des données réglementaires officielles.

## 5. Prototypes, tests et validations externes

Le rapport EXT06 v1.1 dénombre 80 tests nouveaux (145 au total) et trois prototypes techniques à réaliser au Lot 0 :
1. en-tête scellé et contrainte différée dans un batch D1 (CP8) ;
2. déclencheurs d'immutabilité et de transitions ;
3. recalcul d'un jour de métriques dans la limite CPU d'un Worker (MT7).

Les documents consultés spécifient ces tests et prototypes, mais ne fournissent pas de résultats d'exécution, de journaux CI, de mesures de performance ni de preuves de validation technique. Leur statut reste donc **NON EXÉCUTÉ / NON PROUVÉ dans le corpus examiné**.

Le rapport EXT06 identifie aussi R42–R51 comme points réglementaires à confirmer et associe plusieurs lots à ces prérequis. Aucun justificatif d'une validation externe correspondante n'a été retrouvé dans les pièces examinées. Les décisions PO D72–D86 ne remplacent pas ces validations.

## 6. Registre des constats EXT06

| ID | Constat | Priorité documentaire |
|---|---|---|
| EXT06-A01 | Tableau D72–D86 du rapport du 03/11/2026 obsolète : les 15 décisions sont désormais arbitrées | Haute |
| EXT06-A02 | D72 : procédure de secours à réécrire au regard du Super Admin APEX immuable et de ses pouvoirs décidés | Haute |
| EXT06-A03 | D74 : supprimer toute attribution implicite du droit de réintégration corrective ; droit explicitement accordé par l'administrateur | Haute |
| EXT06-A04 | D77 : navigation et page d'accueil fondées sur les permissions effectives, pas seulement le rôle | Haute |
| EXT06-A05 | D78 : retirer catalogue universel, mesures et observations cliniques par défaut ; conserver le minimum décidé | Haute |
| EXT06-A06 | D80 : afficher exactement « Comptabilité à titre indicatif » | Haute |
| EXT06-A07 | D83 : supprimer le prérequis de validation préalable par l'expert-comptable ; plan standard adaptable par l'administrateur | Haute |
| EXT06-A08 | D84 : distinguer classification thérapeutique fonctionnelle et données réglementaires officielles | Moyenne |
| EXT06-A09 | D75 : reporter les garanties PO complémentaires dans la spécification consolidée | Moyenne |
| EXT06-A10 | Tests et prototypes Lot 0 sans preuve d'exécution dans le corpus | Haute |
| EXT06-A11 | R42–R51 : validation réglementaire externe non démontrée par les pièces consultées | Haute |

## 7. Limites et interdictions

Cette revue ne modifie aucune source EXT06, EXT05 ou conception principale. Elle n'autorise :
- aucune modification de code, schéma ou migration ;
- aucun prototype, test, build, benchmark ou déploiement ;
- aucune activation de M20 ou des services ;
- aucune modification DEMO, PM2, service ou système ;
- aucune réouverture des décisions D72–D86 déjà arbitrées ;
- aucune interprétation des décisions PO au-delà de leur formulation documentée.

Toute intégration documentaire consolidée ou réalisation technique nécessite une autorisation PO distincte et le maintien des décisions arbitrées.

---
**Fin du rapport v0.9 — audit documentaire en cours.**
