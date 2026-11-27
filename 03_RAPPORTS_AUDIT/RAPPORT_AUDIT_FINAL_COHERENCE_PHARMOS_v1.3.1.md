# PHARM'OS — Rapport d'audit final de cohérence v1.3.1

**Version** : **1.3.1** (audit correctif) — 2026-11-04 · remplace pour usage la v1.3 du 2026-11-04, conservée sans modification
**Historique** : v1.3 — audit final de cohérence · **v1.3.1 — revue croisée des quatre livrables v1.3 : 18 écarts démontrés (EV-01 à EV-18, §16), matrice des dépendances corrigée (§17), quantités recontrôlées (§18), décisions réservées (§19), points non résolubles (§20), référentiel commun RÉF-1 à RÉF-10.** Détail : `JOURNAL_CORRECTIONS_PHARMOS_v1.3.1.md`.
**Nature** : audit documentaire indépendant (architecture, SaaS / On-Premise, cybersécurité, qualité logicielle, documentation,
risques réglementaires). **Aucun code, schéma SQL, migration, installation, déploiement ni test technique n'a été réalisé.**
**Base auditée** : livrables v1.2.1 du 2026-11-04 et documents de référence (§2), tous traités **en lecture seule** ; pour l'audit correctif v1.3.1, les quatre livrables v1.3, également en lecture seule (§2.4).
**Statut** : rapport soumis au propriétaire du projet. Il ne vaut ni arbitrage, ni validation juridique, ni autorisation de
développement.

**Référentiel de statuts utilisé** : **[DÉCIDÉ]** arbitré par le propriétaire · **[PROPOSÉ]** non approuvé · **[RECO]**
recommandation du présent audit · **[TECH]** preuve technique requise · **[JURIDIQUE]** validation par un professionnel ou une
autorité compétente · **[À CONFIRMER]** information insuffisante ou contradictoire.

**Documents associés (v1.3.1)** : `JOURNAL_CORRECTIONS_PHARMOS_v1.3.1.md` · `PLAN_DIRECTEUR_FINALISATION_PHARMOS_v1.3.1.md` · `REGISTRE_ACTIONS_PHARMOS_v1.3.1.md` ·
`TABLEAU_SYNTHESE_GO_P0_PHARMOS_v1.3.1.md`.

---

## Référentiel commun v1.3.1 (texte identique dans les quatre livrables)

Cette section est reproduite **à l'identique** dans le rapport d'audit, le plan directeur, le registre d'actions et le tableau
de synthèse v1.3.1. Toute modification ultérieure doit être appliquée simultanément aux quatre documents.

### RÉF-1 — Statut du GO P0

> **GO P0 CONDITIONNEL** : orientation **proposée** par l'audit et le comité, **sous réserve** de l'autorisation écrite du
> propriétaire (C-01) et de la satisfaction des conditions applicables (C-02 à C-08). **À la date du document, aucune condition
> n'est remplie et P0 n'est pas autorisé.**
>
> Cette orientation **ne signifie pas** : P0 déjà autorisé · prototypes déjà autorisés · développement du produit autorisé ·
> Lot 0 autorisé · architecture validée · pilote autorisé · commercialisation autorisée.

### RÉF-2 — Conditions du GO P0 (C-01 à C-08)

| ID | Objet exact | Action(s) | Responsable fonctionnel | Statut | Dépendances | Preuve attendue | Bloque |
|---|---|---|---|---|---|---|---|
| C-01 | Autorisation écrite du propriétaire fixant le périmètre de P0 : prototypes jetables, isolés du dépôt applicatif, données exclusivement fictives, aucune réutilisation de code sans revue et décision, liste des prototypes autorisés ; sous S2 ou S3, dérogation expresse au §16.2 du dossier v1.2.1 | A-PRO-01 | Propriétaire | Non remplie | A-PRO-02 (peut figurer dans la même décision) | Décision écrite, datée, signée ou confirmée | Démarrage de P0 |
| C-02 | Choix d'un scénario de séquencement (S1, S2 ou S3, RÉF-5), qui lève la contradiction ED-01 sur les prérequis de P0 | A-PRO-02 (décision) ; A-DOC-01 (alignement documentaire ultérieur) | Propriétaire ; rédacteur documentaire | Non remplie | — | Décision écrite ; puis nouvelles versions des documents alignées | Démarrage de P0 (décision) ; l'alignement documentaire ne bloque pas le démarrage |
| C-03 | Liste des moteurs candidats d'ARB-01 fixée ; protocoles des prototypes dépendant du moteur étendus à chaque candidat | A-PRO-05 ; A-TEC-01 | Propriétaire (liste) ; architecte (protocoles) | Non remplie | A-TEC-01 dépend de A-PRO-05 | Décision écrite ; protocoles révisés | S1 : démarrage de P0 · S2 et S3 : prototypes G2, G3 et PT-13 (RÉF-3) ; ne bloque ni PT-08 ni PT-12 |
| C-04 | Protocole approuvé avant toute exécution, pour chaque prototype : objectif, environnement, données, scénario, critère chiffré, preuve attendue, relecteur désigné | A-TEC-02 ; A-PRO-20 | Architecte ; responsable qualité | Non remplie | A-PRO-20 ; A-TEC-01 (protocoles G2, G3, PT-13) ; A-TEC-03 (protocoles utilisant les jeux de données) | Protocoles signés par l'approbateur et le relecteur | Démarrage de chaque prototype, donc démarrage de P0 pour les premiers prototypes |
| C-05 | Données exclusivement fictives ; aucune donnée réelle de patient, de collaborateur ou de pharmacie | Règle fixée par A-PRO-01 ; jeux de données spécifiés par A-TEC-03 | Responsable qualité | Non remplie | A-PRO-01 | Périmètre de A-PRO-01 ; spécification des jeux de données ; attestation dans chaque rapport | Exécution de tout prototype (règle) ; prototypes utilisant les jeux de données (A-TEC-03) |
| C-06 | PT-07 et le critère « fonctionnement local » de PT-13 suspendus jusqu'à l'arbitrage d'ARB-08 ; les autres prototypes n'en dépendent pas | A-PRO-06 ; A-TEC-09 | Propriétaire | Non remplie (ARB-08 non arbitré) | A-TEC-19 (préparation facultative) | Décision écrite ARB-08 | PT-07 ; critère « fonctionnement local » de PT-13 ; Lot 0 (rapport PT-07 exigé avant le Lot 0, registre v1.2.1 §4) ; validation de l'architecture : [RECO], DI-01 |
| C-07 | Preuves archivées pour chaque prototype : rapport, mesures brutes, journaux, versions des outils, empreintes | A-TEC-18 | Responsable qualité | Non remplie (aucune exécution) | Chaque exécution | Archive vérifiable | Acceptation de chaque prototype ; clôture de P0 ; validation de l'architecture |
| C-08 | Aucun résultat de prototype ne vaut décision ; ARB-01 et la validation de l'architecture font l'objet de décisions écrites du propriétaire | A-PRO-15 ; A-PRO-21 | Propriétaire | Non remplie (règle applicable dès les premiers résultats) | A-TEC-13 ; A-TEC-17 | Décisions écrites ARB-01 et validation de l'architecture | Validation de l'architecture ; Lot 0 |

**Aucune condition n'est déclarée remplie** : aucune preuve n'existe à la date du document.

### RÉF-3 — Groupes de prototypes et dépendance à ARB-08

| Groupe | Prototypes | Prérequis propres (en plus de C-01, C-04, C-05) | ARB-08 requis ? |
|---|---|---|---|
| G1 — indépendants du moteur et d'ARB-08 | PT-08, PT-12 | Aucun | Non |
| G2 — dépendants du moteur, sans ARB-08 | PT-01, PT-02, PT-03, PT-04, PT-05, PT-06, PT-09, PT-10, PT-11 | C-03 (A-PRO-05, A-TEC-01) ; A-TEC-03 pour PT-01 à PT-06, PT-09, PT-10 | Non |
| G3 — configuration locale de référence | PT-07 | C-03 (moteur du mode local) ; ARB-08 (A-PRO-06) | **Oui** |
| PT-13 — comparatif des moteurs | PT-13 | RÉF-4 | Uniquement pour le critère « fonctionnement local » |
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
| Configuration locale | PT-07 (A-TEC-09), après ARB-08 (A-PRO-06) | Requis pour le seul critère « fonctionnement local » | Critère marqué « non mesuré ». Le rapport PT-07 est exigé **avant le Lot 0** (registre v1.2.1 §4 : chaque prototype produit un rapport avant le Lot 0) ; l'exiger dès la validation de l'architecture (J-D) est une [RECO] à confirmer (DI-01) |
| Avis juridique | A-JUR-01 (R10, R1) | Requis pour le critère « contraintes juridiques » | Critère marqué « non renseigné » ; ARB-01 ne peut pas être arbitré (A-PRO-15) |
| Compléments | PT-14 (ordonnanceur) et PT-16 (approfondissement de la concurrence sur le stock), via A-TEC-16 | Conditionnel : seulement s'ils sont acceptés (A-PRO-17) | **Refusés** : aucun effet bloquant, risque traité selon le plan directeur §8.4 · **Acceptés** : résultat intégré au critère concerné avant la clôture de PT-13, sauf décision contraire du propriétaire dans A-PRO-17 (DI-04) |

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
| J-A | Nouvelle version des documents v1.2.1 corrigeant les écarts ED-01 à ED-18 (ED-02 porte sur 18 points depuis la v1.3.1), validée par le propriétaire ; les écarts EV-01 à EV-18 propres aux livrables v1.3 sont corrigés par la v1.3.1 elle-même | A-DOC-* |
| J-B1 | Conditions de démarrage de P0 : C-01, C-02, ainsi que C-04 et C-05 pour les premiers prototypes. **Sous S1**, en outre : C-03 et C-04 pour tous les protocoles, ARB-05, ARB-06, ARB-09 et ARB-08. **Sous S2 et S3** : aucune autre condition pour G1 ; les conditions de G2, G3 et PT-13 figurent en RÉF-3 et RÉF-5 | A-PRO-01, A-PRO-02, A-PRO-20, A-TEC-02 |
| J-C | Rapports P0 archivés et revus (C-07) ; rapport PT-13 dont chaque critère est mesuré ou explicitement « non mesuré » ou « non renseigné » | Exécutions ; A-TEC-18 |
| J-B2 | ARB-01 arbitré **après** J-C et l'avis R10 ; proposition INC-27 validée ou refusée ; politique de gestion des clés adoptée | A-PRO-15, A-PRO-14, A-SEC-01 |
| J-D | Architecture validée par écrit (C-08) ; [RECO] critère « fonctionnement local » de PT-13 mesuré (exigence N3), à confirmer (DI-01) | J-C, J-B2, A-SEC-05, A-TEC-17, A-PRO-21 |
| J-B3 | Arbitrages et avis nécessaires au Lot 1 | A-PRO-03, A-PRO-04, A-PRO-07 à A-PRO-12 ; A-JUR-03, A-JUR-05 ; A-DOC-10 |
| J-E | Socle (Lot 0) vert dans les deux modes | J-D ; rapports PT-01 à PT-13 (registre v1.2.1 §4) ; A-PRO-13 [RECO, DI-07] ; A-PRO-23 [RECO, DI-05] |
| J-F(n) | Critère de sortie de chaque lot | Prérequis consolidés du lot |
| J-G | Qualification, sécurité et conformité | Lots du périmètre pilote |
| J-H | Bilan du pilote | J-G ; critères H-1 à H-9 du plan directeur |
| J-I | Autorisation de commercialisation par le propriétaire | J-H ; conditions I-1 à I-8 du plan directeur |

Aucune date n'est fixée ; toute planification calendaire relève du propriétaire [À CONFIRMER].

### RÉF-7 — Chemin critique

1. A-PRO-20 (rôles de P0), A-PRO-01 (autorisation) et A-PRO-02 (scénario).
2. A-PRO-05 (moteurs candidats) → A-TEC-01 → A-TEC-02 (avec A-TEC-03) : protocoles G2 approuvés.
3. Exécution de G2 : A-TEC-04, A-TEC-05, A-TEC-06, A-TEC-07, A-TEC-08, A-TEC-14, A-TEC-15 ; avec A-PRO-22 → A-TEC-13 (rapport PT-13).
4. A-PRO-15 (ARB-01), qui exige aussi A-JUR-01 (avis R10, R1).
5. A-TEC-17 (dossier de validation) → A-PRO-21 (validation de l'architecture), qui exige aussi A-PRO-14 (INC-27) et A-SEC-01 (clés).
6. Lot 0, qui exige aussi les rapports PT-01 à PT-13 (registre v1.2.1 §4) ; A-PRO-13 (INC-01) et A-PRO-23 (interprétation du
   responsive) y sont rattachés à titre de [RECO] (DI-07, DI-05).

Branches pouvant devenir critiques : avis R10 (délai externe) → ARB-01 · ARB-08 → PT-07 → critère local de PT-13 → validation
de l'architecture · INC-27 (avec PT-12) → validation de l'architecture · A-SEC-01 et A-SEC-05 → dossier de validation. Les
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

### RÉF-10 — Échelle de priorité des actions

| Priorité | Définition |
|---|---|
| P0-BLOQUANT | Indispensable avant le démarrage de P0, **quel que soit le scénario** retenu |
| P0-PARALLÈLE | Avance pendant P0 sans bloquer son démarrage ; peut bloquer un prototype particulier (indiqué dans la colonne « Effet sur P0 ») |
| P1 | Nécessaire avant la validation de l'architecture ou le démarrage du Lot 0 |
| P2 | Nécessaire pour une phase ultérieure (Lot 1 et suivants, pilote, commercialisation) |

Règle : la priorité exprime une **dépendance vérifiable**, non une importance. Si le propriétaire retient S1, les actions dont la
colonne « Effet sur P0 » indique « S1 : bloque le démarrage » deviennent bloquantes pour le démarrage de P0. Aucune exigence
juridique ou de sécurité n'est supprimée ni reportée au-delà de l'échéance fixée par le registre v1.2.1.

*Fin du référentiel commun v1.3.1.*

---

## 1. Synthèse exécutive

| Domaine | Constat | Appréciation |
|---|---|---|
| Décisions D1–D86 | 86 lignes, continues, sans doublon ; 16 [DÉCIDÉ] (D3, D72–D86), 63 [PROPOSÉ], 5 à réexaminer, 2 caduques — **identique aux chiffres annoncés** | Cohérent |
| D3 | Présentée comme arbitrée et formulée de façon compatible dans les cinq livrables v1.2.1 ; distinction D3 / ARB-01 respectée | Cohérent |
| Points ouverts | R1–R55, PD-01–PD-08, ARB-01–ARB-14, PT-01–PT-13 : séries complètes, sans identifiant manquant ; un seul point fermé (ARB-02) | Cohérent ; **responsables et conditions de clôture insuffisamment précisés** |
| Contradictions | 41 recensées ; 6 bloquantes dont **5 non résolues** (INC-06, INC-13, INC-14, INC-27, INC-39) ; 3 clôtures **conditionnelles** à signaler (INC-10, INC-23, INC-28) | Non résolu — bloque le Lot 0 et les suivants, pas nécessairement P0 |
| Architecture portable | Cohérente sur le papier ; **aucune preuve expérimentale** ; les protocoles PT-01, PT-02, PT-03 et PT-10 ne couvrent que D1 et SQLite alors que PostgreSQL reste candidat | Non démontrée |
| Prérequis de P0 | Le dossier exige ARB-05, ARB-06 et ARB-09 avant P0 ; le registre les rattache aux Lots 1 et 1b ; ARB-01 est déclaré bloquant pour P0 alors que P0 doit l'éclairer ; ARB-08 « bloque P0 » selon le registre v1.2.1 mais n'est nécessaire qu'à PT-07 et au critère « fonctionnement local » de PT-13 | **Contradiction documentaire (ED-01)** ; scénarios S1 à S3 (RÉF-5) et groupes de prototypes (RÉF-3) |
| Tests | Séries spécifiées et numérotées ; **aucun test préparé, exécuté ni archivé** ; deux renvois de plages périmés (DEP-01 à DEP-12, BKP-01 à BKP-06) | Spécification seulement |
| Sécurité, confidentialité, IA | Règles cohérentes (exclusion des données sensibles des LLM, IA explicative, aucune exécution sensible) ; lacunes sur la gestion des clés, l'accès exceptionnel et l'immuabilité en mode local | Lacunes [TECH] et [JURIDIQUE] |
| Réglementaire | R1–R55 tous ouverts, aucune source officielle collectée (PD-06) | Non validé |
| Plan | Lots 0–15 ordonnés ; **18 dépendances absentes des colonnes « Prérequis »** du dossier (§10.2 ; 14 annoncées en v1.3, EV-15) | À corriger |
| Livrables v1.3 (audit correctif) | 18 écarts démontrés entre les quatre livrables v1.3 : dépendances aux compléments facultatifs, prérequis de PT-13, ARB-08, priorités, conditions C-01 à C-08, jalons, chemin critique (EV-01 à EV-18) | Corrigés en v1.3.1 (§16 à §18) |

**Conclusion de gouvernance (§14 et RÉF-1)** : orientation **GO P0 CONDITIONNEL** proposée par le comité, sous réserve de
l'autorisation écrite du propriétaire (C-01) et des conditions C-02 à C-08 ; **aucune condition n'est remplie et P0 n'est pas
autorisé** à la date du document. **NO-GO** pour le Lot 0, le développement fonctionnel, le pilote et la commercialisation.

---

## 2. Inventaire des sources consultées et manquantes

Empreintes SHA-256 de **28 fichiers de `docs/` et du README calculées avant l'audit** ; comparaison après rédaction : §3.3.

### 2.1 Documents principaux v1.2.1

| # | Fichier | Version | Présence | Contenu | Rôle dans l'audit |
|---|---|---|---|---|---|
| 1 | `CONCEPTION_PHARMOS_v1.2.1.md` | 1.2.1 — 2026-11-04 | Présent (1 393 lignes) | Exploitable, sections 0 à 20 | Dossier maître audité |
| 2 | `MATRICE_DECISIONS_D1_D86_v1.2.1.md` | 1.2.1 | Présent (217 l.) | Exploitable, 86 lignes D | Référence des décisions |
| 3 | `REGISTRE_POINTS_OUVERTS_PHARMOS_v1.2.1.md` | 1.2.1 | Présent (244 l.) | Exploitable | R, PD, ARB, PT |
| 4 | `RAPPORT_AUDIT_COHERENCE_v1.2.1.md` | 1.2.1 | Présent (348 l.) | Exploitable, 41 lignes INC | Contradictions |
| 5 | `RAPPORT_VALIDATION_CROISEE_v1.2.1.md` | 1.2.1 | Présent (131 l.) | Exploitable | Contrôles antérieurs (non repris sans recalcul) |
| 6 | `MATRICE_DEPLOIEMENT_SAAS_ONPREMISE_v1.2.1.md` | 1.2.1 | Présent (339 l.) | Exploitable | Architecture double mode, tests DEP/LOC/LIC/BKP/UPD |
| 7 | `JOURNAL_MODIFICATIONS_v1.2.1.md` | 1.2.1 | Présent (209 l.) | Exploitable, 144 entrées | Traçabilité v1.2 → v1.2.1 |

### 2.2 Extensions et documents de référence

| Fichier | Version | Présence | Contenu | Rôle |
|---|---|---|---|---|
| `CONCEPTION_PHARMOS_v1.md` | 1.1 (nom de fichier « v1 ») | Présent (904 l.) | Exploitable ; champ « Version » absent de l'en-tête [À CONFIRMER] | Base v1.1 (§6.1, §9.4…) |
| `EXT02_RH_TEAM_MANAGEMENT.md` | 1.0 | Présent (798 l.) | Exploitable | M15, M16, caisse nominative |
| `EXT03_ORDONNANCES_PRESCRIPTEURS.md` | 1.0 (proposition) | Présent (744 l.) | Exploitable ; **remplacé** par la v1.1 | Historique |
| `archives/AUDIT_EXT03_v1.1/EXT03_ORDONNANCES_PRESCRIPTEURS_v1.1.md` | 1.1 (révision après audit) | Présent (1 232 l.) | Exploitable | **Version applicable d'EXT03** |
| `archives/AUDIT_EXT03_v1.1/00, 02 à 06, LISEZMOI` | — | Présents | Exploitables | Grille D27–D40, dépendances, **matrice de tests EXT03** (SEC-*, T-*), points réglementaires |
| `EXT04_CNAM_CONVENTIONS_ASSURANCES.md` | 1.0 (proposition) | Présent (847 l.) | Exploitable | M18, TP-01 à TP-42 |
| `EXT05_ANNULATIONS_RETOURS_DELEGATIONS.md` | 1.0 (proposition) | Présent (825 l.) | Exploitable | M19, AN, RE, DR |
| `EXT06_CATALOGUE_GOUVERNANCE_FINANCE_v1.1.md` | 1.1 | Présent (1 490 l.) | Exploitable | **Version applicable d'EXT06** ; ANN, NAV, SRV, CAT, FIN, MOF, GOV |
| `EXT06_CATALOGUE_GOUVERNANCE_FINANCE.md` | 1.0 | Présent | Exploitable ; remplacé | Historique |
| `EXT06_v1.1_RAPPORT_DE_REVUE.md` | — | Présent (341 l.) | Exploitable | Revue d'EXT06 |
| Livrables v1.2 (5 fichiers) | 1.2 — 2026-11-03 | Présents | Remplacés par la v1.2.1 | Contrôle de non-écrasement uniquement |
| `README.md` | — | Présent | Exploitable | Non audité au fond |

### 2.3 Pièces citées mais non fournies

| Pièce | Citée par | Statut |
|---|---|---|
| `00_JOURNAL_DU_PROJET.md` | Registre PD-02 ; dossier v1.2.1 | **NON FOURNI / NON ACCESSIBLE** |
| `02_RH_TEAM_MANAGEMENT.md`, `03_ORDONNANCES_PRESCRIPTEURS.md` (noms de l'archive d'origine) | `archives/AUDIT_EXT03_v1.1/00_RAPPORT_AUDIT_EXT03.md` | **NON FOURNI sous ces noms** ; correspondance avec EXT02 / EXT03 v1.0 probable mais **non vérifiée** [À CONFIRMER] |
| Fichier `01_…` de l'archive EXT03 v1.1 | Numérotation 00, 02–06 | Absent ; aucun renvoi explicite trouvé [À CONFIRMER] |
| Sources juridiques officielles (textes, conventions, décisions) | PD-06, R1–R55 | **NON FOURNI** |
| Maquettes d'écran par format | PD-07 | **NON FOURNI** |
| Contrats SaaS, licence locale, conditions d'intervention | PD-08 | **NON FOURNI** |
| Schéma consolidé par lot | PD-05 | **NON FOURNI** (attendu ultérieurement ; ne pas créer à ce stade) |
| Rapports de prototypes PT-01 à PT-13 | Registre §4 | **NON FOURNI** (aucun prototype exécuté) |
| Résultats et preuves de tests | Dossier §15 | **NON FOURNI** (aucun test exécuté) |
| Configuration matérielle de référence du mode B | ARB-08 | **NON FOURNI** |
| Matrice de tests EXT02 numérotée | Dossier §15.1 (« par domaine, non numérotés ») | Inexistante par construction |

### 2.4 Sources supplémentaires de l'audit correctif v1.3.1

| Fichier | Version | Présence | Rôle |
|---|---|---|---|
| `RAPPORT_AUDIT_FINAL_COHERENCE_PHARMOS_v1.3.md` | 1.3 — 2026-11-04 | Présent | Base du présent rapport ; conservé sans modification |
| `PLAN_DIRECTEUR_FINALISATION_PHARMOS_v1.3.md` | 1.3 | Présent | Base du plan directeur v1.3.1 |
| `REGISTRE_ACTIONS_PHARMOS_v1.3.md` | 1.3 (65 actions) | Présent | Base du registre v1.3.1 ; graphe des dépendances recalculé |
| `TABLEAU_SYNTHESE_GO_P0_PHARMOS_v1.3.md` | 1.3 | Présent | Base du tableau de synthèse v1.3.1 |

---

## 3. Méthodologie et limites de l'audit

### 3.1 Méthode

1. Inventaire des fichiers effectivement présents dans le dépôt (`docs/`), lecture de leurs en-têtes.
2. Empreintes SHA-256 de tous les fichiers de `docs/` et du README **avant** l'audit.
3. Recalcul **à partir des lignes réelles** (scripts de lecture seule) : décisions par statut, identifiants R / PD / ARB / PT,
   contradictions par gravité et statut, séries de tests définies et citées, inventaire des tables.
4. Croisement des colonnes « Bloque » du registre avec les colonnes « Prérequis » du plan (dossier §16.2).
5. Relecture ciblée des sections critiques (dossier §6, §7.2, §10–§13, §16, §19 ; matrice de déploiement §3–§5, §8 ; registre
   §3–§4 ; audit §3, §7).
6. Rédaction des constats dans les **seuls** nouveaux livrables v1.3.
7. **v1.3.1** : revue croisée des quatre livrables v1.3 (lecture seule) ; reconstitution du graphe des dépendances du registre
   d'actions (recherche de cycles, de dépendances facultatives et de jalons inversés) ; recomptage des prérequis du dossier §16.2 ;
   rédaction dans cinq nouveaux fichiers v1.3.1 ; empreintes SHA-256 avant et après (annexe A).

### 3.2 Limites

| # | Limite |
|---|---|
| L1 | Contrôles automatisés fondés sur des motifs textuels : ils détectent les incohérences de structure et de chiffres, pas toutes les incohérences de sens |
| L2 | Aucune vérification juridique ou réglementaire : aucune source officielle n'est disponible (PD-06) |
| L3 | Aucune preuve technique : aucun prototype ni test n'a été exécuté ; toute appréciation de faisabilité est [TECH] |
| L4 | L'inventaire des tables repose sur une extraction des tableaux de définition réalisée lors de la consolidation v1.2 (fichier de travail hors dépôt, 345 lignes) ; il est **régénérable** mais n'est pas un document source |
| L5 | Les extensions EXT03 v1.1, EXT04 et EXT05 sont des propositions : leur cohérence interne n'est pas une validation |

### 3.3 Protection des sources

| Contrôle | Résultat |
|---|---|
| Empreintes SHA-256 avant audit | v1.3 : 29 empreintes · **v1.3.1 : 33 empreintes** (32 fichiers de `docs/`, dont les 4 livrables v1.3, + README) et 8 empreintes du code et de la configuration |
| Empreintes après rédaction des livrables v1.3.1 | Annexe A (contrôle final v1.3.1) |
| Fichiers modifiés hors création des livrables v1.3.1 | Annexe A (contrôle final v1.3.1) |

---

## 4. Cohérence des décisions D1–D86

### 4.1 Recalcul à partir des lignes de `MATRICE_DECISIONS_D1_D86_v1.2.1.md`

| Catégorie | Constaté (lignes réelles) | Annoncé (synthèse de la matrice, dossier §18, audit v1.2.1 §1) | Écart | Explication | Correction documentaire |
|---|---|---|---|---|---|
| Lignes de décision | 86 (D1 à D86, continues, aucun doublon) | 86 | 0 | — | Aucune |
| [DÉCIDÉ] ARBITRÉE | 16 : D3, D72 à D86 | 16 | 0 | D3 arbitrée en v1.2.1 | Aucune |
| [PROPOSÉ] PROPOSÉE | 63 | 63 | 0 | — | Aucune |
| À RÉEXAMINER | 5 : D2, D6, D10, D15, D37 | 5 | 0 | — | Aucune |
| CADUQUE | 2 : D61, D66 | 2 | 0 | Remplacées par D75 et par l'administrateur unique | Aucune |
| Non arbitrées | 70 | 70 | 0 | 86 − 16 | Aucune |
| Synthèse par plage (D1–D16 … D72–D86) | Totaux de chaque plage égaux au décompte des lignes | Idem | 0 | — | Aucune |

### 4.2 Contrôles qualitatifs

| Contrôle | Résultat | Appréciation |
|---|---|---|
| D3 dans les cinq livrables v1.2.1 | Présentée comme arbitrée ; formulation compatible (base physiquement distincte, serveur partagé possible, mêmes règles SaaS / On-Premise) ; aucune mention résiduelle « à réexaminer » | Cohérent |
| Distinction D3 / ARB-01 | Explicite dans la matrice (D3), le registre (ARB-02), le dossier (§7.2, T4, T5) et la matrice de déploiement (§1, §4.2, §5) | Cohérent |
| D72 à D86 | Aucun autre statut qu'ARBITRÉE dans aucun document ; notes de renvoi v1.2.1 sur D72, D78 et D83 sans modification du texte arbitré | Cohérent ; voir ED-14 |
| Tiers payant D6, D37, D44 | D6 et D37 à réexaminer, D44 proposée, toutes trois rattachées à INC-06 | Cohérent avec l'audit v1.2.1 ; conflit non tranché |
| Colonnes « Modules » et « Tests » | Renseignées pour toutes les lignes | Cohérent ; renvoi de plage périmé sur D2 (ED-03) |
| D2 | « À réexaminer » ; sa résolution dépend d'ARB-01 et potentiellement de R10 (localisation des données de santé) | Dépendance juridique non signalée dans la matrice (ED-12) |

---

## 5. Cohérence des points R, PD, ARB et PT

### 5.1 Complétude des séries (`REGISTRE_POINTS_OUVERTS_PHARMOS_v1.2.1.md`)

| Série | Attendu | Trouvé | Manquants | Doublons | Statuts constatés |
|---|---|---|---|---|---|
| R | R1–R55 | 55 | Aucun | R42–R51 apparaissent deux fois **par construction** (§1.2 définition, §1.3 précisions) — pas un doublon de définition | 55 OUVERT |
| PD | PD-01–PD-08 | 8 | Aucun | Aucun | 7 OUVERT, 1 PARTIELLEMENT TRAITÉ (PD-03) |
| ARB | ARB-01–ARB-14 | 14 | Aucun | Aucun | 13 OUVERT, 1 ARBITRÉ (ARB-02) |
| PT | PT-01–PT-13 | 13 | Aucun | Aucun | **Pas de colonne « Statut »** ; aucun prototype exécuté |

La seule clôture (ARB-02) repose sur un arbitrage explicite du propriétaire, enregistré lors de la mission v1.2.1. Le présent
audit **ne ferme aucun point**.

### 5.2 Responsables, dépendances et conditions de clôture

| Élément | Constat | Appréciation | Correction recommandée [RECO] |
|---|---|---|---|
| Responsables | Les points R ont une colonne « Interlocuteurs » (INPDP, CNAM, juriste…) mais **aucun responsable interne** chargé d'obtenir la réponse ; ARB, PD et PT n'ont aucun responsable | Insuffisant | Ajouter une colonne « Responsable » dans une révision ultérieure du registre ; en attendant, le registre d'actions v1.3.1 désigne un responsable attendu par action |
| Conditions de clôture | Règles génériques au §5 « Suivi » ; aucune condition propre à chaque point | Insuffisant pour les points bloquants | Critère de clôture individuel défini dans le registre d'actions v1.3.1 |
| Dépendances ARB → lots | 11 incohérences entre la colonne « Bloque » du registre et la colonne « Prérequis » du dossier §16.2 | Non conforme | §10.2 ; ED-01, ED-02 |
| Dépendances R → lots | 9 incohérences (R14, R15, R17, R19, R22, R52, R53, R54, R55 ; 5 annoncées en v1.3, EV-15) | Non conforme | §10.2 ; ED-02 |
| Liens vers les décisions | Présents (colonne « Lié à » des ARB ; « Origine » de R52–R55) | Conforme | — |
| PT : statut, environnement, preuve | Absents du registre v1.2.1 | À compléter (ED-16) ; **ne bloque pas P0** : ces éléments figurent dans chaque protocole approuvé (C-04) — v1.3.1, EV-18 | Annexe B du présent rapport ; plan directeur §8–§9 |
| PT-01 : repli « Option A2 (SQLite seul) » | Le repli présuppose un choix de moteur | Biais en faveur d'une option | ED-04 |

---

## 6. Audit des contradictions INC-01 à INC-41

### 6.1 Recalcul à partir des 41 lignes de `RAPPORT_AUDIT_COHERENCE_v1.2.1.md` §3

| Indicateur | Constaté | Annoncé | Écart |
|---|---|---|---|
| Contradictions | 41 (INC-01 à INC-41, aucun manquant, aucun doublon) | 41 | 0 |
| Bloquantes (B) | 6 | 6 | 0 |
| Majeures (M) | 24 | 24 | 0 |
| Mineures (m) | 11 | 11 | 0 |
| RÉSOLUE-ARB | 9 (INC-07, 08, 09, 10, 11, 12, 17, 20, 40) | 9 | 0 |
| RÉSOLUE-DOC | 7 (INC-02, 19, 23, 24, 28, 30, 34) | 7 | 0 |
| PROPOSÉE | 19 | 19 | 0 |
| OUVERTE | 6 (INC-06, 13, 14, 16, 35, 38) | 6 | 0 |
| Résolues (total) | 16 | 16 | 0 |
| Non résolues (PROPOSÉE + OUVERTE) | 25 | 25 | 0 |
| Bloquantes non résolues | 5 (INC-06, 13, 14, 27, 39) | 5 | 0 |

**Clôtures conditionnelles** [À CONFIRMER] : INC-10 « RÉSOLUE-ARB (principe) ; interprétation à confirmer (ARB-10) », INC-23
« RÉSOLUE-DOC (EXT06 v1.1 validée dans son principe) », INC-28 « RÉSOLUE-DOC (D34 reste proposée) ». Elles sont comptées comme
résolues alors qu'elles dépendent d'un point encore ouvert. **Recommandation** : ne pas les rouvrir par déduction, mais les
signaler comme « résolution conditionnelle » dans une révision ultérieure (ED-11).

### 6.2 Contradictions bloquantes non résolues

#### INC-06 — Périmètre du tiers payant en v1

| Rubrique | Contenu |
|---|---|
| Documents et sections | Dossier v1.1 §11.2 (D6) ; EXT03 v1.1 (D37) ; EXT04 (D44) ; matrice D6, D37, D44 ; registre ARB-03 et §3.1 ; dossier v1.2.1 §16.2 (Lots 5, 7b) |
| Formulations contradictoires | D6 « CNAM plus tard » · D37 « reporté, compatibilité préservée » · D44 « modèle complet livré, aucune convention active par défaut » |
| Risque réel | Lot 5 (caisse) : colonnes de parts organisme présentes ou non dès l'origine ; migration de rattrapage sur **chaque base de pharmacie** (D3) si le choix change ; activation de règles sans source officielle (R23, R31) |
| Décisions ou preuves manquantes | ARB-03 (O1 / O2 / O3) ; statut officiel de la convention CNAM (R23, INC-35) ; sources des taux et listes (R31) |
| Options | O1 reporté (D6 / D37) · O2 modèle complet, conventions inactives (D44) · O3 intermédiaire (registre §3.1) |
| Autorité | **Propriétaire** pour le périmètre ; **CNAM, SPOT, CNOPT, juriste** pour R23 et R31 [JURIDIQUE] |
| Critère de clôture | ARB-03 arbitré et enregistré ; D6, D37, D44 mises en cohérence dans la matrice ; Lot 5 dimensionné en conséquence. L'activation de toute convention reste conditionnée à R23 et R31 |
| Effet sur P0 | **Aucun** |

#### INC-13 — Accès du Super Admin aux données des pharmacies

| Rubrique | Contenu |
|---|---|
| Documents et sections | D72 (matrice) ; dossier v1.1 §9.4 ; EXT06 v1.1 W1 ; dossier v1.2.1 §6.1 (« Accès aux données métier » [RECO, INC-13 — à confirmer, R52]) ; registre ARB-09, R52 |
| Formulations contradictoires | D72 : le Super Admin crée, bloque, supprime et recrée les comptes · v1.1 §9.4 : accès support « désactivé par défaut, ouvert temporairement par le titulaire » · EXT06 W1 : « le support APEX n'obtient **jamais** d'accès aux données » |
| Distinction à établir | (1) **administration technique** de l'instance, sans lecture des données ; (2) **gestion des comptes** (D72, décidé) ; (3) **accès exceptionnel temporaire** aux données métier (non décidé) |
| Risque réel | Accès éditeur non maîtrisé à des données de santé (PAT-N2/N3) et de paie (RH-N4) ; qualification juridique de l'éditeur ; compromission du compte Super Admin, dont l'identifiant de messagerie est public (RG3) |
| Décisions ou preuves manquantes | ARB-09 ; R52 (statut de l'éditeur, conditions contractuelles, durée de conservation du journal plateforme, déclaration INPDP) ; modèle technique de l'accès exceptionnel (durée, périmètre, double journal, révocation) |
| Options | a) aucun accès éditeur aux données (EXT06 W1) · b) accès exceptionnel ouvert par l'administrateur de la pharmacie, limité, authentifié, justifié, journalisé (recommandation v1.2.1) · c) accès du Super Admin sur sa seule autorité (contraire aux trois sources) |
| Autorité | **Propriétaire** (ARB-09) ; **juriste / INPDP** (R52) [JURIDIQUE] |
| Critère de clôture | ARB-09 arbitré ; R52 répondu par écrit ; tests ADM et journal plateforme définis pour l'option retenue |
| Effet sur P0 | **Aucun** : aucun prototype ne met en œuvre l'accès aux données métier |

#### INC-14 — Pouvoirs et limites du Super Admin en On-Premise

| Rubrique | Contenu |
|---|---|
| Documents et sections | D72 ; exigence « aucune dépendance permanente aux serveurs APEX » (dossier §5.1) ; matrice de déploiement §4.7, §6, RD9 ; registre ARB-05 |
| Formulations contradictoires | D72 : compte central immuable gérant les accès des pharmacies · mode B : installation autonome, sans connexion permanente |
| Risque réel | **Immuabilité non garantissable en local** : quiconque accède au système et aux fichiers de la base peut contourner un déclencheur (dossier §6.1 : « contrôle en base [TECH] ») ; dépendance cachée à APEX ; récupération impossible sans e-mail |
| Décisions ou preuves manquantes | ARB-05 (a / b / c) ; ARB-04 (récupération) ; preuve technique de la protection réellement atteignable en local [TECH] |
| Options | a) aucun Super Admin opérant localement, gestion par licence signée · b) Super Admin local hors ligne avec code signé · c) intervention APEX ponctuelle autorisée par l'administrateur (combinable avec a) |
| Autorité | **Propriétaire** (ARB-05) ; **juriste** pour les responsabilités en mode B (R55) |
| Critère de clôture | ARB-05 arbitré ; règle d'immuabilité reformulée pour le mode B en termes de **détection** (intégrité signée, journal) et non seulement d'interdiction ; tests LOC et ADM correspondants |
| Effet sur P0 | **Indirect** : PT-07 (installateur) et PT-12 (signature) produisent des éléments utiles à ARB-05 sans en dépendre |

#### INC-27 — Référentiels, versions, signatures et paquets

| Rubrique | Contenu |
|---|---|
| Documents et sections | Dossier v1.1 `ref_*` (global, sans tenant) ; EXT03 v1.1 `medical_specialties` (`tenant_id` NULL) ; EXT06 v1.1 (référentiels mixtes) ; registre §3.2 ; matrice de déploiement §4.12, §4.15 ; DEP-11 ; PT-12 |
| Formulations contradictoires | Référentiel global partagé dans une base commune · une base physiquement distincte par pharmacie (D3, décidé) et un mode B hors ligne |
| Risque réel | Contradiction **confirmée** par D3 : aucune table globale partagée possible ; risques d'écrasement de données locales, d'incompatibilité de versions de schéma, de paquet altéré, de divergence entre pharmacies |
| Décisions ou preuves manquantes | Validation de la proposition (registre §3.2) ; preuve de la chaîne de signature hors ligne (PT-12) ; politique de garde de la clé privée APEX [TECH] ; droits de diffusion des référentiels officiels (R31, R43) [JURIDIQUE] |
| Options | a) paquets signés importés dans chaque base (proposition) · b) service de référentiel en ligne interrogé à la demande (incompatible avec le mode B hors ligne) · c) saisie locale intégrale |
| Autorité | **Propriétaire** (validation) ; **architecte** (preuve PT-12) ; **juriste** (droits de diffusion) |
| Critère de clôture | Proposition validée et enregistrée comme décision ; PT-12 concluant ; DEP-11 spécifié pour la version retenue |
| Effet sur P0 | PT-12 **éclaire** la décision et peut démarrer sans elle |

#### INC-39 — Architecture Cloudflare v1.1 incompatible avec le fonctionnement autonome

| Rubrique | Contenu |
|---|---|
| Documents et sections | Dossier v1.1 §6.1, §6.4–§6.6 ; dossier v1.2.1 §13 ; matrice de déploiement §1–§5 ; ARB-01 ; PT-01 à PT-13 |
| Formulations contradictoires | v1.1 : Workers, D1, R2, hébergement géré · exigence décidée : mode B autonome sur le matériel de la pharmacie |
| Risque réel | Divergence des deux modes (atomicité D1 `batch` / transactions, déclencheurs, recherche plein texte, tâches planifiées) ; double coût de test ; moteur choisi sans preuve |
| Décisions ou preuves manquantes | ARB-01 ; preuves P0 **pour chaque moteur candidat**, y compris PostgreSQL, aujourd'hui absent des protocoles PT-01, PT-02, PT-03 et PT-10 (ED-04) ; contrainte de localisation des données de santé (R10) susceptible d'exclure certains hébergements [JURIDIQUE] |
| Options | A1 Cloudflare + Node.js/SQLite local · A2 Node.js + SQLite dans les deux modes · A3 PostgreSQL (SaaS) + SQLite (local) · A3' PostgreSQL dans les deux modes · autre option documentée |
| Autorité | **Propriétaire** (ARB-01), au vu du rapport PT-13 et de l'avis juridique sur R10 |
| Critère de clôture | Rapport PT-13 archivé avec mesures ; ARB-01 arbitré et enregistré ; architecture validée (phase D) |
| Effet sur P0 | P0 **est** le moyen de clôture : INC-39 bloque le Lot 0, pas P0 |

### 6.3 Autres contradictions ouvertes

| ID | Objet | Autorité | Critère de clôture | Bloque |
|---|---|---|---|---|
| INC-16 | Validation à distance (D73) en mode B hors de l'officine | Propriétaire (ARB-07) | ARB-07 arbitré ; RSP-08 et LOC-07 spécifiés pour l'option retenue | Lot 1 |
| INC-35 | Statut de la convention CNAM fondé sur la presse | CNAM, SPOT, CNOPT, juriste (R23) | Réponse écrite datée et sourcée rattachée à R23 | Lot 7b (activation) |
| INC-38 | Effet de la fin d'une licence ou d'un abonnement | Propriétaire (ARB-06) ; juriste (contrats, PD-08) | ARB-06 arbitré ; LIC-03 à LIC-05 confirmés | Lots 1, 1b |

### 6.4 Contradictions au statut PROPOSÉE (19)

Elles ont une résolution documentée mais **non validée**. Elles se ferment par la validation séquentielle des extensions
(ARB-14 : EXT03 v1.1 → EXT04 → EXT05) ou par une validation explicite du propriétaire lors du schéma par lot (PD-05). Aucune
ne bloque P0. INC-01 (`coverage_rules`) doit être tranchée **avant toute création de tables** ; l'audit v1.2.1 indique
« avant le Lot 1 » dans la ligne INC-01 et « Lots 2 et 3 » dans son §7 (ED-13).

---

## 7. Audit de l'architecture SaaS / On-Premise

### 7.1 Cohérence de l'architecture cible (dossier §13, matrice de déploiement §2–§4)

| Composant | Description documentée | Cohérence documentaire | Preuve technique disponible | Statut |
|---|---|---|---|---|
| Frontend SPA / PWA commun | Une seule application servie par les deux modes (DEP-07, RSP-12) | Cohérent | Aucune | [TECH] |
| API Hono `/api/v1` | Mêmes routes, deux points d'entrée (`entry/saas.ts`, `entry/local.ts`) | Cohérent | Aucune | [TECH] |
| Services métier M00–M20 | Communs ; n'appellent que des ports ; interdiction des conditions de mode (DEP-01) | Cohérent | Aucune | [TECH] |
| Ports et adaptateurs | 12 ports listés (Database, ObjectStorage, JobScheduler, Clock, Secrets, LicenseVerifier, Notifier, AiGateway, OcrGateway, ReferentialSource, BackupService, UpdateService) | Cohérent entre dossier et matrice | Aucune | [TECH] |
| Base indépendante par pharmacie | [DÉCIDÉ D3] ; routage depuis la session authentifiée ; base d'instance séparée | Cohérent dans les cinq documents | Aucune (PT-05, PT-06) | Principe décidé ; mise en œuvre [TECH] |
| Stockage documentaire | R2 / S3 (SaaS) ; dossier local (B) ; liens temporaires servis après contrôle des droits | Cohérent | Aucune | [TECH] |
| Ordonnanceur | Outbox + rattrapage ; planificateur local + rattrapage au démarrage (LOC-08) | Cohérent | Aucune | [TECH] — **aucun prototype dédié** (ED-06) |
| Secrets | Coffre de l'hébergeur (A) / coffre local chiffré (B) | Cohérent | Aucune | [TECH] — **gestion des clés non spécifiée** (§8.2) |
| Licence | Abonnement (A) / fichier signé vérifié hors ligne (B) ; expiration = lecture seule [PROPOSÉ, ARB-06] | Cohérent | Aucune (PT-12) | [TECH] + [PROPOSÉ] |
| Sauvegarde | Indépendante par base ; second support à la charge de la pharmacie en mode B | Cohérent | Aucune (PT-09) | [TECH] ; responsabilités [JURIDIQUE R55] |
| Mises à jour | Paquets signés, sauvegarde préalable, migration base par base (UPD-07) | Cohérent | Aucune (PT-06, PT-12) | [TECH] |
| Notifications | Port `Notifier`, connecteurs optionnels | Cohérent ; peu détaillé | Aucune | [TECH] |
| Connecteurs OCR et IA | Optionnels, désactivés par défaut en mode B ; aucune donnée de santé vers un LLM | Cohérent | Aucune | [TECH] + [JURIDIQUE R16, R22] |

### 7.2 Indépendance du cœur métier vis-à-vis de l'environnement

La règle (DEP-01 : `core/` et `modules/` n'importent jamais `adapters/` ni une API d'environnement) est **énoncée** de façon
cohérente. Elle n'est **pas démontrée** : aucun code n'existe et la vérification automatique est un test spécifié, non préparé.
Trois points de friction documentaires subsistent :

| # | Point | Constat | Statut |
|---|---|---|---|
| A1 | Unité de travail (T3) | Le dossier la décrit comme « `batch()` (D1) ou transaction (SQLite) » ; l'option A3 / A3' (PostgreSQL) n'a pas d'adaptateur décrit dans le dossier, seulement dans la matrice de déploiement §3 | [À CONFIRMER] — ED-05 |
| A2 | Déclencheurs d'immutabilité | Écrits en dialecte SQLite (`RAISE(ABORT)`) dans les sources ; un seul dialecte exigé (T2) ; incompatible avec A3 sans double écriture | [TECH] — PT-03, PT-13 |
| A3 | Recherche plein texte | FTS5 (SQLite) présumé ; aucun équivalent décrit pour PostgreSQL ; repli trigrammes | [TECH] — PT-10 |

### 7.3 Risques de divergence entre les deux modes

| Domaine | Risque | Couverture documentaire | Preuve requise |
|---|---|---|---|
| Atomicité | D1 n'offre pas de transaction interactive ; même logique métier sur `batch` et sur transactions | Unité de travail commune avec gardes (matrice §3) | PT-01, PT-02, PT-13 |
| Transactions et concurrence sur les stocks | Écritures concurrentes de plusieurs postes ; gardes réévaluées | RG7 ; T-ATO, DEP-05 | PT-13 : scénario de concurrence sur le stock intégré à son protocole par A-TEC-01 (ED-07) ; PT-16 en approfondissement facultatif |
| Contraintes et déclencheurs | Contraintes différées, `RAISE(ABORT)`, transitions | PT-03 | PT-03 étendu aux moteurs candidats |
| Migrations | N bases, reprise, version par base | PT-06, UPD-07 | PT-06 |
| Restauration | D'une base sans effet sur les autres ; entre modes (DEP-09) | BKP-07, DEP-09 | PT-09 (restauration d'une base) ; restauration croisée A ↔ B : PT-15 proposé, **facultatif** ; à défaut, risque ouvert jusqu'à DEP-09 (ED-06) |
| Indexation et recherche | FTS5 / trigrammes / PostgreSQL | PT-10 | PT-10 étendu |
| Sauvegarde à chaud | Instantané cohérent sans bloquer la caisse | PT-09, BKP-04 | PT-09 par moteur |
| Tâches planifiées | Poste éteint la nuit ; hébergement géré sans cron | LOC-08 ; matrice §5 (remarque Hosted) | **Aucun prototype principal** ; PT-14 proposé, **facultatif** ; à défaut, risque ouvert jusqu'aux tests LOC-08 (Lot 1b) (ED-06) |
| Fonctionnement hors ligne | Deux notions : poste déconnecté (D10, Lot 14) et installation autonome (mode B) | Dossier §8 ; matrice §6 | LOC-02, LOC-03 (Lot 1b) |
| Signature des mises à jour | Vérification hors ligne par clé publique | PT-12 | PT-12 |
| Horloge | Dérive du serveur local | PT-08, LOC-05 | PT-08 |

### 7.4 Appréciation

L'architecture est **cohérente sur le plan documentaire**. Sa **portabilité n'est pas démontrée** : aucune affirmation de
faisabilité ne peut être retenue avant les résultats mesurés de P0. La présence de PostgreSQL parmi les candidats d'ARB-01 sans
protocole de prototype correspondant (hors PT-13) est l'écart technique le plus important du corpus.

---

## 8. Audit sécurité, confidentialité et IA

### 8.1 Cohérence des règles

| Thème | Règle documentée | Sources | Cohérence | Statut |
|---|---|---|---|---|
| Échelles de confidentialité | `RH-N0` à `RH-N4` (collaborateurs) et `PAT-N1` à `PAT-N3` (patients) | Dossier §10.1 ; EXT02 §11.1 ; EXT03 v1.1 §9 ; INC-29 | Cohérent ; préfixage **proposé** [RECO, INC-29 PROPOSÉE] | [PROPOSÉ] |
| Cloisonnement et chiffrement | PAT-N2/N3 et RH-N3/N4 cloisonnés, chiffrés au niveau applicatif, lecture journalisée (`sensitive_access_log`) | Dossier §10.2 S1 | Cohérent | [TECH] |
| Tiers payant | Aucun diagnostic stocké (D49) | EXT04 ; S3 | Cohérent | [PROPOSÉ] (D49) ; [JURIDIQUE R29] |
| Actes et services | `service_acts` traité comme sensible (INC-32) | S4 ; D78 | Cohérent avec D78 [DÉCIDÉ] | [JURIDIQUE R54] |
| Données financières | Exceptions comptables sans donnée de santé ni de paie (S5) ; marges sous `analytics.margin.read` (D79) | EXT06 ; D79 | Cohérent | [DÉCIDÉ D79] |
| Super Admin | Gère les accès, pas les données ; MFA obligatoire ; récupération sans e-mail (ARB-04) | §6.1, §6.4, §12 | Cohérent avec D72 ; accès exceptionnel non arbitré (INC-13) | [DÉCIDÉ D72] + [PROPOSÉ] |
| MFA et step-up | MFA TOTP obligatoire pour Super Admin et administrateurs ; step-up pour les décisions sensibles | §12 | Cohérent | [TECH] |
| Séparation des permissions | Ordre d'évaluation unique ; permissions de gouvernance jamais attribuées par rôle | §6.5 ; EXT06 §6.3 | Cohérent | [PROPOSÉ] (EXT06 validée dans son principe) |
| Journalisation | Audit chaîné ; journal plateforme immuable ; vérification après restauration | §8, §12 | Cohérent | [TECH] ; durée de conservation [JURIDIQUE R5, R52] |
| Sauvegardes et restauration | Archive chiffrée, manifeste signé, chaîne de hachage vérifiée | Matrice §4.9 ; BKP | Cohérent | [TECH] |
| Licences | Fichier signé ; jamais de blocage des données | Matrice §4.8 ; registre §3.3 | Cohérent | [PROPOSÉ ARB-06] |
| Hors ligne | Données de santé sur le matériel de la pharmacie (S7) | §10.2 | Cohérent | [TECH] ; [JURIDIQUE R10, R55] |
| IA | Chiffres déterministes, IA explicative ; lecture seule ; brouillons uniquement ; aucune exécution sensible ; liste blanche de champs ; jamais PAT-N2/N3, RH-N3/N4, services nominatifs | Dossier §11 | Cohérent | [TECH] ; [JURIDIQUE R22] |
| OCR | Service APEX optionnel, repli sur l'appareil, jamais via un LLM (D41 proposée) | §11 ; EXT03 v1.1 | Cohérent | [PROPOSÉ D41] ; [JURIDIQUE R16] |

### 8.2 Lacunes identifiées

| # | Lacune | Risque | Nature | Action |
|---|---|---|---|---|
| S-01 | **Gestion des clés** : génération, garde, rotation, sauvegarde et perte des clés de chiffrement applicatif (PAT, RH) et de la clé privée de signature APEX non spécifiées | Perte définitive de données chiffrées ; compromission de toute la chaîne de mises à jour et de licences | [TECH] + [RECO] | A-SEC-01 |
| S-02 | **Accès exceptionnel** : durée, périmètre, double journal, révocation et notification non spécifiés, faute d'arbitrage d'ARB-09 | Accès éditeur non maîtrisé | [PROPOSÉ] + [JURIDIQUE R52] | A-PRO-04, A-JUR-03 |
| S-03 | **Immuabilité du Super Admin en mode B** : un déclencheur ne protège pas contre un administrateur système local | Contournement non détecté | [TECH] | A-SEC-02 |
| S-04 | **Identifiant public du Super Admin** (adresse de messagerie) | Ciblage par hameçonnage | [RECO] | A-SEC-03 |
| S-05 | **Liste blanche IA** : énoncée, non établie (champs, agrégats, seuil de petits effectifs D34 proposé) | Fuite par agrégats trop fins | [TECH] + [JURIDIQUE R22] | A-SEC-04 |
| S-06 | **Analyse de menaces** : aucune modélisation formelle (SaaS multi-bases sur serveur partagé, poste local, appareils mobiles, paquets signés) | Angles morts | [RECO] | A-SEC-05 |
| S-07 | **Notification de violation de données** : non traitée | Non-conformité possible | [JURIDIQUE] (rattachable à R1) | A-JUR-08 |

### 8.3 Cohérence INC-13 / ARB-09 / R52

Les trois éléments sont **alignés** : même proposition (accès exceptionnel autorisé, limité, authentifié, justifié, journalisé),
même statut (ouvert), mêmes références croisées. Le dossier §6.1 marque correctement la règle « [RECO, INC-13 — à confirmer,
R52] ». La colonne « Ce qu'il ne peut **pas** faire » du tableau §6.1 (« Lire ou modifier les données métier ») est elle aussi
marquée [RECO] : aucune règle n'est présentée comme décidée au-delà de D72.

---

## 9. Audit des tests et des inventaires

### 9.1 Inventaire des tables

| Chiffre | Méthode | Constat |
|---|---|---|
| **345** | Lignes des tableaux de définition extraites des six documents de conception (dossier v1.1, EXT02, EXT03 v1.1, EXT04, EXT05, EXT06 v1.1), y compris les lignes « modification » et « remplacement » | Base de l'extraction |
| **243** | Noms de tables **distincts** parmi les lignes de **définition** | Confirmé par recalcul |
| **10 noms définis dans deux documents** | 8 tables de caisse (dossier v1.1 et EXT02) ; `prescriptions` (dossier v1.1 et EXT03 v1.1) ; `coverage_rules` (EXT02 et EXT04) | **L'audit v1.2.1 et le dossier v1.2.1 §1 annoncent 9** : `prescriptions` est traitée comme un remplacement et non comme un doublon (ED-08) |
| **244** | 243 noms − 0 + 1 : le nom `coverage_rules` recouvre **deux tables de sens différent** (planning / prise en charge), comptées séparément | Confirmé : somme des modules du dossier §7.4 = 244 |
| **248** | 244 + 4 tables nouvelles de la v1.2 (`platform_audit_log`, `tenant_data_deletion_requests`, `backup_runs`, `instance_versions`) | Confirmé (audit v1.2.1 §5.2) |
| Écart de présentation | Les cellules du §7.4 pour M15 et M18 contiennent respectivement 43 et 33 noms entre accents graves, pour des totaux affichés de 42 et 32 : le nom d'origine `coverage_rules` y est cité entre parenthèses à côté du nom proposé | Pas d'erreur de compte ; présentation ambiguë (ED-09) |

**Conflit `coverage_rules`** : EXT02 §2.3 (couverture du planning, M15) et EXT04 §3.2 (règles de prise en charge, M18) définissent
deux tables différentes sous le même nom. Les noms `shift_coverage_rules` et `payer_coverage_rules` sont **proposés** (INC-01,
PROPOSÉE) ; les sources EXT02 et EXT04 ne sont pas modifiées et ne doivent pas l'être à ce stade. Le renommage doit être validé
avant toute création de table.

### 9.2 Séries de tests — reconstitution

| Série | Identifiants réellement définis | Document de définition | Renvois incohérents | Origine de l'écart |
|---|---|---|---|---|
| DEP | DEP-01 à DEP-13 (13) | Matrice de déploiement §8.1 | « DEP-01 à DEP-12 » dans la ligne D2 de la matrice des décisions | Référence non mise à jour lors de l'ajout de DEP-13 (v1.2.1) — ED-03 |
| BKP | BKP-01 à BKP-07 (7) | Matrice de déploiement §8.4 | « BKP-01 à BKP-06 » dans RD3 de la matrice de déploiement | Idem (BKP-07 ajouté en v1.2.1) — ED-03 |
| UPD | UPD-01 à UPD-07 (7) | Matrice de déploiement §8.5 | Aucun | — |
| LOC | LOC-01 à LOC-08 (8) | Matrice de déploiement §8.2 | Aucun | — |
| LIC | LIC-01 à LIC-06 (6) | Matrice de déploiement §8.3 | Aucun | — |
| ADM | ADM-01 à ADM-10 (10) | Dossier v1.2.1 §15 | Aucun | — |
| ANN | ANN-01 à ANN-09 et ANN-11 à ANN-17 (EXT06 v1.1) ; ANN-18 à ANN-21 (dossier) | EXT06 v1.1 ; dossier §15 | **ANN-10 n'existe que sous la forme « ANN-10 [v1.1] »** dans EXT06 v1.1 : il est défini mais avec un marqueur dans la cellule d'identifiant | Présentation ; non bloquant — ED-10 |
| NAV | NAV-01 à NAV-04 (EXT06 v1.1) ; NAV-05 à NAV-07 (dossier) | EXT06 v1.1 ; dossier | Aucun | Séries réparties sur deux documents, sans doublon |
| SRV | SRV-01 à SRV-07 (EXT06 v1.1) ; SRV-08 à SRV-12 (dossier) | Idem | Aucun | Idem |
| CAT | CAT-01 à CAT-13 (EXT06 v1.1) ; CAT-14 à CAT-16 (dossier) | Idem | Aucun | Idem |
| FIN | FIN-01 à FIN-20 (EXT06 v1.1) ; FIN-21 à FIN-25 (dossier) | Idem | Aucun | Idem |
| RSP | RSP-01 à RSP-16 (16) | Dossier §15.3 | Aucun (renvois mis à jour en v1.2.1) | — |
| MOF | MOF-01 à MOF-07 (7) | EXT06 v1.1 | Aucun | — |
| TP | TP-01 à TP-42 (42) | EXT04 | Aucun | — |
| SEC-*, T-*, C-*, O-* | Définis | Archive EXT03 v1.1 `05_MATRICE_TESTS.md` | Aucun | Document hors des livrables principaux, mais présent |
| EXT02 | Non numérotés (« par domaine ») | EXT02 §12.2 | — | Non traçables individuellement (ED-15) |

### 9.3 État d'exécution

| Niveau | État constaté |
|---|---|
| Test spécifié | Toutes les séries ci-dessus |
| Test préparé (jeu de données, script, environnement) | **Aucun** |
| Test exécuté | **Aucun** |
| Test réussi | **Aucun** |
| Preuve archivée | **Aucune** |

Les contrôles réalisés lors des missions v1.2, v1.2.1 et du présent audit sont des **contrôles documentaires** (comptages,
références croisées, empreintes). Ils ne constituent en aucun cas des tests d'exécution.

---

## 10. Audit du plan de développement

### 10.1 Ordre des lots (dossier v1.2.1 §16.2)

L'ordre P0 → 0 → 1 → 1b → 2 → 3 → 4 → 5 → 5b/5c/5d → 6 → 7/7b/7c → 8–15 est **logique** : socle, gouvernance, catalogue, stock,
achats, vente, puis extensions et services dépendants. Les lots 1 à 5 créent les tables structurantes et doivent intégrer dès
l'origine les colonnes prévues par les extensions (principe §16.1-3) — ce qui suppose INC-01, INC-06 et la validation des
extensions **avant** ces lots.

### 10.2 Dépendances absentes des colonnes « Prérequis » du dossier §16.2

| # | Point | Déclaré bloquant (registre) | Prérequis du lot (dossier §16.2) | Écart |
|---|---|---|---|---|
| 1 | ARB-01 | P0, Lot 0 | Lot 0 : « P0 concluant » | ARB-01 absent des prérequis du Lot 0 ; et « bloque P0 » contredit « ARB-01 arbitré à l'issue de P0 » |
| 2 | ARB-03 | Lots 5, 7b | Lot 5 : « Lots 3, 4 ; R49 ; D59, D60 » ; Lot 7b : « INC-06 tranché » | Absent du Lot 5 ; présent indirectement au Lot 7b |
| 3 | ARB-05 | Lots 1, 1b | Lot 1b : « Lot 1 » | Absent du Lot 1b |
| 4 | ARB-06 | Lots 1, 1b | Lot 1b : « Lot 1 » | Absent du Lot 1b |
| 5 | ARB-08 | P0, Lot 1b | P0 : « ARB-05, ARB-06, ARB-09 » ; Lot 1b : « Lot 1 » | Absent des deux |
| 6 | ARB-09 | Lot 1 | Lot 1 : « ARB-04 à ARB-07, ARB-11, ARB-12 » | Absent du Lot 1 |
| 7 | ARB-10 | Lot 5d | Lot 5d : « D59–D70 ; R34–R41 » | Absent |
| 8 | ARB-13 | Lot 9c | Lot 9c : « R47 » | Absent |
| 9 | ARB-14 | Lots 5b et suivants | Lot 5b : « D27–D43 ; R1–R13 » | Absent |
| 10 | R22 | Lot 13 | Lot 13 : « D7 » | Absent |
| 11 | R52 | Lot 1 | Lot 1 : « R48 » | Absent |
| 12 | R53 | Lot 1 | Lot 1 : « R48 » | Absent |
| 13 | R54 | Lot 9c | Lot 9c : « R47 » | Absent |
| 14 | R55 | Lot 1b | Lot 1b : « Lot 1 » | Absent |
| 15 | R14 | Lot 5b (« Bloquant 5b ») | Lot 5b : « Lot 5 ; D27–D43 ; R1–R13 » | Absent (ajout v1.3.1) |
| 16 | R15 | Lot 5b | Idem | Absent (ajout v1.3.1) |
| 17 | R19 | Lot 5b | Idem | Absent (ajout v1.3.1) |
| 18 | R17 | Lot 10 (« Bloquant 10 ») | Lot 10 : « Lots 3, 5, 5b ; D5 ; R18, R35, R36 » | Absent (ajout v1.3.1) |
| 19 | ARB-05, ARB-06, ARB-09 | Lots 1, 1b (registre) | **Prérequis de P0** (dossier §16.2 ; audit v1.2.1 §7) | Exigence plus stricte dans le dossier que dans le registre, sans justification écrite ; traitée par les scénarios RÉF-5 (ED-01) |

La règle du registre (§5 « Suivi ») : « un lot ne démarre pas si un point listé dans sa colonne « Bloque » est ouvert ».
La colonne « Bloque » du registre fait donc foi ; le dossier §16.2 doit être aligné (ED-02). **Recomptage v1.3.1** : 18 écarts de
dépendance (lignes 1 à 18) ; la ligne 19 est une exigence plus stricte du dossier, traitée par ED-01.

### 10.3 Risques de développement prématuré

| Risque | Cause | Conséquence |
|---|---|---|
| Lot 0 avant ARB-01 | Pression calendaire | Socle réécrit si le moteur change ; double dialecte |
| Lots 1–5 avant INC-01, INC-06, ARB-14 | Tables définitives créées sans les colonnes des extensions | Migrations de rattrapage sur chaque base de pharmacie (D3) |
| Lot 1 avant ARB-09 et R52 | Accès éditeur implanté sans cadre | Refonte de la gouvernance et exposition juridique |
| Lot 1b avant ARB-05, ARB-08, R55 | Installateur sans configuration ni responsabilités | Déploiement local non supportable |
| Lot 7b avant R23, R31 | Activation de conventions sans source | Facturation erronée aux organismes |

### 10.4 Parallélisation possible

Peuvent avancer en parallèle de P0 sans engager de décision irréversible : arbitrages propriétaires (ARB-03 à ARB-14),
démarches juridiques (R1–R55, PD-06, PD-08), maquettes (PD-07), validation séquentielle des extensions (ARB-14), schéma par lot
(PD-05) **au niveau documentaire uniquement**.

### 10.5 Critères de sortie

Le dossier §16.1 définit une « Definition of Done » commune (tests dans les deux modes, responsive, isolation, sauvegarde) mais
**pas de critère de sortie propre à chaque lot**. Le plan directeur v1.3 (§11) propose un critère de sortie par lot [RECO].

---

## 11. Registre des écarts documentaires

Chaque écart est à corriger **ultérieurement**, dans une nouvelle version des documents concernés ; aucun document source n'a été
modifié par le présent audit.

| ID | Document et section | Problème | Correction recommandée [RECO] | Gravité | Bloque |
|---|---|---|---|---|---|
| ED-01 | Dossier v1.2.1 §16.2 (ligne P0) ; audit v1.2.1 §7 ; registre §3 (ARB-01, ARB-05, ARB-06, ARB-08, ARB-09) | Prérequis de P0 contradictoires : le dossier et l'audit exigent ARB-05, ARB-06 et ARB-09 avant P0, le registre les rattache aux Lots 1 et 1b ; ARB-01 « bloque P0 » alors qu'il doit être arbitré « à l'issue de P0 » ; ARB-08 bloque P0 (PT-07) sans figurer dans les prérequis | Le propriétaire choisit un scénario de séquencement (RÉF-5) ; aligner ensuite le dossier §16.2, l'audit §7 et la colonne « Bloque » du registre sur ce choix | Majeure | Décision GO P0 |
| ED-02 | Dossier v1.2.1 §16.2 (colonne « Prérequis ») | 18 dépendances du registre absentes (§10.2 ; 14 en v1.3, EV-15) | Reporter dans chaque lot les ARB et R de la colonne « Bloque » du registre | Majeure | Lots 0 à 13 |
| ED-03 | Matrice des décisions v1.2.1, ligne D2 ; matrice de déploiement v1.2.1, RD3 | Plages périmées « DEP-01 à DEP-12 » et « BKP-01 à BKP-06 » (séries réelles : DEP-01 à DEP-13, BKP-01 à BKP-07) | Remplacer par les plages complètes | Mineure | — |
| ED-04 | Registre v1.2.1 §4 (PT-01, PT-02, PT-03, PT-10, PT-13) | PostgreSQL candidat (ARB-01 A3) mais absent des protocoles PT-01 à PT-03 et PT-10 ; repli de PT-01 « Option A2 (SQLite seul) » qui présuppose un choix ; critères de PT-13 incomplets (contraintes et déclencheurs, recherche, exploitation, fonctionnement local, complexité de maintenance) | Rendre les protocoles indépendants du moteur (un passage par moteur candidat) ; remplacer le repli de PT-01 par « résultat versé au comparatif PT-13 » ; compléter les critères de PT-13 (plan directeur §8.3) | Majeure | P0 |
| ED-05 | Dossier v1.2.1 §13.3 (T3) | L'unité de travail n'est décrite que pour D1 et SQLite ; aucun adaptateur PostgreSQL ; mention « prototype Lot 0 » au lieu de P0 | Décrire l'unité de travail par moteur candidat ; corriger « Lot 0 » en « P0 » | Mineure | — |
| ED-06 | Registre v1.2.1 §4 | Aucun prototype pour l'ordonnanceur (tâches planifiées, rattrapage), la restauration croisée A ↔ B (DEP-09) et la chaîne de clés de chiffrement | Compléments PT-14, PT-15, PT-17 proposés, **facultatifs** (A-PRO-17) ; en cas de refus, traitement alternatif ou risque explicitement ouvert (plan directeur §8.4) | Majeure | Aucun blocage automatique ; risque traité ou accepté par le propriétaire avant la validation de l'architecture |
| ED-07 | Registre v1.2.1 PT-13 ; dossier §19.2 RG7 | La concurrence est mesurée « en caisse » sans scénario de stock (FEFO, réservations logiques, dernier lot disponible) | Intégrer un scénario de concurrence sur le stock au protocole de PT-13 (A-TEC-01) ; PT-16 en approfondissement facultatif | Majeure | P0 |
| ED-08 | Audit v1.2.1 §1 ; dossier v1.2.1 §1 | « 9 tables définies dans deux documents » alors que 10 noms le sont (`prescriptions` est traitée comme un remplacement) | Préciser « 10 noms, dont 1 remplacement documenté (`prescriptions`) » | Mineure | — |
| ED-09 | Dossier v1.2.1 §7.4 (M15, M18) | Le nom d'origine `coverage_rules` est cité entre accents graves dans les cellules, ce qui donne 43 et 33 noms pour des totaux de 42 et 32 | Citer le nom d'origine sans mise en forme de code, ou en note | Mineure | — |
| ED-10 | EXT06 v1.1 (tests ANN) | Identifiant « ANN-10 **[v1.1]** » : le marqueur dans la cellule d'identifiant gêne la traçabilité automatique | Déplacer le marqueur dans une autre colonne lors d'une révision d'EXT06 | Mineure | — |
| ED-11 | Audit v1.2.1 §3 (INC-10, INC-23, INC-28) | Clôtures conditionnelles comptées comme résolues | Ajouter la mention « résolution conditionnelle » et le point dont elle dépend | Mineure | — |
| ED-12 | Matrice des décisions v1.2.1, ligne D2 | La dépendance à R10 (localisation des données de santé) n'est pas signalée alors qu'elle peut restreindre les options d'hébergement d'ARB-01 | Ajouter R10 dans les dépendances de D2 et d'ARB-01 | Majeure | Choix ARB-01 |
| ED-13 | Audit v1.2.1, ligne INC-01 et §7 | Échéance du renommage `coverage_rules` : « avant le Lot 1 » dans INC-01, « Lots 2 et 3 » dans le §7 | Retenir une échéance unique : avant toute création de table (au plus tard Lot 0 pour le schéma par lot PD-05) | Mineure | Lot 1 |
| ED-14 | Matrice des décisions v1.2.1 (D72, D78, D83) | Notes de renvoi v1.2.1 ajoutées à des décisions arbitrées | Les isoler dans une colonne « Notes » distincte du texte arbitré | Mineure | — |
| ED-15 | EXT02 §12.2 ; dossier v1.2.1 §15.1 | Tests d'EXT02 non numérotés | Numéroter lors de la validation d'EXT02 (préfixes libres à vérifier) | Mineure | Lots 10b, 11b |
| ED-16 | Registre v1.2.1 §4 | Les PT n'ont ni statut, ni environnement, ni preuve attendue, ni responsable | Ajouter ces colonnes (plan directeur §8.2 propose le contenu) | Majeure | Pilotage ; **ne bloque pas P0** (éléments portés par chaque protocole approuvé, C-04 ; EV-18) |
| ED-17 | Registre v1.2.1 (toutes séries) | Aucun responsable interne ; conditions de clôture génériques | Colonnes « Responsable » et « Critère de clôture » | Majeure | Pilotage |
| ED-18 | Inventaire documentaire | `00_JOURNAL_DU_PROJET.md` non fourni (PD-02) ; noms `02_…` et `03_…` cités par l'archive EXT03 non fournis sous ces noms ; `CONCEPTION_PHARMOS_v1.md` sans champ « Version » | Fournir ou déclarer obsolète le journal ; documenter la correspondance des noms d'archive ; ajouter la version dans une future révision du dossier v1.1 (ou une note de correspondance) | Mineure | — |

---

## 12. Risques critiques

| ID | Risque | Probabilité | Impact | Origine | Mitigation | Suivi |
|---|---|---|---|---|---|---|
| RC-01 | Choix du moteur sans preuve mesurée (PostgreSQL non prototypé) | Moyenne | Critique | ED-04, INC-39 | Protocoles indépendants du moteur ; PT-13 complet ; arbitrage du propriétaire sur rapport | A-TEC-01, A-TEC-13 |
| RC-02 | Divergence fonctionnelle SaaS / On-Premise | Moyenne | Critique | Double mode | PT-11 (suite de conformité, CI double) ; DEP-01, DEP-04, DEP-06 | A-TEC-11 |
| RC-03 | Fuite de données entre pharmacies hébergées sur un même serveur | Faible à moyenne | Critique | D3 (serveur partagé) | PT-05, DEP-13 ; revue de sécurité | A-TEC-05 |
| RC-04 | Perte de données chiffrées par perte de clé | Moyenne | Critique | S-01 | Politique de gestion des clés avant Lot 0 | A-SEC-01 |
| RC-05 | Compromission de la clé de signature APEX (mises à jour, licences, référentiels) | Faible | Critique | PT-12, INC-27 | Garde hors ligne, double contrôle, révocation | A-SEC-01 |
| RC-06 | Accès éditeur aux données de santé sans cadre | Moyenne | Critique | INC-13, R52 | ARB-09 + avis juridique avant Lot 1 | A-PRO-04, A-JUR-03 |
| RC-07 | Hébergement non conforme des données de santé (localisation, agrément) | Inconnue | Critique | R10, R1 | Avis INPDP / juriste **avant** l'arbitrage d'ARB-01 | A-JUR-01 |
| RC-08 | Développement lancé sur des décisions non arbitrées (70) | Élevée | Élevé | Matrice | Prérequis par lot alignés (ED-02) ; revue de passage par phase | A-DOC-02 |
| RC-09 | Migrations de rattrapage sur N bases (INC-01, INC-06, ARB-14 tranchés trop tard) | Moyenne | Élevé | D3 | Tranchés avant le Lot 1 | A-PRO-03, A-PRO-12 |
| RC-10 | Perte de données en installation locale | Moyenne | Critique | Mode B, R55 | Sauvegarde, second support, contrat | A-JUR-06, PT-09 |
| RC-11 | Tiers payant activé sur des informations non officielles | Moyenne | Élevé | INC-35, R23, R31 | Conventions inactives ; activation sur source officielle | A-JUR-04 |
| RC-12 | Présentation de contrôles documentaires comme tests | Faible | Élevé | Qualité | Distinction spécifié / préparé / exécuté / réussi / archivé (§9.3) | A-DOC-07 |

---

## 13. Recommandations

Toutes les recommandations ci-dessous sont **[RECO]** : elles ne valent pas décision et doivent être acceptées ou refusées par
le propriétaire.

| ID | Recommandation | Justification | Liée à |
|---|---|---|---|
| RECO-01 | Statuer sur le séquencement de P0 (RÉF-5) avant toute autorisation | Lever ED-01 | ED-01 |
| RECO-02 | Fixer la liste des moteurs candidats d'ARB-01 avant PT-13 ; si PostgreSQL est retenu comme candidat, l'inclure dans PT-01 à PT-04, PT-09, PT-10 et PT-13 | Comparaison équitable | ED-04, RC-01 |
| RECO-03 | Obtenir un premier avis juridique sur R10 (localisation et hébergement des données de santé) **avant** l'arbitrage d'ARB-01 | Une contrainte de localisation peut éliminer des options | ED-12, RC-07 |
| RECO-04 | Proposer au propriétaire, à titre **facultatif**, des prototypes complémentaires pour l'ordonnanceur, la restauration croisée A ↔ B et la chaîne de clés (PT-14, PT-15, PT-17) ; intégrer la concurrence sur le stock au protocole de PT-13 | Couverture des risques de divergence | ED-06, ED-07 |
| RECO-05 | Établir une politique de gestion des clés (chiffrement applicatif et signature) avant le Lot 0 | RC-04, RC-05 | S-01 |
| RECO-06 | Réaliser une analyse de menaces formelle avant la validation de l'architecture (phase D) | S-06 | — |
| RECO-07 | Reformuler l'immuabilité du Super Admin en mode B en termes de détection (intégrité signée, journal) | INC-14, S-03 | ARB-05 |
| RECO-08 | Compléter le registre : responsable, critère de clôture, statut et preuve pour chaque point et prototype | ED-16, ED-17 | — |
| RECO-09 | Aligner les prérequis du dossier §16.2 sur la colonne « Bloque » du registre | ED-02 | — |
| RECO-10 | Trancher INC-01, INC-06 (ARB-03) et l'ordre de validation des extensions (ARB-14) avant le Lot 1 | Éviter des migrations sur N bases | RC-09 |
| RECO-11 | Conduire P0 sur des prototypes jetables, isolés du dépôt applicatif, avec données fictives | Éviter qu'un prototype devienne du code de production sans revue | C-01, C-05 |
| RECO-12 | Archiver chaque preuve P0 (rapport, mesures, journaux, empreintes, version des outils) | Traçabilité des décisions | C-07 |
| RECO-13 | Corriger les écarts mineurs ED-03, ED-05, ED-08 à ED-11, ED-13 à ED-15, ED-18 lors de la prochaine version documentaire | Qualité documentaire | — |

---

## 14. Conclusion GO / NO-GO pour P0

### 14.1 Recommandation du comité

> Orientation : **RÉF-1** (texte identique dans les quatre livrables). En résumé : **GO P0 CONDITIONNEL** proposé par le comité,
> sous réserve de l'autorisation écrite du propriétaire et des conditions applicables ; **P0 n'est pas autorisé** et aucune
> condition n'est remplie. Cette orientation ne vaut autorisation ni du Lot 0, ni du développement, ni d'un pilote, ni d'une
> commercialisation.

**Motifs** : la conception est cohérente et traçable sur les décisions, les points ouverts et les contradictions ; D3 est
stable ; aucun prototype ne dépend d'une contradiction bloquante non résolue (INC-06, INC-13, INC-14) ; INC-27 et INC-39 sont
précisément ceux que P0 doit éclairer. En revanche, le corpus contient une contradiction sur les prérequis de P0 (ED-01), des
protocoles incomplets ou biaisés (ED-04, ED-06, ED-07, ED-16) et une dépendance juridique non traitée pour le choix du moteur
(ED-12) : un GO inconditionnel n'est pas justifié.

### 14.2 Conditions du GO P0

Les conditions C-01 à C-08 sont définies **une seule fois**, en RÉF-2 (objet, actions, responsable, statut, dépendances, preuve
attendue, effet). Le tableau v1.3, dont les formulations différaient de celles du plan et du tableau de synthèse, est remplacé
(EV-11). **Aucune condition n'est remplie.**

### 14.3 Ce qui peut commencer immédiatement

Sans autorisation particulière, car purement documentaire ou organisationnel : correction des écarts ED-01 à ED-18 dans de
nouvelles versions ; rédaction des protocoles détaillés de P0 ; préparation des jeux de données fictifs (documentaires) ;
sollicitation des interlocuteurs juridiques (R1–R55, en priorité R10, R52, R55) ; constitution du dossier de sources juridiques
(PD-06) ; maquettes par format (PD-07) ; revue séquentielle d'EXT03 v1.1 (ARB-14) ; préparation des options de configuration de
référence (A-TEC-19). L'**exécution** d'un prototype n'en fait pas partie : elle exige C-01 et C-04 (RÉF-8).

### 14.4 Ce qui nécessite un arbitrage du propriétaire

Autorisation de P0 (C-01) ; scénario de séquencement (C-02) ; désignation des rôles de P0 (A-PRO-20) ; liste des moteurs
candidats (C-03) ; inscription de PT-13 (A-PRO-22) ; acceptation ou refus des compléments PT-14 à PT-17 (A-PRO-17) ; ARB-08
(pour PT-07 et le critère local de PT-13) ; interprétation du responsive (A-PRO-23) ;
ARB-01 (après P0) ; ARB-03 à ARB-07 et ARB-09 à ARB-14 (avant les lots concernés) ; validation de la proposition INC-27 ; 70
décisions non arbitrées, par lot ; validation séquentielle des extensions.

### 14.5 Ce qui nécessite une validation juridique

R1 à R55, en priorité : R10 (hébergement, avant ARB-01), R52 et R53 (accès éditeur, suppression, avant Lot 1), R55
(responsabilités en mode B, avant Lot 1b), R16 et R22 (OCR, LLM), R23 et R31 (tiers payant, avant Lot 7b), R44 et R46 (fiscalité,
comptabilité), R47 et R54 (services). Contrats (PD-08).

### 14.6 Ce qui nécessite une preuve technique

PT-01 à PT-13 (protocoles complétés selon ED-04 et ED-07) ; PT-14 à PT-17 seulement s'ils sont acceptés (ED-06) ; politique de gestion des clés (S-01) ; analyse de menaces (S-06) ;
protection réellement atteignable de l'immuabilité en mode local (S-03).

### 14.7 Ce qui reste interdit à ce stade

Liste unique : **RÉF-8**. Elle complète la liste v1.3 par l'exécution de tout prototype avant C-01 et C-04 et par l'arbitrage
d'ARB-01 avant le rapport PT-13 et l'avis R10.

---

## 15. Validations restant à obtenir

| Catégorie | Éléments | Nombre |
|---|---|---|
| Arbitrages nouveaux | ARB-01, ARB-03 à ARB-14 | 13 |
| Décisions non arbitrées | 63 proposées, 5 à réexaminer, 2 caduques à confirmer comme telles | 70 |
| Contradictions non résolues | 19 PROPOSÉE, 6 OUVERTE (dont 5 bloquantes au total, INC-27 et INC-39 inclus) | 25 |
| Points réglementaires | R1 à R55 | 55 |
| Points documentaires | PD-01 à PD-08 (PD-03 partiellement traité) | 8 |
| Prototypes | PT-01 à PT-13 (principaux) ; PT-14 à PT-17 proposés, **facultatifs** (A-PRO-17) | 13 (+ 4 facultatifs) |
| Extensions | EXT03 v1.1, EXT04, EXT05 (séquentiel, ARB-14) ; EXT02 (décisions D17–D26) ; EXT06 v1.1 (validée dans son principe) | 5 |
| Écarts documentaires | ED-01 à ED-18 | 18 |
| Écarts des livrables v1.3 | EV-01 à EV-18 (corrigés par la v1.3.1) | 18 |
| Dépendances incertaines | DI-01 à DI-07 (à confirmer) | 7 |
| Actions du registre | Actions actives du registre v1.3.1 | 71 |

---

## 16. Audit correctif v1.3.1 — écarts propres aux livrables v1.3

Écarts **démontrés** entre les quatre livrables v1.3 (et non dans les sources v1.2.1, traitées par ED-01 à ED-18). Chaque correction est détaillée dans `JOURNAL_CORRECTIONS_PHARMOS_v1.3.1.md`.

| ID | Livrable(s) v1.3 et section | Écart démontré | Correction v1.3.1 | Référence (mission v1.3.1) |
|---|---|---|---|---|
| EV-01 | Registre v1.3 A-TEC-17 (dépendances « A-TEC-04 à A-TEC-16 ») | La préparation du dossier de validation de l'architecture dépendait de A-TEC-16 (compléments PT-14 à PT-17), qui n'existe que si le propriétaire accepte ces compléments : un refus bloquait la validation de l'architecture | A-TEC-17 dépend de A-TEC-16 « pour les compléments acceptés uniquement » ; A-TEC-16 et A-PRO-17 précisent qu'un refus ne bloque ni PT-13 ni la validation | Correction 1 |
| EV-02 | Plan v1.3 §10 (preuves PT-14, PT-15, PT-16, PT-17) ; §14 RC-04 (« PT-17 ») | Les compléments facultatifs figuraient comme preuves obligatoires de la validation de l'architecture | Preuves conditionnelles « si accepté » ; traitement alternatif ou risque explicitement ouvert en cas de refus (plan §8.4, §10) | Correction 1 |
| EV-03 | Plan v1.3 §8.2 (PT-13) et §8.3 ; registre v1.3 A-TEC-13 ; audit v1.3 annexe B.1 ; tableau v1.3 §4 | Quatre listes différentes des prérequis de PT-13 : avis R10 « souhaitable » (plan) alors qu'il conditionne ARB-01 ; PT-07 nécessaire au critère « fonctionnement local » mais absent de A-TEC-13 ; compléments cités dans le plan, absents du registre | Liste unique RÉF-4, reprise à l'identique dans les quatre livrables ; critères local et juridique rattachés ; compléments conditionnels | Correction 2 |
| EV-04 | Plan v1.3 §8.3 ; audit v1.3 ED-07 et RECO-04 ; registre v1.3 A-TEC-01 | Scénario de concurrence sur le stock rattaché tantôt à PT-13, tantôt à PT-16 (facultatif), tantôt « au programme P0 » | Scénario intégré au protocole de PT-13 (critère « écritures concurrentes ») par A-TEC-01 ; PT-16 n'est qu'un approfondissement facultatif | Correction 2 |
| EV-05 | Plan v1.3 §12.1 (J-B1 « arbitrages nécessaires à P0 (C-01 à C-03, ARB-08) ») ; registre v1.3 A-PRO-06 (P0) ; tableau v1.3 §1 | ARB-08 présenté comme prérequis de P0 dans le plan, limité à PT-07 dans la condition C-06 et dans le tableau | RÉF-3 (groupes G1, G2, G3) ; ARB-08 requis pour PT-07 et le critère local de PT-13 ; sous S1 seulement, pour tout P0 (lecture littérale du registre v1.2.1) | Correction 3 |
| EV-06 | Audit v1.3 annexe B.1 (prérequis « Aucun » pour PT-05, PT-06, PT-11) et B.3 (S2, S3) ; plan v1.3 §8.2 | PT-05, PT-06 et PT-11 présentés comme indépendants du moteur, alors que leurs mesures alimentent PT-13 pour chaque candidat et que la suite de conformité inclut l'adaptateur de base ; A-TEC-01 ne les incluait pas | Classés en G2 (dépendants du moteur), avec dépendances incertaines signalées (DI-02, DI-03) ; A-TEC-01 étendue à PT-05, PT-06, PT-11 | Correction 3 |
| EV-07 | Registre v1.3 A-TEC-02 (dépendance globale « A-TEC-01 ») | Aucun protocole, même pour PT-08 ou PT-12, ne pouvait être approuvé avant la liste des moteurs (A-TEC-01 dépend de A-PRO-05) : contradiction avec la vague 1 du scénario S2 | A-TEC-02 dépend de A-TEC-01 pour G2 et PT-13 uniquement, de A-PRO-06 pour PT-07, de A-TEC-03 pour les protocoles utilisant les jeux de données | Corrections 3 et 5 (§5) |
| EV-08 | Registre v1.3 A-PRO-20 (P2) ; plan v1.3 §9 (CA-5) ; C-04 | L'approbation des protocoles et la relecture par un second intervenant sont exigées avant toute exécution, mais la désignation des rôles était classée P2 | Scission : A-PRO-20 (rôles nécessaires à P0, P0-BLOQUANT) et A-PRO-24 (autres rôles, P2) | Correction 4 |
| EV-09 | Registre v1.3 (légende des priorités et colonne « Priorité ») | « P0 = nécessaire au GO P0 ou réalisable immédiatement » mêlait dépendance et opportunité ; A-PRO-21 (prérequis du Lot 0) en P2 ; A-PRO-04, A-PRO-07, A-PRO-08 en P1 alors que le dossier v1.2.1 les exige avant P0 sous lecture littérale | Échelle RÉF-10 (P0-BLOQUANT, P0-PARALLÈLE, P1, P2), colonne « Effet sur P0 » et tableau de correspondance avant / après (registre §7) | Correction 4 |
| EV-10 | Registre v1.3 A-PRO-19 | Cinq réserves de nature différente dans une seule décision ; la date de D3 présentée comme une réserve à « confirmer ou infirmer », au risque d'une réouverture de D3 | Scission en A-DOC-11, A-DOC-12, A-DOC-13 (documentaires, sans décision nouvelle), A-PRO-22 et A-PRO-23 (propriétaire) ; D3 explicitement non rouverte | Correction 5 |
| EV-11 | Audit v1.3 §14.2 ; registre v1.3 §6 ; tableau v1.3 §7 | C-05 rattachée à la seule spécification des jeux de données (A-TEC-03) alors que la règle relève de l'autorisation (A-PRO-01) ; C-04 sans l'action de désignation de l'approbateur ; l'alignement documentaire A-DOC-01 présenté comme une condition de démarrage | RÉF-2 : objet, actions, responsable, statut, dépendances, preuve et effet de chaque condition, texte identique dans les quatre livrables | Correction 6 |
| EV-12 | Tableau v1.3 §7 (états « Applicable », « À appliquer ») ; plan v1.3 (aucune mention du statut du GO P0) | États de conditions ambigus, lisibles comme des conditions satisfaites ; le plan ne rappelait pas que P0 n'est pas autorisé | RÉF-1 dans les quatre livrables : orientation proposée, aucune condition remplie, P0 non autorisé, liste de ce que le GO P0 conditionnel ne signifie pas | Correction 7 |
| EV-13 | Plan v1.3 §12.1 (J-B1, J-B2) | J-B1 identique pour tous les scénarios ; J-B2 (incluant ARB-01) présenté avant J-C alors qu'ARB-01 ne peut être arbitré qu'après le rapport PT-13 | RÉF-6 : J-B1 différencié par scénario ; J-B2 placé après J-C | §5 (jalons) |
| EV-14 | Plan v1.3 §12.2 ; tableau v1.3 (absent) | Chemin critique incomplet : ni la liste des moteurs, ni les protocoles, ni la désignation des rôles, ni la branche ARB-08 → PT-07, ni INC-27 et la politique de clés | RÉF-7, identique dans les quatre livrables ; compléments PT-14 à PT-17 explicitement hors du chemin critique | §5 |
| EV-15 | Audit v1.3 §5.2, §10.2, ED-02 ; registre v1.3 A-DOC-02 | 14 dépendances annoncées absentes du dossier §16.2 ; le recomptage en trouve 18 : R14, R15, R19 (Lot 5b) et R17 (Lot 10) avaient été omis ; « 5 incohérences R » au lieu de 9 | §10.2 complété (lignes 15 à 18) ; ED-02 et A-DOC-02 portés à 18 | §6 |
| EV-16 | Plan v1.3 §11 (prérequis des Lots 0, 5b, 10) | Les rapports de prototypes, exigés par le registre v1.2.1 §4 avant le Lot 0, et les points R14, R15, R19, R17 manquaient aux prérequis consolidés | Prérequis complétés dans le plan v1.3.1 §11 | §5 |
| EV-17 | Registre v1.3 (colonne « Dépendances ») | Dépendance circulaire A-PRO-08 ↔ A-JUR-10 ; A-PRO-15 sans A-PRO-05 ; A-PRO-21 sans A-PRO-14 ni A-SEC-01 alors que le plan §10 les exige ; A-TEC-09 sans le moteur du mode local ; A-DOC-10 entièrement bloquée par ARB-14 et ARB-03 alors que le schéma du Lot 0 n'en dépend pas ; exécutions G2 sans A-TEC-03 | Dépendances corrigées (registre v1.3.1) ; contrôle automatique : aucun cycle (§17.2) | §5 |
| EV-18 | Tableau v1.3 §1 (ED-16 « bloque P0 : Oui ») | ED-16 (statut, environnement, preuve des PT absents du registre v1.2.1) présenté comme bloquant P0, alors que ces éléments figurent dans chaque protocole approuvé (C-04) | ED-16 reclassé « pilotage, ne bloque pas P0 » ; A-DOC-04 en P0-PARALLÈLE | Correction 4 |

Les formulations correctes de la v1.3 ont été conservées ; aucune décision n'a été ajoutée.

---

## 17. Matrice des dépendances corrigée

### 17.1 Dépendances des actions actives (registre v1.3.1)

« Requis par » est calculé à partir des colonnes « Dépendances » du registre v1.3.1. Une dépendance marquée « (conditionnelle) » n'existe que si la condition indiquée dans le registre est remplie.

| Action | Priorité | Dépend de | Requis par |
|---|---|---|---|
| A-DOC-01 | P0-PARALLÈLE | A-PRO-02 | — |
| A-DOC-02 | P1 | — | — |
| A-DOC-03 | P2 | — | — |
| A-DOC-04 | P0-PARALLÈLE | — | — |
| A-DOC-05 | P2 | — | — |
| A-DOC-06 | P1 | — | — |
| A-DOC-07 | P1 | — | — |
| A-DOC-08 | P2 | — | — |
| A-DOC-09 | P2 | A-PRO-03 | — |
| A-DOC-10 | P1 | A-DOC-11, A-PRO-13 (conditionnelle), A-PRO-03 (conditionnelle), A-PRO-12 (conditionnelle) | — |
| A-DOC-11 | P1 | — | A-DOC-10 |
| A-DOC-12 | P2 | — | — |
| A-DOC-13 | P2 | — | — |
| A-PRO-01 | P0-BLOQUANT | A-PRO-02 | A-TEC-04, A-TEC-05, A-TEC-06, A-TEC-07, A-TEC-08, A-TEC-09, A-TEC-10, A-TEC-11, A-TEC-12, A-TEC-14, A-TEC-15, A-TEC-16 |
| A-PRO-02 | P0-BLOQUANT | — | A-DOC-01, A-PRO-01 |
| A-PRO-03 | P2 | — | A-DOC-09, A-DOC-10 |
| A-PRO-04 | P0-PARALLÈLE | A-JUR-03 (conditionnelle) | A-JUR-10 |
| A-PRO-05 | P0-PARALLÈLE | — | A-PRO-15, A-TEC-01, A-TEC-02, A-TEC-09 |
| A-PRO-06 | P0-PARALLÈLE | — | A-TEC-02, A-TEC-09 |
| A-PRO-07 | P0-PARALLÈLE | A-JUR-06 (conditionnelle) | A-JUR-10, A-SEC-02 |
| A-PRO-08 | P0-PARALLÈLE | — | A-JUR-10 |
| A-PRO-09 | P2 | — | — |
| A-PRO-10 | P2 | — | — |
| A-PRO-11 | P2 | A-JUR-05 | — |
| A-PRO-12 | P2 | — | A-DOC-10 |
| A-PRO-13 | P1 | — | A-DOC-10 |
| A-PRO-14 | P1 | A-TEC-12 (conditionnelle) | A-PRO-21 |
| A-PRO-15 | P1 | A-TEC-13, A-JUR-01, A-PRO-05 | A-PRO-21 |
| A-PRO-16 | P2 | — | — |
| A-PRO-17 | P0-PARALLÈLE | — | A-TEC-16 |
| A-PRO-18 | P2 | — | — |
| A-PRO-20 | P0-BLOQUANT | — | A-PRO-24, A-TEC-02 |
| A-PRO-21 | P1 | A-PRO-15, A-PRO-14, A-TEC-17, A-SEC-01 | — |
| A-PRO-22 | P0-PARALLÈLE | — | A-TEC-13 |
| A-PRO-23 | P1 | — | — |
| A-PRO-24 | P2 | A-PRO-20 | — |
| A-TEC-01 | P0-PARALLÈLE | A-PRO-05 | A-TEC-02, A-TEC-04, A-TEC-05, A-TEC-06, A-TEC-07, A-TEC-08, A-TEC-11, A-TEC-14, A-TEC-15 |
| A-TEC-02 | P0-BLOQUANT | A-PRO-20, A-TEC-01 (conditionnelle), A-TEC-03 (conditionnelle), A-PRO-05 (conditionnelle), A-PRO-06 (conditionnelle) | A-TEC-04, A-TEC-05, A-TEC-06, A-TEC-07, A-TEC-08, A-TEC-09, A-TEC-10, A-TEC-11, A-TEC-12, A-TEC-13, A-TEC-14, A-TEC-15, A-TEC-16 |
| A-TEC-03 | P0-PARALLÈLE | — | A-TEC-02, A-TEC-04, A-TEC-05, A-TEC-06, A-TEC-07, A-TEC-08, A-TEC-14, A-TEC-15 |
| A-TEC-04 | P1 | A-PRO-01, A-TEC-02, A-TEC-01, A-TEC-03 | A-TEC-13, A-TEC-17 |
| A-TEC-05 | P1 | A-PRO-01, A-TEC-02, A-TEC-01, A-TEC-03 | A-TEC-13 |
| A-TEC-06 | P1 | A-PRO-01, A-TEC-02, A-TEC-01, A-TEC-03 | A-TEC-13 |
| A-TEC-07 | P1 | A-PRO-01, A-TEC-02, A-TEC-01, A-TEC-03 | A-TEC-13 |
| A-TEC-08 | P1 | A-PRO-01, A-TEC-02, A-TEC-01, A-TEC-03 | A-TEC-13, A-TEC-17 |
| A-TEC-09 | P1 | A-PRO-01, A-PRO-05, A-PRO-06, A-TEC-02 | A-TEC-13, A-TEC-17 |
| A-TEC-10 | P1 | A-PRO-01, A-TEC-02 | A-TEC-17 |
| A-TEC-11 | P1 | A-PRO-01, A-TEC-02, A-TEC-01 | — |
| A-TEC-12 | P1 | A-PRO-01, A-TEC-02 | A-PRO-14 |
| A-TEC-13 | P1 | A-PRO-22, A-TEC-02, A-TEC-04, A-TEC-05, A-TEC-06, A-TEC-07, A-TEC-08, A-TEC-14, A-TEC-15, A-TEC-09, A-JUR-01, A-TEC-16 (conditionnelle) | A-PRO-15 |
| A-TEC-14 | P1 | A-PRO-01, A-TEC-02, A-TEC-01, A-TEC-03 | A-TEC-13 |
| A-TEC-15 | P1 | A-PRO-01, A-TEC-02, A-TEC-01, A-TEC-03 | A-TEC-13, A-TEC-17 |
| A-TEC-16 | P1 | A-PRO-17 (conditionnelle), A-PRO-01, A-TEC-02 | A-TEC-13, A-TEC-17 |
| A-TEC-17 | P1 | A-TEC-04, A-TEC-08, A-TEC-10, A-TEC-15, A-TEC-18, A-TEC-09 (conditionnelle), A-TEC-16 (conditionnelle), A-SEC-01, A-SEC-05 | A-PRO-21 |
| A-TEC-18 | P0-PARALLÈLE | — | A-TEC-17 |
| A-TEC-19 | P0-PARALLÈLE | — | — |
| A-JUR-01 | P0-PARALLÈLE | — | A-PRO-15, A-TEC-13 |
| A-JUR-02 | P0-PARALLÈLE | — | — |
| A-JUR-03 | P1 | — | A-PRO-04 |
| A-JUR-04 | P2 | — | — |
| A-JUR-05 | P1 | — | A-PRO-11 |
| A-JUR-06 | P1 | — | A-PRO-07 |
| A-JUR-07 | P1 | — | — |
| A-JUR-08 | P2 | — | — |
| A-JUR-09 | P1 | — | A-SEC-04 |
| A-JUR-10 | P2 | A-PRO-07, A-PRO-08, A-PRO-04 | — |
| A-JUR-11 | P2 | — | — |
| A-SEC-01 | P1 | — | A-PRO-21, A-TEC-17 |
| A-SEC-02 | P1 | A-PRO-07 (conditionnelle) | — |
| A-SEC-03 | P2 | — | — |
| A-SEC-04 | P1 | A-JUR-09 | — |
| A-SEC-05 | P1 | — | A-TEC-17 |

### 17.2 Contrôles automatiques des dépendances (mission v1.3.1 §5)

| Contrôle | Résultat |
|---|---|
| Dépendances circulaires | Aucune (la v1.3 contenait A-PRO-08 ↔ A-JUR-10, EV-17) |
| Dépendance à une action facultative sans condition d'acceptation | Aucune : seules A-TEC-13 et A-TEC-17 citent A-TEC-16, avec la mention « acceptés uniquement » |
| Jalon dépendant d'un résultat postérieur | Aucun : J-B2 (ARB-01) est placé après J-C (rapports P0) ; J-D après J-B2 ; J-E après J-D (RÉF-6) |
| Action P0-BLOQUANT exigeant un arbitrage non nécessaire | Aucune : A-TEC-02 ne dépend de A-PRO-05, A-PRO-06, A-TEC-01 et A-TEC-03 que pour les protocoles concernés |
| Prototype sans protocole approuvé | À corriger : les 12 actions d'exécution dépendent de A-PRO-01 et de A-TEC-02 ; A-TEC-16 aussi |
| Arbitrage du moteur avant PT-13 | Impossible : A-PRO-15 dépend de A-TEC-13 (rapport PT-13) et de C-08 |
| R10 avant la décision d'hébergement | Garanti : A-PRO-15 (ARB-01) dépend de A-JUR-01 ; le critère juridique de PT-13 en dépend aussi |
| Validation de l'architecture sans preuves | Impossible : A-PRO-21 dépend de A-PRO-15, A-PRO-14, A-TEC-17 (rapports P0 et preuves archivées) et A-SEC-01 ; A-SEC-05 via A-TEC-17 ; preuves juridiques via A-PRO-15 (A-JUR-01) |

### 17.3 Dépendances incertaines (signalées, non supprimées)

| ID | Question | Source | Traitement provisoire v1.3.1 | Autorité |
|---|---|---|---|---|
| DI-01 | Le rapport PT-07 (critère « fonctionnement local » de PT-13) doit-il précéder la validation de l'architecture (J-D), ou seulement le Lot 0 ? | Registre v1.2.1 §4 : rapport de chaque prototype avant le Lot 0 ; exigence N3 (mode local autonome) | Requis avant le Lot 0 ; exigé avant J-D à titre de [RECO] | Propriétaire |
| DI-02 | PT-11 peut-il être exécuté partiellement (adaptateurs autres que la base) avant la liste des moteurs ? | Plan v1.3 §8.2 (« squelette de ports ») | Classé en G2 ; exécution partielle envisageable | Architecte ; propriétaire (autorisation) |
| DI-03 | PT-05 et PT-06 admettent-ils une version minimale indépendante du moteur ? | Registre v1.2.1 PT-05, PT-06 ; PT-13 (mesures par candidat) | Classés en G2 ; version minimale envisageable | Architecte |
| DI-04 | Un complément accepté (PT-14, PT-16) doit-il être terminé avant la clôture de PT-13, ou peut-il suivre ? | Plan v1.3 §8.2 (« s'ils sont acceptés ») | Intégré avant la clôture de PT-13, sauf décision contraire dans A-PRO-17 | Propriétaire |
| DI-05 | L'interprétation du responsive (A-PRO-23) doit-elle précéder le Lot 0 (design system) ou le premier lot à écrans (Lot 1) ? | Dossier v1.2.1 §14.2, §14.4, §16.2 (Lot 0 : design system responsive) | Rattachée au Lot 0 à titre de [RECO] | Propriétaire |
| DI-06 | Les compléments PT-14 à PT-17 dépendent-ils de la configuration de référence (ARB-08) ? | Plan v1.3 §8.4 (non précisé) | Non spécifié : à préciser dans le protocole si le complément est accepté | Architecte |
| DI-07 | Échéance du renommage `coverage_rules` (INC-01) : avant le Lot 0 (schéma par lot) ou avant le Lot 1 ? | Audit v1.2.1, ligne INC-01 (« avant le Lot 1 ») et §7 (« Lots 2 et 3 ») ; ED-13 | Rattaché au Lot 0 à titre de [RECO] | Propriétaire |

---

## 18. Contrôle des quantités et identifiants

Chiffres recalculés sur les lignes des documents sources ; aucun chiffre n'est corrigé par déduction.

| Élément | Source de référence | Constaté | Annoncé par la mission v1.3.1 | Écart | Traitement |
|---|---|---|---|---|---|
| Décisions | Matrice v1.2.1 | 86 (D1–D86, continues : oui) | 86 | 0 | — |
| Décisions arbitrées | Matrice v1.2.1 | 16 (D3, D72–D86) | Voir ligne suivante | — | — |
| Arbitrages nouveaux (ARB) | Registre des points ouverts v1.2.1 §3 | **14 identifiants** (ARB-01 à ARB-14) : 13 ouverts, 1 fermé (ARB-02) | « 16 arbitrages nouveaux ARB-01 à ARB-14 » | **Écart de l'énoncé** : ARB-01 à ARB-14 compte 14 identifiants ; le nombre 16 correspond aux **décisions arbitrées** (D3, D72–D86) | Les deux chiffres sont conservés, chacun pour son objet ; aucune source n'est modifiée |
| Décisions proposées | Matrice v1.2.1 | 63 | 63 | 0 | — |
| Décisions à réexaminer | Matrice v1.2.1 | 5 (D2, D6, D10, D15, D37) | 5 | 0 | — |
| Décisions caduques | Matrice v1.2.1 | 2 (D61, D66) | 2 | 0 | — |
| Points réglementaires | Registre v1.2.1 §1 | 55 (R1–R55) | 55 | 0 | — |
| Points documentaires | Registre v1.2.1 §2 | 8 (PD-01–PD-08) | 8 | 0 | — |
| Prototypes principaux | Registre v1.2.1 §4 | 13 (PT-01–PT-13) | 13 | 0 | — |
| Prototypes complémentaires | Plan directeur v1.3 §8.4 | 4 proposés (PT-14–PT-17), non inscrits au registre v1.2.1 | 4 | 0 | Facultatifs (A-PRO-17) |
| Écarts documentaires (sources v1.2.1) | Audit v1.3 §11 | 18 (ED-01–ED-18) | 18 | 0 | ED-02 porte désormais sur 18 points au lieu de 14 (EV-15) |
| Écarts propres aux livrables v1.3 | Présent rapport §16 | 18 (EV-01–EV-18) | — | — | Nouveau en v1.3.1 |
| Actions du registre | Registre v1.3 → v1.3.1 | 65 → **71 actives** (− A-PRO-19, + 7) | 65, « sauf si les corrections justifient un nouveau total » | +6 | Justifié par les corrections 3 et 5 (registre §6, §7) |
| Contradictions | Audit v1.2.1 §3 | 41 | 41 | 0 | — |
| — résolues / non résolues | Audit v1.2.1 §3 | 16 / 25 | 16 / 25 | 0 | Dont 3 résolutions conditionnelles (INC-10, INC-23, INC-28 ; ED-11) |
| — bloquantes | Audit v1.2.1 §3 | 6 bloquantes, dont **5 non résolues** (INC-06, INC-13, INC-14, INC-27, INC-39) | « 5 bloquantes » | Précision | Le chiffre 5 désigne les bloquantes **non résolues** ; INC-17, bloquante, est résolue (D3) |
| Contradictions de classification | Audit v1.2.1 §3 | INC-13 reclassée OUVERTE en v1.2.1 sur instruction du propriétaire | — | — | A-DOC-12 (consignation, sans décision nouvelle) |
| Tables | Dossier v1.2.1 §7.4 | 244 (243 noms distincts ; `coverage_rules` = 2 tables) ; 248 avec les 4 nouvelles | 244 / 243 / 248 | 0 | Vérification reproductible : A-DOC-11 |
| Tests DEP / BKP / UPD | Matrice de déploiement v1.2.1 §8 | 13 / 7 / 7 | 12-13 / 6-7 / 6-7 | Renvois périmés seulement | ED-03 (A-DOC-03) |

---

## 19. Décisions réservées exclusivement au propriétaire (contrôle D)

Aucune de ces décisions n'est acquise. Chacune exige une décision écrite et datée du propriétaire.

| Action | Décision | Priorité | Requise avant |
|---|---|---|---|
| A-DOC-08 | Inventaire documentaire : fournir ou déclarer obsolète `00_JOURNAL_DU_PROJET.md` (PD-02) ; documenter la correspondance des noms `02_…`, `03_…` de l'archive EXT03 ; ajouter la version du dossier v1.1  | P2 | Aucun |
| A-DOC-10 | Produire le schéma consolidé par lot, au niveau **documentaire** uniquement (PD-05), à partir des décisions validées | P1 | Aucun |
| A-PRO-01 | Autoriser ou refuser P0 et en fixer le périmètre : prototypes jetables, isolés du dépôt applicatif, liste des prototypes autorisés, données exclusivement fictives (C-05), aucune réutilisation de code  | P0-BLOQUANT | Bloque l'exécution de tout prototype (C-01) |
| A-PRO-02 | Choisir le scénario de séquencement de P0 (S1 prudent, S2 rapide, S3 intermédiaire | P0-BLOQUANT | Bloque le démarrage (C-02) ; détermine les autres conditions de démarrage (RÉF-5) |
| A-PRO-03 | ARB-14 : ordre de validation des extensions ; puis validation séquentielle d'EXT03 v1.1, EXT04, EXT05 | P2 | Aucun |
| A-PRO-04 | ARB-09 : accès de l'éditeur aux données métier (INC-13) | P0-PARALLÈLE | S1 : bloque le démarrage de P0 · S3 : bloque G2, G3 et PT-13 · S2 : aucun effet sur P0 ; requise avant le Lot 1 (registre v1.2.1) |
| A-PRO-05 | Fixer la liste des moteurs candidats d'ARB-01 soumis à PT-13 | P0-PARALLÈLE | S1 : bloque le démarrage · S2 et S3 : bloque G2, G3 et PT-13 ; ne bloque pas G1 (PT-08, PT-12) |
| A-PRO-06 | ARB-08 : configuration matérielle et système de référence du mode B | P0-PARALLÈLE | Bloque PT-07 et le critère « fonctionnement local » de PT-13 ; S1 : bloque le démarrage (registre v1.2.1 : ARB-08 « bloque P0 ») ; rapport P |
| A-PRO-07 | ARB-05 : Super Admin en installation locale (INC-14) | P0-PARALLÈLE | S1 : bloque le démarrage de P0 · S3 : bloque G2, G3 et PT-13 · S2 : aucun effet sur P0 ; requise avant le Lot 1 (registre v1.2.1) |
| A-PRO-08 | ARB-06 : effet de la fin d'un abonnement, d'une licence ou d'un module (INC-38) | P0-PARALLÈLE | S1 : bloque le démarrage de P0 · S3 : bloque G2, G3 et PT-13 · S2 : aucun effet sur P0 ; requise avant le Lot 1 (registre v1.2.1) |
| A-PRO-09 | ARB-04 : procédure de récupération du compte Super Admin | P2 | Aucun |
| A-PRO-10 | ARB-07 : validation à distance en mode B hors de l'officine (INC-16) | P2 | Aucun |
| A-PRO-11 | ARB-11 (titulaire / administrateur) et ARB-12 (code d'accès initial) | P2 | Aucun |
| A-PRO-12 | ARB-03 : périmètre du tiers payant en v1 (INC-06) ; mise en cohérence de D6, D37, D44 | P2 | Aucun |
| A-PRO-13 | Valider le renommage `shift_coverage_rules` / `payer_coverage_rules` (INC-01) | P1 | Aucun |
| A-PRO-14 | Valider ou refuser la proposition de paquets de référentiels signés (INC-27, registre §3.2) | P1 | Aucun (PT-12 l'éclaire sans en dépendre) |
| A-PRO-15 | ARB-01 : choisir le moteur d'exécution et de base | P1 | Aucun sur le démarrage ; ne peut être arbitrée qu'après le rapport PT-13 (C-08) |
| A-PRO-16 | ARB-10 (second collaborateur) et ARB-13 (nom du client) | P2 | Aucun |
| A-PRO-17 | Accepter ou refuser, complément par complément, l'ajout des prototypes complémentaires PT-14 à PT-17 (plan directeur §8.4) ; en cas de refus, le traitement du risque suit le plan directeur §8.4 | P0-PARALLÈLE | Bloque uniquement A-TEC-16 ; un refus, total ou partiel, ne bloque ni PT-13 ni la validation de l'architecture |
| A-PRO-18 | Arbitrer les 70 décisions non arbitrées, par lot, avant le démarrage de chaque lot (dont confirmation de D61, D66 caduques) | P2 | Aucun |
| A-PRO-20 | Désigner nominativement les rôles nécessaires à P0 : approbateur des protocoles, relecteur (second intervenant, CA-5), responsable qualité, architecte, responsable sécurité | P0-BLOQUANT | Bloque l'approbation des protocoles (C-04), donc le démarrage |
| A-PRO-21 | Valider formellement l'architecture (phase D) | P1 | Aucun sur P0 ; prérequis du Lot 0 |
| A-PRO-22 | Confirmer par écrit l'inscription de PT-13 (comparatif des moteurs, ajouté par la v1.2.1) au programme P0 | P0-PARALLÈLE | Bloque PT-13 uniquement |
| A-PRO-23 | Trancher l'interprétation de l'exigence responsive : distinction entre une restriction d'usage décidée (dossier v1.2.1 §14.2) et une dégradation (§14.4) | P1 | Aucun |
| A-PRO-24 | Attribuer nominativement les autres rôles de gouvernance (plan directeur §13) | P2 | Aucun |
| A-TEC-02 | Rédiger les protocoles détaillés de P0 (objectif, environnement, données, scénario, critère chiffré, preuve) et les faire approuver | P0-BLOQUANT | Bloque l'exécution de chaque prototype (C-04) |
| A-JUR-10 | Faire rédiger les contrats : abonnement SaaS, licence locale, conditions d'intervention de l'éditeur, fin de licence (PD-08) | P2 | Aucun |
| A-SEC-01 | Rédiger la politique de gestion des clés : chiffrement applicatif (PAT, RH), signature APEX (mises à jour, licences, référentiels) ; génération, garde hors ligne, double contrôle, rotation, sauvegarde | P1 | Aucun sur P0 ; prérequis de la validation de l'architecture |
| A-SEC-03 | Évaluer le risque lié à l'identifiant public du Super Admin et proposer des mesures (S-04) | P2 | Aucun |
| A-SEC-04 | Établir la liste blanche des champs et agrégats transmissibles à un LLM, avec le seuil de petits effectifs (S-05) | P1 | Aucun |

S'y ajoutent : les 70 décisions non arbitrées (A-PRO-18) et la validation séquentielle des extensions EXT03 v1.1, EXT04, EXT05 (A-PRO-03). **D3 et D72 à D86 sont [DÉCIDÉ] et ne figurent pas dans cette liste.**

---

## 20. Points non résolubles documentairement (contrôle E)

| Nature | Points | Actions |
|---|---|---|
| Décision du propriétaire | Autorisation de P0, scénario, rôles, moteurs candidats, compléments PT-14 à PT-17, ARB-01 et ARB-03 à ARB-14, INC-01, INC-27, interprétation du responsive, inscription de PT-13, 70 décisions, validation de l'architecture | §19 |
| Avis juridique | R1 à R55 (aucun validé), en priorité R10 et R1 (avant ARB-01), R52, R53, R48, R37 (avant Lot 1), R55 (avant Lot 1b) ; notification de violation ; contrats | A-JUR-01 à A-JUR-11 |
| Preuve technique | PT-01 à PT-13 ; PT-14 à PT-17 s'ils sont acceptés ; gestion des clés (S-01) ; immuabilité en mode local (S-03) ; analyse de menaces (S-06) ; dépendances incertaines DI-02, DI-03, DI-06 | A-TEC-*, A-SEC-* |
| Source officielle | Statut de la convention CNAM (R23, INC-35), taux et listes (R31), textes INPDP (R1, R10), référentiels officiels (R43) ; dossier de sources PD-06 | A-JUR-02, A-JUR-04 |
| Validation métier | Configuration de référence du mode local (ARB-08) ; validation des extensions (ARB-14) ; responsive au comptoir (A-PRO-23) ; nom du client (ARB-13) | A-PRO-06, A-TEC-19, A-PRO-03, A-PRO-23, A-PRO-16 |
| Pièce documentaire manquante | `00_JOURNAL_DU_PROJET.md` ; noms `02_…`, `03_…` de l'archive EXT03 ; maquettes (PD-07) ; contrats (PD-08) | A-DOC-08, A-JUR-10 |

---


## Annexe B — Audit des prototypes P0 et de leurs dépendances

### B.1 Appréciation de chaque prototype (registre v1.2.1 §4)

| ID | Objet | Adéquation | Écart constaté | Prérequis réel |
|---|---|---|---|---|
| PT-01 | Unité de travail sur D1 (`batch`) | Pertinent si A1 reste candidat | Repli « Option A2 » présuppose un choix (ED-04) | Liste des candidats (C-03) |
| PT-02 | Unité de travail sur SQLite | Pertinent pour A1 et A2 | — | Idem |
| PT-03 | Contraintes, `RAISE(ABORT)`, déclencheurs | Pertinent | Limité à D1 et SQLite (ED-04) | Idem |
| PT-04 | Recalcul des métriques, génération comptable d'une journée | Pertinent | « Limite du moteur retenu » alors qu'aucun moteur n'est retenu : mesurer par candidat | Idem |
| PT-05 | Routage vers la base de la pharmacie | Pertinent ; conforme à D3 | Ajouter la tentative de forcer l'identifiant (couverte par DEP-13) | Moteurs candidats (G2, RÉF-3) [DI-03] |
| PT-06 | Migrations sur N bases | Pertinent | Préciser le retour arrière par base | Moteurs candidats (G2, RÉF-3) [DI-03] |
| PT-07 | Installateur local | Pertinent | Dépend de la configuration de référence | **ARB-08** |
| PT-08 | Horloge du serveur local | Pertinent | — | Aucun |
| PT-09 | Sauvegarde à chaud | Pertinent | À mesurer par moteur candidat | Liste des candidats |
| PT-10 | Recherche plein texte | Pertinent | FTS5 présumé ; équivalent PostgreSQL absent ; arabe à tester explicitement | Liste des candidats |
| PT-11 | Suite de conformité et CI double | Pertinent | Suppose un squelette de ports ; à cantonner au prototype | Moteurs candidats (G2, RÉF-3) : la suite inclut l'adaptateur de base [DI-02] |
| PT-12 | Signature des paquets | Pertinent | Garde de la clé non couverte (S-01) | Aucun |
| PT-13 | Comparatif des moteurs | Indispensable | Critères incomplets ; pas de scénario de stock (ED-04, ED-07) — corrigés par A-TEC-01 | Liste unique RÉF-4 |

### B.2 Nécessité réelle d'ARB-05, ARB-06 et ARB-09 avant P0

| Arbitrage | Objet | Prototype qui en dépend | Indispensable avant P0 ? | Classement |
|---|---|---|---|---|
| ARB-05 | Super Admin en installation locale | Aucun ; PT-07 et PT-12 produisent des éléments utiles à la décision | **Non démontré** | Décision pouvant être prise après démonstration ; requise avant les Lots 1 et 1b |
| ARB-06 | Effet de la fin d'une licence | Aucun ; PT-12 vérifie la signature d'une licence quel que soit son effet | **Non démontré** | Requise avant les Lots 1 et 1b |
| ARB-09 | Accès de l'éditeur aux données | Aucun | **Non démontré** | Requise avant le Lot 1 ; dépend aussi de R52 |
| ARB-08 | Configuration de référence du mode B | PT-07 ; mesures locales de PT-13 et DEP-12 | **Oui pour PT-07** et pour le critère « fonctionnement local » de PT-13 ; **non** pour G1 et G2 (RÉF-3) | Indispensable avant PT-07 |
| ARB-01 | Moteur | — | **Non** : P0 l'éclaire | Décision que P0 doit aider à prendre |
| Liste des candidats | Périmètre de PT-13 | PT-01 à PT-06, PT-09, PT-10, PT-11, PT-13 (G2 et PT-13, RÉF-3) | **Oui** pour ces prototypes ; **non** pour G1 (PT-08, PT-12) | Décision de cadrage du propriétaire |
| Autorisation de P0 | Périmètre, données, isolement | Tous | **Oui** | Décision du propriétaire |
| Rôles de P0 (A-PRO-20) | Approbateur et relecteur des protocoles | Tous (C-04, CA-5) | **Oui** | Décision du propriétaire (v1.3.1, EV-08) |

Le présent audit **ne tranche pas** : il constate que le dossier exige ces trois arbitrages avant P0 sans justification écrite,
et qu'aucun protocole ne les utilise.

### B.3 Scénarios de séquencement (choix réservé au propriétaire)

Les scénarios S1, S2 et S3 sont définis **une seule fois**, en RÉF-5, et tiennent compte de la distinction G1 / G2 / G3 (RÉF-3).
Le tableau v1.3 est remplacé : il classait PT-05, PT-06 et PT-11 parmi les prototypes indépendants du moteur et ne distinguait pas
l'effet d'ARB-08 selon les scénarios (EV-05, EV-06).

---

## Annexe A — Contrôle d'intégrité des sources (SHA-256)

Empreintes SHA-256 relevées **avant** la revue v1.3.1 et **après** la création des cinq livrables v1.3.1. Toute différence
signalerait une modification d'une source : **aucune n'est constatée**.

### A.1 Documents (33 fichiers, dont les quatre livrables v1.3 relus en lecture seule)

| Fichier | SHA-256 avant | Identique après |
|---|---|---|
| `docs/CONCEPTION_PHARMOS_v1.2.1.md` | `05b3361bd29922b048554b4b09442b66950ed7986fc8a992b07b3e13ce359a9e` | Oui |
| `docs/CONCEPTION_PHARMOS_v1.2.md` | `ffb5b062b58ba8376ef1811b4d2ab5998f283f64ed2c85c62bce473ff3a3997c` | Oui |
| `docs/CONCEPTION_PHARMOS_v1.md` | `61d8abe0e0f944f538668f5c1cf51e36f911e0f33e4ad9849a86f7f06ba945a5` | Oui |
| `docs/EXT02_RH_TEAM_MANAGEMENT.md` | `56801c7a2b70396a4c262148d2157ca6f7aec0078de816d6248263cbb7f85e15` | Oui |
| `docs/EXT03_ORDONNANCES_PRESCRIPTEURS.md` | `c253b1ecf56123dd2cba255d707b132425fe9839a619fd970888d2d74ec1b0df` | Oui |
| `docs/EXT04_CNAM_CONVENTIONS_ASSURANCES.md` | `eb1993be0b5ccc1c7dd21bdf8ac11f83cdbde2b62972e0484ca7f067451846fe` | Oui |
| `docs/EXT05_ANNULATIONS_RETOURS_DELEGATIONS.md` | `c3f6bb7ce18bae10e2936dd5709dc02422298194e4dbf93beead76875ce33e59` | Oui |
| `docs/EXT06_CATALOGUE_GOUVERNANCE_FINANCE.md` | `944d8e1312adca028a3d6a50c1a1a7736f4bd005d2cba3334ae223c0878ed36d` | Oui |
| `docs/EXT06_CATALOGUE_GOUVERNANCE_FINANCE_v1.1.md` | `0b9a4cb6faf81f6f6856f1cae8b92607bfd7d27f6111e983f8f83c9d7e412eeb` | Oui |
| `docs/EXT06_v1.1_RAPPORT_DE_REVUE.md` | `e2981719425c3211ff83d6cf32bebcbfe4bac14811cb22a6b84cc3d09efe873e` | Oui |
| `docs/JOURNAL_MODIFICATIONS_v1.2.1.md` | `21d6276cd5a8c8c61ef60696492172f9f18addd44177ea557428c8f9db14448b` | Oui |
| `docs/MATRICE_DECISIONS_D1_D86.md` | `3405ec1696088ec9af42f34ef854687867decced738cd3b57636a41ac72eb037` | Oui |
| `docs/MATRICE_DECISIONS_D1_D86_v1.2.1.md` | `01f2bef9257c51cc403247609988a23314af79fd92d1cd804f1993b2e1140e21` | Oui |
| `docs/MATRICE_DEPLOIEMENT_SAAS_ONPREMISE.md` | `608e387ecf751b2281611c9a7504d41f23e58513e1f6a06b0006080c4be9bf15` | Oui |
| `docs/MATRICE_DEPLOIEMENT_SAAS_ONPREMISE_v1.2.1.md` | `b14ffcf800bb710928835643f808cbc4443e7ba46bf1c5766ab6697548454308` | Oui |
| `docs/PLAN_DIRECTEUR_FINALISATION_PHARMOS_v1.3.md` | `c7691172b30dde85f4bc814afb95875b739663a087b9a4ec1ffbe366ae892854` | Oui |
| `docs/RAPPORT_AUDIT_COHERENCE_v1.2.1.md` | `8867bf8f0012f4c1e522c866566f6a4b11ec4432b411ee808f47fdbb721c7b35` | Oui |
| `docs/RAPPORT_AUDIT_COHERENCE_v1.2.md` | `ee16c0e6430cc3281d3ed0e5b0d56d0ffeaf530efbc0c7bd6e361c61969e9c84` | Oui |
| `docs/RAPPORT_AUDIT_FINAL_COHERENCE_PHARMOS_v1.3.md` | `b5bc5fa199bbe0fcc31dbbe06864fd03ad66b798c74148435d5904e3244b8e09` | Oui |
| `docs/RAPPORT_VALIDATION_CROISEE_v1.2.1.md` | `f33a7908dc45c93a09e778eeccfbd57872641d661256092eb3c33e37333b6cb5` | Oui |
| `docs/REGISTRE_ACTIONS_PHARMOS_v1.3.md` | `e9d956818e1cc73f0db9d0d3864136afc0ff593735c4fa14fa8ccb23af777e92` | Oui |
| `docs/REGISTRE_POINTS_OUVERTS_PHARMOS.md` | `de1429fe3f37afd864b3af1f2d8a79f95fb96f2350768318eb35539d7c6012d2` | Oui |
| `docs/REGISTRE_POINTS_OUVERTS_PHARMOS_v1.2.1.md` | `c9d4185914460d4c768256aa01474fe0bc58d8afe1623c3597ccec11c16c6a15` | Oui |
| `docs/TABLEAU_SYNTHESE_GO_P0_PHARMOS_v1.3.md` | `77980b21fba9c1f518557fa77dab7baf9ba599eb61c65f10f7be0988cce80a18` | Oui |
| `docs/archives/AUDIT_EXT03_v1.1/00_RAPPORT_AUDIT_EXT03.md` | `f0319b9cd8f88fb387635c89011cc774fbf72cb2023f6c3a482e8c3aa44ec137` | Oui |
| `docs/archives/AUDIT_EXT03_v1.1/02_GRILLE_DECISIONS_D27_D40.md` | `2eaa38ad9833eee2638ea1afbc0686728102b009b0395c0a213fed80d158934c` | Oui |
| `docs/archives/AUDIT_EXT03_v1.1/03_MODIFICATIONS_CONCEPTION_v1.2.md` | `219ea821be130edcf7f43fe5cde79155f0aacf122bc07bdde411bf43a9ee31cd` | Oui |
| `docs/archives/AUDIT_EXT03_v1.1/04_MATRICE_DEPENDANCES.md` | `a5119162e3a1a9199b55b51f5e6664b476737ee6f79781672c8195b05833b720` | Oui |
| `docs/archives/AUDIT_EXT03_v1.1/05_MATRICE_TESTS.md` | `07bd55add5f025ed59520286939bf1308e7490ea0cf057558806c6d9e1cb4096` | Oui |
| `docs/archives/AUDIT_EXT03_v1.1/06_POINTS_REGLEMENTAIRES.md` | `9c067bbb1fbb86a685ccad9847b2c04d52331fe4cb268100a1cb5aa2abf916ca` | Oui |
| `docs/archives/AUDIT_EXT03_v1.1/EXT03_ORDONNANCES_PRESCRIPTEURS_v1.1.md` | `f1bddd6f9c2fd2274ac3fc5e6bda6c89d6945b4c1a72cda0c5c95e724001cd43` | Oui |
| `docs/archives/AUDIT_EXT03_v1.1/LISEZMOI.md` | `d09c4bfc67d9726a058a6f236e594743e47562307d2befcc425009bdf15aebe3` | Oui |
| `README.md` | `e17d65d3c6d0f68ff7b6cfac14ada46ac19d7cad719064c034954898305171e0` | Oui |

### A.2 Code et configuration (8 fichiers, contrôle B)

| Fichier | SHA-256 avant | Identique après |
|---|---|---|
| `public/static/style.css` | `ef73096ba718c5f42e9bc3233e4bf719f7191143d1f385ed7cfdd7ccf913e49c` | Oui |
| `src/index.tsx` | `36eb7e17c477e7378d04e23e60492d50f109755b0b165dcfb5db0363567ae43d` | Oui |
| `src/renderer.tsx` | `34d78b7ea8bbc0d81d2eff5a68845e62dbb1b888b97f77fad709e3e242ae48c3` | Oui |
| `package-lock.json` | `12ffdcdb3f28c43c2ea6883b3ca5745b6d3bee6d8ad7c9c2415fde0ecaa26ca8` | Oui |
| `package.json` | `48a1d33220b8601fd0426624bfecb8074aa90ef5519664f2e6730236a3e02d2c` | Oui |
| `tsconfig.json` | `b33b584ae4dc3c9a63c1ed7f07f40f85348139251865e755165083125b87a2a8` | Oui |
| `vite.config.ts` | `219bdf338831deee9c85577f331fac3630bd176465a548222285bfaa2f117ae0` | Oui |
| `wrangler.jsonc` | `82c579d51ea471151a58831542eb66f16b4a9bcb20975b421579212c43db9804` | Oui |

Résultat : 33/33 documents et 8/8 fichiers de code ou de configuration identiques. Les cinq fichiers v1.3.1 sont
les seuls fichiers créés ; aucun fichier existant n'a été modifié, renommé, supprimé ni écrasé.

---

*Fin du rapport d'audit final de cohérence v1.3.1 — l'orientation GO P0 CONDITIONNEL ne vaut pas autorisation (RÉF-1).*