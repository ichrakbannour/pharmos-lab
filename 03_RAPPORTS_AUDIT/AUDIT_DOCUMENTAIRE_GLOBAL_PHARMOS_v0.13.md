# PHARM'OS — Audit documentaire global v0.13

**Date :** 27/11/2026  
**Complément de :** audits documentaires globaux v0.10 à v0.12.  
**Périmètre :** GO P0 v1.3.2, plan directeur v1.3.2, registre des actions v1.3.2, P88 (incluant mise à jour P90), P91 et index du dossier Décisions PO.  
**Nature :** audit documentaire des autorisations, portes de validation et statuts de réalisation. Aucun code, aucune migration, aucun test, aucune opération système.

## 1. Verdict exécutif

Trois périmètres doivent rester strictement distincts :

1. **GO P0 — prototypes et validation d'architecture** : orientation conditionnelle proposée dans les documents v1.3.2 ; aucune preuve consultée ne démontre l'autorisation écrite C-01, le choix du scénario C-02 ou l'approbation des protocoles C-04.
2. **P88 — réalisation du chantier STOCK 0022** : GO conditionnel PO distinct du GO P0, daté du 25/11/2026 et encadré par les contrôles P84/P86/P87. Le préflight R0 a été bloqué par PRQ-3 ; la reprise Option A s'est arrêtée pendant R0d.
3. **P91 — préparation Option B** : mission préparatoire uniquement ; aucun palier exécuté, aucune suppression, aucun déplacement, aucune copie et aucune libération mémoire.

**Conclusion : aucun GO P0 ne peut être déduit du GO conditionnel P88. Aucune étape de réalisation P88 n'est validée et P91 n'a pas modifié l'état du système.**

Le dossier contient aussi une formulation d'index devenue inexacte : les 42 sources originales sont bien présentes individuellement dans le miroir d'archive 07_SOURCES_ORIGINALES, même si elles ne sont pas toutes recopiées au niveau canonique de 04_DECISIONS_PO.

## 2. GO P0 — statut documentaire

### 2.1 Conditions C-01 à C-08

Le tableau GO P0 v1.3.2, daté du 04/11/2026, indique explicitement :
- GO P0 CONDITIONNEL : orientation proposée, non autorisation ;
- aucune condition C-01 à C-08 déclarée remplie à la date du document ;
- C-01 : autorisation écrite du propriétaire fixant le périmètre de P0, les prototypes autorisés, l'isolation du dépôt applicatif et l'usage exclusif de données fictives ;
- C-02 : choix écrit d'un scénario S1, S2 ou S3 ;
- C-04 : protocole approuvé avant l'exécution de chaque prototype ;
- C-05 : données exclusivement fictives ;
- C-07 : preuves archivées pour chaque prototype ;
- C-08 : décisions écrites distinctes pour ARB-01 et la validation de l'architecture.

Les conditions C-03 et C-06 s'appliquent selon les prototypes et niveaux précisés dans le tableau ; elles ne doivent pas être transformées en conditions universelles d'ouverture de P0.

### 2.2 Registre des actions

Le registre v1.3.2 maintient notamment :
- A-PRO-01 (autorisation et périmètre de P0) : ouverte ;
- A-PRO-02 (choix du scénario) : ouverte ;
- A-PRO-20 (désignation nominative des rôles P0) : ouverte ;
- A-TEC-02 (protocoles approuvés) : bloquée pour G1, G2 et PT-13 selon les dépendances propres à chaque groupe ;
- A-PRO-15 (ARB-01) : bloquée par le rapport PT-13 et l'avis R10 ;
- A-PRO-21 (validation formelle de l'architecture) : bloquée par A-PRO-15, A-PRO-14, A-TEC-17 et A-SEC-01.

Le registre se termine en indiquant qu'aucune action n'est réalisée à la date de sa version. Aucune mise à jour plus récente du plan directeur, du tableau GO P0 ou du registre d'actions n'a été identifiée dans l'arborescence consultée.

### 2.3 État au 27/11/2026 : prudence sur les preuves

Le statut « aucune condition remplie » du 04/11 est une photographie datée, pas une preuve autonome de l'état au 27/11. Les pièces plus récentes consultées (P88/P90/P91) ne documentent pas la satisfaction des conditions C-01 à C-08 du GO P0.

En conséquence, le verdict prudent au 27/11 est :
**autorisation P0 non démontrée dans le corpus consulté ; aucun prototype P0 ne doit être considéré comme autorisé sur cette base.**

## 3. P88 / P90 — GO conditionnel STOCK et arrêt effectif

### 3.1 Nature de l'autorisation

P88 consigne un GO conditionnel du PO du 25/11/2026 pour le chantier de réalisation STOCK issu de P86, après arbitrage de RES-1 et RES-2. Il rappelle expressément que cette autorisation ne permet pas de contourner les contrôles, gels, critères d'acceptation et limites antérieures.

Ce GO porte sur le périmètre STOCK et ne remplace pas :
- C-01 et C-02 du GO P0 ;
- l'approbation des protocoles C-04 ;
- les décisions ARB-01 et de validation d'architecture ;
- l'autorisation d'ouvrir le Lot 0 ou les lots fonctionnels.

### 3.2 Préflight R0

P88 rapporte :
- disque libre : 1 129 200 Ko, supérieur au plancher de lancement de 1 000 000 Ko ;
- sauvegarde M0 et auto-backup Git constatés ;
- restauration de l'archive M0 : non testée ;
- DEMO intacte et /health OK ;
- 21 migrations officielles, dernière 0021 ;
- MemAvailable : 489 648 kB, puis 497 432–497 900 kB, sous le plancher PRQ-3 de 500 000 kB.

**Verdict R0 : BLOQUÉ.** Aucune modification technique et aucune suite de tests n'ont été lancées à cette étape.

### 3.3 Reprise P90 — Option A

La mise à jour P90 de P88 rapporte :
- cinq relevés consécutifs au-dessus de 500 000 kB avant la tentative ;
- R0d lancé sur un seul fichier parmi 58 ;
- arrêt après huit secondes lorsque MemAvailable est tombée à 470 860 kB ;
- zéro fichier terminé, zéro résultat de test ;
- R1, R2, R3, R4a–R4e, P86-V1, R7a, R5, PC-1 et R6 non exécutés ;
- migration 0022 non créée ;
- G6 non proposée et non promue ;
- C1–C9 non évalués ; porte PR-0 non franchie ;
- phase 2 suspendue ;
- DEMO intacte.

La baisse de mémoire observée pendant ce fichier ne démontre pas que tous les tests ont le même profil de consommation. Elle suffit néanmoins à établir que cette tentative Option A n'a pas pu franchir R0d en respectant le seuil.

## 4. P91 — Option B : préparation sans exécution

P91 confirme :
- mission exclusivement préparatoire ;
- aucun palier exécuté ;
- aucun fichier de /tmp supprimé, déplacé ou modifié ;
- aucun nettoyage ni copie durable créée ;
- résidu P90 conservé ; codes.json non ouvert ;
- archives P76/P77/P78 à préserver ;
- archive /tmp/backup/webapp_backup_ir3ywwou.tar.gz tronquée, origine non documentée, aucune copie durable ;
- l'Option B ne peut être exécutée sans décisions PO distinctes sur les éléments concernés.

La clôture P91 rappelle explicitement : P88 non relancée, migration 0022 non créée, G6 non proposée, phase 2 suspendue. P91 ne vaut donc ni GO de reprise ni preuve de libération mémoire.

## 5. Portes et dépendances — état consolidé

| Périmètre | Porte / condition | État démontré dans les sources consultées | Conséquence |
|---|---|---|---|
| GO P0 | C-01 autorisation écrite P0 | Non démontrée | P0 non autorisé sur la base du corpus |
| GO P0 | C-02 choix S1/S2/S3 | Non démontré | Séquencement non choisi |
| GO P0 | C-04 protocoles approuvés | Aucun protocole approuvé identifié | Aucun prototype exécutable sur cette base |
| GO P0 | C-07 preuves | Aucun prototype exécuté dans le registre v1.3.2 | Pas de preuve d'acceptation |
| GO P0 | C-08 décisions ARB-01 / architecture | Non démontrées | Architecture et Lot 0 non autorisés |
| STOCK P88 | R0 / PRQ-3 | Bloqué par mémoire sous 500 000 kB | Pas de réalisation |
| STOCK P90 | R0d | Interrompu ; 0 fichier terminé, 0 résultat | Aucune étape P88 validée |
| STOCK P88 | Migration 0022 | Non créée | Aucune migration à considérer comme appliquée |
| STOCK P88 | G6 | Non proposée, non promue | Aucun jalon G6 acquis |
| STOCK P91 | Option B | Préparée uniquement | Aucun palier ni nettoyage autorisé |

## 6. Index du dossier PO — précision de publication

Le fichier 04_DECISIONS_PO/README.md affirme que les 42 fichiers sources ne sont pas encore publiés individuellement dans le dépôt. Cette phrase est trop générale au regard de l'arborescence actuelle : les 42 fichiers sont présents individuellement dans le miroir 07_SOURCES_ORIGINALES/EXPORT_SOURCES_AUDIT_PHARMOS_20261127/.

La distinction exacte est :
- **miroir d'archive** : 42 sources originales publiées individuellement et précédemment contrôlées octet par octet ;
- **dossier canonique 04_DECISIONS_PO** : seules certaines pièces sont copiées à ce niveau ; la matrice D1–D86 v1.3.3 n'y est pas encore publiée comme fichier canonique.

Le présent audit recommande de corriger uniquement la formulation de l'index, sans déplacer ni réécrire les sources originales.

## 7. Registre des constats

| ID | Constat | Statut |
|---|---|---|
| **AUD-059** | Le GO conditionnel P88 STOCK pourrait être confondu avec le GO P0 prototypes alors que leurs périmètres et conditions sont distincts | Ouvert — clarification de gouvernance |
| **AUD-060** | La photographie « aucune condition C-01 à C-08 remplie » date du 04/11 ; les sources postérieures consultées ne démontrent pas leur satisfaction au 27/11 | Ouvert — statut P0 non actualisé par preuve |
| **AUD-061** | L'index PO affirme que les 42 sources ne sont pas publiées individuellement dans le dépôt, alors qu'elles existent dans le miroir d'archive ; la matrice v1.3.3 reste absente du dossier canonique PO | Ouvert — formulation et traçabilité à préciser |

## 8. Actions documentaires recommandées

1. Maintenir deux registres de portes distincts : GO P0 prototypes et GO conditionnel STOCK P88.
2. Ne jamais considérer le GO P88 comme preuve de C-01, C-02 ou C-04 du GO P0.
3. Avant toute reprise P88, attendre une nouvelle décision PO et les conditions techniques explicitement exigées ; P91 ne vaut pas autorisation de palier.
4. Actualiser le tableau GO P0 et le registre d'actions uniquement après réception de preuves datées pour C-01 à C-08 ; ne pas remplacer leur statut sur simple déduction.
5. Corriger l'index PO pour distinguer le miroir des 42 originaux et les copies canoniques du dossier 04.
6. Maintenir la matrice v1.3.3 comme référence PO vérifiée, en préservant les versions historiques et sans altérer le miroir original.

Aucune action technique ni aucune action sur /tmp, DEMO, PM2, migrations, fichiers de sauvegarde ou services n'est autorisée par ce rapport.

## 9. Sources vérifiées

- 03_RAPPORTS_AUDIT/PLAN_DIRECTEUR_FINALISATION_PHARMOS_v1.3.2.md — RÉF-1 à RÉF-8, jalons et interdictions.
- 03_RAPPORTS_AUDIT/TABLEAU_SYNTHESE_GO_P0_PHARMOS_v1.3.2.md — statut GO P0, conditions C-01 à C-08, groupes de prototypes et portes.
- 03_RAPPORTS_AUDIT/REGISTRE_ACTIONS_PHARMOS_v1.3.2.md — A-PRO-01, A-PRO-02, A-PRO-15, A-PRO-20, A-PRO-21, A-TEC-02, A-TEC-13, A-JUR-01 et statut général des actions.
- 07_SOURCES_ORIGINALES/EXPORT_SOURCES_AUDIT_PHARMOS_20261127/4_DECISIONS_STOCK_P82_P91/P88_RAPPORT_EXECUTION.md — GO conditionnel, R0, mise à jour P90 et statuts exacts.
- 07_SOURCES_ORIGINALES/EXPORT_SOURCES_AUDIT_PHARMOS_20261127/4_DECISIONS_STOCK_P82_P91/P91_RAPPORT_PREPARATION_OPTION_B.md — préparation, paliers, interdictions et clôture.
- 04_DECISIONS_PO/README.md et arborescence GitHub main — distinction entre miroir d'archive et dossier canonique.
