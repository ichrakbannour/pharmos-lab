# PHARM'OS — Audit documentaire global
**Version :** 0.6 — différentiel de la matrice D1–D86, revue EXT03 v1.1 et consolidation P81–P91  
**Date :** 27/11/2026  
**Dépôt :** https://github.com/ichrakbannour/pharmos-lab  
**Branche :** `main`  
**Statut :** audit documentaire en cours. Ce rapport n'autorise aucune réalisation technique.

## 1. Objet et sources examinées

Cette version complète v0.5 par l'examen des 14 versions de la matrice D1–D86, du registre P81, des addenda P83/P85/P87 et des rapports P88/P89/P91, ainsi que du dossier EXT03 v1.1 et de ses six pièces d'audit.

Archive source :
- `EXPORT_SOURCES_AUDIT_PHARMOS_20261127.zip` — 498 326 octets ;
- SHA-256 : `b3a4d228927300c6c12b18b5082fb4c797599fc762102a9e08a97fd47d0f9fa5` ;
- 42 entrées ; `unzip -t` sans erreur ;
- P81 : 17 719 octets, SHA-256 `dec4d6420c5e5ecf54d4e31410c4776a45b3f63e160dae7193a958cd41c2232c`.

Le manifeste d'export affirme que les 42 fichiers ont été comparés à leurs originaux (contenu, taille et SHA-256) sans écart. Le contrôle indépendant ici confirme l'empreinte de l'archive, ses 42 entrées et l'empreinte P81. Le croisement individuel P82–P86 avec les manifestes M0 de P88/P87 demeure NON VÉRIFIÉ.

## 2. Différentiel des 14 versions de la matrice D1–D86

Le parcours chronologique fait apparaître la séquence suivante :

| Version | Évolution documentée | Effet sur les statuts D1–D86 |
|---|---|---|
| v1.2 | Consolidation initiale du 03/11 | Base historique |
| v1.2.1 | D3 arbitrée : base physiquement distincte par pharmacie | Total : 16 arbitrées, 63 proposées, 5 à réexaminer, 2 caduques |
| v1.2.2 | Annotations d'harmonisation issues des arbitrages Finance (points a à m) | Aucun statut modifié |
| v1.2.3 | D40 arbitrée le 10/11 ; réservation sans avance, défaut de deux jours comptables, paramétrage ADMIN | Total : 17 arbitrées, 62 proposées, 5 à réexaminer, 2 caduques |
| v1.2.4 | Décisions « PHARM'OS CONTROL & AUDIT » A à K consignées sans créer de D | Aucun statut modifié |
| v1.2.5 | QO-01 du PO | Aucun statut D modifié |
| v1.2.6 | QO-02 du PO | Aucun statut D modifié |
| v1.2.7 | QO-03 du PO | Aucun statut D modifié |
| v1.2.8 | QO-04 du PO | Aucun statut D modifié |
| v1.2.9 | QO-05 du PO | Aucun statut D modifié |
| v1.3.0 | QO-06 du PO | Aucun statut D modifié |
| v1.3.1 | QO-11 / décision 1 : V-1 acceptée sous conditions | Aucun statut D1–D86 modifié |
| v1.3.2 | QO-11 / décision 2 : H-b retenue sous réserves | Aucun statut D1–D86 modifié |
| v1.3.3 | QO-11 / décision 3 : T-3 acceptée avec précautions (a), (b), (c) | Aucun statut D1–D86 modifié |

**Constat :** les statuts de la matrice restent à 17 arbitrées, 62 proposées, 5 à réexaminer et 2 caduques. Les annotations QO-01 à QO-11 enregistrent des règles ou arbitrages sans créer de nouvelles décisions numérotées D. QO-11 demeure explicitement PARTIELLEMENT ARBITRÉE, NON CLÔTURÉE. HAR-07, HAR-27 et E-2 restent ouverts ; l'arbitrage T-3 ne constitue aucune validation réglementaire.

**Précaution de lecture :** les identifiants locaux « D1 », « D2 » et « D3 » employés dans QO-11 désignent les trois décisions de cette question et ne modifient pas les statuts des décisions numérotées D1 à D86 de la matrice.

## 3. EXT03 v1.1 — proposition non validée et réserves réglementaires

Le document EXT03 v1.1 et six pièces d'audit ont été examinés. Le fichier `LISEZMOI.md` est un ajout de copie, non une pièce de l'archive initiale.

Le dossier de revue du 03/11 décrit EXT03 v1.1 comme une version révisée soumise à validation, et non comme une extension définitivement approuvée. La grille D27–D43 indique explicitement « soumis à validation du propriétaire » et ses cases « Décision retenue » sont vierges. Elle ne peut donc pas être présentée comme un arbitrage PO complet.

**Décalage identifié :** la grille EXT03 conserve D40 comme proposition « courte (48 h) » non arbitrée, alors que la matrice v1.2.3 et les arbitrages PO ultérieurs enregistrent D40 comme arbitrée : réservation sans avance, expiration par défaut de deux jours comptables (et non 48 heures calendaires), configurable par l'ADMIN. La grille EXT03 doit rester historique sur ce point ; la décision ultérieure prévaut.

La revue EXT03 identifie notamment des risques d'atomicité, de contrôle des réglementés, de retours, de réidentification et d'OCR. Elle mentionne un mécanisme d'annulation de batch à prouver par prototype et la qualité réelle de l'OCR manuscrit FR/AR à mesurer. Ces éléments sont des constats/recommandations de revue, pas des preuves de réalisation.

Les points réglementaires R1–R22 sont des questions à confirmer auprès des autorités ou conseils compétents ; le document précise qu'aucune règle de droit n'est présumée. Échéances particulièrement bloquantes :
- avant le lot 5b : R1–R7, R10, R14, R15 et R19 ;
- avant le lot 5c (OCR) : R16 ;
- avant le lot 10 : R17 et R18 ;
- avant production : R8, R9 et R20–R22.

**Conclusion EXT03 :** pas de validation réglementaire externe démontrée dans le corpus. D27–D39 et D41–D43 ne sont pas établies comme arbitrées par la grille consultée ; D40 est couverte par un arbitrage ultérieur dans la matrice. L'intégration d'EXT03 v1.1 dans la conception ne doit pas être considérée comme une autorisation de développement ou de mise en production.

## 4. Chaîne des décisions STOCK : corrections et précisions

### 4.1 P81 — registre métier officiel

P81 confirme DP-1 à DP-6 et C1 à C4. Les invariants structurants comprennent quatre états physiques (DISPONIBLE, EN_CONTROLE, QUARANTAINE, PERTE), suivi par lot obligatoire pour les médicaments, réservations par produit avec choix FEFO du lot à la vente, mouvements comme source de vérité, et liste fermée des entrées physiques.

P81 maintient O-5, O-6, O-9, O-10 et O-11 ouverts à la date de son établissement. Il conserve aussi les vigilances V-1 et V-2, sans décider leurs modalités techniques. Le registre précise qu'il n'autorise ni code, ni migration, ni test, ni déploiement.

### 4.2 P83 — décisions confirmées et mise à jour de certains points ouverts

P83 confirme Q-1 à Q-12, H-1 à H-6 et les précautions C-P82-1 à C-P82-3. Il précise notamment :
- Q-5 : la levée de QUARANTAINE est réservée à l'ADMIN, avec authentification forte ;
- Q-11 : le passage en PERTE est irréversible ;
- Q-12 : les motifs normalisés sont portés par une table de référence, ce qui traite O-11 pour la forme ;
- O-5, O-6, O-9 et O-10 restent ouverts ;
- V-3 (formule CAT-11 et convention P82) reste NON APPROUVÉ ;
- DP1-R7 reste REPORTÉ : sorties de QUARANTAINE et destruction des quantités en PERTE reportées à un lot ultérieur.

P83 ne vaut pas autorisation de réalisation, migration 0022, test, intervention DEMO, promotion G6 ou application G7.

### 4.3 P85 et P87 — arbitrages et limites d'autorisation

P85 confirme A-T1, A-T2 et A-T3 : sécurisation de livraison sous conditions ; phase 1 autorisée sous conditions et phase 2 suspendue ; option A de restitution minimale R7a approuvée dans le périmètre prévu.

P87 est la référence officielle des arbitrages PO P86-1 à P86-13 et des précautions A et B. Il confirme notamment :
- critères C1–C8 révisés, C7 scindé en C7a/C7b et ajout de C9 ;
- seuils P84 utilisés comme planchers de départ, durcissables mais non assouplis ;
- report de la phase 2 par défaut et porte de capacité PC-1 avant G6 ;
- passage en PERTE non exposé dans V1/V3 tant que le parcours d'interface complet n'est pas livré ;
- O-5, O-6, O-9 et O-10 maintenus ouverts ;
- V-3 maintenu NON APPROUVÉ ;
- précaution A : pas d'opération PERTE directement par API sans parcours utilisateur complet et confirmation liée à l'opération, la quantité et ses conséquences irréversibles ;
- précaution B : P86-V1 doit être un lot identifié, traité avant R4e.

P87 indique expressément que les décisions métier ne valent pas autorisation de réalisation. Les autorisations techniques et les portes G6/G7 demeurent distinctes.

## 5. P88–P91 — état réel et limites de reprise

- **P88 :** GO conditionnel du 25/11, mais préflight R0 BLOQUÉ par PRQ-3 lorsque MemAvailable est sous le plancher de 500 000 kB. La migration 0022 n'est pas créée ; G6 n'est ni proposée ni promue.
- **P90 :** aucun fichier officiel autonome n'est retrouvé. En revanche, P88 contient une section « MISE À JOUR P90 » documentant la reprise Option A et l'arrêt à R0d, huit secondes après le lancement du premier fichier de test, sur franchissement du seuil mémoire. Aucune étape de P88 n'est validée. La lacune porte donc sur le livrable autonome, pas sur l'absence totale de trace de P90.
- **P89 :** corrige l'hypothèse de P88 selon laquelle le contenu de /tmp/Shmem expliquerait les variations de MemAvailable. Shmem et le contenu de /tmp sont constants pendant les relevés ; ils expliquent une contrainte structurelle, pas les variations. La cause des variations reste NON IDENTIFIÉE. L'origine et le statut du plus gros fichier de /tmp/backup restent inconnus.
- **P91 :** préparation de l'Option B uniquement. Les paliers sont des propositions ; aucun palier n'a été exécuté. Aucune suppression, copie, déplacement ou libération de mémoire n'est autorisée par ce rapport. P88 n'est pas relancée ; 0022 n'est pas créée ; G6 n'est pas proposée ; phase 2 suspendue.

Toute action sur les éléments de /tmp, les archives P73/P76/P77/P78, le résidu P90, les services, la DEMO ou les migrations exige une décision PO distincte. Les estimations de gain mémoire de P91 ne sont pas des mesures d'effet réel.

## 6. Registre des constats actualisés

| ID | Statut au 27/11/2026 | Conclusion |
|---|---|---|
| AUD-028 — original P81 | Source retrouvée ; publication exacte GitHub à vérifier | SHA-256 original confirmé dans l'archive. Ne pas remplacer l'original par une transcription. |
| AUD-030 — matrice v1.3.3 | Différentiel documentaire effectué | 14 versions examinées ; statuts D1–D86 inchangés après v1.2.3 ; QO-11 partielle. |
| AUD-031 — EXT03 v1.1 | Sources examinées ; validation non démontrée | Grille D27–D43 non signée ; D40 obsolète dans cette grille ; R1–R22 restent des questions réglementaires. |
| AUD-040 — P83 | Décisions PO retrouvées | Q-1 à Q-12, H-1 à H-6 et C-P82-1 à C-P82-3 confirmés ; O-11 traité pour la forme. |
| AUD-041 — P86/P87 | Décisions PO retrouvées | P87 est la référence officielle ; V-3 reste non approuvé ; O-5/O-6/O-9/O-10 restent ouverts. |
| AUD-047 — P90 | Pas de rapport autonome ; contenu intégré à P88 | Ne pas reconstituer de fichier P90. |
| AUD-048 — hypothèse tmpfs de P88 | Hypothèse infirmée par P89 | Contrainte structurelle confirmée ; cause des variations mémoire inconnue. |
| AUD-049 — Option B | Préparation seulement | Aucun palier P91 exécuté ; aucune suppression autorisée. |
| AUD-050 — EXT03 réglementaire | Ouvert | Aucune validation juridique/réglementaire externe n'est prouvée par le corpus. |
| AUD-051 — O-11 | Traité pour la forme par P83 Q-12 | La table de référence est confirmée ; ne pas maintenir O-11 comme ouvert sans cette nuance. |

## 7. Publication des sources dans GitHub

L'archive et les sources ont été examinées depuis la pièce jointe. **Les 42 originaux ne sont pas tous publiés individuellement dans le dépôt.** La présence de l'archive jointe ou d'une empreinte dans un rapport ne vaut pas archivage source GitHub.

Priorités de publication exacte :
1. P81, en conservant les 17 719 octets et le SHA-256 d'origine ;
2. matrice v1.3.3, en la distinguant des versions historiques déjà présentes ;
3. P83, P85 et P87, comme preuves d'arbitrages PO ;
4. P88, P89 et P91, comme rapports de réalisation/diagnostic/préparation ;
5. EXT03 v1.1 et les six pièces d'audit, en conservant leur statut de proposition.

Le fichier `LISEZMOI.md` d'EXT03 doit être identifié comme ajout de copie, non comme pièce d'origine. Les sources déjà présentes dans le dépôt ne doivent pas être dupliquées ni écrasées sans comparaison d'empreinte.

## 8. Interdictions et décisions restant à solliciter

Aucune conclusion de cet audit n'autorise le développement, les tests, la migration 0022, la promotion G6, l'application G7, le déploiement, le nettoyage de /tmp, la suppression de fichiers, une modification système, PM2 ou une intervention DEMO.

Les points métier explicitement ouverts restent O-5, O-6, O-9 et O-10. V-3 reste NON APPROUVÉ. Les réserves réglementaires EXT03 R1–R22 ne sont pas levées par le dossier documentaire. P90 reste sans rapport autonome.

---
**Fin du rapport v0.6 — audit documentaire en cours.**
