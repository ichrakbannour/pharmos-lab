# PHARM'OS — Tableau de synthèse GO P0 v1.3.2

**Version** : **1.3.2** (corrections ciblées) — 2026-11-04 · remplace pour usage la v1.3.1 (`TABLEAU_SYNTHESE_GO_P0_PHARMOS_v1.3.1.md`), conservée sans modification
**Historique** : v1.3 — synthèse initiale · v1.3.1 — points bloquants par scénario, conditions toutes « Non remplie », prototypes par groupe · **v1.3.2 — PT-13 clôturable sans PT-07 ni avis R10 (critères « non mesuré » / « non renseigné » motivés ; avis R10 toujours requis pour ARB-01), conditions C-01 à C-08 par niveau, priorités recalculées (4 / 14 / 25 / 28) et séparées du caractère obligatoire.** Détail : `JOURNAL_CORRECTIONS_PHARMOS_v1.3.2.md`.
**Objet** : vue d'ensemble, sur une page, des éléments conditionnant le lancement éventuel des prototypes P0.
**Sources** : `RAPPORT_AUDIT_FINAL_COHERENCE_PHARMOS_v1.3.2.md` (constats), `PLAN_DIRECTEUR_FINALISATION_PHARMOS_v1.3.2.md`
(planification), `REGISTRE_ACTIONS_PHARMOS_v1.3.2.md` (actions A-*), `JOURNAL_CORRECTIONS_PHARMOS_v1.3.2.md` (corrections).
**Statut** : synthèse documentaire. Elle ne vaut ni décision, ni autorisation de développement.

## Recommandation de gouvernance

> **GO P0 CONDITIONNEL** — orientation **proposée**, soumise à l'autorisation écrite du propriétaire (RÉF-1).
> **À la date du document : aucune condition C-01 à C-08 n'est remplie ; P0 n'est pas autorisé.** Les conditions s'appliquent par
> niveau (RÉF-2) ; les actions préparatoires documentaires n'en requièrent aucune. Cette orientation ne vaut
> autorisation ni des prototypes, ni du développement, ni du Lot 0, ni de l'architecture, ni d'un pilote, ni d'une commercialisation.

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

## 1. Points bloquants

« Démarrage de P0 » : premier prototype autorisé. La colonne distingue les scénarios (RÉF-5), dont le choix appartient au propriétaire.

| Point | Nature | Bloque le démarrage de P0 ? | Bloque | Action(s) |
|---|---|---|---|---|
| ED-01 — prérequis de P0 contradictoires (dossier §16.2 / registre) | Documentaire | **Oui, tous scénarios**, jusqu'à la décision C-02 ; l'alignement documentaire (A-DOC-01) suit sans bloquer | Démarrage de P0 | A-PRO-02 ; A-DOC-01 |
| Autorisation écrite et rôles de P0 non établis | Propriétaire | **Oui, tous scénarios** | Démarrage de P0 | A-PRO-01, A-PRO-20 |
| Aucun protocole approuvé | Technique / qualité | **Oui, tous scénarios** (C-04, prototype par prototype) | Chaque prototype | A-TEC-02 |
| ED-04 — PostgreSQL candidat absent des protocoles ; liste des moteurs non fixée | Technique | S1 : oui · S2, S3 : non pour G1 (PT-08, PT-12) | G2 (PT-01 à PT-06, PT-09, PT-10, PT-11), G3, PT-13 | A-PRO-05, A-TEC-01 |
| ARB-08 — configuration de référence du mode B | Propriétaire | S1 : oui (registre v1.2.1 « bloque P0 ») · S2, S3 : non | PT-07 ; **mesure** du critère « fonctionnement local » de PT-13 (sans bloquer sa clôture documentaire ni ARB-01) ; Lot 0 (rapport PT-07) ; Lot 1b | A-PRO-06 (préparation : A-TEC-19) |
| ARB-05, ARB-06, ARB-09 (prérequis de P0 selon le dossier §16.2) | Propriétaire | S1 : oui · S3 : non pour G1, oui pour G2, G3, PT-13 · S2 : non (avant le Lot 1) | Selon le scénario ; Lots 1, 1b | A-PRO-07, A-PRO-08, A-PRO-04 |
| ED-16 — PT sans statut, environnement ni preuve attendue dans le registre v1.2.1 | Documentaire | **Non** : ces éléments figurent dans chaque protocole approuvé (C-04) | Complétude du registre des points ouverts | A-DOC-04 ; A-TEC-02 |
| INC-06 — périmètre du tiers payant | Contradiction bloquante | Non | Lot 5 (Lot 1 pour le schéma) | A-PRO-12 |
| INC-13 — accès éditeur aux données | Contradiction bloquante | Non (sauf S1 et S3 via ARB-09, ci-dessus) | Lot 1 | A-PRO-04, A-JUR-03 |
| INC-14 — Super Admin en mode B | Contradiction bloquante | Non (sauf S1 et S3 via ARB-05, ci-dessus) | Lots 1, 1b | A-PRO-07, A-SEC-02 |
| INC-27 — paquets de référentiels | Contradiction bloquante | Non (PT-12 l'éclaire) | Validation de l'architecture ; Lot 0 | A-PRO-14, A-TEC-12 |
| INC-39 — architecture portable | Contradiction bloquante | Non (P0 l'instruit) | Validation de l'architecture ; Lot 0 | A-TEC-13, A-PRO-15 |
| ED-02 — 18 prérequis absents du plan des lots (14 annoncés en v1.3) | Documentaire | Non | Lots 0 à 13 | A-DOC-02 |
| ED-12 — R10 non rattaché au choix du moteur | Juridique | Non | ARB-01 (obligatoire) ; critère juridique de PT-13 (à défaut « non renseigné », sans bloquer sa clôture) | A-DOC-06, A-JUR-01 |

## 2. Décisions du propriétaire

| Décision | Priorité (RÉF-10) | Requise avant | Action |
|---|---|---|---|
| Désignation des rôles nécessaires à P0 (approbateur, relecteur, preuves) | P0-BLOQUANT | Démarrage de P0 | A-PRO-20 |
| Autorisation et périmètre de P0 (C-01) | P0-BLOQUANT | Démarrage de P0 | A-PRO-01 |
| Scénario de séquencement S1 / S2 / S3 (C-02) | P0-BLOQUANT | Démarrage de P0 | A-PRO-02 |
| Liste des moteurs candidats (C-03) | P0-PARALLÈLE (S1 : bloque le démarrage) | G2, G3, PT-13 | A-PRO-05 |
| Inscription de PT-13 au programme P0 | P0-PARALLÈLE | PT-13 | A-PRO-22 |
| Acceptation ou refus de PT-14 à PT-17, complément par complément (refus non bloquant) | P0-PARALLÈLE | Contenu de P0 ; clôture de PT-13 si PT-14 ou PT-16 acceptés | A-PRO-17 |
| ARB-08 configuration de référence | P0-PARALLÈLE (S1 : bloque le démarrage) | PT-07 ; mesure du critère local de PT-13 ; Lot 0 | A-PRO-06 |
| ARB-05, ARB-06, ARB-09 | P0-PARALLÈLE (S1 et S3 : voir §1) | Selon le scénario ; Lots 1, 1b | A-PRO-07, A-PRO-08, A-PRO-04 |
| ARB-01 moteur | P1 | Validation de l'architecture ; **après** le rapport PT-13 **et** l'avis R10 (obligatoire même si PT-13 est clos avec réserve) | A-PRO-15 |
| INC-27 paquets de référentiels | P1 | Validation de l'architecture | A-PRO-14 |
| INC-01 renommage | P2 (reclassée en v1.3.2) | Avant le Lot 1 et avant toute création de table ; Lot 0 à titre de [RECO] non bloquant (DI-07) | A-PRO-13 |
| Interprétation du responsive | P1 | Lot 0 [RECO, DI-05] | A-PRO-23 |
| Validation de l'architecture | P1 | Lot 0 | A-PRO-21 |
| ARB-14 extensions ; ARB-03 tiers payant | P2 | Lot 1 (schéma) | A-PRO-03, A-PRO-12 |
| ARB-04, ARB-07, ARB-11, ARB-12 | P2 | Lots 1, 1b | A-PRO-09, A-PRO-10, A-PRO-11 |
| Autres rôles de gouvernance | P2 | Lot 1 | A-PRO-24 |
| ARB-10, ARB-13 | P2 | Lots 5d, 9c | A-PRO-16 |
| 70 décisions non arbitrées | P2 | Par lot | A-PRO-18 |

L'ancienne action A-PRO-19 (« réserves v1.2.1 ») est **retirée** : ses cinq objets sont répartis entre A-DOC-11 (inventaire des
tables, vérification documentaire), A-DOC-12 (INC-13, consignation), A-PRO-22 (PT-13), A-PRO-23 (responsive) et A-DOC-13
(référence documentaire de la date de D3). **D3 est arbitrée ; la date de D3 n'est pas une nouvelle décision et D3 n'est pas
réouverte.**

## 3. Validations juridiques

| Points | Objet | Requis avant | Action | Priorité opérationnelle (RÉF-10) |
|---|---|---|---|---|
| R10, R1 | Hébergement et traitement des données de santé | ARB-01 ; critère « contraintes juridiques » de PT-13 ; toute décision d'hébergement ; Lot 1 | A-JUR-01 | P0-PARALLÈLE |
| PD-06 | Dossier de sources juridiques | Tous les points R | A-JUR-02 | P2 (reclassée) |
| R52, R53 | Accès de l'éditeur ; suppression des données | Lot 1 | A-JUR-03 | P2 (reclassée) |
| R48, R37 | Pharmacien administrateur ; délégations | Lot 1 | A-JUR-05 | P2 (reclassée) |
| R55 | Responsabilités en mode B | Lot 1b | A-JUR-06 | P2 (reclassée) |
| R42, R43, R44, R51 | Catégories, classification, TVA, prix | Lot 2 | A-JUR-07 | P2 (reclassée) |
| R16, R22 | OCR ; LLM | Lots 5c, 13 | A-JUR-09 | P2 (reclassée) |
| R12, R23 à R33 | Tiers payant | Lot 7b (activation) | A-JUR-04 | P2 |
| Notification de violation | Procédure | Pilote | A-JUR-08 | P2 |
| PD-08 | Contrats | Pilote | A-JUR-10 | P2 |
| Autres R | Par lot | Lot concerné | A-JUR-11 | P2 |

**Aucun point R1 à R55 n'est validé.** Seule A-JUR-01 est P0-PARALLÈLE (elle renseigne un critère de PT-13 et conditionne ARB-01) ;
les autres actions juridiques sont P2, leurs échéances se situant au Lot 1 ou après. **Ce classement ne diminue pas leur caractère
obligatoire** (exigences réglementaires [JURIDIQUE]) ; un démarrage immédiat reste recommandé, les délais externes étant inconnus.

## 4. Prototypes à exécuter (après autorisation écrite uniquement)

Tout prototype exige en outre C-01, C-04 (protocole approuvé) et C-05 (données fictives).

| Groupe (RÉF-3) | ID | Objet | Prérequis propres | Action |
|---|---|---|---|---|
| G1 | PT-08 | Horloge locale | Aucun | A-TEC-10 |
| G1 | PT-12 | Signature des paquets | Aucun | A-TEC-12 |
| G2 | PT-01, PT-02 | Unité de travail atomique, par moteur | C-03 ; A-TEC-03 | A-TEC-04 |
| G2 | PT-03 | Contraintes et déclencheurs | C-03 ; A-TEC-03 | A-TEC-07 |
| G2 | PT-04 | Métriques et génération comptable | C-03 ; A-TEC-03 | A-TEC-08 |
| G2 | PT-05 | Routage par pharmacie (D3) | C-03 ; A-TEC-03 [DI-03] | A-TEC-05 |
| G2 | PT-06 | Migrations sur N bases | C-03 ; A-TEC-03 [DI-03] | A-TEC-06 |
| G2 | PT-09 | Sauvegarde à chaud, restauration d'une base | C-03 ; A-TEC-03 | A-TEC-14 |
| G2 | PT-10 | Recherche FR / AR | C-03 ; A-TEC-03 | A-TEC-15 |
| G2 | PT-11 | Conformité des adaptateurs, CI double | C-03 [DI-02] | A-TEC-11 |
| G3 | PT-07 | Installateur local | C-03 (moteur du mode local) ; **ARB-08** — non requis pour la clôture documentaire de PT-13 ; rapport exigé avant le Lot 0 | A-TEC-09 |
| PT-13 | PT-13 | Comparatif des moteurs | Liste unique RÉF-4 ; PT-07 et avis R10 **conditionnels** (critères « non mesuré » / « non renseigné » motivés) ; clôture documentaire possible ; ARB-01 exige l'avis R10 | A-TEC-13 |
| Compléments | PT-14 à PT-17 (proposés) | Ordonnanceur, restauration croisée, concurrence sur le stock, chaîne de clés | Acceptation, complément par complément (A-PRO-17) ; **refus non bloquant** (plan v1.3.2 §8.4) | A-TEC-16 |

**Aucun prototype n'a été exécuté. Aucun test n'a été exécuté. Aucune mesure n'existe.**

## 5. Documents manquants

| Pièce | Statut |
|---|---|
| `00_JOURNAL_DU_PROJET.md` | NON FOURNI / NON ACCESSIBLE |
| `02_…`, `03_…` cités par l'archive EXT03 v1.1 | NON FOURNI sous ces noms [À CONFIRMER] |
| Sources juridiques officielles (PD-06) | NON FOURNI |
| Maquettes par format (PD-07) | NON FOURNI |
| Contrats (PD-08) | NON FOURNI |
| Schéma consolidé par lot (PD-05) | NON FOURNI (à produire au niveau documentaire) |
| Configuration matérielle de référence (ARB-08) | NON FOURNI |
| Rapports de prototypes et preuves de tests | NON FOURNI (aucune exécution) |
| Protocoles approuvés des prototypes (C-04) | NON FOURNI |
| Décisions écrites C-01, C-02, C-03, ARB-08 | NON FOURNI |

## 6. Anomalies documentaires

| ID | Résumé | Gravité |
|---|---|---|
| ED-01 | Prérequis de P0 contradictoires | Majeure |
| ED-02 | 18 prérequis absents du plan des lots (14 annoncés en v1.3) | Majeure |
| ED-03 | Plages DEP et BKP périmées | Mineure |
| ED-04 | Protocoles P0 incomplets pour PostgreSQL ; repli biaisé de PT-01 | Majeure |
| ED-05 | Unité de travail non décrite pour PostgreSQL ; « Lot 0 » au lieu de « P0 » | Mineure |
| ED-06 | Ordonnanceur, restauration croisée, chaîne de clés sans prototype | Majeure |
| ED-07 | Pas de scénario de concurrence sur le stock | Majeure |
| ED-08 | « 9 » noms définis deux fois au lieu de 10 | Mineure |
| ED-09 | Présentation ambiguë des cellules M15 et M18 | Mineure |
| ED-10 | Identifiant « ANN-10 [v1.1] » | Mineure |
| ED-11 | Résolutions conditionnelles non signalées | Mineure |
| ED-12 | R10 absent des dépendances de D2 et ARB-01 | Majeure |
| ED-13 | Deux échéances pour INC-01 | Mineure |
| ED-14 | Notes v1.2.1 mêlées au texte de décisions arbitrées | Mineure |
| ED-15 | Tests EXT02 non numérotés | Mineure |
| ED-16 | PT sans statut, environnement ni preuve | Majeure |
| ED-17 | Registre sans responsable ni critère de clôture | Majeure |
| ED-18 | Pièces citées non fournies ; version du dossier v1.1 absente | Mineure |
| EV-01 à EV-18 | Écarts propres aux livrables v1.3 (compléments facultatifs traités comme obligatoires, prérequis de PT-13, ARB-08, priorités, conditions, jalons, chemin critique, cycle de dépendances) | Corrigés par la v1.3.1 (audit v1.3.1 §16) |
| DI-01 à DI-09 | Dépendances incertaines signalées, non supprimées (DI-08, DI-09 ajoutées en v1.3.2) | [À CONFIRMER] (audit v1.3.2 §17.3) |
| EW-01 à EW-12 | Écarts propres aux livrables v1.3.1 (dépendances de PT-13, J-C, priorités P1 sans échéance démontrée, confusion priorité / obligation, conditions sans niveau) | Corrigés par la v1.3.2 (audit v1.3.2 §21) |
| AR-01 à AR-12 | Anomalies résiduelles après correction, dont 4 majeures non documentairement résolubles | Ouvertes (audit v1.3.2 §22.2) |

## 7. Conditions nécessaires au GO P0

Détail complet (niveau d'application, caractère bloquant, actions, responsable, preuve, conséquence) : **RÉF-2**.

| ID | Condition | Niveau(x) | Action(s) | État |
|---|---|---|---|---|
| C-01 | Autorisation écrite et périmètre de P0 | N1 ; N2 | A-PRO-01 | Non remplie |
| C-02 | Scénario de séquencement choisi (lève ED-01) | N1 | A-PRO-02 ; A-DOC-01 | Non remplie |
| C-03 | Moteurs candidats fixés ; protocoles étendus à chacun | N2 (G2, G3, PT-13) ; N1 sous S1 | A-PRO-05 ; A-TEC-01 | Non remplie |
| C-04 | Protocole approuvé, relecteur désigné | N2 (chaque prototype) | A-TEC-02 ; A-PRO-20 | Non remplie |
| C-05 | Données exclusivement fictives | N1 (règle) ; N2 (contrôle) | A-PRO-01 ; A-TEC-03 | Non remplie |
| C-06 | PT-07 suspendu jusqu'à ARB-08 (mesure du critère local de PT-13 ; rapport avant le Lot 0) | N2 (PT-07) ; N3 si PT-07 autorisé ; N4 (Lot 0) | A-PRO-06 ; A-TEC-09 | Non remplie |
| C-07 | Preuves archivées pour chaque prototype | N2 (acceptation) ; N3 ; N4 | A-TEC-18 | Non remplie |
| C-08 | Aucun résultat P0 ne vaut décision ; ARB-01 et architecture par décision écrite | Règle continue ; bloquante en N4 | A-PRO-15 ; A-PRO-21 | Non remplie |

**Aucune condition n'est remplie.** N1 ouverture de P0 · N2 exécution d'un prototype · N3 clôture de P0 · N4 validation de
l'architecture ou Lot 0. Les actions préparatoires documentaires ne requièrent aucune condition ; aucune action ne devient autorisée
du seul fait qu'elle figure dans un plan.

## 8. Ce qui reste interdit à ce stade

Voir **RÉF-8** (référentiel commun).

## 9. Actions par priorité opérationnelle (registre v1.3.2)

| Priorité | Actions |
|---|---|
| P0-BLOQUANT (4) | A-PRO-01, A-PRO-02, A-PRO-20, A-TEC-02 |
| P0-PARALLÈLE (14) | A-DOC-01, A-DOC-04, A-PRO-04, A-PRO-05, A-PRO-06, A-PRO-07, A-PRO-08, A-PRO-17, A-PRO-22, A-TEC-01, A-TEC-03, A-TEC-18, A-TEC-19, A-JUR-01 |
| P1 (25) | A-DOC-02, A-DOC-06, A-DOC-07, A-DOC-10, A-DOC-11, A-PRO-14, A-PRO-15, A-PRO-21, A-PRO-23, A-TEC-04, A-TEC-05, A-TEC-06, A-TEC-07, A-TEC-08, A-TEC-09, A-TEC-10, A-TEC-11, A-TEC-12, A-TEC-13, A-TEC-14, A-TEC-15, A-TEC-16, A-TEC-17, A-SEC-01, A-SEC-05 |
| P2 (28) | Voir le registre v1.3.2 |
| **Total** | **71** = 4 + 14 + 25 + 28 |

Totaux v1.3.1 → v1.3.2 : P0-BLOQUANT 4 → 4 · P0-PARALLÈLE 15 → 14 ·
P1 33 → 25 · P2 19 → 28. Actions reclassées : A-PRO-13, A-JUR-02, A-JUR-03, A-JUR-05, A-JUR-06, A-JUR-07, A-JUR-09, A-SEC-02, A-SEC-04 (registre v1.3.2 §7, §9). Leur caractère
obligatoire est inchangé.

Sous S1, A-PRO-04, A-PRO-05, A-PRO-06, A-PRO-07, A-PRO-08 et A-TEC-01 deviennent bloquantes pour l'ouverture de P0 (RÉF-10).

## 10. Verdict documentaire v1.3.2

**VALIDÉE AVEC RÉSERVES** (audit v1.3.2 §23 ; anomalies résiduelles AR-01 à AR-12). Ce verdict porte sur la cohérence documentaire
et **ne vaut aucune autorisation** : P0, prototypes, développement, validation de l'architecture, Lot 0, pilote et commercialisation
restent non autorisés.

---

*Fin du tableau de synthèse v1.3.2 — l'orientation GO P0 CONDITIONNEL ne vaut pas autorisation (RÉF-1).*