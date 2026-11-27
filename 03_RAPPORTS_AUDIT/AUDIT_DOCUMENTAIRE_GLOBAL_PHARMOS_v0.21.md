# PHARM'OS — Audit documentaire global v0.21
**Date :** 27/11/2026  
**Périmètre :** EXT06 v1.0 canonique — consolidation fonctionnelle D72, D77, D84 et contrôle des garanties D73, D75, D79, D80.  
**Référence PO :** matrice D1–D86 v1.3.3.  
**Nature :** consolidation documentaire. Aucun changement applicatif.

## 1. Résultat exécutif

La consolidation demandée est effectuée dans EXT06 v1.0, au-delà du registre de réconciliation §11.1 : les décisions et garanties ont été propagées dans les workflows, principes, schémas, règles de permissions et critères de navigation.

**D72, D77 et D84 sont consolidées dans le corps fonctionnel d'EXT06.** Les garanties D73, D75, D79 et D80 ont également été reprises et contrôlées dans les sections correspondantes.

Le registre §11 conserve les formulations historiques du document initial ; elles ne sont pas présentées comme l'état courant. Le §11.1 a été mis à jour pour indiquer les consolidations effectuées et les preuves techniques restant à produire.

## 2. D72 — Gouvernance globale SUPERADMIN APEX

### Consolidation
- Ajout du workflow W0 dédié au compte SUPERADMIN APEX immuable `apexproib@gmail.com`.
- Périmètre de plateforme explicité : SaaS et On-Premises, comptes administrateurs de pharmacies, licences, abonnements, paramètres et supervision.
- Pouvoir de créer, bloquer, supprimer et recréer les comptes d'administration propres à chaque pharmacie.
- Identifiants distincts par pharmacie ; code initial généré automatiquement ; changement du mot de passe à la première connexion.
- Rappel de l'isolation physique des bases par pharmacie (D3).
- Séparation explicite entre suppression/régénération d'un accès et suppression des données métier ; aucun historique n'est supprimé automatiquement.
- Récupération renforcée du SUPERADMIN sans rendre le compte modifiable ou supprimable.
- Ajout de la traçabilité des opérations de gouvernance dans `platform_audit_log` et distinction d'une éventuelle demande de suppression de données via `tenant_data_deletion_requests`.
- Retrait de `SECOURS_APEX` comme mode de désignation d'un administrateur de pharmacie dans `tenant_administrators` : la récupération du SUPERADMIN n'est pas un transfert d'administrateur local.

### Point volontairement non inventé
La procédure historique de « secours APEX » pour remplacer un pharmacien-administrateur local n'est pas l'arbitrage D72. Elle est dissociée et reste à spécifier séparément si elle est encore nécessaire. Aucun accès APEX aux données métier n'est déduit du rôle SUPERADMIN.

## 3. D77 — Accueil fondé sur les permissions effectives

- Le critère NV6 a été corrigé : l'accueil après connexion dépend du rôle **et des permissions effectives**, pas du rôle seul.
- Le §6.3 précise que les tuiles, menus, raccourcis et actions visibles sont filtrés après prise en compte des rôles, octrois/retraits individuels, délégations valides, habilitations, qualifications, présence, plafonds et séparations de tâches.
- Les préférences utilisateur peuvent réorganiser ou masquer les éléments autorisés, mais ne peuvent ni faire apparaître un module interdit ni créer un droit.
- Les contrôles côté serveur restent obligatoires pour chaque route et action.

Le registre historique §11 conserve sa formulation d'origine ; le §11.1 indique désormais que la règle courante est consolidée dans NV6 et §6.3.

## 4. D84 — Classification thérapeutique standard initiale

- L'axe `FAMILLE_THERAPEUTIQUE` reçoit une classification thérapeutique standard initiale.
- La classification est modifiable par l'administrateur de pharmacie.
- Elle est explicitement distincte des champs et référentiels réglementaires officiels.
- Le schéma `classification_nodes` identifie la source du nœud ; `MODELE_STANDARD` est distingué de `OFFICIEL`, `LOCAL` et `APEX`.
- Aucun référentiel officiel ou ATC n'est présumé : les sources réglementaires éventuelles restent soumises à R43.

La composition détaillée du jeu initial de nœuds devra être spécifiée lors de la préparation des données de référence ; elle n'est pas inventée par le présent audit.

## 5. D73 — Dérogation à distance

Contrôle de la propagation effectué dans W4 et `permission_exceptions` :
- décision prise par l'administrateur lui-même, jamais par substitution ;
- appareil enrôlé et MFA renforcée au moment de la décision ;
- confirmation explicite, motif obligatoire et opération précisément identifiée ;
- usage unique et journalisation ;
- mode B prévu pour l'accès distant sécurisé au serveur local On-Premises ;
- exclusion des opérations réglementées prévues par D73 ;
- si aucune validation présentielle ou distante conforme n'est disponible, l'opération reste bloquée ou est refusée.

Le schéma de `permission_exceptions` référence l'appareil et l'horodatage de vérification MFA. La conformité réelle du mode distant et l'absence de contournement restent à tester.

## 6. D75 — Jour calendaire et jour d'exploitation

Le §5.2 précise maintenant :
- jour calendaire par défaut, calculé à partir de l'horodatage serveur et du fuseau de l'officine ;
- modes alternatifs GARDES ou PERMANENTE uniquement sur activation explicite de l'administrateur ;
- motif obligatoire et MFA à l'activation et à chaque changement ;
- paramétrage documenté, versionné et journalisé ;
- prise d'effet non rétroactive ;
- absence de bascule ou modification silencieuse ;
- conservation de l'auteur, du motif, de la date d'effet et de la preuve MFA dans le paramétrage `settings` / `duty_periods`.

Le calcul effectif des périodes et l'absence de rétroactivité doivent être vérifiés par des tests lors d'une réalisation autorisée.

## 7. D79 — Confidentialité des marges, y compris G25

Le §5.8 impose `analytics.margin.read` pour toute donnée de marge, taux, agrégat ou classement fondé sur la marge, y compris G25. Le seul droit `ANALYTICS.READ` ne suffit pas.

La règle documentaire couvre l'interface, les exports, les API et les réponses agrégées côté serveur. L'absence de fuite par agrégats, exports ou endpoints doit être démontrée par tests autorisés ; aucun contrôle applicatif n'a été exécuté ici.

## 8. D80 — Pré-comptabilité M20

Contrôle et consolidation dans W13 et les schémas :
- libellé exact **« Comptabilité à titre indicatif »** sur tous les écrans, états et exports comptables ;
- une écriture validée et ses lignes deviennent immuables ;
- aucune modification ou suppression d'une écriture validée ;
- correction par contre-passation liée à l'écriture d'origine (`reverses_id`, `correlation_id`), puis nouvelle écriture si nécessaire ;
- contrôles débit/crédit et cohérence des lignes au sein du même batch atomique ;
- après verrouillage, correction dans la période ouverte suivante ; réouverture selon D82, jamais après export définitif.

Le schéma distingue les brouillons des écritures validées. L'immutabilité, les contre-passations et l'atomicité restent à démontrer par tests et contrôles techniques autorisés.

## 9. D83 — Plan comptable standard initial

La consolidation confirme :
- modèle standard fourni dès l'initialisation de la pharmacie ;
- usage indicatif dans M20 ;
- adaptation possible par l'administrateur ;
- historisation des modifications et contrôle de cohérence avec les règles de passation ;
- aucune validation préalable obligatoire d'un expert-comptable pour utiliser le modèle standard indicatif ;
- avis du comptable possible, mais non prérequis.

La permission `accounting.setup` a été corrigée pour ne plus imposer un mécanisme « administrateur + comptable (quatre yeux) » comme condition de disponibilité du plan standard.

## 10. Registre des résultats

| Décision | Consolidation documentaire | Preuve technique restante |
|---|---|---|
| D72 | **Effectuée** — W0, principes, schéma et traçabilité | Vérifier l'immutabilité du compte, les pouvoirs plateforme, l'isolation et l'absence de suppression indirecte des données |
| D73 | **Effectuée** — W4 et journal des dérogations | MFA step-up, appareil enrôlé, mode B On-Premises, usage unique et exclusions |
| D75 | **Effectuée** — §5.2 | Versionnement, journalisation, MFA, fuseau et non-rétroactivité |
| D77 | **Effectuée** — NV6 et §6.3 | Navigation réelle et refus serveur selon droits effectifs |
| D79 | **Effectuée** — §5.8, y compris G25 | Absence de fuite via UI, API, exports et agrégats |
| D80 | **Effectuée** — W13 et schémas M20 | Immutabilité, contre-passation, nouvelle écriture et atomicité |
| D83 | **Effectuée** — principes, schéma et permission `accounting.setup` | Initialisation, adaptation, historisation et cohérence |
| D84 | **Effectuée** — §4.4 et schémas de classification | Chargement du jeu initial et séparation des données officielles |

## 11. Éléments restant ouverts

1. La procédure exceptionnelle de remplacement d'un administrateur de pharmacie en dehors du transfert normal n'est pas définie par D72 ; elle ne doit pas être confondue avec la récupération du SUPERADMIN.
2. La liste concrète des nœuds du modèle thérapeutique standard initial reste à établir ; aucun référentiel réglementaire ou ATC n'est supposé.
3. Les validations réglementaires R42–R51 restent soumises à leurs sources et statuts propres.
4. Les huit décisions consolidées nécessitent des preuves applicatives et des tests ; le présent audit ne prétend pas qu'elles sont déjà implémentées.

## 12. Garanties de périmètre

- Matrice PO v1.3.3 inchangée ; aucun arbitrage PO créé, rouvert ou modifié.
- EXT06 v1.1 historique non réécrite.
- EXT06 v1.0 canonique mise à jour uniquement sur le plan documentaire.
- Aucun code, API, schéma de base réellement déployé, compte, droit effectif applicatif, test, état DEMO ou service système inspecté ou modifié.
- Aucune migration, opération système, nettoyage, sauvegarde, déploiement ou intervention PM2 exécuté.

---
**Fin du rapport v0.21.**
