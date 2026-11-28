# PHARM'OS — Complément R0 : restauration Git et actualisation du précontrôle

**Date :** 28/11/2026  
**Référence :** P92 v2, P86 §3/§8.2/§14, précontrôle R0 du 27/11/2026.  
**Portée :** contrôle en lecture seule du bundle Git restauré et inspection statique des sources ; aucun code applicatif modifié.

## 1. Restauration Git — vérifiée

- Bundle reconstitué : `PHARMOS_FULL_HISTORY_20261127.bundle`
- Taille : 412 557 360 octets.
- SHA-256 : `9c85e5bb7b419e2aebc3c59a08dd4a9420af6364b3d581e45e84265b89619b75` — conforme au manifeste Genspark.
- `git bundle verify` : **OK**, bundle complet.
- Clone local isolé : `/mnt/data/PHARMOS_RESTAURED_20261128`.
- HEAD : `f59b1e9dcf7ba752ad6dd4b3ed8f0ca96a054954`.
- Branche : `main`.
- Historique : **341 commits** ; 0 tag.
- `git status` : propre.
- `git fsck --connectivity-only` : aucune erreur.

La réserve de forme (11 parties) est levée : toutes les parties ont été reçues, leurs empreintes individuelles correspondent au manifeste et le fichier reconstitué a lui-même été vérifié.

## 2. Actualisation de PR-0

La restauration Git lève le premier blocage de l'ancien rapport : HEAD, branche, historique et état propre sont maintenant vérifiables.

PR-0 reste toutefois **NON FRANCHIE** :

| Prérequis | État au 28/11 |
|---|---|
| Métadonnées Git / HEAD / état propre | **PASS — vérifié** |
| Node.js ≥ 22.18.0 | **PASS — runtime local alternatif v24.11.1 vérifié** ; le Node par défaut reste 22.16.0 |
| Dépendances du projet | **BLOQUÉES** — aucun `node_modules` ; cache npm vide et accès réseau npm indisponible dans cet environnement |
| TMPDIR dédié conforme PRQ-2 | **PASS — créé après contrôle des seuils**, permissions 0700 |
| DEMO `/health` PRQ-5 | **NON VÉRIFIÉ** — aucune requête effectuée |
| Baseline chiffrée des tests | **NON EXÉCUTÉE** |
| Journaux / limites d'exécution PRQ-6/7 | **PARTIELLEMENT PASS** — dossier de journaux hors dépôt créé (0700) ; `ulimit -c 0` et `umask 077` vérifiés dans le shell de préparation |

Runtime : le Node par défaut est v22.16.0, mais un runtime local déjà présent dans l'environnement (`/opt/pyvenv/lib/python3.13/site-packages/playwright/driver/node`) fournit **Node v24.11.1**. La contrainte `engines.node >=22.18.0` est satisfaite par ce runtime ; npm 10.9.2 fonctionne avec ce PATH. Aucun téléchargement ni changement système n'a été effectué. Ce runtime alternatif est local à cette session et doit être explicitement sélectionné pour les commandes projet.\n\nRelevé ponctuel actualisé : mémoire disponible 4 769 880 kB ; espace disque libre sur le volume de travail 29 460 236 kB. Ces mesures dépassent les seuils de lancement P86 à ce relevé, mais doivent être contrôlées avant chaque suite.\n\nRépertoires isolés préparés hors du dépôt : `/mnt/data/PHARMOS_STOCK_MISSION_20261128/tmp` (TMPDIR) et `/mnt/data/PHARMOS_STOCK_MISSION_20261128/logs` (journaux), tous deux en permissions 0700. Aucun fichier existant n'a été supprimé ou déplacé. Le shell de préparation a vérifié `ulimit -c 0` et `umask 077`.\n\nDépendances : aucun cache npm disponible et l'accès DNS/réseau vers `nodejs.org` est indisponible dans cet environnement ; aucune installation n'a été tentée pour éviter une installation partielle non reproductible.

Conformément à P86 §8.2, aucune suite n'est lancée tant que les prérequis applicables ne sont pas satisfaits.

## 3. Constat statique consolidé — RES-2

Les points d'entrée réellement présents sont confirmés :

- V1 : `pharmos/ui/public/on-premise/js/catalogue.js` et `reference.js`, chargés par `index.html`.
- V3 : `CatalogForms.jsx` et `ReferenceCatalog.jsx`, utilisés depuis la page `Medicaments.jsx`.

Les quatre formulaires de création/adoption proposent actuellement un suivi par lot non activé par défaut :
- V1 catalogue : case `cat-f-lots` sans attribut `checked`.
- V1 adoption du catalogue national : case `ref-f-lots` sans attribut `checked`.
- V3 création : état initial `tracksLots: false`.
- V3 adoption : état initial `tracksLots: false`.

Les deux chemins serveur `createProduct` et `adoptReference` dans `pharmos/src/catalog/service.ts` convertissent la valeur par `flag(...)` et l'enregistrent ; le contrôle final DP2-R1 imposant le suivi par lot pour MÉDICAMENT n'est pas présent dans les chemins examinés.

**Conclusion statique RES-2 :** l'écart concerne les quatre formulaires et les deux opérations serveur, pas seulement la case V3 de création.

## 4. Constat statique consolidé — RES-1

- `pharmos/src/stock/service.ts` ne contient pas les états de lot `PERTE`, `QUARANTAINE`, `EN_CONTROLE`, ni les opérations de transition attendues.
- `pharmos/src/entry/stockRoutes.ts` expose les routes actuelles de produits, réceptions, sorties et ajustements ; aucune route de transition d'état / PERTE n'est présente.
- Le dépôt restauré contient les migrations pharmacie `0001` à `0021` ; aucune migration candidate `0022` n'a été trouvée dans l'arbre actuel ni dans l'historique Git examiné.

**Conclusion statique RES-1 :** le parcours PERTE ne peut pas être ajouté comme simple formulaire isolé ; le service, les routes et le schéma candidat sont à traiter selon P92, sans promotion de migration.

## 5. Empreintes préliminaires des fichiers examinés

Ces empreintes sont des relevés de départ informatifs ; elles ne constituent pas le manifeste M0, qui ne pourra être établi qu'après franchissement de PR-0.

| Fichier | SHA-256 |
|---|---|
| `pharmos/src/catalog/service.ts` | `fc00f1bd0e9c0ddfd4ec517108ed9ba90388625f6b0e9fc188dce965814d97c2` |
| `pharmos/src/stock/service.ts` | `10d512e7bf4c7ec9a94f00030c35a90eb0ab96dedff824643636930b5c64fc4f` |
| `pharmos/src/entry/stockRoutes.ts` | `84e6236f17f62336833d7ca8f75a8d752eecedaa54a8ff5aedae916fee4af627` |
| `pharmos/src/entry/catalogRoutes.ts` | `e03ace7918e1b9b02da984d0f69d84b2aef27cfc8ca1c308b4ad0c5e3868ec00` |
| `pharmos/ui/public/on-premise/js/catalogue.js` | `d16f389cad375e2741fc227740e4b4b572268385059c3e6a75f2d7a337c4041a` |
| `pharmos/ui/public/on-premise/js/reference.js` | `03ab1ec8ba87e9cfd856ed31f798f8f35b6148980b805c5d777e78b62dee7fc8` |
| `pharmos/ui/saas-src/src/components/CatalogForms.jsx` | `738b350791d1ff49c52bfc9b2b4503e4d171e581cdc64e4fcc32f6344a259228` |
| `pharmos/ui/saas-src/src/components/ReferenceCatalog.jsx` | `1d34bf2566331114a80de97c6e7513e401ca487715536d7c7e6715f1bf10008d` |
| `pharmos/ui/saas-src/src/pages/Medicaments.jsx` | `d0e5a7109db2553d792d18859bc9df6750b32370a81680b6f0f63274f1b74af6` |
| `pharmos/migrations/pharmacy/0021_commandes_catalogue_national.sql` | `1a2f1eb826ff2c3aa8e670ad1335583bf4337e82932cf3025510c924806ba55c` |

## 6. Limites et suite

- Aucun code source modifié.
- Aucun test, build ou benchmark exécuté.
- Aucune migration 0022 créée ou appliquée.
- Aucune base DEMO/réelle, aucun service, PM2 ou systemd touché.
- Aucun commit/push de code ; le dépôt privé GitHub PharmOS n'est pas alimenté par cette opération.
- G6/G7 restent hors autorisation ; phase 2 et PC-1 restent suspendus.

**Suite autorisée :** poursuivre la préparation statique et lever le prérequis Node.js dans un environnement local isolé compatible. La réalisation et l'exécution des tests restent bloquées à PR-0 jusqu'à satisfaction de tous les prérequis P86 applicables.

---
**Conclusion :** la mémoire Git complète est restaurée et vérifiée ; le diagnostic source est désormais fondé sur le véritable historique. Le blocage restant est environnemental et opérationnel, non une absence de code source ou de métadonnées Git.
