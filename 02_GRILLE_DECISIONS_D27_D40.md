# PHARM'OS — EXT03 v1.1 — Grille des arbitrages D27 à D40 (+ D41 à D43 proposées)

**Statut** : soumis à validation du propriétaire (APEX AI DEV). Aucune décision n'est appliquée au dossier principal
tant que la colonne « Décision retenue » n'est pas renseignée.
**Référence** : `EXT03_ORDONNANCES_PRESCRIPTEURS_v1.1.md` (§ cités entre parenthèses).

Légende des modules : M02 Vente · M02b Caisse · M04 Catalogue · M05 Stock · M07 Clients · M08 Finance · M09 Réglementés ·
M11 Commandes · M12 Rapports · M13 IA · M14 Hors-ligne · M15 RH · M16 Laboratoire · M17 Ordonnances.

---

## Tableau de synthèse

| # | Sujet | Décision proposée | Orientation imposée | Décision retenue |
|---|---|---|---|---|
| D27 | Modèle du patient | Fiche client unique + volet patient séparé, cloisonné N1/N2/N3 | Oui | ☐ |
| D28 | Ordonnance sans patient identifié | Seulement si le profil pays le permet ; jamais réglementée / renouvelable | Oui | ☐ |
| D29 | Ordonnance incomplète | Classes A / M / R ; seul A différable | Oui | ☐ |
| D30 | Niveau de saisie | Prescrit + délivré, saisie accélérée | — | ☐ |
| D31 | Spécialité dans les statistiques | Snapshot à la date de prescription | Oui | ☐ |
| D32 | Référentiel des spécialités | Liste APEX + ajouts locaux mappés | — | ☐ |
| D33 | Scan des ordonnances | Obligatoire si exigé par le profil pays | — | ☐ |
| D34 | Petits effectifs | k = 5 + contrôles anti-croisement | — | ☐ |
| D35 | Prescripteur vers le LLM | Pseudonymisé | — | ☐ |
| D36 | Journal des accès sensibles | Table dédiée chaînée | — | ☐ |
| D37 | Tiers payant | Reporté, compatibilité préservée | Oui | ☐ |
| D38 | Posologie | Facultative, chiffrée, hors IA et rapports | — | ☐ |
| D39 | Hors ligne | 1re délivrance simple uniquement | — | ☐ |
| D40 | Réservation reliquat | Courte (48 h), pharmacien pour réglementés | — | ☐ |
| D41 | Moteur OCR | Hébergé APEX, repli sur l'appareil ; LLM exclu | Nouvelle | ☐ |
| D42 | Effet d'un retour sur le droit | Neutralisation par défaut | Nouvelle | ☐ |
| D43 | Délai de complétion `A_COMPLETER` | 7 jours puis blocage des délivrances suivantes | Nouvelle | ☐ |

---

## D27 — Modèle du patient

| Rubrique | Contenu |
|---|---|
| **Description** | Où stocker l'identité et les données de santé d'une personne servie sur ordonnance, alors que le cahier des charges impose une source unique pour les clients. |
| **Options** | **A.** Fiche client unique (`customers`) + volet patient 1-1 (`patient_profiles`) cloisonné. **B.** Table `patients` indépendante, liée ou non aux clients. **C.** Tout dans `customers` (colonnes supplémentaires). |
| **Recommandation** | **A**, avec trois niveaux : N1 commercial, N2 identification patient, N3 médical (ordonnances, délivrances, scans). |
| **Justification fonctionnelle** | Une personne = une fiche (achats, crédit, fidélité, ordonnances) ; pas de doublons ni de resaisie ; le patient peut être différent du payeur (enfant, parent). |
| **Justification technique** | Le cloisonnement par table permet des droits et une journalisation des lectures dédiés (`sensitive_access_log`) et un chiffrement ciblé ; C mélangerait les données commerciales et médicales et rendrait le contrôle d'accès colonne par colonne fragile ; B créerait deux référentiels à dédoublonner. |
| **Conséquences** | M07 : `has_patient_profile`, déplacement de `cin_optionnel` vers le volet chiffré, permission `patient.merge`. M02 : la recherche client ne révèle pas l'existence d'un volet patient aux rôles non habilités. M12 : les rapports commerciaux n'accèdent jamais à N2/N3. M13 : aucun accès. M08 : l'identifiant d'assuré (N2) prépare D37. |
| **Décision proposée** | **Valider A** (fiche client unique, volet patient séparé, données médicales protégées, accès journalisé). |

## D28 — Ordonnance sans patient identifié

| Rubrique | Contenu |
|---|---|
| **Description** | Peut-on enregistrer et délivrer une ordonnance sans rattacher un patient identifié (client de passage) ? |
| **Options** | **A.** Toujours interdit. **B.** Autorisé pour les ordonnances simples, paramétré par la pharmacie. **C.** Autorisé seulement si le **profil de conformité du pays** le permet pour le type d'ordonnance, jamais pour les ordonnances réglementées ou renouvelables, ni dès qu'une ligne porte un produit réglementé. |
| **Recommandation** | **C**. |
| **Justification fonctionnelle** | Préserve la fluidité du comptoir pour une ordonnance simple (antibiotique courant, par exemple) tout en interdisant les cas où l'identité est une obligation ou une nécessité de suivi (renouvellement). |
| **Justification technique** | La règle est portée par le profil pays (non abaissable localement) ; la contrainte `CHECK(patient_customer_id NOT NULL OR patient_non_identifie)` est complétée par un contrôle serveur sur les lignes ; l'ajout d'un produit réglementé déclenche une bascule obligatoire vers l'identification. |
| **Conséquences** | M09 : identité garantie pour toute écriture au registre. M14 : hors ligne, idem. M12 : les ordonnances non identifiées sont comptées sans `patient_pseudo` (exclues des comptes de patients distincts, signalé dans les rapports). M07 : pas de création implicite de fiche. |
| **Décision proposée** | **Valider C** ; le blocage des médicaments réglementés en l'absence des informations obligatoires est non contournable. |

## D29 — Ordonnance incomplète

| Rubrique | Contenu |
|---|---|
| **Description** | Que faire lorsqu'il manque des informations au moment de la délivrance ? |
| **Options** | **A.** Tout bloquer. **B.** Autoriser avec statut `A_COMPLETER` sauf réglementées (v1.0). **C.** Classifier chaque élément : **A** administratif (différable), **M** médical/pharmaceutique (bloquant, levée possible uniquement quand le pharmacien établit l'information et la documente), **R** réglementaire (bloquant absolu). |
| **Recommandation** | **C** (§4.8). |
| **Justification fonctionnelle** | Une coordonnée de médecin manquante ne doit pas empêcher de servir le patient ; une date de prescription, un dosage illisible ou une mention légale manquante, si. La v1.0 confondait ces niveaux (« scan manquant » et « prescripteur à compléter » traités pareil). |
| **Justification technique** | Table `prescription_completeness_items` (élément, catégorie, niveau, source de la règle) ; recalcul serveur à la validation ; `rx.override` sans effet sur un BLOQUANT ; règle locale jamais moins stricte que le profil pays ; valeur OCR non confirmée = manquante. |
| **Conséquences** | M02 : bandeau de complétude par catégorie. M09 : catégorie R systématique pour les classes réglementées. M15 : `rx.override` limité aux contrôles VALIDATION. M14 : mêmes règles hors ligne, revalidées au retour. M12 : KPI « ordonnances complètes » (axe Conformité, EXT02). |
| **Décision proposée** | **Valider C** : aucune ordonnance ne peut être délivrée en contournant une obligation légale. Lien D43 (délai de complétion). |

## D30 — Niveau de saisie des lignes

| Rubrique | Contenu |
|---|---|
| **Description** | Saisir seulement ce qui est délivré, ou aussi ce qui est prescrit ? |
| **Options** | **A.** Délivré seulement. **B.** Prescrit + délivré. |
| **Recommandation** | **B**, avec saisie accélérée (le scan du produit délivré crée la ligne prescrite identique en une touche). |
| **Justification fonctionnelle** | Sans le prescrit, impossible de calculer reliquats, renouvellements et substitutions, ni de bloquer un dépassement. |
| **Justification technique** | Le solde (§15) se calcule par différence prescrit − délivré net ; A rendrait les exigences du §3 de l'audit (soldes calculés, jamais saisis) impossibles. |
| **Conséquences** | M02 : écran à deux colonnes (prescrit / délivré). M11 : demande engagée. M12 : statistiques par produit prescrit ou délivré. Objectif ergonomique : ordonnance de 5 lignes < 60 s. |
| **Décision proposée** | **Valider B**. |

## D31 — Spécialité dans les statistiques

| Rubrique | Contenu |
|---|---|
| **Description** | Quelle spécialité attribuer à une ordonnance dans les rapports si la fiche du médecin change ? |
| **Options** | **A.** Snapshot à la date de prescription. **B.** Spécialité actuelle du médecin. **C.** Les deux, au choix dans le rapport. |
| **Recommandation** | **A** : la spécialité est figée sur l'ordonnance (`specialty_id_snapshot`), déterminée à la **date de prescription** à partir de `prescriber_specialties` historisée. Option d'affichage secondaire « spécialité actuelle » possible dans l'état A, jamais par défaut. |
| **Justification fonctionnelle** | Les séries historiques restent stables ; une ordonnance rédigée par un généraliste devenu cardiologue reste comptée en médecine générale. |
| **Justification technique** | Distinction formelle de trois cas de correction (W4) : erreur de saisie sur l'ordonnance → rectification versionnée et recalcul tracé de `rx_facts` ; évolution de la fiche → date d'effet, snapshots antérieurs inchangés ; erreur de fiche depuis l'origine → rectification proposée par lot, validée par le titulaire. Jamais de recalcul silencieux. |
| **Conséquences** | M12 : `rx_facts.specialty_id` issu du snapshot. M13 : analyseur P4 sur snapshot. Tests S11. |
| **Décision proposée** | **Valider A**. |

## D32 — Référentiel des spécialités

| Rubrique | Contenu |
|---|---|
| **Description** | Qui gère la liste des spécialités médicales ? |
| **Options** | **A.** Liste APEX commune, non modifiable. **B.** Liste APEX commune + ajouts locaux contrôlés. **C.** Liste propre à chaque pharmacie. |
| **Recommandation** | **B** ; chaque ajout local doit être rattaché à un **groupe APEX** (`MEDECINE_GENERALE`, `MEDICALE`, `CHIRURGICALE`, `DENTAIRE`, `AUTRE`) ; demande de promotion au référentiel APEX possible. |
| **Justification fonctionnelle** | Couvre les spécialités rares sans fragmenter les statistiques. |
| **Justification technique** | `medical_specialties.tenant_id NULL` = référentiel ; une spécialité utilisée est désactivée, jamais supprimée ; trilingue FR/AR/EN. |
| **Conséquences** | M04 (lot 2) : chargement du référentiel. M12 : comparabilité multi-pharmacies future (SaaS). |
| **Décision proposée** | **Valider B**. |

## D33 — Scan des ordonnances

| Rubrique | Contenu |
|---|---|
| **Description** | La numérisation est-elle obligatoire ? |
| **Options** | **A.** Facultative. **B.** Obligatoire pour toutes. **C.** Obligatoire lorsque le profil pays l'exige (réglementées par défaut), facultative sinon. |
| **Recommandation** | **C**. L'existence de l'OCR (§14) ne rend pas le scan obligatoire ; le scan ne remplace pas l'original papier tant que R6/R16 ne sont pas confirmés. |
| **Justification fonctionnelle** | Preuve utile pour les réglementées et les contrôles ; contrainte inutile au comptoir pour une ordonnance simple. |
| **Justification technique** | Scan obligatoire = élément de catégorie R (bloquant) ; facultatif = catégorie A. Stockage R2 chiffré, SHA-256, URL signées courtes. |
| **Conséquences** | M09 : pièce justificative liée au registre. Coût de stockage (R2) à estimer. R5/R6/R16. |
| **Décision proposée** | **Valider C**. |

## D34 — Seuil de petits effectifs

| Rubrique | Contenu |
|---|---|
| **Description** | Masquer les cellules statistiques correspondant à trop peu de patients pour éviter la réidentification. |
| **Options** | **A.** k = 3 (v1.0). **B.** k = 5. **C.** Aucun. |
| **Recommandation** | **B**, complété par les contrôles C2 à C10 (§18.2) : suppression secondaire, au plus 3 dimensions croisées, tranches d'âge larges, détection des requêtes différentielles, arrondi, quota. |
| **Justification fonctionnelle** | Une officine de quartier a des effectifs faibles : un médecin rare × un médicament rare × un mois identifie souvent une personne. k = 5 est la valeur usuelle en statistique de santé. |
| **Justification technique** | Seuil calculé sur les patients distincts (`patient_pseudo`) ; `report_query_log` mémorise l'empreinte de population pour la détection différentielle ; appliqué aussi aux outils de l'IA. |
| **Conséquences** | M12 : moteur de masquage commun états / tableau de bord / exports. M13 : seuil ≥ rapports. Perte de détail sur les petits volumes (acceptée). |
| **Décision proposée** | **Valider B** + contrôles anti-croisement. |

## D35 — Nom des prescripteurs transmis au LLM

| Rubrique | Contenu |
|---|---|
| **Description** | Le nom du médecin peut-il figurer dans les données envoyées au fournisseur du modèle de langage ? |
| **Options** | **A.** Pseudonymisé (`PRESC-07`), réidentifié côté serveur à l'affichage. **B.** En clair. |
| **Recommandation** | **A**. |
| **Justification fonctionnelle** | Le LLM n'a pas besoin du nom pour expliquer une tendance ; l'utilisateur voit le nom réel à l'écran. |
| **Justification technique** | Table de correspondance serveur, jamais transmise ; liste blanche de champs. |
| **Conséquences** | M13 : couche de pseudonymisation/réidentification. R9. |
| **Décision proposée** | **Valider A**. |

## D36 — Journal des accès sensibles

| Rubrique | Contenu |
|---|---|
| **Description** | Où journaliser les lectures (consultations, recherches, impressions, exports) de données patients ? |
| **Options** | **A.** Table dédiée `sensitive_access_log`. **B.** Journal d'audit général. |
| **Recommandation** | **A**, immuable et chaînée, avec `report_query_log` pour les requêtes statistiques. |
| **Justification fonctionnelle** | Revue mensuelle par le titulaire, alertes de volume inhabituel, réponse aux demandes des personnes. |
| **Justification technique** | Volumétrie des lectures très supérieure aux écritures ; conservation et purge spécifiques ; requêtes de revue simples. |
| **Conséquences** | M00 : nouvelle table et politique de conservation. M15 : attribution par collaborateur. |
| **Décision proposée** | **Valider A**. |

## D37 — Tiers payant (CNAM, mutuelles)

| Rubrique | Contenu |
|---|---|
| **Description** | Gestion de la part prise en charge par la CNAM ou les mutuelles. |
| **Options** | **A.** En v1. **B.** Reporté, architecture compatible. **C.** Reporté sans préparation. |
| **Recommandation** | **B** : aucune fonctionnalité en v1 ; compatibilité préservée par le lien ligne à ligne vente ↔ ordonnance, l'identifiant d'assuré chiffré (N2), l'organisme comme tiers distinct, `party_ledger` multi-tiers ; colonnes `part_patient` / `part_organisme` ajoutées plus tard par migration additive ; extension dédiée ultérieure. |
| **Justification fonctionnelle** | Le tiers payant implique conventions, bordereaux, télétransmission, rejets, lettrage : un sous-projet à part entière, dépendant de la CNAM (R12). |
| **Justification technique** | Pas de colonnes « fantômes » inutilisées ; les points d'extension évitent toute refonte. |
| **Conséquences** | M08 : type de tiers `ORGANISME` futur. M02 : 100 % payé par le client en v1. M07 : `customers.type = convention` conservé. |
| **Décision proposée** | **Valider B**. |

## D38 — Posologie

| Rubrique | Contenu |
|---|---|
| **Description** | Saisir la posologie prescrite ? |
| **Options** | **A.** Non saisie. **B.** Facultative. **C.** Obligatoire. |
| **Recommandation** | **B** : texte facultatif, chiffré, jamais structuré par l'OCR, jamais utilisé par l'IA ni par les rapports ; la **durée de traitement** reste un champ structuré facultatif (utile au calcul de la quantité). |
| **Justification fonctionnelle** | Utile pour l'étiquetage et le conseil, sans alourdir le comptoir. |
| **Justification technique** | Minimisation ; donnée de santé N3. |
| **Conséquences** | M07 : suivi de traitement (avec consentement) utilise la durée, pas la posologie. M13 : exclue. |
| **Décision proposée** | **Valider B**. |

## D39 — Ventes sur ordonnance hors ligne

| Rubrique | Contenu |
|---|---|
| **Description** | Que permettre lorsque le réseau est coupé ? |
| **Options** | **A.** Interdites. **B.** Ordonnances simples (v1.0). **C.** Uniquement la **première délivrance** d'une ordonnance `SIMPLE`, sans produit réglementé, sans reliquat ni renouvellement d'une ordonnance existante, sans OCR serveur ; revalidation complète par le serveur au retour, anomalies à traiter. |
| **Recommandation** | **C**. |
| **Justification fonctionnelle** | Le comptoir continue de servir les cas simples ; les cas à risque attendent le réseau. |
| **Justification technique** | Hors ligne, le solde d'une ordonnance existante n'est pas fiable (une autre caisse a pu délivrer) ; les réglementés exigent le registre continu. Produits `REGLEMENTE` marqués non vendables dans le cache. ULID + idempotence déjà prévus. |
| **Conséquences** | M14 : filtrage du cache, file de synchronisation, écran des anomalies. M09 : aucune écriture hors ligne. |
| **Décision proposée** | **Valider C**. |

## D40 — Réservation de stock pour les reliquats

| Rubrique | Contenu |
|---|---|
| **Description** | Réserver des lots pour un patient qui reviendra chercher son reliquat ? |
| **Options** | **A.** Aucune. **B.** Réservation courte paramétrable. |
| **Recommandation** | **B** : 48 h par défaut, prolongeable une fois par un pharmacien ; pour un produit réglementé, réservation par un pharmacien uniquement ; aucune réservation n'est un mouvement de stock. |
| **Justification fonctionnelle** | Évite de faire revenir le patient pour rien. |
| **Justification technique** | `stock_reservations` existant (dossier §4.4) ; expiration paresseuse ; stock disponible = stock − réservé. |
| **Conséquences** | M05 : origine `RELIQUAT_ORDONNANCE`. M11 : la réservation n'est pas une demande ; la demande engagée l'est. |
| **Décision proposée** | **Valider B**. |

---

## Décisions nouvelles issues de l'audit

### D41 — Moteur OCR

| Rubrique | Contenu |
|---|---|
| **Description** | Quelle technologie et quel hébergement pour la reconnaissance des ordonnances (§14.5) ? |
| **Options** | **A.** Sur l'appareil. **B.** Service OCR hébergé par APEX. **C.** Prestataire OCR externe. **D.** LLM multimodal généraliste. |
| **Recommandation** | **B** avec repli **A** ; **C** seulement après R16 et contrat de sous-traitance ; **D** interdite. |
| **Justification fonctionnelle** | Qualité suffisante sur l'imprimé, partielle sur le manuscrit ; la confirmation humaine reste obligatoire dans tous les cas. |
| **Justification technique** | Une image d'ordonnance contient des données nominatives et médicales : elle ne peut pas partir vers le LLM externe. Service isolé, sans outil ni mémoire, sans réutilisation pour l'entraînement. |
| **Conséquences** | Nouveau lot 5c ; coût d'infrastructure à chiffrer ; dépend de D2 (hébergement) et R10. |
| **Décision proposée** | **Valider B + A**, interdire D. |

### D42 — Effet d'un retour sur le droit à délivrance

| Rubrique | Contenu |
|---|---|
| **Description** | Un produit retourné redonne-t-il droit à une nouvelle délivrance sur la même ordonnance ? |
| **Options** | **A.** Restitution automatique. **B.** Neutralisation par défaut, restitution exceptionnelle par un pharmacien avec motif. **C.** Jamais de restitution. |
| **Recommandation** | **B** ; l'annulation dans la session restitue toujours le droit. |
| **Justification fonctionnelle** | Évite qu'un cycle retour / redélivrance permette de dépasser la prescription ; couvre l'erreur de délivrance. |
| **Justification technique** | `prescription_adjustments` (`NEUTRALISATION_RETOUR`, `RESTITUTION_RETOUR`) ; formule §15.2. |
| **Conséquences** | M02 : choix au moment du retour. M05 : stock réintégré selon l'état, indépendamment du droit. M09 : retour réglementé en quarantaine. R14, R15. |
| **Décision proposée** | **Valider B**. |

### D43 — Délai de complétion des ordonnances `A_COMPLETER`

| Rubrique | Contenu |
|---|---|
| **Description** | Combien de temps une ordonnance peut-elle rester avec des éléments administratifs manquants ? |
| **Options** | **A.** Sans limite (rappel seulement). **B.** Délai paramétrable, puis blocage des délivrances suivantes de cette ordonnance jusqu'à complétion. |
| **Recommandation** | **B**, 7 jours par défaut ; alerte au titulaire à échéance ; la délivrance déjà faite n'est jamais remise en cause. |
| **Justification fonctionnelle** | Empêche l'accumulation de fiches médecins incomplètes qui dégradent les statistiques. |
| **Justification technique** | Drapeau `en_retard` calculé paresseusement ; contrôle à la validation. |
| **Conséquences** | M17 : liste « à compléter » priorisée. M15 : KPI de conformité de l'équipe (sans sanction automatique). |
| **Décision proposée** | **Valider B**. |
