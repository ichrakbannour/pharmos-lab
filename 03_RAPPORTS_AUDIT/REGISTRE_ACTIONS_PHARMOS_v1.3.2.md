# PHARM'OS — Registre d'actions v1.3.2

**Version** : **1.3.2** (corrections ciblées) — 2026-11-04 · remplace pour usage la v1.3.1 (`REGISTRE_ACTIONS_PHARMOS_v1.3.1.md`), conservée sans modification
**Historique** : v1.3 — 65 actions · v1.3.1 — échelle P0-BLOQUANT / P0-PARALLÈLE / P1 / P2, 71 actions actives · **v1.3.2 — dépendances A-TEC-09 et A-JUR-01 de A-TEC-13 rendues conditionnelles (A-JUR-01 reste obligatoire pour ARB-01), colonne « Caractère obligatoire » séparée de la priorité, 9 priorités réexaminées et reclassées, justification de chaque P1 (§8), réexamen détaillé (§9).** Détail : `JOURNAL_CORRECTIONS_PHARMOS_v1.3.2.md`.
**Objet** : actions nécessaires pour lever les écarts, contradictions, points ouverts et prérequis identifiés par `RAPPORT_AUDIT_FINAL_COHERENCE_PHARMOS_v1.3.2.md` et ordonnancés par `PLAN_DIRECTEUR_FINALISATION_PHARMOS_v1.3.2.md`.
**Statut** : registre **proposé** ; aucune action n'est engagée, aucune n'est réalisée. Les responsables sont des **rôles attendus** ; leur attribution nominative appartient au propriétaire (A-PRO-20, A-PRO-24).

**Légende**
- **Priorité opérationnelle** : échéance réelle (RÉF-10 A : P0-BLOQUANT, P0-PARALLÈLE, P1, P2).
- **Caractère obligatoire** : nature de l'exigence (RÉF-10 B), indépendante de la priorité ; une exigence classée P2 reste intégralement obligatoire.
- **Effet sur P0** : effet réel sur l'ouverture, la poursuite ou la clôture de P0, selon le scénario retenu (RÉF-5).
- **Dépendances** : obligatoires sauf mention « conditionnelle », « recommandée », [RECO] ou « accepté(s) uniquement ».
- **Statut** : **Ouverte** (peut démarrer) · **Bloquée** (attend la dépendance indiquée) · **Conditionnelle** (n'existe que si le propriétaire l'accepte). Aucune action n'est au statut « Réalisée ».
- **Décision requise** : **Oui** si l'action suppose un arbitrage ou une validation du propriétaire.

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

## Synthèse

| Catégorie | Actions actives | P0-BLOQUANT | P0-PARALLÈLE | P1 | P2 |
|---|---|---|---|---|---|
| Documentaire (A-DOC) | 13 | 0 | 2 | 5 | 6 |
| Propriétaire (A-PRO) | 23 | 3 | 7 | 4 | 9 |
| Technique (A-TEC) | 19 | 1 | 4 | 14 | 0 |
| Juridique (A-JUR) | 11 | 0 | 1 | 0 | 10 |
| Sécurité (A-SEC) | 5 | 0 | 0 | 2 | 3 |
| **Total** | **71** | **4** | **14** | **25** | **28** |

Actions actives : **71** (inchangé depuis la v1.3.1 ; aucune action créée ni retirée en v1.3.2). Contrôle : 4 + 14 + 25 + 28 = 71.

Totaux v1.3.1 → v1.3.2 : P0-BLOQUANT 4 → 4 · P0-PARALLÈLE 15 → 14 · P1 33 → 25 · P2 19 → 28 (9 actions reclassées, §7).

**Actions P0-BLOQUANT (quel que soit le scénario)** : A-PRO-01, A-PRO-02, A-PRO-20, A-TEC-02.

**Actions P0-PARALLÈLE** : A-DOC-01, A-DOC-04, A-PRO-04, A-PRO-05, A-PRO-06, A-PRO-07, A-PRO-08, A-PRO-17, A-PRO-22, A-TEC-01, A-TEC-03, A-TEC-18, A-TEC-19, A-JUR-01.

---

## 1. Actions documentaires

| ID | Description | Priorité opérationnelle | Caractère obligatoire | Effet sur P0 | Catégorie | Responsable attendu | Dépendances | Livrable attendu | Critère de clôture | Statut | Décision requise |
|---|---|---|---|---|---|---|---|---|---|---|---|
| A-DOC-01 | Aligner les prérequis de P0 dans le dossier §16.2, l'audit §7 et la colonne « Bloque » du registre sur le scénario de séquencement retenu (ED-01) | P0-PARALLÈLE | Recommandation ou mesure de maîtrise (cohérence documentaire) | Aucun sur le démarrage : la décision C-02 (A-PRO-02) suffit ; l'alignement des sources v1.2.1 suit | Documentaire | Rédacteur documentaire | A-PRO-02 | Nouvelles versions des trois documents (sans écraser la v1.2.1) | Prérequis de P0 identiques dans les trois documents | Bloquée (A-PRO-02) | Non |
| A-DOC-02 | Reporter dans la colonne « Prérequis » de chaque lot du dossier §16.2 les ARB et R de la colonne « Bloque » du registre (ED-02 : **18 écarts** après recomptage v1.3.1, dont R14, R15, R19 pour le Lot 5b et R17 pour le Lot 10) | P1 | Recommandation ou mesure de maîtrise (cohérence documentaire) | Aucun | Documentaire | Rédacteur documentaire | — | Nouvelle version du dossier | 0 écart entre registre et dossier (contrôle automatique) | Ouverte | Non |
| A-DOC-03 | Corriger les plages périmées « DEP-01 à DEP-12 » (matrice des décisions, D2) et « BKP-01 à BKP-06 » (matrice de déploiement, RD3) (ED-03) | P2 | Recommandation ou mesure de maîtrise (cohérence documentaire) | Aucun | Documentaire | Rédacteur documentaire | — | Nouvelles versions | Plages conformes aux séries définies | Ouverte | Non |
| A-DOC-04 | Compléter le registre des points ouverts : colonnes « Responsable », « Critère de clôture » ; pour les PT : « Statut », « Environnement », « Preuve attendue » (ED-16, ED-17) | P0-PARALLÈLE | Recommandation ou mesure de maîtrise (cohérence documentaire) | Aucun : l'environnement, la preuve attendue et le statut de chaque prototype figurent dans son protocole approuvé (A-TEC-02) | Documentaire | Rédacteur documentaire ; responsable qualité | — | Nouvelle version du registre | Chaque R, PD, ARB, PT a un responsable et un critère de clôture | Ouverte | Non |
| A-DOC-05 | Corrections mineures : ED-05 (T3, « P0 » au lieu de « Lot 0 »), ED-08 (10 noms, dont 1 remplacement), ED-09 (présentation §7.4), ED-10 (ANN-10), ED-11 (résolutions conditionnelles), ED-13 (échéance INC-01), ED-14 (notes D72, D78, D83) | P2 | Recommandation ou mesure de maîtrise (cohérence documentaire) | Aucun | Documentaire | Rédacteur documentaire | — | Nouvelles versions | Chaque écart traité ou explicitement reporté | Ouverte | Non |
| A-DOC-06 | Ajouter R10 aux dépendances de D2 (matrice) et d'ARB-01 (registre) (ED-12) | P1 | Recommandation ou mesure de maîtrise (traçabilité d'une exigence réglementaire, R10) | Aucun : le positionnement de R10 avant ARB-01 est garanti par la dépendance A-PRO-15 → A-JUR-01 | Documentaire | Rédacteur documentaire | — | Nouvelles versions | R10 cité dans les deux lignes | Ouverte | Non |
| A-DOC-07 | Créer un registre de suivi des tests à cinq niveaux (spécifié, préparé, exécuté, réussi, preuve archivée), initialisé à « spécifié » pour toutes les séries | P1 | Recommandation ou mesure de maîtrise (preuve, qualité) | Aucun sur le démarrage ; recommandée avant la première exécution pour enregistrer les niveaux de test | Documentaire | Responsable qualité | — | Registre des tests | Toutes les séries listées ; aucun niveau supérieur sans preuve | Ouverte | Non |
| A-DOC-08 | Inventaire documentaire : fournir ou déclarer obsolète `00_JOURNAL_DU_PROJET.md` (PD-02) ; documenter la correspondance des noms `02_…`, `03_…` de l'archive EXT03 ; ajouter la version du dossier v1.1 par une note de correspondance (ED-18) | P2 | Recommandation ou mesure de maîtrise (cohérence documentaire) | Aucun | Documentaire | Rédacteur documentaire ; propriétaire | — | Note de correspondance | Chaque pièce citée est fournie ou déclarée absente | Ouverte | Oui (statut du journal) |
| A-DOC-09 | Numéroter les tests d'EXT02 (préfixes vérifiés libres) lors de sa validation (ED-15) | P2 | Recommandation ou mesure de maîtrise (cohérence documentaire) | Aucun | Documentaire | Responsable qualité | A-PRO-03 | Nouvelle version d'EXT02 ou annexe de tests | Tests EXT02 traçables par identifiant | Bloquée (A-PRO-03) | Non |
| A-DOC-10 | Produire le schéma consolidé par lot, au niveau **documentaire** uniquement (PD-05), à partir des décisions validées | P1 | Recommandation ou mesure de maîtrise (cohérence documentaire) | Aucun | Documentaire | Architecte ; rédacteur | A-DOC-11 ; A-PRO-13 [RECO, DI-07] ; A-PRO-03 et A-PRO-12 (pour les Lots 1 à 5 uniquement) | Document de schéma par lot (aucun fichier SQL) | Schéma du Lot 0 validé avant le Lot 0 ; schémas des Lots 1 à 5 validés avant le Lot 1 | Bloquée | Oui |
| A-DOC-11 | Vérifier et documenter l'inventaire des tables par une méthode d'extraction reproductible, en lecture seule, dont les résultats sont archivés : 243 noms distincts, 244 tables (`coverage_rules` désigne deux tables), 248 avec les 4 tables nouvelles, 10 noms définis dans deux documents dont `prescriptions` (remplacement documenté) — **issue de la scission de A-PRO-19** | P1 | Recommandation ou mesure de maîtrise (vérification reproductible) | Aucun | Documentaire | Rédacteur documentaire ; architecte | — | Note d'inventaire décrivant la méthode et les résultats | Chiffres reproduits à l'identique par une seconde personne | Ouverte | Non (vérification documentaire ; la validation du schéma relève de A-DOC-10) |
| A-DOC-12 | Consigner que la classification d'INC-13 en OUVERTE (ARB-09) applique l'instruction du propriétaire de la mission v1.2.1 (« points qui restent ouverts : INC-13 ») ; **aucune décision nouvelle requise** — issue de la scission de A-PRO-19 | P2 | Recommandation ou mesure de maîtrise (cohérence documentaire) | Aucun | Documentaire | Rédacteur documentaire | — | Mention dans la prochaine version de l'audit de cohérence | Mention présente ; décomptes inchangés (19 PROPOSÉE, 6 OUVERTE) | Ouverte | Non |
| A-DOC-13 | Compléter la référence documentaire de l'arbitrage D3 : date de la décision si le propriétaire la communique, à défaut maintenir « date non précisée dans les sources ; enregistrement le 2026-11-04 ». **D3 n'est pas rouverte** ; il ne s'agit pas d'une décision d'architecture — issue de la scission de A-PRO-19 | P2 | Recommandation ou mesure de maîtrise (cohérence documentaire) | Aucun | Documentaire | Rédacteur documentaire ; propriétaire (information) | — | Mention mise à jour dans une nouvelle version de la matrice des décisions | Référence complétée ou maintenue explicitement | Ouverte | Non (information ; D3 est [DÉCIDÉ]) |

---

## 2. Actions du propriétaire

Toutes ces actions relèvent exclusivement du propriétaire ; aucune n'est présentée comme acquise.

| ID | Description | Priorité opérationnelle | Caractère obligatoire | Effet sur P0 | Catégorie | Responsable attendu | Dépendances | Livrable attendu | Critère de clôture | Statut | Décision requise |
|---|---|---|---|---|---|---|---|---|---|---|---|
| A-PRO-01 | Autoriser ou refuser P0 et en fixer le périmètre : prototypes jetables, isolés du dépôt applicatif, liste des prototypes autorisés, données exclusivement fictives (C-05), aucune réutilisation de code sans revue et décision ; sous S2 ou S3, dérogation expresse au dossier v1.2.1 §16.2 — condition C-01 | P0-BLOQUANT | Gouvernance (décision du propriétaire) ; règle de maîtrise associée (données fictives, C-05) | Bloque l'exécution de tout prototype (C-01) | Propriétaire | Propriétaire | A-PRO-02 (peut figurer dans la même décision) | Décision écrite datée | Décision enregistrée | Ouverte | Oui |
| A-PRO-02 | Choisir le scénario de séquencement de P0 (S1 prudent, S2 rapide, S3 intermédiaire — RÉF-5) — condition C-02 | P0-BLOQUANT | Gouvernance (décision du propriétaire) | Bloque le démarrage (C-02) ; détermine les autres conditions de démarrage (RÉF-5) | Propriétaire | Propriétaire | — | Décision écrite | Scénario enregistré ; A-DOC-01 débloquée | Ouverte | Oui |
| A-PRO-03 | ARB-14 : ordre de validation des extensions ; puis validation séquentielle d'EXT03 v1.1, EXT04, EXT05 | P2 | Gouvernance (décision du propriétaire) | Aucun | Propriétaire | Propriétaire ; pharmaciens référents | — | Décisions écrites par extension | Chaque extension validée, amendée ou rejetée | Ouverte | Oui |
| A-PRO-04 | ARB-09 : accès de l'éditeur aux données métier (INC-13) | P0-PARALLÈLE | Gouvernance (décision du propriétaire) ; exigence réglementaire associée (R52) | S1 : bloque le démarrage de P0 · S3 : bloque G2, G3 et PT-13 · S2 : aucun effet sur P0 ; requise avant le Lot 1 (registre v1.2.1) | Propriétaire | Propriétaire | A-JUR-03 recommandée | Décision écrite | ARB-09 arbitré et enregistré | Ouverte | Oui |
| A-PRO-05 | Fixer la liste des moteurs candidats d'ARB-01 soumis à PT-13 — condition C-03 | P0-PARALLÈLE | Gouvernance (décision du propriétaire) | S1 : bloque le démarrage · S2 et S3 : bloque G2, G3 et PT-13 ; ne bloque pas G1 (PT-08, PT-12) | Propriétaire | Propriétaire ; architecte (conseil) | — | Décision écrite | Liste enregistrée ; A-TEC-01 débloquée | Ouverte | Oui |
| A-PRO-06 | ARB-08 : configuration matérielle et système de référence du mode B | P0-PARALLÈLE | Gouvernance (décision du propriétaire) | Bloque PT-07 et la mesure du critère « fonctionnement local » de PT-13 (sans bloquer la clôture documentaire de PT-13 ni ARB-01) ; S1 : bloque le démarrage (registre v1.2.1 : ARB-08 « bloque P0 ») ; rapport PT-07 requis avant le Lot 0 | Propriétaire | Propriétaire ; pharmaciens référents | — | Décision écrite | Configuration enregistrée ; PT-07 débloqué | Ouverte | Oui |
| A-PRO-07 | ARB-05 : Super Admin en installation locale (INC-14) | P0-PARALLÈLE | Gouvernance (décision du propriétaire) ; exigence de sécurité associée (S-03) | S1 : bloque le démarrage de P0 · S3 : bloque G2, G3 et PT-13 · S2 : aucun effet sur P0 ; requise avant le Lot 1 (registre v1.2.1) | Propriétaire | Propriétaire | A-JUR-06 recommandée | Décision écrite | ARB-05 arbitré | Ouverte | Oui |
| A-PRO-08 | ARB-06 : effet de la fin d'un abonnement, d'une licence ou d'un module (INC-38) | P0-PARALLÈLE | Gouvernance (décision du propriétaire) ; exigence contractuelle associée (PD-08) | S1 : bloque le démarrage de P0 · S3 : bloque G2, G3 et PT-13 · S2 : aucun effet sur P0 ; requise avant le Lot 1 (registre v1.2.1) | Propriétaire | Propriétaire | — (un avis juridique préalable sur les effets de la fin de licence reste recommandé) | Décision écrite | ARB-06 arbitré | Ouverte | Oui |
| A-PRO-09 | ARB-04 : procédure de récupération du compte Super Admin | P2 | Gouvernance (décision du propriétaire) | Aucun | Propriétaire | Propriétaire ; responsable sécurité | — | Décision écrite | ARB-04 arbitré | Ouverte | Oui |
| A-PRO-10 | ARB-07 : validation à distance en mode B hors de l'officine (INC-16) | P2 | Gouvernance (décision du propriétaire) | Aucun | Propriétaire | Propriétaire | — | Décision écrite | ARB-07 arbitré | Ouverte | Oui |
| A-PRO-11 | ARB-11 (titulaire / administrateur) et ARB-12 (code d'accès initial) | P2 | Gouvernance (décision du propriétaire) ; exigence réglementaire associée (R48) | Aucun | Propriétaire | Propriétaire | A-JUR-05 pour ARB-11 | Décisions écrites | ARB-11 et ARB-12 arbitrés | Ouverte | Oui |
| A-PRO-12 | ARB-03 : périmètre du tiers payant en v1 (INC-06) ; mise en cohérence de D6, D37, D44 | P2 | Gouvernance (décision du propriétaire) ; exigences réglementaires associées (R12, R23 à R33) | Aucun | Propriétaire | Propriétaire | — | Décision écrite | ARB-03 arbitré ; D6, D37, D44 mises à jour | Ouverte | Oui |
| A-PRO-13 | Valider le renommage `shift_coverage_rules` / `payer_coverage_rules` (INC-01) | P2 | Gouvernance (décision du propriétaire) | Aucun ; rattachement au Lot 0 maintenu à titre de [RECO] non bloquant (DI-07) | Propriétaire | Propriétaire ; architecte | — | Décision écrite | INC-01 clos par décision | Ouverte | Oui |
| A-PRO-14 | Valider ou refuser la proposition de paquets de référentiels signés (INC-27, registre §3.2) | P1 | Gouvernance (décision du propriétaire) | Aucun (PT-12 l'éclaire sans en dépendre) | Propriétaire | Propriétaire | A-TEC-12 recommandée | Décision écrite | INC-27 clos par décision | Ouverte | Oui |
| A-PRO-15 | ARB-01 : choisir le moteur d'exécution et de base | P1 | Gouvernance (décision du propriétaire) | Aucun sur l'ouverture de P0 ; ne peut être arbitrée qu'après le rapport PT-13 (C-08) **et** l'avis R10 (A-JUR-01) | Propriétaire | Propriétaire | A-TEC-13 ; A-JUR-01 (obligatoire, y compris lorsque PT-13 est clos avec le critère « contraintes juridiques » non renseigné) ; A-PRO-05 | Décision écrite | ARB-01 arbitré ; D2 mise à jour | Bloquée (rapport PT-13, avis R10) | Oui |
| A-PRO-16 | ARB-10 (second collaborateur) et ARB-13 (nom du client) | P2 | Gouvernance (décision du propriétaire) | Aucun | Propriétaire | Propriétaire | — | Décisions écrites | Arbitrés avant les Lots 5d et 9c | Ouverte | Oui |
| A-PRO-17 | Accepter ou refuser, complément par complément, l'ajout des prototypes complémentaires PT-14 à PT-17 (plan directeur §8.4) ; en cas de refus, le traitement du risque suit le plan directeur §8.4 | P0-PARALLÈLE | Gouvernance (décision du propriétaire) | Bloque uniquement A-TEC-16 ; un refus, total ou partiel, ne bloque ni PT-13 ni la validation de l'architecture | Propriétaire | Propriétaire | — | Décision écrite | Décision enregistrée ; registre mis à jour si accepté | Ouverte | Oui |
| A-PRO-18 | Arbitrer les 70 décisions non arbitrées, par lot, avant le démarrage de chaque lot (dont confirmation de D61, D66 caduques) | P2 | Gouvernance (décision du propriétaire) | Aucun | Propriétaire | Propriétaire | Lot concerné | Décisions écrites | Aucune décision non arbitrée dans les prérequis du lot | Ouverte | Oui |
| A-PRO-20 | Désigner nominativement les rôles nécessaires à P0 : approbateur des protocoles, relecteur (second intervenant, CA-5), responsable qualité, architecte, responsable sécurité — **scission v1.3.1** ; les autres rôles relèvent de A-PRO-24 | P0-BLOQUANT | Gouvernance (décision du propriétaire) | Bloque l'approbation des protocoles (C-04), donc le démarrage | Propriétaire | Propriétaire | — | Liste écrite des rôles de P0 | Rôles de P0 désignés par écrit | Ouverte | Oui |
| A-PRO-21 | Valider formellement l'architecture (phase D) | P1 | Gouvernance (décision du propriétaire) | Aucun sur P0 ; prérequis du Lot 0 | Propriétaire | Propriétaire | A-PRO-15 ; A-PRO-14 ; A-TEC-17 ; A-SEC-01 (adoption) | Procès-verbal | Architecture validée par écrit | Bloquée | Oui |
| A-PRO-22 | Confirmer par écrit l'inscription de PT-13 (comparatif des moteurs, ajouté par la v1.2.1) au programme P0 — issue de la scission de A-PRO-19. Les missions v1.3 et v1.3.1 du propriétaire le désignent comme prototype principal ; la confirmation peut figurer dans la liste des prototypes autorisés par A-PRO-01 | P0-PARALLÈLE | Gouvernance (décision du propriétaire) | Bloque PT-13 uniquement | Propriétaire | Propriétaire | — | Décision écrite ou mention dans la décision A-PRO-01 | PT-13 inscrit au programme autorisé | Ouverte | Oui |
| A-PRO-23 | Trancher l'interprétation de l'exigence responsive : distinction entre une restriction d'usage décidée (dossier v1.2.1 §14.2) et une dégradation (§14.4) — issue de la scission de A-PRO-19 | P1 | Gouvernance (décision du propriétaire) | Aucun | Propriétaire | Propriétaire ; pharmaciens référents | — | Décision écrite | Interprétation enregistrée ; critère RSP-16 précisé | Ouverte | Oui |
| A-PRO-24 | Attribuer nominativement les autres rôles de gouvernance (plan directeur §13) — scission v1.3.1 de A-PRO-20 | P2 | Gouvernance (décision du propriétaire) | Aucun | Propriétaire | Propriétaire | A-PRO-20 | Liste des rôles | Rôles attribués | Ouverte | Oui |

---

## 3. Actions techniques

Aucun prototype n'a été exécuté. Toute exécution exige l'autorisation écrite de P0 (C-01, A-PRO-01) et l'approbation du protocole concerné (C-04, A-TEC-02). Les groupes G1, G2, G3 sont définis en RÉF-3. Une dépendance marquée « conditionnelle » n'est requise que dans le cas indiqué.

| ID | Description | Priorité opérationnelle | Caractère obligatoire | Effet sur P0 | Catégorie | Responsable attendu | Dépendances | Livrable attendu | Critère de clôture | Statut | Décision requise |
|---|---|---|---|---|---|---|---|---|---|---|---|
| A-TEC-01 | Rendre indépendants du moteur (un passage par candidat) les protocoles de PT-01 à PT-06, PT-09, PT-10, PT-11 et PT-13 (PT-05, PT-06, PT-11 : DI-02, DI-03) ; remplacer le repli de PT-01 ; compléter les critères de PT-13 ; **intégrer le scénario de concurrence sur le stock au protocole de PT-13** (ED-04, ED-07) | P0-PARALLÈLE | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Bloque G2 et PT-13 ; ne bloque pas G1 | Technique | Architecte | A-PRO-05 | Protocoles révisés (document) | Protocoles couvrant chaque candidat et tous les critères du plan §8.3 | Bloquée (A-PRO-05) | Non |
| A-TEC-02 | Rédiger les protocoles détaillés de P0 (objectif, environnement, données, scénario, critère chiffré, preuve) et les faire approuver — condition C-04 | P0-BLOQUANT | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Bloque l'exécution de chaque prototype (C-04) | Technique | Architecte ; responsable qualité | A-PRO-20 ; A-TEC-01 (protocoles G2 et PT-13) ; A-TEC-03 (protocoles utilisant les jeux de données) ; A-PRO-05 et A-PRO-06 (approbation du protocole PT-07) | Protocoles approuvés | Approbation écrite (CA-1) | Bloquée (A-PRO-20) ; puis ouverte pour G1, bloquée pour G2 et PT-13 jusqu'à A-TEC-01 et pour PT-07 jusqu'à A-PRO-06 | Oui (approbation) |
| A-TEC-03 | Spécifier les jeux de données fictifs (300 ventes / jour, 30 000 références FR / AR, N pharmacies) — condition C-05 | P0-PARALLÈLE | Recommandation ou mesure de maîtrise (preuve technique [TECH]) ; exigence de confidentialité (données exclusivement fictives) | Bloque G2 (PT-01 à PT-06, PT-09, PT-10) ; ne bloque pas G1 | Technique | Responsable qualité | — | Spécification des jeux de données | Spécification revue ; aucune donnée réelle | Ouverte | Non |
| A-TEC-04 | Exécuter PT-01 et PT-02 (unité de travail atomique) pour chaque candidat | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Contribue à la clôture de P0 et à PT-13 | Technique | Architecte | A-PRO-01, A-TEC-02 (protocole issu de A-TEC-01), A-TEC-03 | Rapport et preuves | Critère PT-01 / PT-02 (plan §9) | Bloquée | Non |
| A-TEC-05 | Exécuter PT-05 (routage vers la base de la pharmacie authentifiée, DEP-13) | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Contribue à la clôture de P0 et à PT-13 | Technique | Architecte ; responsable sécurité | A-PRO-01, A-TEC-02 (protocole issu de A-TEC-01), A-TEC-03 [DI-03] | Rapport et preuves | 0 accès croisé ; tentatives forcées refusées et journalisées | Bloquée | Non |
| A-TEC-06 | Exécuter PT-06 (migrations séparées sur N bases, UPD-07) | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Contribue à la clôture de P0 et à PT-13 | Technique | Architecte | A-PRO-01, A-TEC-02 (protocole issu de A-TEC-01), A-TEC-03 [DI-03] | Rapport et preuves | Critère PT-06 | Bloquée | Non |
| A-TEC-07 | Exécuter PT-03 (contraintes, déclencheurs) pour chaque candidat | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Contribue à la clôture de P0 et à PT-13 | Technique | Architecte | A-PRO-01, A-TEC-02 (protocole issu de A-TEC-01), A-TEC-03 | Rapport et preuves | Critère PT-03 | Bloquée | Non |
| A-TEC-08 | Exécuter PT-04 (métriques, génération comptable) pour chaque candidat | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Contribue à la clôture de P0 et à PT-13 | Technique | Architecte | A-PRO-01, A-TEC-02 (protocole issu de A-TEC-01), A-TEC-03 | Rapport et preuves | Critère PT-04 | Bloquée | Non |
| A-TEC-09 | Exécuter PT-07 (installateur local) | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Mesure du critère « fonctionnement local » de PT-13 (non requise pour la clôture documentaire de PT-13) ; rapport requis avant le Lot 0 (registre v1.2.1 §4) | Technique | Architecte | A-PRO-01, A-PRO-05, A-PRO-06, A-TEC-02 | Rapport et preuves | Critère PT-07 | Bloquée (ARB-08) | Non |
| A-TEC-10 | Exécuter PT-08 (horloge du serveur local) | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Peut figurer parmi les premiers prototypes exécutés (G1) | Technique | Architecte | A-PRO-01, A-TEC-02 | Rapport et preuves | Critère PT-08 | Bloquée | Non |
| A-TEC-11 | Exécuter PT-11 (suite de conformité des adaptateurs, CI double) | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Contribue à la clôture de P0 | Technique | Architecte ; responsable qualité | A-PRO-01, A-TEC-02 (protocole issu de A-TEC-01) [DI-02] | Rapport de CI | DEP-01 et DEP-04 automatisés et verts | Bloquée | Non |
| A-TEC-12 | Exécuter PT-12 (signature et vérification hors ligne des paquets) | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Peut figurer parmi les premiers prototypes exécutés (G1) | Technique | Architecte ; responsable sécurité | A-PRO-01, A-TEC-02 | Rapport et preuves | 100 % des paquets altérés refusés | Bloquée | Non |
| A-TEC-13 | Exécuter PT-13 (comparatif des moteurs) et rédiger le rapport comparatif chiffré | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Conditionne la clôture de P0 et ARB-01 ; ni ARB-08 (PT-07) ni l'absence d'avis R10 n'empêchent sa clôture documentaire ; une réserve sur le critère juridique n'autorise pas ARB-01 | Technique | Architecte | A-PRO-01 ; A-PRO-05 ; A-PRO-22 ; A-TEC-02 (protocole de PT-13 approuvé ; l'approbation du protocole de PT-07 n'est pas requise) ; A-TEC-04, A-TEC-05, A-TEC-06, A-TEC-07, A-TEC-08, A-TEC-14, A-TEC-15 ; A-TEC-09 (conditionnelle : uniquement si le critère « fonctionnement local » est mesuré ; à défaut, critère « non mesuré » motivé) ; A-JUR-01 (conditionnelle : uniquement pour renseigner le critère « contraintes juridiques » ; à défaut, critère « non renseigné » motivé) ; A-TEC-16 (pour PT-14 et PT-16 acceptés uniquement) | Rapport comparatif | Chaque critère du plan §8.3 mesuré, analysé, ou déclaré « non mesuré » / « non renseigné » avec son motif et son effet sur ARB-01 ; aucune mesure non exécutée présentée comme un résultat ; clôture documentaire possible avec ces réserves (RÉF-4) | Bloquée (exécutions G2, protocole PT-13) | Non |
| A-TEC-14 | Exécuter PT-09 (sauvegarde à chaud, restauration d'une base) pour chaque candidat | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Contribue à la clôture de P0 et à PT-13 | Technique | Architecte | A-PRO-01, A-TEC-02 (protocole issu de A-TEC-01), A-TEC-03 | Rapport et preuves | Critère PT-09 | Bloquée | Non |
| A-TEC-15 | Exécuter PT-10 (recherche plein texte FR et AR) pour chaque candidat | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Contribue à la clôture de P0 et à PT-13 | Technique | Architecte | A-PRO-01, A-TEC-02 (protocole issu de A-TEC-01), A-TEC-03 | Rapport et preuves | Critère PT-10 | Bloquée | Non |
| A-TEC-16 | Pour chaque prototype complémentaire **accepté** (PT-14 ordonnanceur, PT-15 restauration croisée A ↔ B, PT-16 concurrence sur le stock, PT-17 chaîne de clés) : protocole, exécution, rapport ; un complément refusé rend l'action sans objet pour lui, son risque étant traité selon le plan directeur §8.4 | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Aucun sur le démarrage ; aucun effet bloquant sur PT-13 ni sur la validation de l'architecture en cas de refus | Technique | Architecte ; responsable sécurité | A-PRO-17 (acceptation du complément concerné), A-PRO-01, A-TEC-02 | Rapports et preuves | Critères du plan §8.4 | Conditionnelle (A-PRO-17) | Non |
| A-TEC-17 | Préparer le dossier de validation de l'architecture (plan §10) à partir des rapports P0 | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Aucun sur P0 ; prérequis de la validation de l'architecture | Technique | Architecte | A-TEC-04 à A-TEC-08, A-TEC-10 à A-TEC-15 ; A-TEC-18 ; A-TEC-09 [RECO, DI-01] ; A-TEC-16 (compléments acceptés uniquement) ; A-SEC-01 ; A-SEC-05 | Dossier de validation | Chaque élément du plan §10 documenté avec sa preuve | Bloquée | Non |
| A-TEC-18 | Archiver les preuves de chaque prototype (rapport, mesures, journaux, versions, empreintes) — condition C-07 | P0-PARALLÈLE | Recommandation ou mesure de maîtrise (preuve archivée, C-07) | Conditionne l'acceptation de chaque prototype (C-07) et la clôture de P0 | Technique | Responsable qualité | Chaque exécution | Archive des preuves | Preuves complètes pour chaque PT exécuté (CA-4) | Bloquée | Non |
| A-TEC-19 | Préparer, sans installation ni test, les options de configuration matérielle et logicielle de référence du mode local (enquête documentaire auprès de pharmacies volontaires) pour instruire ARB-08 | P0-PARALLÈLE | Recommandation ou mesure de maîtrise (préparation, sans installation) | Aucun ; instruit A-PRO-06 (ARB-08) | Technique | Architecte ; pharmaciens référents | — | Note d'options | Options documentées et remises au propriétaire | Ouverte | Non |

---

## 4. Actions juridiques

Aucune réponse juridique n'a été obtenue. Les interlocuteurs sont ceux du registre des points ouverts v1.2.1. La priorité P2 d'une action juridique indique son échéance ; elle ne diminue ni son caractère obligatoire ni l'intérêt d'un démarrage anticipé.

| ID | Description | Priorité opérationnelle | Caractère obligatoire | Effet sur P0 | Catégorie | Responsable attendu | Dépendances | Livrable attendu | Critère de clôture | Statut | Décision requise |
|---|---|---|---|---|---|---|---|---|---|---|---|
| A-JUR-01 | Obtenir un avis sur R10 et R1 : hébergement, localisation et régime (déclaration ou autorisation INPDP) des données de santé, en SaaS et en mode B | P0-PARALLÈLE | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier | Renseigne le critère « contraintes juridiques » de PT-13 (à défaut « non renseigné », sans bloquer sa clôture documentaire) ; **bloque ARB-01** (A-PRO-15) ; ne bloque pas l'ouverture de P0 | Juridique | Juriste ; propriétaire (saisine) | — | Avis écrit daté et sourcé | Réponse rattachée à R10 et R1 | Ouverte | Non |
| A-JUR-02 | Constituer le dossier de sources juridiques (textes, dates, versions) rattachées aux points R (PD-06) | P2 | Recommandation ou mesure de maîtrise (sources) au service des exigences réglementaires | Aucun ; appuie A-JUR-01 et les points R sans en être un prérequis formel | Juridique | Juriste | — | Dossier de sources | Chaque point R prioritaire dispose d'au moins une source officielle ou d'un constat d'absence | Ouverte | Non |
| A-JUR-03 | Obtenir un avis sur R52 et R53 : statut de l'éditeur, accès aux données, journal plateforme, suppression des données d'une pharmacie | P2 | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier | Aucun | Juridique | Juriste ; INPDP | — | Avis écrit | Réponses rattachées à R52 et R53 | Ouverte | Non |
| A-JUR-04 | Obtenir le statut officiel de la convention CNAM et les sources des taux et listes (R12, R23 à R33 ; INC-35) | P2 | Exigence réglementaire [JURIDIQUE] (conditionne l'activation du tiers payant) | Aucun | Juridique | Juriste ; CNAM, SPOT, CNOPT | — | Réponses écrites | Réponses rattachées ; aucune convention activée sans source | Ouverte | Non |
| A-JUR-05 | Obtenir un avis sur R48 et R37 (responsabilité du pharmacien administrateur, délégations) | P2 | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier | Aucun | Juridique | Juriste ; Ordre | — | Avis écrit | Réponses rattachées | Ouverte | Non |
| A-JUR-06 | Obtenir un avis sur R55 (responsabilités en installation locale) | P2 | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier | Aucun | Juridique | Juriste | — | Avis écrit | Réponse rattachée | Ouverte | Non |
| A-JUR-07 | Obtenir les réponses sur R42, R43, R44, R51 (catégories, classification, TVA, prix réglementés) | P2 | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier | Aucun | Juridique | Juriste ; DPM ; expert-comptable | — | Réponses écrites | Réponses rattachées avant le Lot 2 | Ouverte | Non |
| A-JUR-08 | Qualifier l'obligation de notification des violations de données et la procédure associée (S-07) | P2 | Obligation juridique à qualifier [JURIDIQUE] (notification de violation) | Aucun | Juridique | Juriste ; INPDP | — | Avis écrit | Procédure validée avant le pilote | Ouverte | Non |
| A-JUR-09 | Obtenir un avis sur R16 (OCR par un sous-traitant) et R22 (agrégats transmis à un LLM étranger) | P2 | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier | Aucun | Juridique | Juriste ; INPDP | — | Avis écrit | Réponses rattachées avant les Lots 5c et 13 | Ouverte | Non |
| A-JUR-10 | Faire rédiger les contrats : abonnement SaaS, licence locale, conditions d'intervention de l'éditeur, fin de licence (PD-08) | P2 | Exigence contractuelle | Aucun | Juridique | Juriste | A-PRO-07, A-PRO-08, A-PRO-04 | Projets de contrats | Contrats validés avant le pilote | Bloquée | Oui (validation) |
| A-JUR-11 | Traiter les autres points R par lot (R2 à R9, R11, R14 à R21, R34 à R41, R45 à R47, R49, R50, R54) | P2 | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier | Aucun | Juridique | Juriste | Lot concerné | Réponses écrites | Points R du lot fermés avec source avant le lot | Ouverte | Non |

---

## 5. Actions de sécurité

Même règle : la priorité indique l'échéance, non le caractère obligatoire.

| ID | Description | Priorité opérationnelle | Caractère obligatoire | Effet sur P0 | Catégorie | Responsable attendu | Dépendances | Livrable attendu | Critère de clôture | Statut | Décision requise |
|---|---|---|---|---|---|---|---|---|---|---|---|
| A-SEC-01 | Rédiger la politique de gestion des clés : chiffrement applicatif (PAT, RH), signature APEX (mises à jour, licences, référentiels) ; génération, garde hors ligne, double contrôle, rotation, sauvegarde, révocation, perte (S-01) | P1 | Exigence de sécurité | Aucun sur P0 ; prérequis de la validation de l'architecture | Sécurité | Responsable sécurité | — | Politique de gestion des clés | Politique revue ; éprouvée par PT-17 si accepté | Ouverte | Oui (adoption) |
| A-SEC-02 | Définir la protection réellement atteignable de l'immuabilité du Super Admin en mode B (détection par intégrité signée et journal, et non seulement interdiction) (S-03, INC-14) | P2 | Exigence de sécurité | Aucun | Sécurité | Responsable sécurité ; architecte | A-PRO-07 recommandée | Note technique | Note revue et rattachée à ARB-05 | Ouverte | Non |
| A-SEC-03 | Évaluer le risque lié à l'identifiant public du Super Admin et proposer des mesures (S-04) | P2 | Exigence de sécurité | Aucun | Sécurité | Responsable sécurité | — | Note d'analyse | Mesures proposées au propriétaire | Ouverte | Oui (mesures) |
| A-SEC-04 | Établir la liste blanche des champs et agrégats transmissibles à un LLM, avec le seuil de petits effectifs (S-05) | P2 | Exigence de sécurité ; exigence réglementaire associée (R22) | Aucun | Sécurité | Responsable sécurité | A-JUR-09 | Liste blanche | Liste validée avant le Lot 13 | Ouverte | Oui (validation) |
| A-SEC-05 | Réaliser une analyse de menaces formelle (SaaS multi-bases sur serveur partagé, poste local, appareils mobiles, chaîne de paquets signés) (S-06) | P1 | Exigence de sécurité | Aucun sur P0 ; prérequis de la validation de l'architecture | Sécurité | Responsable sécurité | — | Analyse de menaces | Analyse revue avant la validation de l'architecture | Ouverte | Non |

---

## 6. Actions retirées

| ID | Motif | Remplacée par | Statut |
|---|---|---|---|
| A-PRO-19 | Décomposition demandée (mission v1.3.1, correction 5) (EV-10) | A-DOC-11, A-DOC-12, A-DOC-13, A-PRO-22, A-PRO-23 | Retirée en v1.3.1 ; identifiant non réutilisé |

---

## 7. Correspondance des priorités v1.3.1 → v1.3.2

Toutes les actions actives sont listées. La correspondance v1.3 → v1.3.1 reste consultable dans le registre v1.3.1 §7. Les écarts EW cités sont décrits au rapport d'audit v1.3.2 §21.

| ID | Priorité v1.3.1 | Priorité v1.3.2 | Caractère obligatoire | Justification v1.3.2 |
|---|---|---|---|---|
| A-DOC-01 | P0-PARALLÈLE | P0-PARALLÈLE | Recommandation ou mesure de maîtrise (cohérence documentaire) | Inchangée |
| A-DOC-02 | P1 | P1 | Recommandation ou mesure de maîtrise (cohérence documentaire) | Inchangée |
| A-DOC-03 | P2 | P2 | Recommandation ou mesure de maîtrise (cohérence documentaire) | Inchangée |
| A-DOC-04 | P0-PARALLÈLE | P0-PARALLÈLE | Recommandation ou mesure de maîtrise (cohérence documentaire) | Inchangée |
| A-DOC-05 | P2 | P2 | Recommandation ou mesure de maîtrise (cohérence documentaire) | Inchangée |
| A-DOC-06 | P1 | P1 | Recommandation ou mesure de maîtrise (traçabilité d'une exigence réglementaire, R10) | Inchangée |
| A-DOC-07 | P1 | P1 | Recommandation ou mesure de maîtrise (preuve, qualité) | Inchangée |
| A-DOC-08 | P2 | P2 | Recommandation ou mesure de maîtrise (cohérence documentaire) | Inchangée |
| A-DOC-09 | P2 | P2 | Recommandation ou mesure de maîtrise (cohérence documentaire) | Inchangée |
| A-DOC-10 | P1 | P1 | Recommandation ou mesure de maîtrise (cohérence documentaire) | Inchangée |
| A-DOC-11 | P1 | P1 | Recommandation ou mesure de maîtrise (vérification reproductible) | Inchangée |
| A-DOC-12 | P2 | P2 | Recommandation ou mesure de maîtrise (cohérence documentaire) | Inchangée |
| A-DOC-13 | P2 | P2 | Recommandation ou mesure de maîtrise (cohérence documentaire) | Inchangée |
| A-PRO-01 | P0-BLOQUANT | P0-BLOQUANT | Gouvernance (décision du propriétaire) ; règle de maîtrise associée (données fictives, C-05) | Inchangée |
| A-PRO-02 | P0-BLOQUANT | P0-BLOQUANT | Gouvernance (décision du propriétaire) | Inchangée |
| A-PRO-03 | P2 | P2 | Gouvernance (décision du propriétaire) | Inchangée |
| A-PRO-04 | P0-PARALLÈLE | P0-PARALLÈLE | Gouvernance (décision du propriétaire) ; exigence réglementaire associée (R52) | Inchangée |
| A-PRO-05 | P0-PARALLÈLE | P0-PARALLÈLE | Gouvernance (décision du propriétaire) | Inchangée |
| A-PRO-06 | P0-PARALLÈLE | P0-PARALLÈLE | Gouvernance (décision du propriétaire) | Effet de ARB-08 limité à PT-07, à la mesure du critère local et au Lot 0 — EW-11 |
| A-PRO-07 | P0-PARALLÈLE | P0-PARALLÈLE | Gouvernance (décision du propriétaire) ; exigence de sécurité associée (S-03) | Inchangée |
| A-PRO-08 | P0-PARALLÈLE | P0-PARALLÈLE | Gouvernance (décision du propriétaire) ; exigence contractuelle associée (PD-08) | Inchangée |
| A-PRO-09 | P2 | P2 | Gouvernance (décision du propriétaire) | Inchangée |
| A-PRO-10 | P2 | P2 | Gouvernance (décision du propriétaire) | Inchangée |
| A-PRO-11 | P2 | P2 | Gouvernance (décision du propriétaire) ; exigence réglementaire associée (R48) | Inchangée |
| A-PRO-12 | P2 | P2 | Gouvernance (décision du propriétaire) ; exigences réglementaires associées (R12, R23 à R33) | Inchangée |
| A-PRO-13 | P1 | P2 **(reclassée)** | Gouvernance (décision du propriétaire) | Réexamen v1.3.2 : échéance sourcée « avant le Lot 1 » (audit v1.2.1, ligne INC-01) et « avant toute création de table » ; aucune source ne rattache les tables `coverage_rules` au Lot 0 ; la P1 de la v1.3.1 reposait sur une [RECO] (DI-07) — EW-07 |
| A-PRO-14 | P1 | P1 | Gouvernance (décision du propriétaire) | Inchangée |
| A-PRO-15 | P1 | P1 | Gouvernance (décision du propriétaire) | Maintien explicite de la dépendance juridique d'ARB-01 malgré la clôture possible de PT-13 avec réserve — EW-02 |
| A-PRO-16 | P2 | P2 | Gouvernance (décision du propriétaire) | Inchangée |
| A-PRO-17 | P0-PARALLÈLE | P0-PARALLÈLE | Gouvernance (décision du propriétaire) | Inchangée |
| A-PRO-18 | P2 | P2 | Gouvernance (décision du propriétaire) | Inchangée |
| A-PRO-20 | P0-BLOQUANT | P0-BLOQUANT | Gouvernance (décision du propriétaire) | Inchangée |
| A-PRO-21 | P1 | P1 | Gouvernance (décision du propriétaire) | Inchangée |
| A-PRO-22 | P0-PARALLÈLE | P0-PARALLÈLE | Gouvernance (décision du propriétaire) | Inchangée |
| A-PRO-23 | P1 | P1 | Gouvernance (décision du propriétaire) | Inchangée |
| A-PRO-24 | P2 | P2 | Gouvernance (décision du propriétaire) | Inchangée |
| A-TEC-01 | P0-PARALLÈLE | P0-PARALLÈLE | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Inchangée |
| A-TEC-02 | P0-BLOQUANT | P0-BLOQUANT | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Inchangée |
| A-TEC-03 | P0-PARALLÈLE | P0-PARALLÈLE | Recommandation ou mesure de maîtrise (preuve technique [TECH]) ; exigence de confidentialité (données exclusivement fictives) | Inchangée |
| A-TEC-04 | P1 | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Inchangée |
| A-TEC-05 | P1 | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Inchangée |
| A-TEC-06 | P1 | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Inchangée |
| A-TEC-07 | P1 | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Inchangée |
| A-TEC-08 | P1 | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Inchangée |
| A-TEC-09 | P1 | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Effet précisé : PT-07 ne bloque plus indirectement PT-13, J-C ni ARB-01 — EW-04, EW-11 |
| A-TEC-10 | P1 | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Inchangée |
| A-TEC-11 | P1 | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Inchangée |
| A-TEC-12 | P1 | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Inchangée |
| A-TEC-13 | P1 | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Dépendances A-TEC-09 et A-JUR-01 rendues conditionnelles conformément aux règles de repli de RÉF-4 ; A-PRO-01 et A-PRO-05 ajoutées (cadrage RÉF-4) ; portée de la dépendance à A-TEC-02 limitée au protocole de PT-13 (EW-03) — EW-01, EW-02, EW-05 |
| A-TEC-14 | P1 | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Inchangée |
| A-TEC-15 | P1 | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Inchangée |
| A-TEC-16 | P1 | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Inchangée |
| A-TEC-17 | P1 | P1 | Recommandation ou mesure de maîtrise (preuve technique [TECH]) | Inchangée |
| A-TEC-18 | P0-PARALLÈLE | P0-PARALLÈLE | Recommandation ou mesure de maîtrise (preuve archivée, C-07) | Inchangée |
| A-TEC-19 | P0-PARALLÈLE | P0-PARALLÈLE | Recommandation ou mesure de maîtrise (préparation, sans installation) | Inchangée |
| A-JUR-01 | P0-PARALLÈLE | P0-PARALLÈLE | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier | Distinction entre dépendance conditionnelle (clôture de PT-13) et dépendance obligatoire (ARB-01) — EW-02. Priorité P0-PARALLÈLE maintenue après réexamen : l'avis renseigne un critère d'un livrable de P0 (PT-13) sans bloquer l'ouverture ; démarrage immédiat recommandé (délai externe) |
| A-JUR-02 | P0-PARALLÈLE | P2 **(reclassée)** | Recommandation ou mesure de maîtrise (sources) au service des exigences réglementaires | Réexamen v1.3.2 : PD-06 bloque les Lots 2, 5, 7b, 7c, 9c, 10 (registre v1.2.1) ; aucune action ne dépend formellement de A-JUR-02 ; la justification v1.3.1 (« famille P0 ») confondait importance et échéance ; démarrage immédiat recommandé — EW-08 |
| A-JUR-03 | P1 | P2 **(reclassée)** | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier | Réexamen v1.3.2 : échéance sourcée avant le Lot 1 (R52, R53 au registre v1.2.1) ; aucune dépendance documentée vers la validation de l'architecture, le Lot 0 ou un prototype (incidence éventuelle sur le journal plateforme signalée : DI-09) ; exigence réglementaire **intégralement maintenue** ; démarrage anticipé recommandé, notamment sous S1 ou S3 où ARB-09 précède P0 — EW-06 |
| A-JUR-04 | P2 | P2 | Exigence réglementaire [JURIDIQUE] (conditionne l'activation du tiers payant) | Inchangée |
| A-JUR-05 | P1 | P2 **(reclassée)** | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier | Réexamen v1.3.2 : R48 avant le Lot 1, R37 avant le Lot 5d (registre v1.2.1) ; requise par A-PRO-11 (P2) ; aucune incidence documentée sur l'architecture, le Lot 0 ou un prototype ; exigence réglementaire intégralement maintenue — EW-06 |
| A-JUR-06 | P1 | P2 **(reclassée)** | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier | Réexamen v1.3.2 : R55 avant le Lot 1b (registre v1.2.1) ; recommandée pour A-PRO-07 ; aucune incidence documentée sur l'architecture, le Lot 0 ou un prototype ; exigence réglementaire intégralement maintenue ; démarrage anticipé recommandé sous S1 ou S3 (ARB-05 avant P0) — EW-06 |
| A-JUR-07 | P1 | P2 **(reclassée)** | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier | Réexamen v1.3.2 : R42, R43, R44, R51 avant le Lot 2 (registre v1.2.1) ; aucune action n'en dépend avant le Lot 2 ; exigence réglementaire intégralement maintenue — EW-06 |
| A-JUR-08 | P2 | P2 | Obligation juridique à qualifier [JURIDIQUE] (notification de violation) | Inchangée |
| A-JUR-09 | P1 | P2 **(reclassée)** | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier | Réexamen v1.3.2 : R16 avant le Lot 5c, R22 avant le Lot 13 (registre v1.2.1) ; requise par A-SEC-04 (P2) ; incidence éventuelle de R22 sur le port AiGateway non documentée (DI-09) ; exigence réglementaire intégralement maintenue — EW-06 |
| A-JUR-10 | P2 | P2 | Exigence contractuelle | Inchangée |
| A-JUR-11 | P2 | P2 | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier | Inchangée |
| A-SEC-01 | P1 | P1 | Exigence de sécurité | Inchangée |
| A-SEC-02 | P1 | P2 **(reclassée)** | Exigence de sécurité | Réexamen v1.3.2 : échéance documentée avec ARB-05 (RECO-07), avant les Lots 1 et 1b ; ni le plan §10 ni A-PRO-21 ne l'exigent ; l'incidence possible sur l'architecture du mode local n'est pas démontrée et est signalée (DI-08) — si DI-08 est confirmée, retour en P1 ; exigence de sécurité intégralement maintenue ; prise en compte recommandée dans l'analyse de menaces A-SEC-05 — EW-06 |
| A-SEC-03 | P2 | P2 | Exigence de sécurité | Inchangée |
| A-SEC-04 | P1 | P2 **(reclassée)** | Exigence de sécurité ; exigence réglementaire associée (R22) | Réexamen v1.3.2 : liste blanche exigée avant le Lot 13 ; dépend de A-JUR-09 (P2) ; aucune incidence documentée sur l'architecture, le Lot 0 ou un prototype ; exigence de sécurité intégralement maintenue — EW-06 |
| A-SEC-05 | P1 | P1 | Exigence de sécurité | Inchangée |

---

## 8. Justification des actions classées P1

RÉF-10 : une action n'est P1 que si une dépendance démontrée la rend nécessaire avant la validation de l'architecture ou le Lot 0. Chaque P1 est justifiée ci-dessous.

| Action | Dépendance qui le démontre | Phase bloquée | Preuve attendue | Conséquence de son absence |
|---|---|---|---|---|
| A-DOC-02 | Audit §10.2, écart n° 1 : ARB-01 absent des prérequis du Lot 0 dans le dossier §16.2 | Lot 0 (puis chaque lot) | Nouvelle version du dossier ; contrôle « 0 écart » | Lot 0 ouvert sur des prérequis incomplets (RC-08) |
| A-DOC-06 | ARB-01 (A-PRO-15, P1) doit tracer R10 (ED-12) | Validation de l'architecture (via ARB-01) | Nouvelles versions de la matrice et du registre | Incohérence documentaire ; ARB-01 reste protégé par la dépendance A-PRO-15 → A-JUR-01 |
| A-DOC-07 | Acceptation de PT-11 (DEP-01, DEP-04 exécutés et verts) et critère de sortie du Lot 0 exprimés en niveaux de tests | Validation de l'architecture (PT-11, plan §10) ; Lot 0 | Registre des tests à cinq niveaux | Niveau des tests non traçable (RC-12) |
| A-DOC-10 | Dossier §16.2 et plan §11 : schéma du Lot 0 validé avant le Lot 0 (PD-05) | Lot 0 | Document de schéma par lot validé (aucun fichier SQL) | Lot 0 sans schéma validé |
| A-DOC-11 | A-DOC-10 en dépend | Lot 0 (via A-DOC-10) | Note d'inventaire reproduite par une seconde personne | Schéma du Lot 0 fondé sur un inventaire non vérifié |
| A-PRO-14 | Plan §10 (INC-27) ; A-PRO-21 en dépend | Validation de l'architecture | Décision écrite | Validation de l'architecture impossible |
| A-PRO-15 | Plan §10 (moteur) ; A-PRO-21 en dépend | Validation de l'architecture ; Lot 0 | Décision écrite ARB-01 | Validation de l'architecture et Lot 0 impossibles |
| A-PRO-21 | Plan §11 : phase D validée avant le Lot 0 (J-D) | Lot 0 | Procès-verbal de validation | Lot 0 impossible |
| A-PRO-23 | Dossier v1.2.1 §16.2 : design system responsive au Lot 0 — rattachement à confirmer (DI-05) | Lot 0 [RECO, DI-05] | Décision écrite | Design system du Lot 0 fondé sur une interprétation non tranchée |
| A-TEC-04 | Registre v1.2.1 §4 (rapport de chaque prototype avant le Lot 0) ; élément du plan §10 | Validation de l'architecture ; Lot 0 | Rapport et preuves archivées (C-07) | Élément du plan §10 non démontré ; validation de l'architecture et Lot 0 impossibles |
| A-TEC-05 | Registre v1.2.1 §4 (rapport de chaque prototype avant le Lot 0) ; élément du plan §10 | Validation de l'architecture ; Lot 0 | Rapport et preuves archivées (C-07) | Élément du plan §10 non démontré ; validation de l'architecture et Lot 0 impossibles |
| A-TEC-06 | Registre v1.2.1 §4 (rapport de chaque prototype avant le Lot 0) ; élément du plan §10 | Validation de l'architecture ; Lot 0 | Rapport et preuves archivées (C-07) | Élément du plan §10 non démontré ; validation de l'architecture et Lot 0 impossibles |
| A-TEC-07 | Registre v1.2.1 §4 (rapport de chaque prototype avant le Lot 0) ; élément du plan §10 | Validation de l'architecture ; Lot 0 | Rapport et preuves archivées (C-07) | Élément du plan §10 non démontré ; validation de l'architecture et Lot 0 impossibles |
| A-TEC-08 | Registre v1.2.1 §4 (rapport de chaque prototype avant le Lot 0) ; élément du plan §10 | Validation de l'architecture ; Lot 0 | Rapport et preuves archivées (C-07) | Élément du plan §10 non démontré ; validation de l'architecture et Lot 0 impossibles |
| A-TEC-09 | Registre v1.2.1 §4 (rapport avant le Lot 0) ; validation de l'architecture seulement si DI-01 est confirmée ; **non requis pour la clôture de PT-13** | Lot 0 | Rapport PT-07 et preuves (C-07) | Lot 0 impossible ; critère local de PT-13 « non mesuré » |
| A-TEC-10 | Registre v1.2.1 §4 (rapport de chaque prototype avant le Lot 0) ; élément du plan §10 | Validation de l'architecture ; Lot 0 | Rapport et preuves archivées (C-07) | Élément du plan §10 non démontré ; validation de l'architecture et Lot 0 impossibles |
| A-TEC-11 | Registre v1.2.1 §4 (rapport de chaque prototype avant le Lot 0) ; élément du plan §10 | Validation de l'architecture ; Lot 0 | Rapport et preuves archivées (C-07) | Élément du plan §10 non démontré ; validation de l'architecture et Lot 0 impossibles |
| A-TEC-12 | Registre v1.2.1 §4 (rapport de chaque prototype avant le Lot 0) ; élément du plan §10 | Validation de l'architecture ; Lot 0 | Rapport et preuves archivées (C-07) | Élément du plan §10 non démontré ; validation de l'architecture et Lot 0 impossibles |
| A-TEC-13 | A-PRO-15 (ARB-01) en dépend | Validation de l'architecture (via ARB-01) ; clôture de P0 | Rapport comparatif clos selon RÉF-4 | ARB-01 impossible |
| A-TEC-14 | Registre v1.2.1 §4 (rapport de chaque prototype avant le Lot 0) ; élément du plan §10 | Validation de l'architecture ; Lot 0 | Rapport et preuves archivées (C-07) | Élément du plan §10 non démontré ; validation de l'architecture et Lot 0 impossibles |
| A-TEC-15 | Registre v1.2.1 §4 (rapport de chaque prototype avant le Lot 0) ; élément du plan §10 | Validation de l'architecture ; Lot 0 | Rapport et preuves archivées (C-07) | Élément du plan §10 non démontré ; validation de l'architecture et Lot 0 impossibles |
| A-TEC-16 | Conditionnelle : si un complément est accepté, intégration avant la clôture de PT-13 (DI-04) ou au plan §10 | Aucune en cas de refus | Rapports des compléments acceptés | Refus : aucun effet bloquant (plan §8.4) ; acceptation : élément concerné non démontré |
| A-TEC-17 | A-PRO-21 en dépend | Validation de l'architecture | Dossier de validation | Validation de l'architecture impossible |
| A-SEC-01 | Plan §10 (gestion des clés) ; A-PRO-21 en dépend | Validation de l'architecture | Politique de gestion des clés adoptée | Validation de l'architecture impossible |
| A-SEC-05 | Plan §10 (analyse de menaces) ; A-TEC-17 en dépend | Validation de l'architecture | Analyse de menaces revue | Validation de l'architecture impossible |

Les actions A-PRO-23 (DI-05), A-TEC-09 (DI-01, pour la seule validation de l'architecture) et A-DOC-07 (dépendance de maîtrise, non formelle) reposent en partie sur une interprétation à confirmer : voir le rapport d'audit v1.3.2 §22 (anomalies résiduelles).


---

## 9. Réexamen détaillé des actions désignées (mission v1.3.2 §4.2)

| Action | Échéance réelle (source) | Dépendances entrantes | Dépendances sortantes | Incidence sur l'architecture | Incidence sur le Lot 0 | Incidence sur un prototype | Preuve de clôture | Priorité v1.3.1 → v1.3.2 | Caractère obligatoire |
|---|---|---|---|---|---|---|---|---|---|
| A-JUR-01 | R10, R1 « Bloquant 5b » (registre v1.2.1) ; avant ARB-01 (ED-12, RECO-03) | — | A-PRO-15 (obligatoire) ; A-TEC-13 (conditionnelle) | Oui, via ARB-01 (hébergement) | Via ARB-01 | Critère « contraintes juridiques » de PT-13 (non bloquant pour sa clôture) | Avis écrit daté et sourcé | P0-PARALLÈLE → P0-PARALLÈLE | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier (inchangé) |
| A-JUR-02 | PD-06 : Lots 2, 5, 7b, 7c, 9c, 10 (registre v1.2.1) | — | Aucune dépendance formelle | Aucune documentée | Aucune documentée | Aucune | Dossier de sources | P0-PARALLÈLE → P2 | Recommandation ou mesure de maîtrise (sources) au service des exigences réglementaires (inchangé) |
| A-JUR-03 | R52 « Avant le Lot 1 » ; R53 « Avant le Lot 1 (procédure) ; avant toute fin de contrat » (registre v1.2.1) | — | A-PRO-04 (recommandée) | Non documentée (journal plateforme, accès exceptionnel : DI-09) | Aucune documentée | Aucune | Avis écrit rattaché à R52, R53 | P1 → P2 | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier (inchangé) |
| A-JUR-05 | R48 « Avant le lot 1 » ; R37 « Avant le lot 5d » (registre v1.2.1) | — | A-PRO-11 | Aucune documentée | Aucune documentée | Aucune | Avis écrit | P1 → P2 | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier (inchangé) |
| A-JUR-06 | R55 « Avant le Lot 1b » (registre v1.2.1) | — | A-PRO-07 (recommandée) | Aucune documentée | Aucune documentée | Aucune | Avis écrit | P1 → P2 | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier (inchangé) |
| A-JUR-07 | R42, R43, R44, R51 « Avant le lot 2 » (registre v1.2.1) | — | — | Aucune documentée | Aucune documentée | Aucune | Réponses écrites | P1 → P2 | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier (inchangé) |
| A-JUR-09 | R16 « Bloquant 5c » ; R22 « Avant production (lot 13) » (registre v1.2.1) | — | A-SEC-04 | Non documentée (port AiGateway : DI-09) | Aucune documentée | Aucune | Avis écrit | P1 → P2 | Exigence réglementaire [JURIDIQUE] ; obligation juridique éventuelle à qualifier (inchangé) |
| A-SEC-02 | Avec ARB-05 (RECO-07 ; INC-14), avant les Lots 1 et 1b | A-PRO-07 (recommandée) | — | Possible pour le mode local, non démontrée (DI-08) | Aucune documentée | Aucune | Note technique revue, rattachée à ARB-05 | P1 → P2 | Exigence de sécurité (inchangé) |
| A-SEC-04 | Liste validée avant le Lot 13 | A-JUR-09 | — | Aucune documentée | Aucune documentée | Aucune | Liste blanche validée | P1 → P2 | Exigence de sécurité ; exigence réglementaire associée (R22) (inchangé) |
| A-PRO-13 | « Avant le Lot 1 » (audit v1.2.1, INC-01) ; « avant toute création de table » ; Lot 0 à titre de [RECO] (DI-07) | — | A-DOC-10 ([RECO], conditionnelle) | Aucune | [RECO] seulement | Aucune | Décision écrite | P1 → P2 | Gouvernance (décision du propriétaire) (inchangé) |

Aucune exigence n'est supprimée ni allégée : seules les échéances sont alignées sur les sources. Le démarrage anticipé des démarches juridiques reste recommandé, leur délai étant externe et inconnu.


---

## 10. Correspondance avec les conditions du GO P0

Les conditions C-01 à C-08 (niveau d'application, caractère bloquant, actions, responsable, preuve, conséquence) sont définies **une seule fois**, en RÉF-2 ; ce tableau en donne l'index.

| Condition | Niveau(x) | Actions |
|---|---|---|
| C-01 | N1 ; N2 | A-PRO-01 |
| C-02 | N1 | A-PRO-02 (décision) ; A-DOC-01 (alignement ultérieur) |
| C-03 | N2 (G2, G3, PT-13) ; N1 sous S1 | A-PRO-05 ; A-TEC-01 |
| C-04 | N2 (chaque prototype) | A-TEC-02 ; A-PRO-20 |
| C-05 | N1 (règle) ; N2 (contrôle) | A-PRO-01 ; A-TEC-03 |
| C-06 | N2 (PT-07) ; N3 si PT-07 autorisé ; N4 (Lot 0) | A-PRO-06 ; A-TEC-09 |
| C-07 | N2 (acceptation) ; N3 ; N4 | A-TEC-18 |
| C-08 | Règle continue ; bloquante en N4 | A-PRO-15 ; A-PRO-21 |

---

*Fin du registre d'actions v1.3.2 — aucune action n'est réalisée à la date du document.*