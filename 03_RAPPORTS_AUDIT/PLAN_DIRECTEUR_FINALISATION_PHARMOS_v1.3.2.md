# PHARM'OS — Plan directeur de finalisation v1.3.2

**Version** : **1.3.2** (corrections ciblées) — 2026-11-04 · remplace pour usage la v1.3.1 (`PLAN_DIRECTEUR_FINALISATION_PHARMOS_v1.3.1.md`), conservée sans modification
**Historique** : v1.3 — plan directeur initial · v1.3.1 — compléments facultatifs, prérequis de PT-13 unifiés, ARB-08 limité, jalons corrigés · **v1.3.2 — PT-07 et avis R10 conditionnels pour la clôture de PT-13 (l'avis R10 reste obligatoire pour ARB-01), J-C sans le rapport PT-07, conditions C-01 à C-08 par niveau (RÉF-2), priorités séparées du caractère obligatoire (RÉF-10).** Détail : `JOURNAL_CORRECTIONS_PHARMOS_v1.3.2.md`.
**Nature** : plan de gouvernance et de finalisation **proposé** au propriétaire du projet. Il ne crée aucune décision : chaque
élément est marqué **[DÉCIDÉ]**, **[PROPOSÉ]**, **[RECO]**, **[TECH]**, **[JURIDIQUE]** ou **[À CONFIRMER]**.
**Fondement** : `RAPPORT_AUDIT_FINAL_COHERENCE_PHARMOS_v1.3.2.md` et les livrables v1.2.1 (lecture seule).
**Documents associés** : `JOURNAL_CORRECTIONS_PHARMOS_v1.3.2.md` · `REGISTRE_ACTIONS_PHARMOS_v1.3.2.md` (actions A-*) · `TABLEAU_SYNTHESE_GO_P0_PHARMOS_v1.3.2.md`.
**Rappel** : aucun code, schéma SQL, migration, installation ni déploiement n'est autorisé par ce document.

---

## Référentiel commun v1.3.2 (texte identique dans les quatre livrables)

Cette section est reproduite **à l'identique** dans le rapport d'audit, le plan directeur, le registre d'actions et le tableau
de synthèse v1.3.2. Toute modification ultérieure doit être appliquée simultanément aux quatre documents.

### RÉF-1 — Statut du GO P0

> **GO P0 CONDITIONNEL** : orientation **proposée** par l'audit et le comité, **sous réserve** de l'autorisation écrite du
> propriétaire (C-01) et de la satisfaction des conditions C-02 à C-08 **au niveau où chacune s'applique** (RÉF-2). **À la date du
> document, aucune condition n'est remplie et P0 n'est pas autorisé.**
>
> Cette orientation **ne signifie pas** : P0 déjà autorisé · prototypes déjà autorisés · développement du produit autorisé ·
> Lot 0 autorisé · architecture validée · pilote autorisé · commercialisation autorisée.

### RÉF-2 — Conditions du GO P0 (C-01 à C-08), par niveau d'application

Les conditions ne s'appliquent pas toutes au même moment. Quatre niveaux sont distingués :
**N1** ouverture de P0 · **N2** exécution d'un prototype donné · **N3** clôture de P0 · **N4** validation de l'architecture ou
ouverture du Lot 0.

| ID | Objet exact | Niveau(x) d'application | Bloquant ? | Action(s) | Responsable attendu | Preuve de satisfaction | Conséquence en cas de non-satisfaction | Statut |
|---|---|---|---|---|---|---|---|---|
| C-01 | Autorisation écrite du propriétaire fixant le périmètre de P0 : prototypes jetables, isolés du dépôt applicatif, données exclusivement fictives, aucune réutilisation de code sans revue et décision, liste des prototypes autorisés ; sous S2 ou S3, dérogation expresse au §16.2 du dossier v1.2.1 | N1 ; N2 (seuls les prototypes listés peuvent être exécutés) | Oui, tous scénarios | A-PRO-01 (A-PRO-02 peut figurer dans la même décision) | Propriétaire | Décision écrite, datée, signée ou confirmée, listant les prototypes autorisés | P0 ne s'ouvre pas ; aucun prototype ne peut être exécuté. Les actions préparatoires documentaires restent possibles | Non remplie |
| C-02 | Choix d'un scénario de séquencement (S1, S2 ou S3, RÉF-5), qui lève la contradiction ED-01 sur les prérequis de P0 | N1 | Oui pour la décision ; non pour l'alignement documentaire | A-PRO-02 (décision) ; A-DOC-01 (alignement ultérieur) | Propriétaire ; rédacteur documentaire | Décision écrite ; puis nouvelles versions des documents alignées | P0 ne s'ouvre pas ; ED-01 reste ouvert | Non remplie |
| C-03 | Liste des moteurs candidats d'ARB-01 fixée ; protocoles des prototypes dépendant du moteur étendus à chaque candidat | N2 pour G2, G3 et PT-13 · **sous S1 : N1** | Oui pour les prototypes concernés ; ne bloque ni PT-08 ni PT-12 (S2, S3) | A-PRO-05 ; A-TEC-01 (dépend de A-PRO-05) | Propriétaire (liste) ; architecte (protocoles) | Décision écrite ; protocoles révisés | G2, G3 et PT-13 ne peuvent pas être exécutés ; sous S1, P0 ne s'ouvre pas | Non remplie |
| C-04 | Protocole approuvé avant toute exécution : objectif, environnement, données, scénario, critère chiffré, preuve attendue, relecteur désigné | N2 (chaque prototype) ; de fait N1 pour le premier prototype exécuté | Oui pour le prototype concerné | A-TEC-02 ; A-PRO-20 | Architecte ; responsable qualité | Protocole signé par l'approbateur et le relecteur | Le prototype concerné ne peut pas être exécuté ; la **rédaction** de son protocole reste possible | Non remplie |
| C-05 | Données exclusivement fictives ; aucune donnée réelle de patient, de collaborateur ou de pharmacie | Règle fixée en N1 (A-PRO-01) ; contrôlée en N2 (chaque exécution) | Oui pour chaque exécution | A-PRO-01 (règle) ; A-TEC-03 (jeux de données) | Responsable qualité | Spécification des jeux de données ; attestation dans chaque rapport | Exécution interdite ; un rapport sans attestation n'est pas accepté | Non remplie |
| C-06 | PT-07 suspendu jusqu'à l'arbitrage d'ARB-08 ; la **mesure** du critère « fonctionnement local » de PT-13 en dépend ; aucun autre prototype n'en dépend | N2 (PT-07 seulement) ; N3 si PT-07 figure au périmètre autorisé ; N4 pour le Lot 0 (rapport PT-07 exigé avant le Lot 0, registre v1.2.1 §4) ; validation de l'architecture : [RECO], DI-01 | Oui pour PT-07 et pour le Lot 0 ; **non** pour la clôture documentaire de PT-13 (critère « non mesuré » motivé) ni pour ARB-01 | A-PRO-06 ; A-TEC-09 (préparation facultative : A-TEC-19) | Propriétaire (ARB-08) ; architecte (PT-07) | Décision écrite ARB-08 ; rapport PT-07 | PT-07 non exécutable ; critère local de PT-13 déclaré « non mesuré » ; Lot 0 impossible tant que le rapport PT-07 manque | Non remplie (ARB-08 non arbitré) |
| C-07 | Preuves archivées pour chaque prototype : rapport, mesures brutes, journaux, versions des outils, empreintes | N2 (acceptation de chaque prototype) ; N3 ; N4 | Oui pour l'acceptation du prototype concerné | A-TEC-18 | Responsable qualité | Archive vérifiable par un tiers | Prototype non accepté ; son résultat ne peut éclairer aucune décision | Non remplie (aucune exécution) |
| C-08 | Aucun résultat de prototype ne vaut décision ; ARB-01 et la validation de l'architecture font l'objet de décisions écrites du propriétaire | Règle applicable dès le premier résultat ; bloquante en N4 | Oui pour ARB-01, la validation de l'architecture et le Lot 0 | A-PRO-15 ; A-PRO-21 | Propriétaire | Décisions écrites ARB-01 et validation de l'architecture | Aucun résultat ne peut être présenté comme une décision ; validation de l'architecture et Lot 0 impossibles | Non remplie |

| Niveau | Conditions applicables |
|---|---|
| N1 — Ouverture de P0 | C-01, C-02 ; C-04 et C-05 pour le premier prototype exécuté. **Sous S1**, en outre : C-03, protocoles de tous les prototypes, ARB-05, ARB-06, ARB-08, ARB-09 (RÉF-5) |
| N2 — Exécution d'un prototype donné | C-01 (prototype listé), C-04 (son protocole), C-05 ; C-03 pour G2, G3 et PT-13 ; C-06 pour PT-07 ; C-07 pour son acceptation |
| N3 — Clôture de P0 | C-07 pour chaque prototype exécuté ; rapport PT-13 clos selon RÉF-4 ; rapport de chaque prototype du périmètre autorisé, dont PT-07 s'il y figure |
| N4 — Validation de l'architecture ou Lot 0 | C-07 ; C-08 ; preuves du plan directeur §10 ; pour le Lot 0, en outre, rapports de tous les prototypes PT-01 à PT-13, dont PT-07 (C-06, registre v1.2.1 §4) |

**Aucune condition n'est déclarée remplie** : aucune preuve n'existe à la date du document. Les conditions ne s'appliquent pas
toutes avant toute action : les **actions préparatoires documentaires** (rédaction des protocoles, spécification des jeux de
données, préparation des options de configuration, corrections documentaires, constitution des dossiers de sources) ne
requièrent aucune des conditions C-01 à C-08, sous réserve de la décision du propriétaire lorsqu'elle est requise (par exemple la
saisine d'un conseil externe). **Aucune action ne devient autorisée du seul fait qu'elle figure dans un plan ou dans l'orientation
GO P0 CONDITIONNEL.**

### RÉF-3 — Groupes de prototypes et dépendance à ARB-08

| Groupe | Prototypes | Prérequis propres (en plus de C-01, C-04, C-05) | ARB-08 requis ? |
|---|---|---|---|
| G1 — indépendants du moteur et d'ARB-08 | PT-08, PT-12 | Aucun | Non |
| G2 — dépendants du moteur, sans ARB-08 | PT-01, PT-02, PT-03, PT-04, PT-05, PT-06, PT-09, PT-10, PT-11 | C-03 (A-PRO-05, A-TEC-01) ; A-TEC-03 pour PT-01 à PT-06, PT-09, PT-10 | Non |
| G3 — configuration locale de référence | PT-07 | C-03 (moteur du mode local) ; ARB-08 (A-PRO-06) | **Oui** |
| PT-13 — comparatif des moteurs | PT-13 | RÉF-4 | Uniquement pour **mesurer** le critère « fonctionnement local » ; sans PT-07, critère « non mesuré » motivé, sans blocage de la clôture documentaire de PT-13 |
| Compléments proposés | PT-14, PT-15, PT-16, PT-17 | Acceptation par le propriétaire (A-PRO-17), complément par complément | Non spécifié : à préciser dans le protocole si le complément est accepté [À CONFIRMER, DI-06] |

PT-05 et PT-06 sont classés en G2 car le routage et les migrations sur N bases dépendent du moteur (par exemple liaisons
dynamiques D1, fichiers SQLite ou bases PostgreSQL) et leurs mesures alimentent PT-13 pour chaque candidat ; une version
minimale indépendante du moteur reste envisageable [À CONFIRMER, DI-03]. PT-11 est classé en G2 car la suite de conformité
inclut l'adaptateur de base de données ; une exécution partielle sur les autres adaptateurs est envisageable [À CONFIRMER, DI-02]. En attendant
ARB-08, peuvent avancer : l'exécution de G1 et de G2 (une fois leurs propres conditions remplies), la préparation des options de
configuration de référence (A-TEC-19, enquête documentaire auprès de pharmacies volontaires, sans installation ni test) et la
**rédaction** du protocole de PT-07, dont l'**approbation** (C-04) reste subordonnée à ARB-08.

### RÉF-4 — Prérequis de PT-13 (liste unique)

| Catégorie | Élément | Nature | Effet si absent |
|---|---|---|---|
| Cadrage | A-PRO-01 (P0 autorisé) ; A-PRO-22 (inscription de PT-13 au programme confirmée) ; A-PRO-05 (moteurs candidats) ; A-TEC-02 (protocole approuvé, incluant A-TEC-01) | Obligatoire | PT-13 ne démarre pas |
| Prototypes principaux | PT-01 et PT-02 (A-TEC-04) ; PT-03 (A-TEC-07) ; PT-04 (A-TEC-08) ; PT-05 (A-TEC-05) ; PT-06 (A-TEC-06) ; PT-09 (A-TEC-14) ; PT-10 (A-TEC-15) | Obligatoire | Rapport PT-13 incomplet ; ARB-01 non instruit |
| Concurrence sur le stock | Scénario intégré au protocole de PT-13, critère « écritures concurrentes » (dernier lot FEFO disponible, réservations logiques), introduit par A-TEC-01 [RECO, ED-07] | Requis si le protocole approuvé le retient | Critère « concurrence » limité aux postes de caisse |
| Configuration locale | PT-07 (A-TEC-09), après ARB-08 (A-PRO-06) | **Conditionnel** : requis uniquement si le critère « fonctionnement local » est effectivement mesuré | Critère déclaré « non mesuré », avec son motif (ARB-08 non arbitré ou PT-07 non exécuté) ; **clôture documentaire de PT-13 possible**. Indépendamment de PT-13, le rapport PT-07 est exigé **avant le Lot 0** (registre v1.2.1 §4) ; l'exiger dès la validation de l'architecture (J-D) est une [RECO] à confirmer (DI-01) |
| Avis juridique | A-JUR-01 (R10, R1) | **Conditionnel pour la clôture de PT-13** ; **obligatoire pour ARB-01** (A-PRO-15) | Critère déclaré « non renseigné », avec son motif ; clôture documentaire de PT-13 possible, **sans valoir validation juridique** ; ARB-01 ne peut pas être arbitré tant que l'avis manque |
| Compléments | PT-14 (ordonnanceur) et PT-16 (approfondissement de la concurrence sur le stock), via A-TEC-16 | Conditionnel : seulement s'ils sont acceptés (A-PRO-17) | **Refusés** : aucun effet bloquant, risque traité selon le plan directeur §8.4 · **Acceptés** : résultat intégré au critère concerné avant la clôture de PT-13, sauf décision contraire du propriétaire dans A-PRO-17 (DI-04) |

**Règle de clôture documentaire de PT-13** : PT-13 est clos lorsque ses prérequis obligatoires (cadrage, prototypes principaux)
sont satisfaits et que chacun des 13 critères du plan directeur §8.3 est mesuré, analysé, ou déclaré « non mesuré » / « non
renseigné » avec son motif et son effet sur ARB-01. Une clôture avec réserve sur le critère juridique **ne permet pas**
d'arbitrer ARB-01.

**Aucune mesure de PT-13 n'existe** : aucun prototype n'a été exécuté.

### RÉF-5 — Scénarios de séquencement (choix réservé au propriétaire)

| Scénario | Avant le premier prototype | Déroulement | Avantages | Inconvénients | Irréversibilité |
|---|---|---|---|---|---|
| **S1 — Prudent** (lecture littérale du dossier v1.2.1 §16.2 et de la colonne « Bloque » du registre v1.2.1) | C-01, C-02, C-03, C-04 pour tous les protocoles, C-05 ; ARB-05, ARB-06, ARB-09 (dossier) ; ARB-08 (registre : « bloque P0 ») | Tous les prototypes PT-01 à PT-13 après ces conditions ; rapport PT-13 ; puis ARB-01 | Aucune dérogation aux prérequis écrits | P0 attend des arbitrages dont aucun protocole ne dépend (ARB-05, ARB-06, ARB-09) ; ARB-08 retarde aussi G1 et G2 | Aucune |
| **S2 — Démarrage rapide sans engagement irréversible** | C-01 (avec dérogation expresse au dossier §16.2), C-02, C-04 et C-05 pour les protocoles de G1 | Vague 1 : G1 (PT-08, PT-12). Vague 2 : G2 dès C-03 et protocoles approuvés. Vague 3 : PT-07 dès ARB-08. Rapport PT-13 après G2, critère local complété après PT-07. ARB-05, ARB-06, ARB-09 avant le Lot 1 (registre v1.2.1) | Les preuves techniques avancent pendant les arbitrages | Suppose que le propriétaire retire ARB-05, ARB-06, ARB-09 des prérequis de P0 et limite ARB-08 à PT-07 et au critère local | Aucune si C-01, C-05 et C-08 sont respectées |
| **S3 — Intermédiaire** | Comme S2 pour G1 | Vague 1 : G1. G2, G3 et PT-13 après ARB-05, ARB-06, ARB-09 (dossier §16.2) et C-03 ; PT-07 exige en outre ARB-08 | Une partie des preuves démarre ; l'exigence écrite du dossier est conservée pour les prototypes dépendant du moteur | Gain de temps limité ; maintient un prérequis non justifié par les protocoles (constat ED-01) | Aucune |

Le présent référentiel **ne choisit pas** de scénario.

### RÉF-6 — Jalons

| Jalon | Contenu | Prérequis |
|---|---|---|
| J-A | Nouvelle version des documents v1.2.1 corrigeant les écarts ED-01 à ED-18 (ED-02 porte sur 18 points depuis la v1.3.1), validée par le propriétaire ; les écarts EV-01 à EV-18 propres aux livrables v1.3 sont corrigés par la v1.3.1, et les écarts EW-01 à EW-12 propres aux livrables v1.3.1 par la v1.3.2 | A-DOC-* |
| J-B1 | Conditions de démarrage de P0 : C-01, C-02, ainsi que C-04 et C-05 pour les premiers prototypes. **Sous S1**, en outre : C-03 et C-04 pour tous les protocoles, ARB-05, ARB-06, ARB-09 et ARB-08. **Sous S2 et S3** : aucune autre condition pour G1 ; les conditions de G2, G3 et PT-13 figurent en RÉF-3 et RÉF-5 | A-PRO-01, A-PRO-02, A-PRO-20, A-TEC-02 |
| J-C | Rapports des prototypes G1 et G2 archivés et revus (C-07) ; rapport PT-13 clos selon RÉF-4, chaque critère mesuré, analysé ou déclaré « non mesuré » / « non renseigné » avec motif. Le rapport PT-07 (G3) **n'est pas** une condition de J-C | Exécutions G1, G2 ; A-TEC-13 ; A-TEC-18 |
| J-B2 | ARB-01 arbitré **après** J-C **et** l'avis R10 (A-JUR-01) : un rapport PT-13 au critère juridique « non renseigné » permet J-C, pas J-B2 ; proposition INC-27 validée ou refusée ; politique de gestion des clés adoptée | A-PRO-15 (qui exige A-TEC-13, A-JUR-01, A-PRO-05), A-PRO-14, A-SEC-01 |
| J-D | Architecture validée par écrit (C-08) ; [RECO] critère « fonctionnement local » de PT-13 mesuré (exigence N3), à confirmer (DI-01) | J-C, J-B2, A-SEC-05, A-TEC-17, A-PRO-21 |
| J-B3 | Arbitrages et avis nécessaires au Lot 1 | A-PRO-03, A-PRO-04, A-PRO-07 à A-PRO-12 ; A-JUR-03, A-JUR-05 ; A-DOC-10 |
| J-E | Socle (Lot 0) vert dans les deux modes | J-D ; rapports PT-01 à PT-13, dont PT-07 (registre v1.2.1 §4 ; C-06) ; A-PRO-13 [RECO, DI-07] ; A-PRO-23 [RECO, DI-05] |
| J-F(n) | Critère de sortie de chaque lot | Prérequis consolidés du lot |
| J-G | Qualification, sécurité et conformité | Lots du périmètre pilote |
| J-H | Bilan du pilote | J-G ; critères H-1 à H-9 du plan directeur |
| J-I | Autorisation de commercialisation par le propriétaire | J-H ; conditions I-1 à I-8 du plan directeur |

Aucune date n'est fixée ; toute planification calendaire relève du propriétaire [À CONFIRMER].

### RÉF-7 — Chemin critique

1. A-PRO-20 (rôles de P0), A-PRO-01 (autorisation) et A-PRO-02 (scénario).
2. A-PRO-05 (moteurs candidats) → A-TEC-01 → A-TEC-02 (avec A-TEC-03) : protocoles G2 approuvés.
3. Exécution de G2 : A-TEC-04, A-TEC-05, A-TEC-06, A-TEC-07, A-TEC-08, A-TEC-14, A-TEC-15 ; avec A-PRO-22 → A-TEC-13 (rapport PT-13,
   clos documentairement même si les critères local ou juridique sont « non mesuré » / « non renseigné »).
4. A-PRO-15 (ARB-01), qui exige aussi A-JUR-01 (avis R10, R1) : sans cet avis, ARB-01 ne peut pas être arbitré, même si PT-13 est clos.
5. A-TEC-17 (dossier de validation) → A-PRO-21 (validation de l'architecture), qui exige aussi A-PRO-14 (INC-27) et A-SEC-01 (clés).
6. Lot 0, qui exige aussi les rapports PT-01 à PT-13 (registre v1.2.1 §4) ; A-PRO-13 (INC-01) et A-PRO-23 (interprétation du
   responsive) y sont rattachés à titre de [RECO] (DI-07, DI-05).

Branches pouvant devenir critiques : avis R10 (délai externe) → ARB-01 · ARB-08 → PT-07 → Lot 0 (et validation de l'architecture
si DI-01 est confirmée) ; ARB-08 ne bloque ni la clôture de PT-13 ni ARB-01 · INC-27 (avec PT-12) → validation de l'architecture · A-SEC-01 et A-SEC-05 → dossier de validation. Les
compléments PT-14 à PT-17 **ne figurent pas** sur le chemin critique : leur refus ne bloque ni PT-13 ni la validation de
l'architecture.

### RÉF-8 — Interdictions applicables à ce stade

Exécution de tout prototype avant l'autorisation écrite (C-01) et l'approbation de son protocole (C-04) · Lot 0 et tout lot
fonctionnel · schéma SQL, tables ou migrations dans le dépôt applicatif · intégration de code de prototype dans le produit ·
données réelles · installation chez une pharmacie · déploiement d'une version utilisable · pilote · communication commerciale
présentant une conformité non démontrée · activation d'une convention de tiers payant · arbitrage d'ARB-01 avant le rapport
PT-13 et l'avis R10.

### RÉF-9 — Responsabilités proposées

| Rôle | Responsabilités dans la suite immédiate | Actions principales |
|---|---|---|
| Propriétaire du projet | Toutes les décisions et autorisations ; saisine des autorités et des conseils externes | A-PRO-* ; A-JUR-01 (saisine) |
| Comité de gouvernance | Revue de passage de phase ; suivi des actions et des risques | Revue de J-A à J-I |
| Architecte logiciel | Protocoles, exécution et rapports des prototypes ; dossier de validation | A-TEC-01, A-TEC-02, A-TEC-04 à A-TEC-17 |
| Responsable qualité | Approbation conjointe des protocoles ; jeux de données fictifs ; preuves | A-TEC-02, A-TEC-03, A-TEC-18, A-DOC-07 |
| Responsable sécurité | Gestion des clés ; analyse de menaces ; immuabilité en mode local | A-SEC-01 à A-SEC-05 |
| Juriste / conseil réglementaire | Avis sur les points R ; contrats | A-JUR-01 à A-JUR-11 |
| Expert-comptable | Fiscalité, comptabilité, paie | A-JUR-07 (partie), A-JUR-11 (partie) |
| Pharmaciens référents | Validation métier ; options de configuration de référence | A-TEC-19 ; conseil pour A-PRO-03 et A-PRO-06 |
| Rédacteur documentaire | Nouvelles versions des documents, sans écrasement | A-DOC-* |

Rôles proposés ; attribution nominative par le propriétaire : A-PRO-20 (rôles nécessaires à P0) et A-PRO-24 (autres rôles).

### RÉF-10 — Priorité opérationnelle et caractère obligatoire (deux dimensions distinctes)

**A. Priorité opérationnelle** (échéance réelle, établie par une dépendance vérifiable)

| Priorité | Définition |
|---|---|
| P0-BLOQUANT | Indispensable avant l'ouverture de P0, **quel que soit le scénario** retenu |
| P0-PARALLÈLE | Avance pendant P0 sans bloquer son ouverture ; peut bloquer un prototype particulier (indiqué dans la colonne « Effet sur P0 ») |
| P1 | Nécessaire avant la validation de l'architecture ou l'ouverture du Lot 0, par une dépendance démontrée (registre d'actions §8) |
| P2 | Nécessaire pour une phase ultérieure (Lot 1 et suivants, pilote, commercialisation) |

**B. Caractère obligatoire** (nature de l'exigence, indépendante de la priorité)

| Caractère | Définition |
|---|---|
| Obligation juridique | Obligation découlant d'un texte ; son existence et sa portée sont à qualifier par un professionnel compétent [JURIDIQUE] ; aucune n'est présumée |
| Exigence réglementaire | Point réglementaire (R) dont la réponse officielle conditionne un paramétrage ou une fonction [JURIDIQUE] |
| Exigence de sécurité | Mesure de sécurité exigée par la conception ou par l'analyse des risques |
| Exigence contractuelle | Engagement à formaliser dans un contrat |
| Recommandation ou mesure de maîtrise | Mesure de maîtrise du projet (preuve, qualité, protocole) ou recommandation de l'audit |
| Gouvernance | Décision ou validation réservée au propriétaire, ou cohérence documentaire, sans nature juridique propre |

Règles : la priorité exprime l'**échéance réelle** d'une dépendance vérifiable, non l'importance ni le caractère obligatoire. Une
exigence juridique, réglementaire, de sécurité ou contractuelle reste **intégralement obligatoire quelle que soit sa priorité** :
P2 signifie « exigible à une phase ultérieure », jamais « facultatif ». Un démarrage anticipé peut être recommandé (délai externe
inconnu) sans modifier la priorité. Aucune action n'est maintenue en P1 pour préserver son importance. Si le propriétaire retient
S1, les actions dont la colonne « Effet sur P0 » indique « S1 : bloque le démarrage » deviennent bloquantes pour l'ouverture de P0.

*Fin du référentiel commun v1.3.2.*

---

## 1. Vision et périmètre du projet

| Élément | Contenu | Statut | Source |
|---|---|---|---|
| Produit | PHARM'OS — logiciel de gestion d'officine (éditeur APEX AI DEV) | [DÉCIDÉ] | Dossier v1.2.1 §2 |
| Marché | Pharmacies d'officine en Tunisie ; interface FR, AR (RTL), EN | [DÉCIDÉ] | Dossier §2, §5.3 |
| Modules | M00 à M20 (gouvernance, vente, caisse, stock, achats, catalogue, finance, ordonnances, tiers payant, corrections, comptabilité indicative, RH, laboratoire, IA…) | Périmètre consolidé ; extensions EXT03 v1.1, EXT04, EXT05 [PROPOSÉ] | Dossier §4 |
| Modes de déploiement | A — SaaS (hébergé par APEX) ; B — installation locale autonome | [DÉCIDÉ] (exigence du propriétaire) | Dossier §5.1 |
| Hors périmètre de la v1 | À préciser par lot ; tiers payant selon ARB-03 | [À CONFIRMER] | INC-06 |
| Objet du présent plan | Conduire le projet de l'état « conception consolidée » jusqu'à la commercialisation, par phases contrôlées A à I | [RECO] | Missions v1.3, v1.3.1 et v1.3.2 |

---

## 2. Architecture cible et principes non négociables

### 2.1 Architecture cible [PROPOSÉ ; preuve [TECH] requise]

Frontend unique SPA / PWA (FR · AR · EN, responsive) → API Hono `/api/v1` identique dans les deux modes → services métier
communs M00–M20 → **ports** (Database, ObjectStorage, JobScheduler, Clock, Secrets, LicenseVerifier, Notifier, AiGateway,
OcrGateway, ReferentialSource, BackupService, UpdateService) → **adaptateurs** SaaS et local → base d'instance + **une base
physiquement distincte par pharmacie**. Moteur de base : **non tranché** (ARB-01).

### 2.2 Principes non négociables

| # | Principe | Statut | Source |
|---|---|---|---|
| N1 | **Une base de données physiquement distincte par pharmacie**, dans les deux modes ; plusieurs bases possibles sur un même serveur ; aucune base métier commune ; sauvegarde, restauration, migration et export indépendants par pharmacie | **[DÉCIDÉ D3]** | Matrice D3 ; registre ARB-02 |
| N2 | Une seule base de code, même cœur, mêmes écrans, mêmes règles, même sécurité dans les deux modes | [DÉCIDÉ] | Dossier §5.1 |
| N3 | Le mode B fonctionne sans connexion permanente aux serveurs APEX | [DÉCIDÉ] | Dossier §5.1 |
| N4 | Super Admin APEX immuable ; suppression d'un accès ≠ suppression des données ; aucune destruction automatique de l'historique | [DÉCIDÉ D72] | Matrice D72 |
| N5 | Chiffres déterministes ; l'IA explique, n'exécute aucune opération sensible ; aucune donnée de santé ni donnée individuelle de paie vers un service d'IA externe | [DÉCIDÉ] principe du dossier v1.1 §7 ; conformité [JURIDIQUE R22] | Dossier §10.2 S2, §11 |
| N6 | Aucune valeur réglementaire présumée (taux, durées, obligations) ; toute valeur provient d'une source officielle rattachée | [DÉCIDÉ] | Dossier §17.1 |
| N7 | Montants en millimes entiers ; registres immuables ; contre-écritures ; audit chaîné | [PROPOSÉ] (règles du dossier v1.1, non arbitrées une à une) | Dossier §7–§9 |
| N8 | « Comptabilité à titre indicatif » | [DÉCIDÉ D80] | Matrice D80 |
| N9 | Responsive : une fonctionnalité dégradée sur mobile n'est pas validée | [DÉCIDÉ] (exigence) ; interprétation « restriction / dégradation » [À CONFIRMER] | Dossier §14.4 |
| N10 | Aucune décision sensible prise par l'IA ; aucune décision prise à la place du propriétaire | [DÉCIDÉ] | Règles de la mission |

**À ne pas confondre** : N1 (isolation, décidée) et ARB-01 (moteur, ouvert).

---

## 3. Décisions définitivement arbitrées

16 décisions [DÉCIDÉ], recalculées sur les lignes de la matrice v1.2.1 :

| Décision | Objet (résumé) | Effet principal sur le plan |
|---|---|---|
| D3 | Base physiquement distincte par pharmacie | Routage, migrations, sauvegardes par base (PT-05, PT-06, PT-09, DEP-13, BKP-07, UPD-07) |
| D72 | Super Admin APEX immuable ; cycle de vie des accès | Lot 1 ; INC-13, INC-14 restent à trancher |
| D73 | Validation à distance sur smartphone | Lot 1 ; ARB-07 en mode B |
| D74 | Réintégration par un second collaborateur habilité | Lot 5d ; ARB-10 |
| D75 | Jour calendaire, option jour d'exploitation | Lot 5 |
| D76 | Pas d'état de stock `RESERVE` | Lot 3 |
| D77 | Accueil selon les permissions effectives | Lot 1 |
| D78 | Registre des services simplifié | Lot 9c ; ARB-13, R54 |
| D79 | Visibilité des marges par permission | Lot 11 |
| D80 | Comptabilité à titre indicatif | Lot 7c |
| D81 | Import des montants de paie | Lot 7c |
| D82 | Réouverture de période | Lot 7c |
| D83 | Plan comptable standard actif et adaptable | Lot 7c ; R46 |
| D84 | Classification thérapeutique standard interne modifiable | Lot 2 ; R43 |
| D85 | Rapprochement bancaire confirmé | Lot 7 |
| D86 | Seuil de validation | Lot 7 |

Le libellé exact de chaque décision fait foi dans `MATRICE_DECISIONS_D1_D86_v1.2.1.md` (source unique) ; ce tableau n'en est
qu'un résumé et n'en modifie pas le sens.

---

## 4. Décisions encore ouvertes

| Catégorie | Éléments | Nombre | Échéance proposée [RECO] |
|---|---|---|---|
| Arbitrages nouveaux | ARB-01 (moteur), ARB-03 (tiers payant), ARB-04 (récupération du Super Admin), ARB-05 (Super Admin local), ARB-06 (fin de licence), ARB-07 (validation à distance en mode B), ARB-08 (configuration de référence), ARB-09 (accès éditeur aux données), ARB-10 (second collaborateur), ARB-11 (titulaire / administrateur), ARB-12 (code initial), ARB-13 (nom du client), ARB-14 (ordre des extensions) — 14 identifiants ARB-01 à ARB-14, dont ARB-02 fermé | 13 ouverts | §6 |
| Décisions à réexaminer | D2, D6, D10, D15, D37 | 5 | D2 avec ARB-01 ; D6, D37 avec ARB-03 ; D10 avant Lot 14 ; D15 avant Lot 1 |
| Décisions proposées | 63 (D1, D4, D5, D7–D9, D11–D14, D16–D36, D38–D60, D62–D65, D67–D71) | 63 | Par lot, avant son démarrage |
| Décisions caduques | D61, D66 | 2 | Confirmation formelle du propriétaire |
| Propositions sans numéro | INC-27 (paquets de référentiels), INC-38 (fin de licence, liée à ARB-06), préfixes RH-N / PAT-N (INC-29) | 3 | Avant Lot 0 (INC-27), Lot 1 (INC-38), Lot 1 (INC-29) |

---

## 5. Plan de clôture des contradictions

| Groupe | Contradictions | Moyen de clôture | Autorité | Échéance |
|---|---|---|---|---|
| Bloquantes non résolues | INC-06 | ARB-03 ; R23, R31 pour l'activation | Propriétaire ; CNAM / juriste | Avant Lot 5 |
| | INC-13 | ARB-09 ; R52 | Propriétaire ; juriste / INPDP | Avant Lot 1 |
| | INC-14 | ARB-05 ; ARB-04 ; R55 | Propriétaire ; juriste | Avant Lots 1 et 1b |
| | INC-27 | Validation de la proposition (registre §3.2) ; PT-12 | Propriétaire ; architecte | Avant Lot 0 |
| | INC-39 | P0 complet ; PT-13 ; ARB-01 ; avis R10 | Propriétaire ; architecte ; juriste | Avant Lot 0 |
| Ouvertes non bloquantes | INC-16 (ARB-07), INC-35 (R23), INC-38 (ARB-06) | Arbitrage ou réponse officielle | Propriétaire ; CNAM | Lots 1, 1b, 7b |
| Proposées non bloquantes (17 sur 19) | INC-01, 03, 04, 05, 15, 18, 21, 22, 25, 26, 29, 31, 32, 33, 36, 37, 41 (INC-27 et INC-39, également PROPOSÉE, sont traitées ci-dessus avec les bloquantes) | Validation séquentielle des extensions (ARB-14) ; validation au schéma par lot (PD-05) | Propriétaire | Avant le lot qui crée les tables concernées ; **INC-01 avant toute création de table** |
| Résolutions conditionnelles | INC-10 (ARB-10), INC-23, INC-28 (D34) | Confirmation du point dont elles dépendent | Propriétaire | Lots 5d, 11 |

Le décompte exact des contradictions PROPOSÉE fait foi dans `RAPPORT_AUDIT_COHERENCE_v1.2.1.md` §3 (19 lignes).

Critère général de clôture : décision ou réponse écrite, datée, sourcée, enregistrée dans la matrice ou le registre ; impacts
reportés dans les documents concernés par une nouvelle version ; aucune clôture par déduction.

---

## 6. Plan des arbitrages propriétaires

| Ordre [RECO] | Arbitrage | Action | Pourquoi maintenant | Éléments d'aide disponibles | Requis avant |
|---|---|---|---|---|---|
| 1 | Désignation des rôles nécessaires à P0 (approbateur, relecteur, responsable des preuves) | A-PRO-20 | Condition de C-04 | RÉF-9 | Démarrage de P0 (tous scénarios) |
| 2 | Autorisation de P0, périmètre ; choix du scénario S1, S2 ou S3 | A-PRO-01, A-PRO-02 | C-01, C-02 ; lever ED-01 | RÉF-1, RÉF-2, RÉF-5 | Démarrage de P0 (tous scénarios) |
| 3 | Liste des moteurs candidats d'ARB-01 | A-PRO-05 | C-03 ; conditionne les protocoles de G2, G3 et PT-13 | Matrice de déploiement §5 ; RÉF-3 | S1 : démarrage de P0 · S2, S3 : G2, G3, PT-13 (ne bloque ni PT-08 ni PT-12) |
| 4 | Inscription de PT-13 au programme P0 | A-PRO-22 | PT-13 ajouté par la v1.2.1 | RÉF-4 | PT-13 |
| 5 | Acceptation ou refus, complément par complément, de PT-14 à PT-17 | A-PRO-17 | Fixer le contenu de P0 ; le refus n'est pas bloquant (§8.4) | §8.4 | Clôture de PT-13 pour PT-14 et PT-16 **s'ils sont acceptés** ; aucun effet bloquant en cas de refus |
| 6 | ARB-08 — configuration de référence du mode B | A-PRO-06 | Conditionne PT-07 et la seule **mesure** du critère « fonctionnement local » de PT-13 (sans bloquer la clôture documentaire de PT-13 ni ARB-01) | A-TEC-19 (options préparées sans installation) ; pharmacies volontaires | PT-07 ; mesure du critère local de PT-13 ; Lot 0 (rapport PT-07) ; Lot 1b · S1 : démarrage de P0 (registre v1.2.1 « bloque P0 ») |
| 7 | ARB-05, ARB-06, ARB-09 | A-PRO-07, A-PRO-08, A-PRO-04 | Exigés avant P0 par le dossier §16.2 (S1, et S3 pour G2, G3, PT-13) ; avant le Lot 1 selon le registre (S2) | Registre §3 ; avis juridiques recommandés | Selon le scénario (RÉF-5) ; au plus tard Lot 1 / 1b |
| 8 | ARB-14 — ordre de validation des extensions | A-PRO-03 | Stabilité du modèle de données | Registre ARB-14 | Lot 1 (schéma), Lot 5b |
| 9 | ARB-03 — périmètre du tiers payant | A-PRO-12 | Colonnes de la caisse dès le Lot 5 | Registre §3.1 | Lot 1 (schéma), Lot 5 |
| 10 | INC-01 — renommage `coverage_rules` | A-PRO-13 | Avant toute création de table | Audit v1.2.1 INC-01 | Schéma par lot (PD-05) ; rattachement au Lot 0 : [RECO, DI-07] |
| 11 | ARB-04, ARB-07, ARB-11, ARB-12 | A-PRO-09, A-PRO-10, A-PRO-11 | Gouvernance et mode B | Registre §3, §3.3 | Lots 1, 1b |
| 12 | Validation de la proposition INC-27 | A-PRO-14 | Socle des référentiels | Registre §3.2 ; PT-12 | Validation de l'architecture (J-B2) ; Lot 0 |
| 13 | ARB-01 — moteur | A-PRO-15 | **Après** le rapport PT-13 (J-C) **et** l'avis R10 (A-JUR-01) ; l'avis reste obligatoire même si PT-13 est clos avec le critère juridique « non renseigné » | Rapport PT-13 | Validation de l'architecture ; Lot 0 |
| 14 | Validation formelle de l'architecture | A-PRO-21 | Phase D | Dossier A-TEC-17 | Lot 0 |
| 15 | Interprétation de l'exigence responsive | A-PRO-23 | Socle d'interface | Dossier §5.3 | Lot 0 [RECO, DI-05] |
| 16 | Autres rôles de gouvernance | A-PRO-24 | Organisation des lots | §13 | Lot 1 |
| 17 | ARB-10, ARB-13 | A-PRO-16 | Lots tardifs | Registre | Lots 5d, 9c |
| 18 | Décisions D par lot | A-PRO-18 | Avant chaque lot | Matrice | Chaque lot |

Aucun ordre ci-dessus ne choisit le scénario de séquencement : la colonne « Requis avant » indique l'effet de chaque scénario.

Forme attendue de chaque arbitrage : décision écrite, datée, signée ou confirmée par le propriétaire, enregistrée dans la
matrice (numéro D87 et suivants, sauf si elle tranche une décision existante) et dans le registre.

---

## 7. Plan des validations réglementaires

Aucun point R n'est validé. Les échéances ci-dessous sont des **propositions** ; la qualification juridique appartient aux
professionnels et autorités compétents.

| Priorité [RECO] | Points | Objet | Interlocuteurs (registre) | Requis avant |
|---|---|---|---|---|
| J1 | R10, R1 | Hébergement et traitement des données de santé (localisation, déclaration ou autorisation INPDP) | INPDP, ministère de la Santé, juriste | **ARB-01** (choix d'hébergement) ; critère « contraintes juridiques » de PT-13 (sinon « non renseigné ») ; toute décision d'hébergement ; Lot 1 |
| J2 | R52, R53 | Accès de l'éditeur ; suppression des données d'une pharmacie | Juriste, INPDP | Lot 1 |
| J3 | R55 | Responsabilités en installation locale | Juriste | Lot 1b |
| J4 | R48, R37 | Responsabilité du pharmacien administrateur ; délégations | Ordre, juriste | Lot 1 |
| J5 | R42, R43, R44, R51 | Catégories de produits, classification, TVA, prix réglementés | DPM, administration fiscale, expert-comptable | Lot 2 |
| J6 | R49, R36, R38 | Annulation, facturation, avoirs | Juriste, expert-comptable | Lot 5 |
| J7 | R2 à R9, R11, R14 à R19, R21 | Ordonnances, dispensation, conservation, OCR | DPM, Ordre, INPDP | Lots 5b, 5c |
| J8 | R34 à R41 | Retours, remise en vente, réglementés | DPM, Ordre | Lot 5d |
| J9 | R12, R23 à R33 | Tiers payant, CNAM, assurances | CNAM, SPOT, CNOPT, assureurs, juriste | Lot 7b (activation) |
| J10 | R45, R46, R50 | Immobilisations, comptabilité, paie | Expert-comptable, juriste | Lot 7c |
| J11 | R47, R54 | Services pharmaceutiques ; enregistrement nominatif | Ordre, INPDP | Lot 9c |
| J12 | R18, R35, R36 | Registres réglementés | DPM | Lot 10 |
| J13 | R20, R22, R29 | Droits des patients ; transmission aux assureurs ; LLM | INPDP, juriste | Lots 11, 13 |
| J14 | R13, R33 | Autres pays | — | Hors v1 |
| Transverse | PD-06, PD-08 | Dossier de sources juridiques ; contrats (SaaS, licence locale, intervention de l'éditeur, notification de violation) | Juriste | Pilote (phase H) au plus tard |

L'ordre J1 à J14 ci-dessus est un **ordre de démarche** recommandé. La **priorité opérationnelle** des actions juridiques est celle du
registre (RÉF-10) : A-JUR-01 en P0-PARALLÈLE ; A-JUR-02 à A-JUR-11 en P2, leurs échéances se situant au Lot 1 ou après. Ce classement
**ne diminue pas** leur caractère obligatoire ; un démarrage anticipé reste recommandé, les délais externes étant inconnus.

Forme attendue : réponse écrite, datée, sourcée (texte, article, version), rattachée au point R ; paramétrage mis à jour
uniquement à partir de cette source.

---

## 8. Plan détaillé des prototypes P0

### 8.1 Règles de conduite de P0 [RECO]

| # | Règle |
|---|---|
| P0-R1 | Prototypes **jetables**, dans un espace isolé du dépôt applicatif ; aucun code de prototype n'est intégré au produit sans revue et décision |
| P0-R2 | Données **exclusivement fictives**, générées pour l'occasion |
| P0-R3 | Chaque prototype a un protocole approuvé avant exécution (C-04), un rapport et des preuves archivées (C-07) ; aucun prototype n'est exécuté sans protocole approuvé |
| P0-R7 | Aucun prototype n'est exécuté avant l'autorisation écrite du propriétaire (C-01) ; l'orientation « GO P0 CONDITIONNEL » ne vaut pas autorisation (RÉF-1) |
| P0-R4 | Les protocoles dépendant du moteur sont exécutés **pour chaque moteur candidat** retenu par le propriétaire |
| P0-R5 | Un résultat de prototype éclaire une décision ; il ne la prend pas |
| P0-R6 | Aucun déploiement accessible au public ; aucune installation chez une pharmacie |

### 8.2 Matrice des prototypes

« Candidats » désigne la liste des moteurs fixée par le propriétaire pour ARB-01 (A-PRO-05, C-03 ; à défaut : A1 D1, A2 SQLite, A3 PostgreSQL).
Groupes (RÉF-3) : **G1** PT-08, PT-12 · **G2** PT-01 à PT-06, PT-09, PT-10, PT-11 · **G3** PT-07 · **PT-13** (RÉF-4). Tout prototype exige
en outre C-01, C-04 et C-05.

| ID | Objectif | Risque couvert | Dépendances | Prérequis | Environnement | Scénario de test | Résultat attendu | Preuve à conserver | Décision éclairée |
|---|---|---|---|---|---|---|---|---|---|
| PT-01 | Unité de travail atomique avec gardes — variante D1 `batch` | RB1 | — | Candidats incluant D1 | Environnement de développement local émulant D1 | Vente, correction, écriture comptable ; échec injecté à chaque instruction | Aucune écriture partielle | Script, journal d'exécution, état de base avant / après | ARB-01 |
| PT-02 | Idem — variante transaction (SQLite ; PostgreSQL si candidat) | RB1 | PT-01 (même suite) | Candidats | Poste local ; serveur de test | Même suite que PT-01 (DEP-05) | Résultats identiques à PT-01 | Idem, par moteur | ARB-01 |
| PT-03 | Contraintes différées, déclencheurs d'immutabilité et de transitions | RB2 | — | Candidats | Idem | UPDATE / DELETE sur registres ; transitions hors liste ; scellement d'en-tête | Refus systématique, identique entre moteurs ou écart documenté | Script, sorties d'erreur | ARB-01 ; T2 (dialecte unique) |
| PT-04 | Recalcul d'un jour de métriques ; génération comptable d'une journée | RB4 | Jeu fictif 300 ventes / jour | Candidats | Idem ; limites d'exécution de l'hébergeur si A1 | Recalcul complet d'une journée | Durée mesurée sous la limite de chaque environnement | Mesures horodatées | ARB-01 ; découpage des tâches |
| PT-05 | Routage vers la base de la pharmacie authentifiée | RB3, RC-03 | — | Candidats (G2, RÉF-3) [DI-03] | Serveur de test hébergeant N bases fictives | Requêtes concurrentes de plusieurs pharmacies ; tentative de forcer l'identifiant | Aucune lecture ni écriture croisée ; tentative refusée et journalisée (DEP-13) | Journaux, rapport | Validation de D3 en pratique |
| PT-06 | Migrations séparées sur N bases | RB3 | — | Candidats (G2, RÉF-3) [DI-03] | Idem (100 bases fictives) | Migration de 100 bases dont une échoue ; reprise | Version par base consultable ; base en échec fermée en écriture ; reprise idempotente (UPD-07) | Journal de migration, versions | Procédure de mise à jour |
| PT-07 | Installateur local | RB5 | ARB-08 | **ARB-08 arbitré** (A-PRO-06) ; candidats (moteur du mode local) | Configuration de référence | Installation sans intervention technique, service système, droits, antivirus | Installation complète | Vidéo ou journal d'installation, rapport | ARB-05 (éléments), Lot 1b |
| PT-08 | Horloge du serveur local | RB5 | — | Aucun | Poste local | Dérive de 2 h | Alerte ; aucune opération datée silencieusement de façon erronée (LOC-05) | Journal | Règle d'horodatage |
| PT-09 | Sauvegarde à chaud et restauration d'une base | RB6 | Jeu fictif | Candidats | Idem | Sauvegarde sous charge de caisse ; restauration d'une base parmi N | Instantané cohérent ; caisse non bloquée au-delà du seuil ; autres bases intactes (BKP-04, BKP-07) | Mesures, empreintes des archives | ARB-01 ; procédure de sauvegarde |
| PT-10 | Recherche plein texte FR et AR | RB7 | Catalogue fictif de 30 000 références | Candidats | Idem | Requêtes latines et arabes, fautes de frappe | < 150 ms côté serveur ou écart documenté ; pertinence vérifiée sur un jeu de requêtes | Mesures, jeu de requêtes | ARB-01 ; repli trigrammes |
| PT-11 | Suite de conformité des adaptateurs ; CI double | RB8, RC-02 | Squelette de ports (prototype) | Candidats (G2, RÉF-3) [DI-02] | Intégration continue de prototype | Mêmes tests sur les compositions A et B | DEP-01 et DEP-04 automatisés et verts | Rapport de CI | Validation de l'architecture (phase D) |
| PT-12 | Signature et vérification hors ligne des paquets | RB9, RC-05 | — | Aucun | Poste hors ligne | Paquet valide, altéré, non signé ; licence modifiée | Refus des paquets altérés ou non signés (UPD-02, DEP-11, LIC-02) | Journaux, empreintes | INC-27 ; ARB-06 (mise en œuvre) |
| PT-13 | Comparatif des moteurs candidats | RB1, RB3, RB8, RC-01 | RÉF-4 : PT-01 à PT-06, PT-09, PT-10 (obligatoires) ; PT-07 **conditionnel** (uniquement pour mesurer le critère « fonctionnement local » ; à défaut « non mesuré » motivé) ; PT-14 et PT-16 **seulement s'ils sont acceptés** (refus sans effet bloquant, §8.4) | A-PRO-01, A-PRO-22, A-PRO-05, A-TEC-02 (protocole de PT-13) (RÉF-4) ; avis R10 (A-JUR-01) **conditionnel** pour le critère « contraintes juridiques » (à défaut « non renseigné » motivé), **obligatoire** pour ARB-01 | Environnements identiques par candidat | Voir §8.3 | Rapport comparatif chiffré, sans recommandation imposée | Rapport, mesures brutes | **ARB-01** |

### 8.3 Critères complets de PT-13

| Critère | Mesure | Source des données |
|---|---|---|
| Transactions et atomicité | Taux d'écritures partielles sur échec injecté | PT-01, PT-02 |
| Écritures concurrentes | Débit et conflits avec plusieurs postes de caisse ; **scénario de stock** (dernier lot FEFO disponible, réservations logiques) | Protocole de PT-13 : scénario de stock introduit par A-TEC-01 (RÉF-4) ; PT-16 seulement s'il est accepté (approfondissement) |
| Isolation des bases par pharmacie | Absence de fuite ; coût d'ouverture d'une base ; nombre de bases par serveur | PT-05 |
| Contraintes et déclencheurs | Comportement des déclencheurs d'immutabilité et de transitions | PT-03 |
| Sauvegarde et restauration | Durée, cohérence, blocage de caisse ; restauration d'une base | PT-09 |
| Migrations | Durée sur N bases ; reprise ; retour arrière | PT-06 |
| Performances | Recherche, ajout de ligne, tableau de bord sur la volumétrie cible (30 000 références, 300 ventes / jour, 5 ans) | PT-04, PT-10, DEP-12 |
| Recherche | FR et AR | PT-10 |
| Exploitation | Supervision, mises à jour, opérations courantes | Rapport qualitatif argumenté |
| Coût d'hébergement | Estimation pour N pharmacies | Hypothèses explicites |
| Fonctionnement local | Installation, ressources, sauvegarde sur la configuration de référence | PT-07 après ARB-08 ; à défaut, critère déclaré « non mesuré » avec son motif, **sans bloquer la clôture documentaire de PT-13** |
| Complexité de maintenance | Nombre de dialectes, de jeux de déclencheurs, d'adaptateurs | Analyse documentée |
| Contraintes juridiques | Compatibilité avec la réponse sur R10 | Avis A-JUR-01 (R10, R1) ; à défaut, critère déclaré « non renseigné » avec son motif : clôture documentaire de PT-13 possible, **sans valoir validation juridique** ; ARB-01 non arbitrable |

Couverture : les 13 critères ci-dessus ont chacun une source identifiée. **Aucune mesure n'existe à ce jour** ; aucun critère ne
peut être présenté comme mesuré tant que le prototype source n'a pas été exécuté et archivé (C-07).

### 8.4 Prototypes complémentaires proposés [RECO]

Ces prototypes ne figurent pas dans le registre v1.2.1. Ils sont **proposés** pour couvrir les écarts ED-06 et ED-07 ; leur
ajout au registre nécessite l'accord du propriétaire (A-PRO-17). Ils restent **facultatifs** : leur refus ne bloque
automatiquement ni PT-13 ni la validation de l'architecture.

| ID proposé | Objet | Risque couvert | Critère de réussite | Si accepté (A-PRO-17) | Si refusé |
|---|---|---|---|---|---|
| PT-14 | Ordonnanceur : tâches planifiées, rattrapage au démarrage, poste éteint la nuit, absence de cron selon l'hébergement | Divergence des tâches (LOC-08) | Rattrapage sans doublon dans les deux modes | Protocole approuvé (C-04) ; résultat intégré au critère « exploitation » de PT-13 et à l'élément « ordonnanceur » du §10 | Pas d'effet bloquant sur PT-13 ni sur J-D. Le dossier de validation (A-TEC-17) traite l'ordonnanceur par une analyse documentée ; le risque LOC-08 reste **explicitement ouvert** jusqu'aux tests LOC du Lot 1b, sauf acceptation écrite du propriétaire |
| PT-15 | Restauration croisée A ↔ B d'une base de pharmacie | DEP-09 | Données, historique, audit chaîné et pièces intégralement conservés | Protocole approuvé ; résultat intégré à l'élément « sauvegarde, restauration » du §10 | Pas d'effet bloquant. DEP-09 reste au critère de sortie du Lot 15 ; la restauration croisée est déclarée « non démontrée » dans le dossier de validation ; risque **maintenu ouvert** |
| PT-16 | Concurrence sur le stock (plusieurs postes vendant le dernier lot FEFO) | RG7 | Aucun stock négatif non autorisé ; décisions cohérentes ; mêmes résultats entre moteurs | Protocole approuvé ; résultat intégré au critère « écritures concurrentes » de PT-13 avant sa clôture, sauf décision contraire dans A-PRO-17 (DI-04) | Pas d'effet bloquant. Le scénario de stock du protocole de PT-13 (A-TEC-01) reste la mesure de référence ; les tests ST des Lots 3 et 5 complètent la preuve |
| PT-17 | Chaîne de clés : génération, garde, rotation, sauvegarde des clés de chiffrement et de signature | RC-04, RC-05 | Restauration possible après rotation ; perte simulée documentée | Protocole approuvé ; résultat intégré à l'élément « gestion des clés » du §10 | Pas d'effet bloquant. La politique de clés (A-SEC-01) est revue sur une base documentaire ; le risque RC-04 est **maintenu ouvert** et l'épreuve de restauration après rotation est reportée aux tests BKP [À CONFIRMER], sauf acceptation écrite du risque |

Les compléments ne figurent pas sur le chemin critique (RÉF-7). Le rattachement d'un complément accepté à ARB-08 est à préciser
dans son protocole [À CONFIRMER, DI-06].

---

## 9. Critères d'acceptation de chaque prototype

Un prototype est **accepté** si, et seulement si, toutes les conditions suivantes sont remplies :

| # | Condition générale |
|---|---|
| CA-1 | Protocole approuvé avant exécution (objectif, environnement, données, scénario, critère chiffré) |
| CA-2 | Exécution réelle, sur données fictives, dans l'environnement décrit |
| CA-3 | Critère de réussite atteint, ou échec documenté avec analyse et option de repli |
| CA-4 | Preuves archivées : rapport, mesures brutes, journaux, versions des outils, empreintes des artefacts |
| CA-5 | Revue par un second intervenant (architecte ou responsable qualité) |
| CA-6 | Conclusion limitée à ce qui a été mesuré ; aucune extrapolation présentée comme résultat |

| ID | Critère d'acceptation spécifique |
|---|---|
| PT-01 / PT-02 | 100 % des échecs injectés sans écriture partielle, pour chaque moteur candidat |
| PT-03 | 100 % des UPDATE / DELETE interdits refusés ; 100 % des transitions hors liste refusées ; écarts entre moteurs listés |
| PT-04 | Durée mesurée inférieure à la limite d'exécution de chaque environnement, ou découpage démontré |
| PT-05 | 0 lecture ou écriture croisée sur l'ensemble des scénarios ; 100 % des tentatives forcées refusées et journalisées |
| PT-06 | 100 bases migrées ; base en échec isolée ; reprise idempotente ; version de chaque base lisible |
| PT-07 | Installation complète sur la configuration de référence sans intervention technique |
| PT-08 | Dérive détectée au-delà du seuil paramétré ; journalisation |
| PT-09 | Archive cohérente vérifiée par restauration ; blocage de caisse inférieur au seuil ; autres bases intactes |
| PT-10 | Objectif de 150 ms tenu ou écart chiffré ; pertinence FR et AR vérifiée |
| PT-11 | DEP-01 et DEP-04 exécutés automatiquement et verts sur les deux compositions |
| PT-12 | 100 % des paquets altérés ou non signés refusés hors ligne |
| PT-13 | Chacun des 13 critères du §8.3 renseigné par une mesure ou une analyse argumentée, ou explicitement marqué « non mesuré » (fonctionnement local avant ARB-08) ou « non renseigné » (contraintes juridiques sans avis R10), avec l'effet sur ARB-01 indiqué ; aucune mesure non exécutée présentée comme résultat ; aucun moteur recommandé sans justification chiffrée ; **clôture documentaire** possible avec ces réserves (RÉF-4) ; une réserve sur le critère juridique n'autorise pas ARB-01 |
| PT-14 à PT-17 | Uniquement s'ils sont acceptés : critère de réussite du §8.4, selon un protocole approuvé |

---

## 10. Architecture à valider avant le Lot 0 (phase D)

| Élément | Condition de validation | Preuve |
|---|---|---|
| Moteur d'exécution et de base | ARB-01 arbitré par le propriétaire (A-PRO-15), après J-C | Rapport PT-13 ; avis R10 (A-JUR-01) |
| Unité de travail | Décrite et éprouvée pour le moteur retenu | PT-01 / PT-02 |
| Dialecte et déclencheurs | Un seul dialecte, déclencheurs éprouvés | PT-03 |
| Routage et isolation (D3) | Démontrés | PT-05, DEP-13 |
| Migrations par base | Démontrées | PT-06, UPD-07 |
| Sauvegarde, restauration, restauration croisée | Sauvegarde et restauration démontrées ; restauration croisée démontrée si PT-15 est accepté, sinon déclarée « non démontrée » avec risque maintenu ouvert | PT-09 ; PT-15 si accepté |
| Ordonnanceur | Démontré dans les deux modes si PT-14 est accepté ; sinon analyse documentée et risque LOC-08 maintenu ouvert ou accepté par écrit | PT-14 si accepté ; sinon note d'analyse du dossier A-TEC-17 |
| Concurrence sur le stock | Mesurée | Scénario de stock du protocole PT-13 (A-TEC-01) ; PT-16 si accepté |
| Signature des paquets ; référentiels (INC-27) | PT-12 concluant ; proposition validée | PT-12 ; décision |
| Gestion des clés | Politique rédigée, revue et adoptée ; éprouvée si PT-17 est accepté, sinon risque RC-04 maintenu ouvert | A-SEC-01 ; PT-17 si accepté |
| Analyse de menaces | Réalisée et revue | A-SEC-05 |
| Ports et adaptateurs ; CI double | Démontrés | PT-11 |
| Fonctionnement local | Critère « fonctionnement local » de PT-13 mesuré (PT-07) — exigence à ce jalon : [RECO] à confirmer (DI-01) ; rapport PT-07 exigé au plus tard avant le Lot 0 | PT-07 |
| Décision formelle | Le propriétaire valide l'architecture par écrit (A-PRO-21, C-08) | Procès-verbal de validation |

Le refus d'un ou plusieurs compléments PT-14 à PT-17 ne bloque pas automatiquement la validation de l'architecture : l'élément
concerné est alors traité selon la colonne « Si refusé » du §8.4, et le propriétaire valide en connaissance des risques maintenus ouverts.

---

## 11. Feuille de route des lots 0 à 15

Le contenu des lots reste celui du dossier v1.2.1 §16.2. Le présent plan ajoute les **prérequis consolidés** (alignés sur la
colonne « Bloque » du registre, ED-02) et un **critère de sortie** par lot [RECO].

| Lot | Prérequis consolidés | Critère de sortie proposé |
|---|---|---|
| 0 Socle | Phase D validée (J-D) ; ARB-01 ; INC-27 validée ; politique de clés ; rapports PT-01 à PT-13, dont PT-07 (registre v1.2.1 §4) ; INC-01 tranché [RECO, DI-07] ; A-PRO-23 [RECO, DI-05] | Socle portable vert dans les deux modes (DEP-01, DEP-02, DEP-07, RSP-04, MOF-07) |
| 1 Plateforme et gouvernance | Lot 0 ; ARB-04 à ARB-07, ARB-09, ARB-11, ARB-12 ; R48, R52, R53 ; ARB-14 et ARB-03 pour le schéma | ADM, GOV, NAV-05 à 07, LIC, MOF verts dans les deux modes |
| 1b Installation locale | Lot 1 ; ARB-05, ARB-06, ARB-08 ; R55 | LOC, BKP, UPD verts sur la configuration de référence |
| 2 Catalogue | Lot 1 ; R42, R43, R44, R51 ; D4 | CAT, CAT-14 à 16 verts |
| 3 Stock et lots | Lot 2 | ST-01, ST-04, CAT-10, CAT-11 verts |
| 4 Achats | Lot 3 | Tests du dossier (Lot 4) verts |
| 5 Caisse et ventes | Lots 3, 4 ; R49 ; D59, D60 ; **ARB-03** | ANN-01 à ANN-17, DB, CX verts ; responsive comptoir (RSP-02, RSP-15) |
| 5b Ordonnances | Lot 5 ; D27–D43 ; R1–R13 ; **R14, R15, R19** ; **ARB-14** (EXT03 v1.1 validée) | Tests EXT03 v1.1 verts |
| 5c OCR | Lot 5b ; D41 ; R16 | O-01 à O-07, LOC-06 verts |
| 5d Corrections | Lots 5, 5b ; D59–D70 ; R34–R41 ; **ARB-10** | AN, RE, PA, DR, ST, FI, ANN-18 à 21 verts |
| 6 Clients et crédit | Lot 5 | Tests du dossier (Lot 6) verts |
| 7 Finance | Lots 4, 6 | FIN-01 à FIN-07, FIN-25, EXC-08 verts |
| 7b Tiers payant | Lots 5b, 7 ; D44–D58 ; R23–R33 ; ARB-03 ; EXT04 validée | TP-01 à TP-42 verts ; aucune convention active sans source |
| 7c Comptabilité et paie importée | Lots 7, 7b ; R45, R46, R50 | ACC, EXC, FIN-08 à FIN-24 verts |
| 8 Péremptions | Lots 3, 4 | Tests du dossier verts |
| 9 Inventaires | Lot 3 | RSP-06 et tests du dossier verts |
| 9b Laboratoire | Lots 1, 3 | Tests EXT02 « Laboratoire » numérotés et verts (ED-15) |
| 9c Services | Lots 2, 5 ; R47 ; **R54** ; **ARB-13** | SRV-10 à SRV-12 verts |
| 10 Réglementés | Lots 3, 5, 5b ; D5 ; R18, R35, R36 ; **R17** | T-REC-02 vert |
| 10b RH opérationnelle | Lots 1, 5 ; D17 | Tests EXT02 §12.2 numérotés et verts |
| 11 Métriques et rapports | Lots 5–9 ; R46 | MET, RNT, FIN-15 verts |
| 11b Performance et formation | Lots 10b, 11 | Tests EXT02 « KPI » verts |
| 12 Commandes intelligentes | Lots 4, 11 | Tests du dossier verts |
| 13 PHARM'OS AI | Lots 11, 12 ; D7 ; **R22** | S-IA, TP-40, LOC-06 verts ; liste blanche validée |
| 14 Hors-ligne du poste | Lot 5 ; D10, D39, D64 | LOC-03, F-HL verts |
| 15 Industrialisation SaaS | Tous | DEP-09, BKP, UPD verts ; console complète |

En gras : prérequis présents dans le registre mais absents du dossier §16.2 (ED-02 ; 18 écarts recomptés en v1.3.1, audit §10.2).

---

## 12. Dépendances et jalons

### 12.1 Phases

| Phase | Contenu | Entrée | Sortie (jalon) |
|---|---|---|---|
| **A — Consolidation documentaire** | Correction des écarts ED-01 à ED-18 ; registre complété ; protocoles P0 | Audit v1.3.2 | **J-A** : nouvelle version documentaire validée par le propriétaire |
| **B — Arbitrages et validations externes** | Arbitrages du §6 ; démarches juridiques du §7 | En parallèle de A | **J-B1** : conditions de démarrage de P0 (RÉF-6 ; ARB-08 seulement sous S1, sinon pour PT-07) · **J-B2** : ARB-01 **après J-C** et l'avis R10, INC-27, politique de clés · **J-B3** : arbitrages et avis nécessaires au Lot 1 |
| **C — Prototypes P0** | PT-01 à PT-13 (+ PT-14 à PT-17 s'ils sont acceptés) | J-B1 ; protocole approuvé (C-04) et conditions du groupe (RÉF-3) pour chaque prototype | **J-C** : rapports G1, G2 et PT-13 archivés et revus (RÉF-6) ; le rapport PT-07 n'est pas une condition de J-C (exigé avant le Lot 0, J-E) |
| **D — Validation de l'architecture** | §10 | J-C ; J-B2 | **J-D** : architecture validée par écrit par le propriétaire |
| **E — Lot 0 et socle** | Lot 0 | J-D ; rapports PT-01 à PT-13, dont PT-07 (C-06 ; registre v1.2.1 §4) | **J-E** : socle vert dans les deux modes |
| **F — Développement fonctionnel** | Lots 1 à 15, selon §11 | J-E ; prérequis de chaque lot | **J-F(n)** : critère de sortie de chaque lot |
| **G — Qualification, sécurité, conformité** | Recette complète dans les deux modes ; tests d'intrusion ; revue de sécurité ; conformité réglementaire documentée | Lots du périmètre pilote terminés | **J-G** : rapport de qualification et avis juridiques obtenus |
| **H — Pilote contrôlé** | Pharmacies volontaires, périmètre limité, réversibilité | J-G ; §15 | **J-H** : bilan du pilote |
| **I — Industrialisation et commercialisation** | Exploitation, support, contrats, facturation | J-H ; §16 | **J-I** : autorisation de commercialisation par le propriétaire |

### 12.2 Chemin critique [RECO]

Le chemin critique de référence est **RÉF-7** (référentiel commun). Les compléments PT-14 à PT-17 n'y figurent pas.

Aucune date n'est fixée : les délais dépendent des arbitrages et des réponses des autorités. Toute planification calendaire
doit être validée par le propriétaire [À CONFIRMER].

---

## 13. Gouvernance et responsabilités

Les rôles ci-dessous sont **proposés** ; leur attribution nominative appartient au propriétaire [À CONFIRMER] : A-PRO-20 pour
les rôles nécessaires à P0, A-PRO-24 pour les autres. Synthèse des responsabilités immédiates : RÉF-9.

| Rôle | Responsabilités | Ne peut pas |
|---|---|---|
| Propriétaire du projet (APEX AI DEV) | Arbitrages (D, ARB) ; autorisation des phases ; validation de l'architecture ; acceptation des risques | Être remplacé par un avis technique ou par l'IA |
| Comité de gouvernance | Revue de passage de phase ; suivi des actions et des risques ; contrôle de cohérence documentaire | Arbitrer à la place du propriétaire |
| Architecte logiciel | Protocoles et rapports P0 ; architecture ; revue technique | Choisir le moteur |
| Responsable sécurité | Analyse de menaces ; gestion des clés ; revue de sécurité ; tests d'intrusion | Valider la conformité juridique |
| Responsable qualité | Stratégie de tests ; preuves ; distinction spécifié / exécuté ; recette | Présenter un test non exécuté comme exécuté |
| Juriste / conseil réglementaire | Réponses aux points R ; contrats ; données personnelles | — |
| Expert-comptable | R44 à R46, R50 ; plan comptable (D83) | — |
| Pharmaciens référents | Validation métier ; configuration de référence ; pilote | — |
| Rédacteur documentaire | Nouvelles versions des documents ; journal des modifications | Modifier une version publiée |

Règles : toute décision est écrite et datée ; toute version de document est nouvelle (aucun écrasement) ; chaque passage de
phase fait l'objet d'un procès-verbal.

---

## 14. Gestion des risques

| ID | Risque | Mitigation | Indicateur | Phase |
|---|---|---|---|---|
| RC-01 | Moteur choisi sans preuve | Protocoles par candidat ; PT-13 complet | Critères §8.3 renseignés ou explicitement marqués « non mesuré » / « non renseigné » | C, D |
| RC-02 | Divergence SaaS / On-Premise | PT-11 ; CI double dès le Lot 0 | Suite de conformité verte | C–G |
| RC-03 | Fuite entre pharmacies sur serveur partagé | PT-05 ; DEP-13 ; tests d'intrusion | 0 fuite | C, G |
| RC-04 | Perte de clés de chiffrement | Politique de clés (A-SEC-01) ; PT-17 s'il est accepté | Restauration après rotation réussie | D |
| RC-05 | Compromission de la clé de signature | Garde hors ligne, double contrôle | Procédure revue | D |
| RC-06 | Accès éditeur non encadré | ARB-09 ; R52 | Décision et avis obtenus | B |
| RC-07 | Hébergement non conforme | Avis R10 avant ARB-01 | Avis obtenu | B |
| RC-08 | Développement sur décisions non arbitrées | Prérequis consolidés (§11) | 0 lot démarré avec prérequis ouvert sans décision écrite | F |
| RC-09 | Migrations de rattrapage sur N bases | INC-01, ARB-03, ARB-14 avant le Lot 1 | Schéma par lot validé | B, E |
| RC-10 | Perte de données en mode B | Sauvegardes, second support, contrat (R55) | BKP verts ; contrat signé | 1b, H |
| RC-11 | Tiers payant sur informations non officielles | Conventions inactives ; R23, R31 | Source officielle rattachée | 7b |
| RC-12 | Contrôle documentaire présenté comme test | Règle qualité (§13) | Registre des tests à 5 niveaux | Toutes |

Revue des risques à chaque passage de phase ; tout nouveau risque critique suspend la phase en cours jusqu'à décision du
propriétaire [RECO].

---

## 15. Critères de passage au pilote (phase H)

| # | Critère |
|---|---|
| H-1 | Lots du périmètre pilote terminés selon leurs critères de sortie, dans le ou les modes concernés |
| H-2 | Qualification complète (phase G) : tests exécutés, réussis et archivés ; aucun défaut bloquant ouvert |
| H-3 | Tests d'intrusion et revue de sécurité réalisés ; vulnérabilités critiques corrigées |
| H-4 | Avis juridiques obtenus pour les points R du périmètre pilote, en particulier R1, R10, R52, R53, R55 |
| H-5 | Contrats du pilote signés (responsabilités, données, réversibilité, notification de violation) |
| H-6 | Procédures de sauvegarde, restauration, mise à jour et support éprouvées |
| H-7 | Plan de retour arrière et de réversibilité pour chaque pharmacie pilote |
| H-8 | Formation des utilisateurs pilotes ; responsive validé au comptoir réel (RSP-15) |
| H-9 | Décision écrite du propriétaire autorisant le pilote, son périmètre et sa durée |

---

## 16. Conditions préalables à la commercialisation (phase I)

| # | Condition |
|---|---|
| I-1 | Bilan du pilote positif, défauts corrigés, décision écrite du propriétaire |
| I-2 | Conformité réglementaire documentée pour tout le périmètre commercialisé (points R fermés avec source) |
| I-3 | Contrats types validés par un juriste (SaaS, licence locale, intervention de l'éditeur, données personnelles) |
| I-4 | Organisation de support, d'exploitation et de supervision opérationnelle |
| I-5 | Procédures de sécurité opérationnelles (gestion des clés, incidents, notification de violation) |
| I-6 | Facturation APEX et gestion des licences opérationnelles (Lot 15) |
| I-7 | Aucune communication commerciale présentant une conformité non démontrée |
| I-8 | Documentation utilisateur FR, AR, EN |

---

*Fin du plan directeur de finalisation v1.3.2 — document de planification ; l'orientation GO P0 CONDITIONNEL ne vaut pas autorisation (RÉF-1) ; aucun développement autorisé.*