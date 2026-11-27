# PHARM'OS — Précontrôle technique STOCK R0 — autorisation reçue, lancement bloqué

**Date :** 27/11/2026  
**Heure du relevé local :** environ 15:12–15:20 (UTC+01)  
**Référence :** P92 — plan unique STOCK révisé v2 ; P86 §3 (R0), §8.2 (PRQ-1 à PRQ-8), §14 ; P87 §5.2 ; P92 addendum.  
**Nature :** précontrôle technique en copie locale isolée ; aucune modification fonctionnelle.

## 1. Autorisation et périmètre retenu

Le PO a explicitement répondu « je t'autorise » le 27/11/2026, après la demande d'autorisation technique distincte pour R3b et les lots de réalisation.

Cette autorisation est prise en compte pour préparer la mission STOCK P92 dans une **copie locale isolée de l'archive source**. Elle ne vaut pas autorisation de :
- toucher à la DEMO, à une base réelle, aux données d'une pharmacie ou à un service actif ;
- exécuter/appliquer une migration officielle, promouvoir G6 ou exécuter G7 ;
- modifier PM2, systemd, une configuration système ou un processus ;
- supprimer, purger, déplacer ou réparer des fichiers existants ;
- effectuer une opération Git manuelle, un commit ou un push depuis cette mission.

Les prérequis, seuils et conditions d'arrêt P86 restent applicables. L'autorisation ne transforme pas un prérequis non satisfait en PASS.

## 2. Source utilisée et intégrité

- Archive : `PHARMOS_SOURCE_EXPORT_20261127(1).zip`
- SHA-256 : `9efbd14b4449d11a08bddd87efb170496c3275800763a96b6959b6cdea4c3ef`
- 1 917 entrées ZIP ; test d'intégrité ZIP : **OK**.
- 1 911 fichiers source annoncés dans l'inventaire d'export ; aucune métadonnée `.git` ni dépendance `node_modules`.
- Le README d'export rapporte le dernier commit observé `f59b1e9dcf7b`, mais l'état HEAD, `git status`, les tags et les stashes ne peuvent pas être vérifiés depuis cette archive.
- La copie de travail est isolée sous `/mnt/data/PHARMOS_STOCK_MISSION_20261127/PHARMOS_SOURCE_EXPORT_20261127`. Aucun fichier source n'y a été modifié.

## 3. Relevé de ressources local

| Mesure | Observation | Seuil P86 | Lecture |
|---|---:|---:|---|
| Mémoire disponible | 4 859 592 kB | lancement ≥ 500 000 kB ; arrêt < 350 000 kB | au-dessus du plancher à ce relevé |
| Espace disque disponible | 31 157 908 kB | lancement ≥ 1 000 000 kB ; arrêt < 800 000 kB | au-dessus du plancher à ce relevé |
| Swap | 0 kB | non spécifié comme prérequis | observation |
| Node.js | 22.16.0 | projet : ≥ 22.18 | **BLOQUANT** |
| Dépendances installées | absentes | `npm ci` requis selon README_EXPORT | non préparé |
| `TMPDIR` dédié conforme PRQ-2 | non créé / non vérifié | requis avant phase 1 | **NON VÉRIFIÉ** |
| DEMO `/health` avant/après suite | non consulté | PRQ-5 | **NON VÉRIFIÉ** |

Les relevés mémoire/disque sont une photographie unique du conteneur local, pas une garantie de capacité pendant les suites. Aucun benchmark ni réglage système n'a été exécuté.

## 4. Constat statique des sources utiles à R3b

Les fichiers concernés sont présents dans l'archive. Les constats suivants sont des lectures statiques, pas des résultats de test :

### RES-2 — suivi obligatoire par lot

- V1 `pharmos/ui/public/on-premise/js/catalogue.js` : la case `cat-f-lots` est rendue sans sélection initiale ; la valeur envoyée dépend de son état coché.
- V3 `pharmos/ui/saas-src/src/components/CatalogForms.jsx` : `tracksLots: false` dans l'état initial du formulaire produit.
- V3 `pharmos/ui/saas-src/src/components/ReferenceCatalog.jsx` : `tracksLots: false` dans l'état initial du formulaire d'adoption.
- Service `pharmos/src/catalog/service.ts` : `createProduct` et `adoptReference` convertissent `tracksLots` via `flag(...)` et l'enregistrent ; le contrôle DP2-R1 imposant `tracksLots=true` pour MÉDICAMENT n'apparaît pas dans les chemins examinés.

**Écart statique à traiter :** l'interface et le service n'appliquent pas encore, dans les chemins observés, le résultat métier RES-2.

### RES-1 — PERTE

- `pharmos/src/stock/service.ts` ne contient pas les états `PERTE`, `QUARANTAINE`, `EN_CONTROLE`, ni la logique `stock.transfer` attendue.
- `pharmos/src/entry/stockRoutes.ts` expose les routes de stock existantes (produits, réceptions, sorties, ajustements) ; aucune route de transfert d'état n'y apparaît.
- Le dossier contient 21 migrations pharmacie, jusqu'à `0021_commandes_catalogue_national.sql) ; aucune migration candidate 0022 ni répertoire `migrations_candidates` n'est présent.

**Conséquence :** le parcours PERTE complet dépend d'un socle service/routes/schéma à réaliser ; il ne peut pas être traité comme une simple retouche d'interface isolée.

## 5. Porte R0 / prérequis

**PR-0 : NON FRANCHIE — BLOQUÉE.**

Motifs :
1. métadonnées Git absentes : HEAD/status/tags/stashes et ligne de base de dépôt non vérifiables ;
2. Node 22.16.0 inférieur au minimum documenté 22.18 ;
3. PRQ-5 (`/health` DEMO avant/après chaque suite) non vérifié ; aucun accès DEMO n'a été tenté ;
4. PRQ-2 (`TMPDIR` dédié, capacité et limites fixées avant création) non achevé ;
5. dépendances non installées ; aucune suite de base n'a été exécutée.

Conformément à P86 §8.2, la phase 1 n'est pas lancée tant que les prérequis applicables ne sont pas satisfaits. Il serait incorrect de poursuivre vers R1/R2/R3b ou de modifier le code en présentant ce R0 comme franchi.

## 6. État d'exécution

- Code modifié : **NON**.
- Migration candidate 0022 créée : **NON**.
- Migration exécutée : **NON**.
- Tests/builds/benchmarks : **NON EXÉCUTÉS**.
- DEMO/base réelle/service/PM2/systemd : **NON TOUCHÉS**.
- Nettoyage/suppression/déplacement : **AUCUN**.
- Commit/push de code : **AUCUN**.

## 7. Condition concrète de reprise

Pour franchir PR-0 sans déroger au plan, il faut un environnement de réalisation qui fournisse :
1. le checkout source complet avec métadonnées Git accessibles (HEAD et état propre documentés) ;
2. Node.js ≥ 22.18 et les dépendances verrouillées installables ;
3. un environnement de test conforme aux PRQ-2 à PRQ-8 et la possibilité d'effectuer le contrôle de santé strictement en lecture seule exigé par PRQ-5, sans toucher à la DEMO ;
4. les journaux hors dépôt et les copies jetables prévus par P86.

Le dépôt privé de code et le dossier documentaire public ne remplacent pas ce checkout. Aucun code n'a été envoyé dans le dépôt documentaire.

---
**Conclusion :** autorisation technique reçue et prise en compte ; précontrôle R0 réalisé partiellement sur l'archive locale ; **lancement de la réalisation bloqué à PR-0** pour des prérequis vérifiables manquants. Aucun résultat PASS n'est revendiqué.
