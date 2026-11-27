# P83 — Rapport d'exécution : enregistrement consolidé des arbitrages PO STOCK (P82)

Date : 25/11/2026. Nature : **mission documentaire uniquement**. Autorisation : confirmation explicite du PO du 25/11/2026 (Q-1 à Q-12 et H-1 à H-6 approuvés conformément aux recommandations de P82, avec trois précautions complémentaires). Données fictives uniquement.

## 1. Fichiers créés

| Fichier | Contenu |
|---|---|
| `docs/P83_ADDENDUM_DECISIONS_PO_STOCK.md` | Addendum daté du 25/11/2026 au registre : confirmation (§1), synthèse Q-1 à Q-12 (§2), décisions détaillées (§3), interprétations H-1 à H-6 (§4), précautions C-P82-1 à C-P82-3 (§5), contrôle de cohérence (§6), points non confirmés (§7), mise à jour des statuts (§8), limites (§9), évolution (§10) |
| `docs/P83_RAPPORT_EXECUTION.md` | Ce rapport |

Aucun document existant n'a été modifié : l'addendum **complète** le registre P81 (évolution datée prévue à son §12) sans toucher à ses entrées.

## 2. Références vérifiées

Chaque décision de l'addendum a été rapprochée de son texte source.

| Contrôle | Source | Résultat |
|---|---|---|
| Libellés Q-1 à Q-12 et recommandations | P82 spécifications §1 | Les 12 décisions sont présentes ; conditions, limites et conséquences reprises (pas de réduction à l'intitulé) |
| Règles rattachées (V1-R4, V1-R5, V1-R6, V1-R7, V1-R9, V1-R10, V2-R3 à V2-R8, INV-4, INV-5, INV-6, INV-10, §4.3, §8.3) | P82 spécifications §4, §5, §7, §8 | Citées conformément ; aucune règle modifiée |
| H-1 à H-6 | P82 spécifications §1 (interprétations par défaut) | Les 6 interprétations sont reprises dans leur sens d'origine |
| DP1-R3, DP1-R4, DP1-R5, DP1-R7, DP1-R10, DP3-R3, DP4-R1, DP4-R4 à R6, DP5-R1 à R6, C1 à C4, O-5, O-6, O-9, O-10, O-11, V-1, V-2 | Registre P81 §2 à §10 | Références exactes ; textes du registre non modifiés |
| A-T1, A-T2, RR-1, V-3, V-4, lots R0 à R7, G6, G7 | P82 plan §4.4, §7, §12 ; rapport P82 §4, §5 | Références conformes |

## 3. Contrôles de cohérence effectués

| Contrôle | Résultat |
|---|---|
| Décisions confirmées contre DP-1 à DP-6 et C1 à C4 | Aucune contradiction. Deux **précisions de lecture** enregistrées sans modifier le registre : Q-5 sur DP1-R3 (ADMIN seul, authentification forte) ; H-5 sur DP4-R6 |
| Décisions confirmées contre invariants P82 (stock physique, FEFO, réservations, traçabilité, archivage, destruction, corrections et contre-passations) | Cohérentes (addendum §6.2) |
| Point d'attention Q-9 et DP1-R7 | Les corrections négatives en EN_CONTROLE et QUARANTAINE constatent un écart de comptage et ne sont pas des sorties ; C-P82-1 confirme cette lecture. Documenté (addendum §6.1) ; la tenue est à contrôler par les tests prévus en réalisation |
| Trois précautions | Intégrées et rattachées à Q-9, Q-10, Q-11 ; aucune ne modifie un terme des décisions |
| Statut des questions traitées | Q-1 à Q-12 : plus en attente d'arbitrage ; V-1 et V-2 (registre §10.3) levées ; O-11 traité pour la forme ; lacune « levée d'un blocage » comblée (addendum §8) |
| Recommandations techniques non soumises au PO | Non transformées en décision : noms de droits, modèle, séquencement, gardes, contrôles B14 à B22, seuil disque 800 Mo restent des PROPOSITIONS (addendum §9) |

## 4. Observations nouvelles

- **V-5 (VIGILANCE)** : appliquer C-P82-1 (« véritable écart de comptage ») aux motifs ERREUR_SAISIE et AUTRE (exemple : présentation séparée dans le rapport). Réduire la liste des motifs serait une modification de Q-9 et Q-12, donc une décision écrite du PO.
- **V-6 (VIGILANCE)** : C-P82-2 et C-P82-3 supposent un message de refus complet et une confirmation avec quantité. L'interface relève de la mission distincte R7 ; la restitution par l'interface actuelle n'est **pas vérifiée** (NON VÉRIFIÉ). **PROPOSITION technique, non décidée** : paramètre de confirmation explicite côté commande pour le passage en PERTE.
- **Écart de statut dans les documents P82** : ils présentent encore Q-1 à Q-12 « à arbitrer ». Non corrigés (documents historiques) ; l'addendum §8 indique qu'il fait foi pour le statut.
- **Conséquence acceptée de Q-3** : modification du comportement de validation d'une vente adossée à une réservation (V1-R5) ; le repli « conserver la règle actuelle » n'est plus retenu.

## 5. Points restant ouverts (non confirmés par le PO)

| Réf. | Statut |
|---|---|
| O-5 (réception « à contrôler »), O-10 (prérequis Lot 2) | OUVERT |
| O-6 (retour client en contrôle) | OUVERT / REPORTÉ |
| O-7 (`ALTER … ADD COLUMN` en D1), O-8 (volumétrie) | NON VÉRIFIÉ |
| O-9 (0021 en DEMO) | OUVERT |
| A-T1 (couplage code/schéma), A-T2 (mémoire, seuil disque 800 Mo) | À TRAITER selon les conditions de P82 ; non approuvés |
| V-3 (formule CAT-11 avec la convention de P82) | VIGILANCE, **NON APPROUVÉ** ; la formule du registre (DP1-R9) reste seule décision |
| V-4, V-5, V-6 | VIGILANCE |
| G6 (promotion de 0022), G7 (application à la DEMO), R7 (interface), réalisation du STOCK | Décisions distinctes du PO, non autorisées |

## 6. Confirmation : aucune action technique

Aucun changement de code source ; aucune création ni exécution de migration 0022 ; aucun changement dans le dossier officiel des migrations ; aucune modification de base de données ; aucun test exécuté ; aucune intervention sur la DEMO ou systemd ; aucun déploiement ni redémarrage ; aucune opération Git manuelle (commit, push, pull, fetch, merge, rebase, reset, cherry-pick, tag, stash, changement de branche) ; aucun nettoyage, suppression ou réparation du serveur ; aucun lien de fichier créé ; aucun secret ni fichier de codes lu ou affiché. Seules des lectures de documents et des commandes de contrôle en lecture seule ont été faites, et deux fichiers neufs ont été écrits dans `docs/`.

## 7. Contrôle final

Voir §8.

## 8. Résultats de la vérification finale

Relevé du 25/11/2026, en fin de mission (FAIT VÉRIFIÉ) :

| Contrôle | Résultat |
|---|---|
| SHA-256 des 8 documents historiques (P81 : 5 ; P82 : 3), relevé avant et après | 8 sur 8 identiques |
| Présence des décisions dans l'addendum | Q-1 à Q-12 : 12 sections ; H-1 à H-6 : 6 lignes ; C-P82-1 à C-P82-3 : 3 lignes ; chacune une seule fois |
| Défaut de forme (`git diff --no-index --check`) sur les 2 livrables | aucun |
| `git status --short` | uniquement les 2 livrables P83 non suivis |
| Dépôt | branche `main`, HEAD « genspark auto-backup » (97cefe9) ; 0 tag ; 0 stash ; aucun commit manuel |
| `git status` sur `pharmos` et `lot0a` | vide |
| Dernière migration officielle | `0021_commandes_catalogue_national.sql` ; dossier `migrations_candidates` inexistant |
| DEMO | processus 5638 actif, `/health` = `{"status":"ok"}` |
| Dump `pharmos/core` | 750 096 384 o (inchangé) |
| Disque | 1 156 408 Ko libres (97 %) |
| Mémoire | ≈ 629 Mo disponibles |
| Recherche de liens et d'adresses dans les 2 livrables | aucun |

## 9. Condition de clôture

Les décisions Q-1 à Q-12 et H-1 à H-6 sont officiellement enregistrées, avec C-P82-1 à C-P82-3, dans `docs/P83_ADDENDUM_DECISIONS_PO_STOCK.md`. **La réalisation du STOCK et la migration 0022 restent soumises à une autorisation PO distincte.** P84 n'est pas lancé.
