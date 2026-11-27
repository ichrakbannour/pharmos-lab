# P81 — Registre officiel des décisions PO relatives au STOCK

**Statut : DÉCISIONS CONFIRMÉES par le Product Owner (accord du 25/11/2026).** Document de référence pour la suite du module STOCK.
**Portée : décisions métier uniquement.** Ce registre n'autorise ni développement, ni migration, ni test, ni déploiement. Toute réalisation exige une **autorisation distincte** et une **mission distincte**.
Données fictives uniquement. Aucun secret ni code.

Documents historiques (non modifiés, restent la référence des analyses et des options écartées) :
- `docs/P81_ARBITRAGES_METIER_STOCK.md` (« la fiche », cité §n)
- `docs/P81_PLAN_MIGRATION_0022.md` (« le plan », cité §n)
- `docs/P81_RAPPORT_EXECUTION.md`

Libellés : **DÉCIDÉ** (confirmé par le PO) · **REPORTÉ** (décidé de ne pas traiter maintenant) · **OUVERT** (aucune décision, aucune hypothèse retenue) · **VIGILANCE** (précision technique à instruire, aucune décision prise ici) · **HORS PÉRIMÈTRE**.
Identifiants stables pour renvoi dans les futurs plans et tests : `DP1-R1`…, `C1`…, `O-n`.

---

## 1. Synthèse

| Réf. | Objet | Décision |
|---|---|---|
| DP-1 | États du stock | Option **B** : quatre états DISPONIBLE, EN_CONTROLE, QUARANTAINE, PERTE |
| DP-2 | Traçabilité par lot | Option **A** : obligatoire pour le MÉDICAMENT, facultative ailleurs |
| DP-3 | Réservations | Option **A** : par produit ; lot choisi à la vente (FEFO) |
| DP-4 | Sorties manuelles | Réservations bloquantes avec dérogation ADMIN ; écart FEFO tracé ; périmés pour retrait ou destruction ; motifs normalisés |
| DP-5 | Entrées de stock | Option **A** : liste fermée de quatre opérations |
| DP-6 | Migration 0022 | Option **A** : préparation et essais sur copies isolées fictives seulement |
| C1 à C4 | Quatre conséquences complémentaires | Confirmées (§8) |

Points toujours **OUVERTS** : O-5, O-6, O-9, O-10, O-11 (§9). Aucune décision n'est inventée pour eux.

---

## 2. DP-1 — États du stock *(fiche §2)*

**Option retenue : B — quatre états.** DISPONIBLE · EN_CONTROLE · QUARANTAINE · PERTE.

| Réf. | Décision | Statut |
|---|---|---|
| DP1-R1 | Les quatre états ci-dessus sont les seuls états de stock. | DÉCIDÉ |
| DP1-R2 | Tout passage entre états est tracé avec **quantité, motif, auteur et date**. | DÉCIDÉ |
| DP1-R3 | La **levée d'une QUARANTAINE** est réservée au **pharmacien titulaire ou à l'ADMIN**. | DÉCIDÉ |
| DP1-R4 | **PERTE est définitive** : aucun retour de PERTE vers DISPONIBLE (ni vers un autre état). | DÉCIDÉ |
| DP1-R5 | Un **rappel de lot bloque le lot**, **sans transfert automatique** de quantité ; un lot bloqué ou rappelé n'est jamais alloué ni vendu. | DÉCIDÉ |
| DP1-R6 | Le **stock par état est calculé à partir des mouvements** ; pas de seconde source de vérité. | DÉCIDÉ |
| DP1-R7 | Les **sorties de QUARANTAINE** et la **destruction des quantités en PERTE** sont **reportées à un lot ultérieur**. | REPORTÉ |
| DP1-R8 | **ST-04** est interprété comme un rapprochement **calculé à partir des mouvements et des passages d'état**. | DÉCIDÉ |
| DP1-R9 | **CAT-11** : la formule proposée en P81 est acceptée (ci-dessous), **sous réserve de tests de cohérence préparés avant réalisation** (voir C4). | DÉCIDÉ |
| DP1-R10 | **Corrections d'inventaire et contre-passations** : elles portent sur le **stock physique**, pas uniquement sur le disponible (modalités : C3). | DÉCIDÉ |
| DP1-R11 | **Alertes de péremption** : **oui**, y compris pour les quantités en QUARANTAINE ou en PERTE **tant qu'elles sont physiquement détenues**. | DÉCIDÉ |

**Passages entre états retenus** (matrice de la fiche §2, acceptée avec les règles ci-dessus) :

| Depuis \ Vers | DISPONIBLE | EN_CONTROLE | QUARANTAINE | PERTE |
|---|---|---|---|---|
| **DISPONIBLE** | — | oui | oui | oui |
| **EN_CONTROLE** | oui | — | oui | oui |
| **QUARANTAINE** | oui (levée : pharmacien titulaire ou ADMIN, DP1-R3) | non | — | oui |
| **PERTE** | non (DP1-R4) | non | non | — |

Un passage **ne change pas le stock physique** ; une quantité d'un même lot peut être répartie entre plusieurs états. Le statut de lot (ACTIF, BLOQUE, RAPPELE ; périmé et épuisé calculés) est distinct de l'état de la quantité.

**Formule CAT-11 acceptée (reprise de la fiche §2) :**
« reçu + corrections nettes = disponible + en contrôle + quarantaine + perte + sorties nettes », où **« reçu » = réceptions et régularisations seulement** ; les retours clients et les contre-passations de réception sont comptés dans les mouvements nets, pas dans « reçu ».

---

## 3. DP-2 — Traçabilité par lot *(fiche §3)*

**Option retenue : A.**

| Réf. | Décision | Statut |
|---|---|---|
| DP2-R1 | Suivi par lot **obligatoire pour le type MÉDICAMENT** (création et adoption). | DÉCIDÉ |
| DP2-R2 | **Dispositifs médicaux** : suivi **facultatif, désactivé par défaut**. | DÉCIDÉ |
| DP2-R3 | **Autres types** : suivi **facultatif**. | DÉCIDÉ |
| DP2-R4 | Les médicaments **existants sans suivi par lot** sont **signalés « à régulariser »** ; **aucune conversion automatique** de leur historique. | DÉCIDÉ |

Rappel factuel (fiche §3) : le numéro de lot et la péremption se saisissent à la **réception** ; seule l'activation du suivi est imposée à la création ou à l'adoption.

---

## 4. DP-3 — Réservations *(fiche §4)*

**Option retenue : A — réservation par produit.**

| Réf. | Décision | Statut |
|---|---|---|
| DP3-R1 | La réservation porte sur un **produit** ; le **lot est choisi au moment de la vente selon FEFO**. | DÉCIDÉ |
| DP3-R2 | **CAT-10 est reformulé** : « FEFO avec un lot en contrôle, un lot rappelé et une réservation : seul le lot **disponible, non bloqué, non périmé**, de péremption la plus proche, est alloué, **après déduction des réservations du produit** ». | DÉCIDÉ |
| DP3-R3 | Si un **rappel ou une mise en quarantaine** rend une réservation **impossible à honorer**, la **sécurité sanitaire prime** et la réservation est **signalée à son auteur**. | DÉCIDÉ |

---

## 5. DP-4 — Sorties manuelles *(fiche §5)*

| Réf. | Décision | Statut |
|---|---|---|
| DP4-R1 | Les **réservations sont bloquantes** pour une sortie manuelle. | DÉCIDÉ |
| DP4-R2 | **Dérogation ADMIN**, **motivée et tracée**. | DÉCIDÉ |
| DP4-R3 | **FEFO manuel** : un écart est **autorisé**, mais **toujours tracé**. | DÉCIDÉ |
| DP4-R4 | **Lots périmés** : sortie **uniquement pour retrait ou destruction**. | DÉCIDÉ |
| DP4-R5 | **Motifs normalisés** : casse, péremption, rappel, usage interne, autre **avec précision**. | DÉCIDÉ |
| DP4-R6 | Les **passages en QUARANTAINE ou en PERTE ne doivent pas être empêchés par une réservation**. | DÉCIDÉ |

La forme technique des motifs normalisés (colonne dédiée ou convention) n'est **pas** décidée : voir O-11.

---

## 6. DP-5 — Entrées de stock *(fiche §6)*

**Option retenue : A — liste fermée de quatre opérations.** Toute autre augmentation du stock physique est refusée.

| Réf. | Opération autorisée à augmenter le stock | Statut |
|---|---|---|
| DP5-R1 | **Réception physique** (commande ou livraison). | DÉCIDÉ |
| DP5-R2 | **Régularisation d'entrée hors commande**, avec **justificatif obligatoire** et **droit distinct**. | DÉCIDÉ |
| DP5-R3 | **Correction positive d'inventaire**, **justifiée et contrôlée**. | DÉCIDÉ |
| DP5-R4 | **Retour ou annulation de vente revendable**, **limité aux quantités vendues et aux lots d'origine**. | DÉCIDÉ |

| Réf. | Règle impérative | Statut |
|---|---|---|
| DP5-R5 | Une **commande fournisseur**, une **sélection dans le catalogue national**, une **adoption locale**, une **réservation** et un **changement d'état** ne constituent **jamais** une entrée physique en stock et n'augmentent **jamais** le stock physique. | DÉCIDÉ (confirmée) |
| DP5-R6 | Le **retour client en contrôle d'abord** est reporté à un lot ultérieur consacré aux retours (voir O-6). | REPORTÉ |

---

## 7. DP-6 — Migration 0022 *(fiche §7 ; plan §8, §10)*

**Option retenue : A.** La **préparation** et les **essais** sont autorisés **uniquement sur copies isolées fictives**.

| Réf. | Décision | Statut |
|---|---|---|
| DP6-R1 | Fichier candidat **maintenu hors du dossier officiel des migrations**. | DÉCIDÉ |
| DP6-R2 | **Aucune application** à la DEMO ni à une base réelle. | DÉCIDÉ |
| DP6-R3 | **Aucun déploiement.** | DÉCIDÉ |
| DP6-R4 | **Contrôles d'intégrité avant et après chaque essai.** | DÉCIDÉ |
| DP6-R5 | **Arrêt immédiat** en cas d'anomalie. | DÉCIDÉ |
| DP6-R6 | **Retour arrière par abandon de la copie défaillante**, jamais par réparation improvisée. | DÉCIDÉ |
| DP6-R7 | La **promotion** de la migration dans le dossier officiel et son **application à la DEMO** sont **deux décisions distinctes, non autorisées par ce registre**. | DÉCIDÉ |

**Les huit conditions de la fiche §7 sont conservées** (texte de la fiche, inchangé). La condition 1 est désormais remplie par le présent registre pour DP-1 à DP-5 ; elle ne vaut pas autorisation de réaliser.

| N° | Condition conservée | Observation |
|---|---|---|
| 1 | DP-1 à DP-5 décidés par écrit ; sans DP-1, rien ne démarre. | Remplie par ce registre. La réalisation reste soumise à une autorisation distincte. |
| 2 | Essais uniquement sur bases temporaires fictives créées par les tests (jamais le répertoire DEMO, jamais de données réelles, jamais les fichiers de codes). | Conservée |
| 3 | Fichier de migration hors du dossier officiel (dossier « candidat » dédié) tant que la promotion n'est pas décidée ; promotion = décision distincte. | Conservée |
| 4 | Aucune application à la DEMO ni à une base réelle ; l'application à la DEMO exigerait d'abord l'accord sur la 0021 (ordre des migrations). | Conservée (voir O-9) |
| 5 | Contrôles d'intégrité avant et après (**plan §5** ; voir erratum E-1) ; arrêt immédiat si un contrôle échoue (**plan §8.2**). | Conservée |
| 6 | Ressources : un fichier de test à la fois, vérification du disque et de la mémoire avant chaque essai (disque à 97 %, 2 Go de RAM) ; aucune suite complète. | Conservée |
| 7 | Retour arrière prévu avant l'essai : la copie défaillante est abandonnée, jamais « réparée » ; aucun retour de code après usage des états sans restauration. | Conservée |
| 8 | Aucun commit, push ou publication manuel ; compte rendu complet avec commandes et résultats. | Conservée |

---

## 8. Quatre conséquences complémentaires confirmées

| Réf. | Conséquence | Statut |
|---|---|---|
| **C1 — Archivage** | L'archivage d'un produit est **interdit tant qu'une quantité physique subsiste, y compris en QUARANTAINE ou en PERTE**. Cela s'applique jusqu'au lot ultérieur qui traitera les sorties de QUARANTAINE et la destruction des pertes (DP1-R7). | DÉCIDÉ |
| **C2 — PERTE** | Un **indicateur « en attente de destruction »** est fourni, avec **quantité et ancienneté**. **Aucune destruction automatique.** | DÉCIDÉ |
| **C3 — Corrections et contre-passations** | Elles **préservent l'état réel des quantités**. Une **correction négative ne peut pas contourner la procédure de destruction**. Une **contre-passation tient compte de l'état actuel** des quantités concernées. | DÉCIDÉ (principe ; voir V-2) |
| **C4 — CAT-11** | Les **scénarios de cohérence sont préparés avant le code**, puis **exécutés exclusivement sur des copies fictives autorisées**. | DÉCIDÉ |

---

## 9. Points toujours ouverts (aucune décision, aucune hypothèse)

| Réf. | Point | Statut |
|---|---|---|
| **O-5** | Réception « à contrôler » (entrée directement en EN_CONTROLE). | **OUVERT** |
| **O-6** | Retour client en contrôle d'abord ; **reporté** à un lot ultérieur consacré aux retours. | **OUVERT / REPORTÉ** |
| **O-9** | État de la migration 0021 en DEMO : **non vérifié** (DEMO non consultée). | **OUVERT** |
| **O-10** | Prérequis du Lot 2 (critères CAT-14 à CAT-16) : non examiné. | **OUVERT** |
| **O-11** | **Forme technique des motifs normalisés** (colonne dédiée ou convention) : à traiter dans le plan technique. | **OUVERT** |

**Correspondance avec les points ouverts du plan (§9) :** O-1 (définition CAT-11) : traité par DP1-R9. O-2 (sortie depuis un état non disponible) : REPORTÉ, DP1-R7. O-3 (corrections et contre-passations) : principe décidé, DP1-R10 et C3 ; modalités en V-2. O-4 (alertes de péremption) : DÉCIDÉ, DP1-R11. O-7 (comportement du moteur sur `ADD COLUMN`) et O-8 (volumétrie) : questions **techniques** à lever lors du premier essai sur copie, **non** des décisions PO.

**Hors périmètre, inchangés** (non traités par ces arbitrages) : règles réglementaires de retour (D62, D63, D75), prépaiements patients (non spécifiés), fuseau horaire de la péremption (UTC aujourd'hui), réservation pour reliquat d'ordonnance.

---

## 10. Contrôle de cohérence

### 10.1 Fidélité aux sources P81

| Décision | Source P81 | Résultat |
|---|---|---|
| DP-1 (R1 à R11) | fiche §2 ; plan §3, §9 | Conforme : option B, matrice, droit de levée, CAT-11, ST-04, corrections et alertes repris de la fiche |
| DP-2 | fiche §3 | Conforme : option A, dispositif médical facultatif par défaut non, médicaments existants signalés |
| DP-3 | fiche §4 | Conforme : option A, CAT-10 reformulé mot pour mot, sécurité prioritaire |
| DP-4 | fiche §5 | Conforme : réservations A, FEFO manuel A, périmés A, liste de motifs de la fiche |
| DP-5 | fiche §6 | Conforme : option A, quatre opérations, règle impérative, retour en contrôle reporté |
| DP-6 | fiche §7 ; plan §8, §10 | Conforme : option A, huit conditions reprises, promotion et DEMO séparées |

### 10.2 Contradictions entre DP : aucune relevée

| Contrôle | Résultat |
|---|---|
| DP-3 (sécurité prioritaire) ↔ DP-4 (réservations bloquantes) | Compatibles : DP4-R6 exclut explicitement les passages en QUARANTAINE/PERTE du blocage |
| DP-1 (PERTE définitive, destruction reportée) ↔ DP-5 (liste fermée d'entrées) | Compatibles : aucune des quatre entrées ne cible un autre état que DISPONIBLE ; un passage d'état déplace une quantité sans créer d'entrée |
| DP-1 (stock par état calculé des mouvements) ↔ DP-5 (règle impérative) | Compatibles : un changement d'état déplace une quantité sans créer d'entrée |
| DP-2 (lot obligatoire médicament) ↔ DP-3 (lot choisi à la vente) | Compatibles : la réservation par produit présuppose le FEFO par lot, donc le suivi par lot |
| C1 (archivage interdit si physique non nul) ↔ DP1-R7 (destruction reportée) | Compatibles, avec conséquence connue : un produit gardant une quantité en QUARANTAINE ou PERTE reste non archivable jusqu'au lot ultérieur (exprimé dans C1) |
| DP-6 ↔ condition générale | Compatibles : préparation sur copies fictives seulement ; aucune réalisation sans autorisation distincte |

### 10.3 Points de vigilance (précisions techniques à instruire ; aucune décision prise ici)

| Réf. | Point | Où le traiter |
|---|---|---|
| **V-1** | Délimitation entre **DP4-R4** (sortie d'un lot périmé pour « retrait ou destruction ») et **DP1-R7 / C3** (destruction des pertes reportée, correction négative ne contournant pas la procédure de destruction). Lecture de cohérence : DP-4 régit les **sorties depuis DISPONIBLE** avec un motif normalisé ; les sorties depuis QUARANTAINE ou PERTE restent reportées. Cette lecture n'est pas une décision nouvelle : elle doit être **confirmée par le PO** si le plan technique la retient. | Plan technique (avec O-11) |
| **V-2** | **État visé** par une correction négative et par une contre-passation (C3) : l'état exact écrit, et le traitement d'une quantité qui n'est plus en DISPONIBLE, ne sont pas précisés. Aucune règle n'est retenue ici. | Plan technique |

### 10.4 Erratum aux documents historiques (non corrigés dans ces documents)
**E-1.** La fiche (`P81_ARBITRAGES_METIER_STOCK.md`, condition 5 de DP-6) renvoie à « plan §4 » pour les contrôles d'intégrité et à « plan §7 » pour l'arrêt immédiat. Dans le plan, les contrôles sont au **§5** (`## 5. Contrôles d'intégrité avant / après`) et les conditions d'arrêt au **§8.2**. Le §4 traite la stratégie de migration des données et le §7 la non-régression. Même renvoi dans la fiche, risque de DP-1 : « voir plan §7 » désigne en réalité le **§8** (retour arrière). Le fond des conditions n'est pas affecté. Les documents historiques restent inchangés ; ce registre fait foi pour les numéros de section.

---

## 11. Ce que ce registre n'autorise pas

Aucun changement de code ; aucune création ni exécution de migration ; aucune modification de base de données, même de test ; aucun test ; aucune intervention sur la DEMO ou son service ; aucune **promotion** de migration ; aucun **déploiement** ; aucune opération Git manuelle ; aucun nettoyage, suppression ou réparation du serveur.

**Condition générale :** aucun développement ni aucune migration ne commence avant une **autorisation distincte** et la préparation d'une **mission distincte**. Le registre n'engage aucune mission suivante.

## 12. Évolution du registre
Toute modification d'une décision ci-dessus exige une décision écrite du PO et une **nouvelle version** du registre (addendum daté) ; les entrées existantes ne sont pas effacées. Les points OUVERTS ne passent à DÉCIDÉ que sur décision écrite du PO.
