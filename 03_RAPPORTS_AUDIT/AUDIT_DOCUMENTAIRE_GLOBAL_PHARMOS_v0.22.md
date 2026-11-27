# PHARM'OS — Audit documentaire global v0.22
**Date :** 27/11/2026  
**Périmètre :** EXT06 v1.0 canonique — consolidation D78 et contrôle ciblé D81 à D86.  
**Référence PO :** matrice D1–D86 v1.3.3.  
**Nature :** consolidation documentaire. Aucun changement applicatif.

## 1. Résultat exécutif

L'écart D78 repéré dans le corps fonctionnel d'EXT06 a été corrigé. La version précédente indiquait dans son tableau §11.1 que D78 était globalement repris, alors que le workflow W8 et le schéma des prestations autorisaient encore un identifiant patient, des observations sensibles et des mesures structurées (exemple : tension). Cette possibilité contredisait explicitement l'arbitrage PO.

La correction a été propagée dans G13, W8, les schémas `service_definitions` / `service_acts`, les règles SV3/SV5/SV6 et le tableau de réconciliation §11.1. La notification est explicitement limitée à une liste blanche : type de service/opération, nom du client et date.

**D78 est désormais consolidée dans le corps fonctionnel d'EXT06.** Les décisions D81 à D86 ont également été contrôlées : aucun nouvel écart fonctionnel majeur n'a été identifié dans les sections examinées.

## 2. D78 — Suppression des champs cliniques et restriction des notifications

### Écart constaté avant correction
- W8 permettait un patient facultatif ou obligatoire selon le service.
- `service_definitions` comportait `patient_requis`, `consentement_requis` et une liste de données saisies.
- `service_acts` comportait `customer_id`, des observations et des mesures de santé structurées, dont un exemple de tension.
- SV6 encadrait le chiffrement de ces données au lieu d'interdire leur collecte dans ce registre.
- SV3 proposait des modèles de services préchargés (désactivés), alors que D78 n'autorise pas un catalogue universel imposé par APEX.

### Correction publiée
- G13 présente un registre simple : type de service/opération, nom du client et date ; champs opérationnels facultatifs configurés par l'administrateur.
- W8 ne crée aucun dossier patient/clinique, observation médicale, mesure de santé structurée ou autre donnée clinique.
- Aucun catalogue universel ou modèle de service n'est préchargé ou imposé par APEX.
- `service_definitions` ne contient plus de champ patient/consentement ni de liste libre de données cliniques ; les champs facultatifs sont limités aux informations opérationnelles non cliniques.
- La référence d'autorisation est facultative et informative ; l'autorisation réglementaire bloquante, la désactivation automatique à échéance et l'habilitation obligatoire par service ont été retirées conformément à D78 (conception v1.2.1, §7.4, §15.1 et tests SRV-08 à SRV-11).
- W8 et SV2–SV4 reposent sur le statut actif/inactif géré par l'administrateur et la permission effective `services.perform`, sans gate réglementaire non arbitré.
- Les dépendances et le plan de lot 9c ne présentent plus les habilitations RH comme condition obligatoire par service ni l'activation réglementaire comme verrou technique ; R47 reste un cadrage réglementaire externe.
- La relation `service_acts`–`customers` a été retirée : le nom du client est conservé en texte conformément à D78.
- Les tests de conception SRV-01, SRV-02, SRV-03 et SRV-07 ont été réécrits pour ne plus attendre un blocage d'autorisation, une désactivation automatique ou une habilitation obligatoire ; ils vérifient les références informatives, les permissions effectives et l'absence de données cliniques. Les tests SRV-08 à SRV-12 ont été ajoutés pour couvrir l'installation sans catalogue, la création réservée à l'administrateur, l'enregistrement minimal, l'absence de champs cliniques et la lecture journalisée.
- Le type de service pouvant révéler une information de santé, la lecture du registre reste limitée aux permissions effectives et journalisée dans `sensitive_access_log` ; les actes nominatifs sont exclus de l'IA.
- `service_acts` conserve le type de service, le nom du client, l'auteur et la date ; les colonnes d'observations, de mesures et d'identifiant patient ont été retirées.
- SV3, SV5 et SV6 reflètent désormais cette portée.
- Les permissions `services.acts.read` et `services.perform` ne présument plus de données patient ni d'une habilitation obligatoire par service.
- La notification est définie comme une liste blanche limitée au type d'opération/service, au nom du client et à la date.
- Le tableau §11.1 indique maintenant explicitement la consolidation et les preuves à obtenir.

Les conditions réglementaires applicables à l'activation des services restent soumises à R47 ; cette correction ne vaut pas validation réglementaire.

## 3. Contrôle D81 à D86

| Décision | Contrôle documentaire | Résultat |
|---|---|---|
| D81 | W12 et schéma de paie : import périodique des montants par collaborateur ; calcul laissé au comptable ou au logiciel de paie ; aucun moteur de paie intégré | **Aligné** |
| D82 | W13 et `accounting_exports` : réouverture par l'administrateur avec motif ; période figée après export définitif | **Aligné** |
| D83 | Modèle standard indicatif fourni à l'initialisation, adaptable par l'administrateur, sans validation préalable obligatoire d'un expert-comptable | **Consolidé en v0.21 ; confirmé** |
| D84 | Classification thérapeutique standard initiale distincte des référentiels officiels, modifiable par l'administrateur | **Consolidé en v0.21 ; confirmé** |
| D85 | W11 : rapprochement proposé, confirmation humaine obligatoire, écarts visibles et tracés | **Aligné** |
| D86 | W9 : seuil paramétrable et modifiable par l'administrateur ; validation au-delà du seuil par l'administrateur | **Précisé et aligné** |

D81 à D86 restent des exigences de conception : leur présence documentaire ne prouve pas leur implémentation.

## 4. Vérifications après publication

Contrôles textuels effectués sur la version relue depuis `main` :
- absence de `patient_requis` et de `customer_id` dans le modèle de prestation ;
- absence des colonnes `observations` et `mesures` dans `service_acts` ;
- absence des exemples de services préchargés ;
- présence de la saisie minimale type de service/opération, nom du client et date/heure ;
- présence de la liste blanche de notification ;
- présence de champs facultatifs strictement non cliniques ;
- absence de libellé résiduel « données patient » dans la permission `services.acts.read` ;
- absence de `habilitation_requise_code` et d'`autorisation_reglementaire` bloquante dans `service_definitions` ;
- absence de condition d'habilitation obligatoire et de gate réglementaire dans W8/SV2–SV4 ;
- absence de renvoi résiduel aux habilitations RH ou à un verrou réglementaire dans la dépendance et la feuille de route du lot 9c ;
- relation de client en texte, sans relation `service_acts` vers `customers` ;
- tests SRV-01/02/03/07/08–12 conformes au modèle D78 ;
- lecture du registre limitée et journalisée, actes nominatifs exclus de l'IA ;
- maintien de la ligne D78 comme décision arbitrée issue de la matrice PO v1.3.3.

## 5. Éléments restant ouverts

1. R47 : validation réglementaire des prestations et de leurs conditions d'activation, sur sources externes fiables.
2. R44 : validation du traitement fiscal/TVA des services.
3. Les exigences réglementaires R42–R51 restent à sourcer et valider selon leurs propres échéances.
4. Les contrôles applicatifs D78 (interface, API, exports, notifications, absence de stockage clinique) et D81–D86 restent à démontrer par des tests dans un périmètre explicitement autorisé.
5. Les autres réserves de v0.21 restent ouvertes : procédure exceptionnelle de remplacement d'un administrateur local hors transfert normal ; composition détaillée du modèle thérapeutique standard initial.

## 6. Garanties de périmètre

- Matrice PO v1.3.3 inchangée ; aucun arbitrage créé, rouvert ou modifié.
- EXT06 v1.1 historique non réécrite.
- EXT06 v1.0 canonique modifiée uniquement sur le plan documentaire.
- Aucun code, API, base réellement déployée, droit effectif applicatif, test, état DEMO ou service système inspecté ou modifié.
- Aucune migration, opération système, nettoyage, sauvegarde, déploiement ou intervention PM2 exécuté.

---
**Fin du rapport v0.22.**
