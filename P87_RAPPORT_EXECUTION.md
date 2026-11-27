# PHARM'OS — P87
## Rapport d'exécution — Enregistrement officiel des arbitrages PO P86

**Date :** 25/11/2026.
**Nature :** mission **strictement documentaire**. Aucune action technique.
**Livrable principal :** `docs/P87_ADDENDUM_DECISIONS_PO_P86.md` (référence officielle des arbitrages P86 confirmés).
**Livrable présent :** `docs/P87_RAPPORT_EXECUTION.md`.

---

## 1. Travaux réalisés

1. Relevé de départ en lecture seule : empreintes des 19 documents P81 à P86 (`/home/user/work/p87/sha_avant.txt`, hors dépôt), état Git, ressources, DEMO.
2. Relecture du dossier d'arbitrage P86 (intégral), du plan P86 (séquencement, R4a à R7a, PC-1, prérequis, conditions G6, effets d'une modification, conditions de lancement, clôture), de P83 (Q-11, C-P82-1 à 3), de P85 (A-T1 à A-T3, autorisations F-1 à F-4, non-autorisations, R7a-E1 à E8, R7a-C02 à C08), du registre P81 (DP1-R4, DP2-R1 à R4) et de P82 (V2-R8).
3. Rédaction de l'addendum : enregistrement des **13 arbitrages** (§3), des **deux précautions** (§4), du **statut des autorisations** (§5), de la **règle G6** qui en découle (§6), du **séquencement** (§7), des **réserves** (§8), de la **cohérence documentaire** (§9) et d'un tableau récapitulatif (§10).
4. Rédaction du présent rapport.

## 2. Décisions enregistrées

| Réf. | Statut enregistré |
|---|---|
| P86-1 à P86-11 | **DÉCISIONS PO CONFIRMÉES** (recommandations du dossier P86 retenues telles que présentées) |
| P86-12 | **DÉCISION PO CONFIRMÉE : maintenir ouverts** O-5, O-6, O-9, O-10 |
| P86-13 | **DÉCISION PO CONFIRMÉE : maintenir NON APPROUVÉ** V-3 |
| Précaution A (PERTE et API) | **PRÉCAUTION CONFIRMÉE** ; complète C-P82-3 et Q-11 ; ne vaut pas autorisation de développer le parcours |
| Précaution B (P86-V1) | **CHOIX EXPLICITE DU PO** : refus explicite et compréhensible, motif expliqué, correction possible dans le parcours, traitement avant R4e, pas de suspension générale |

Les intitulés et identifiants P86-n sont repris du dossier P86. Les conditions, limites, dépendances et conséquences de chaque point figurent à l'addendum §3. Les passages que le dossier présentait comme OBSERVATION, HYPOTHÈSE ou LIMITE **gardent leur étiquette**.

## 3. Contrôles de cohérence (sur les textes)

| # | Contrôle | Résultat |
|---|---|---|
| 1 | Compatibilité des 13 décisions avec P81 à P85 | Aucune contradiction relevée (dossier P86 §9, recontrôlé par lecture pour DP1-R4, DP2-R1 à R4, Q-11, C-P82-1 à 3, A-T1 à A-T3) |
| 2 | Q-11, C-P82-3 et précaution A | **Pas de contradiction** : la précaution A durcit sans modifier aucun terme (addendum §9, point 2) |
| 3 | P86-V1 avant R4e | Enregistré ; **position exacte non fixée** (RES-2) |
| 4 | Séquencement R0→R1→R2→R3→R4a→gel→R4b–R4e→R7a→R5→PC-1→R6→G6 | Cohérent avec P86-11, P86-5, P86-2, P86-3 et A1-M2 |
| 5 | Décision métier / proposition technique / autorisation d'exécution | Distinguées (addendum §5) |
| 6 | O-5, O-6, O-9, O-10 ouverts ; V-3 NON APPROUVÉ | Conservés |
| 7 | A-T1, A-T2, A-T3 non élargies ; phase 2 toujours SUSPENDUE | Vérifié (addendum §5.3) |
| 8 | Documents P81 à P86 non modifiés | **19/19 empreintes identiques** |

**Contradiction réelle entre décisions PO : aucune.**

## 4. Réserves signalées (non tranchées)

| Réf. | Réserve | Traitement |
|---|---|---|
| **RES-1** | **Précaution A et conception de R4b.** Le plan P86 prévoit un passage en PERTE par service et route HTTP avec paramètre de confirmation ; les scénarios PER-04, PER-05 et R7a-E6 supposent qu'un passage confirmé puisse s'exécuter. Lue **à la lettre**, la précaution A interdit tout passage en PERTE par API tant que le parcours complet manque. Le PO n'a pas précisé la lecture. | **À CONFIRMER par le PO avant R4b.** Plan P86 non révisé par P87. |
| **RES-2** | **Précaution B.** La « possibilité de corriger dans le parcours utilisateur » suppose de modifier les écrans de création et d'adoption, **hors R7a** (A-T3 « exclusivement », R7a-AC7, P86-S2). Aucune autorisation existante ne le couvre ; la place « avant R4e » par rapport au gel R4a→G6 et la nature du lot ne sont pas fixées. | **À arbitrer par le PO** ; le plan P86 §13 indique « plan à réviser ». |
| RES-3 | Précisions de vocabulaire : « neuf vérifications » (C7b est une réserve, non bloquante) ; place de PC-1 (dossier « avant G6 », plan « avant R6 », compatibles) ; texte de la formulation neutre illustratif | Sans effet sur les décisions |
| RES-4 | Les documents P86 gardent « PROPOSITION », « EN ATTENTE » (PRH-1) et « À CONFIRMER » (PRH-3) | **L'addendum P87 prévaut** pour les points enregistrés ; PRH-2, PRH-4 et PRH-5 restent ouverts |

## 5. Autorisations qui restent ABSENTES (NON AUTORISÉ)

Réalisation STOCK (R0 à R6) ; création ou exécution de la migration 0022 ; modification de `compose.ts` ; développement de R7a ; développement du traitement de P86-V1 ; développement du parcours de passage en PERTE ; adaptation de `schema_parite`, `p33`, `p34` ; promotion **G6** ; **G7** ; phase 2 et exécution de PC-1 ; tests, builds, benchmarks, déploiement, copies de version ; mission d'interface ; suppression, redémarrage, systemd, opération Git manuelle.

**A-T1, A-T2 et A-T3 restent limitées à leur portée initiale** (P85 §8.1 : autorisations futures F-1 à F-4, valables dans une mission de réalisation lancée). Le plan P86 §14 point 1 (arbitrage consigné par écrit) est **rempli** ; le point 2 (**autorisation explicite de lancer la réalisation**) **ne l'est pas**.

**Points ouverts conservés :** O-5, O-6, O-9, O-10 OUVERTS ; O-7, O-8 NON VÉRIFIÉS ; V-3 NON APPROUVÉ ; V-4, V-5, V-6 en VIGILANCE.

## 6. Confirmation de l'absence d'action technique

| Contrôle | Résultat |
|---|---|
| Empreintes P81 à P86 (19 fichiers) | 19/19 identiques à la ligne de base P87 |
| Agrégats `src`, `migrations`, `tests`, `tools`, `ui/public/on-premise`, `ui/saas-src/src`, `package.json`, `tsconfig.json` | 8/8 identiques à la ligne de base P86 |
| Migrations officielles | 21 fichiers, dernier `0021_commandes_catalogue_national.sql` ; `migrations_candidates` **absent** |
| Dump `pharmos/core` | 750 096 384 o, inchangé |
| Git | HEAD `5f8cc87` (inchangé depuis le début de P87) ; 0 tag, 0 stash ; seuls fichiers non suivis : les documents P87 ; **aucune opération Git manuelle** |
| DEMO | PID 5638 actif ; `/health` : `{"status":"ok"}` (lecture seule) |
| Ressources (relevé instantané) | disque ≈ 1 139 364 Ko libres (97 %) ; mémoire disponible ≈ 611 612 kB |
| Scan de motifs sensibles (liste fixée hors document) dans l'addendum | 0 occurrence |
| Forme de l'addendum | 0 espace final, 0 tabulation |

Aucun code, aucune migration, aucune base, aucun test, aucun build, aucun benchmark, aucun déploiement, aucune intervention sur la DEMO ou systemd, aucun nettoyage n'ont eu lieu. Aucun fichier de codes, de comptes ou de secrets n'a été ouvert. Aucun lien n'a été créé.

## 7. Limites (LIMITE / NON VÉRIFIÉ)

- Contrôles de cohérence **sur les textes seulement** ; aucune exécution.
- Lectures **partielles** des sources pour cet addendum : P81 (registre : DP1-R4 et DP2-R1 à R4 seulement ; plan non relu), P82 (V2-R8 et Q-11 seulement), P84 (non relu ; cité via P85 et P86), plan P86 (sections ciblées), rapports P81 à P86 (rapport P86 §5 seulement). Les références aux critères P84 sont reprises **telles que citées** par le dossier P86 et P85.
- Le comptage des « 17 fichiers D1 » (T-5) reste **non recompté** ; la baisse d'espace libre disque depuis P85 reste **non analysée**.
- La confirmation du PO est celle de la demande P87 ; en cas d'écart de lecture, la formulation du PO prévaut.
- La mémoire disponible relevée (≈ 612 Mo) est proche du plancher de 500 Mo fixé par P86-4 : relevé instantané, non une garantie.

## 8. État de clôture

Les deux livrables sont remis. **La mission P87 est terminée et s'arrête ici.** Je n'ai pas lancé P88, la réalisation STOCK, la migration 0022 ni la promotion G6. Ces actions restent soumises à une **autorisation technique distincte du PO**.

**Décisions attendues du PO avant toute réalisation :** (1) lecture de la précaution A (RES-1) ; (2) place, nature et autorisation du traitement de P86-V1 (RES-2) ; (3) autorisation explicite de lancer la réalisation (plan P86 §14, point 2).

---
Fin du rapport P87.
