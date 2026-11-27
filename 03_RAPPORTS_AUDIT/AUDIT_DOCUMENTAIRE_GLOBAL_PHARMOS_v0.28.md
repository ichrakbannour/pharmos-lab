# PHARM'OS — Audit documentaire global v0.28

**Date :** 27/11/2026  
**Périmètre :** nettoyage du registre historique des constats AUD-052 à AUD-064 par rapprochement des identifiants, clôtures déjà documentées et points encore ouverts.  
**Nature :** consolidation documentaire ; aucune action technique.

## 1. Résultat exécutif

Les constats historiques AUD-052, AUD-053 et AUD-054 du rapport v0.10 décrivent les mêmes sujets que les constats ultérieurs AUD-062, AUD-063 et AUD-064. Ils sont donc **clos comme doublons/supersédés**, sans effacer leurs rapports d'origine.

| Ancien constat | Constat de suivi | État actuel | Motif |
|---|---|---|---|
| AUD-052 — droit stock.return.decide attribué par défaut dans EXT05 | AUD-062 | **Clos** | EXT05 corrigée et moteur de droits EXT02 recroisé ; octroi individuel explicite, aucun droit par défaut |
| AUD-053 — D74 encore « à arbitrer » dans EXT06 | AUD-063 | **Clos pour EXT06 canonique v1.0** | §5.2 et §11.1 alignés sur D74 et la matrice v1.3.4 ; EXT06 v1.1 historique conservée sans modification |
| AUD-054 — règles D75 insuffisamment propagées dans EXT05 | AUD-064 | **Clos documentairement** | renvoi EXT05 et règles détaillées EXT06 recroisés ; tests applicatifs non exécutés |

Les identifiants historiques ne sont pas supprimés : les rapports v0.10 à v0.18 restent des instantanés datés.

## 2. État consolidé des autres constats connus

- **AUD-055 / AUD-056** : clos au niveau de l'arbitrage INC-06 et de sa propagation documentaire (rapport v0.25).
- **AUD-057** : clos pour la lecture courante de D40 via l'addendum de conception (rapport v0.23) ; conception v1.2.1 historique non réécrite.
- **AUD-058 / AUD-061** : clos pour la publication et la traçabilité de la matrice canonique et du miroir de 42 sources (rapport v0.14).
- **AUD-059 / AUD-060** : clos pour leur périmètre documentaire ; GO P0 distinct de STOCK P88 et statut C-01 à C-08 actualisé au 27/11/2026 (rapport v0.27).
- **AUD-062 / AUD-063 / AUD-064** : clos dans les limites indiquées ci-dessus (rapport v0.25).

## 3. Points métier et opérationnels qui restent ouverts

La clôture des constats documentaires ne clôt pas les décisions métier ou les autorisations suivantes :

1. **O-5, O-6, O-9 et O-10 STOCK** : maintenus explicitement OUVERTS par la décision PO P86-12 consignée dans P87. Aucun n'est réputé décidé par le présent rapport.
2. **V-3 / CAT-11** : NON APPROUVÉ ; lecture du PO toujours requise.
3. **P88 / STOCK 0022** : migration 0022 non créée ; G6 non proposée/promue ; P88 non relancée ; phase 2 suspendue ; P91 n'a exécuté aucun palier.
4. **INC-06 / R23–R33** : arbitrage de périmètre clos par option B ; vérifications réglementaires/conventionnelles encore ouvertes ; CNAM/mutuelles exclues de la v1.
5. **D45–D58** : propositions détaillées non globalement approuvées ; D44 seule est arbitrée.
6. **GO P0** : non autorisé ; C-01 à C-08 non remplies selon la revue du 27/11/2026.

## 4. Limites de cette clôture

- « Clos » signifie que le constat documentaire a été corrigé, clarifié ou remplacé par un constat de suivi.
- Cela ne signifie pas que les fonctionnalités correspondantes sont développées, testées ou validées dans l'application.
- Les permissions réellement appliquées, les calculs D75, les parcours stock, les exigences réglementaires et les sauvegardes restent sans preuve applicative nouvelle dans ce rapport.
- Aucun code, schéma, base, test, migration, déploiement, service système ou environnement DEMO n'a été modifié.

---
**Fin du rapport v0.28.**
