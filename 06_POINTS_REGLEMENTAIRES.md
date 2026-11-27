# PHARM'OS — EXT03 v1.1 — Points réglementaires restant à confirmer

**Avertissement** : PHARM'OS ne présume d'aucune règle de droit. Les hypothèses ci-dessous sont des **valeurs de
conception prudentes**, à confirmer par les autorités et conseils compétents (Tunisie d'abord). Tant qu'un point
n'est pas confirmé, la règle applique la valeur la plus stricte (blocage ou validation par un pharmacien).

Priorité : **Bloquant 5b** (avant le cœur Ordonnances) · **Bloquant 5c** (avant l'OCR) · **Bloquant 10** (avant le
registre des réglementés) · **Avant production** · **Futur**.

## 1. Points R1 à R13 (repris de la v1.0, statut mis à jour)

| # | Sujet | Hypothèse de conception | Question précise à poser | Interlocuteurs | Priorité |
|---|---|---|---|---|---|
| R1 | Traitement de données de santé | Déclaration / autorisation INPDP (loi organique n° 2004-63) ; base légale : obligation de traçabilité pour la délivrance, consentement pour les usages facultatifs | Régime applicable (déclaration ou autorisation) à un logiciel SaaS d'officine ; qui est responsable du traitement (pharmacie) et sous-traitant (APEX) ? | INPDP, juriste | Bloquant 5b |
| R2 | Mentions obligatoires d'une ordonnance par type | Paramétrées par type et classées en catégorie R | Liste officielle des mentions par type (simple, renouvelable, chaque tableau de substances) ; une ordonnance sans identité du patient est-elle recevable pour un médicament non réglementé ? (D28) | Ordre des pharmaciens, DPM | Bloquant 5b |
| R3 | Durée de validité | Paramétrable par type | Délai de présentation et de validité par type ; un reliquat d'une occurrence peut-il être délivré après l'ouverture de la suivante ? | DPM, textes | Bloquant 5b |
| R4 | Droit de substitution | Prudente par défaut | Générique ↔ princeps, conditionnement, dosage : conditions, information du prescripteur, mention sur l'ordonnance ; existe-t-il un répertoire officiel des groupes génériques ? | Ordre, DPM | Bloquant 5b |
| R5 | Durées de conservation | Paramétrables | Durées pour ordonnances, copies, registres, journaux d'accès ; action à échéance (archivage, anonymisation, destruction) | Textes, juriste | Bloquant 5b |
| R6 | Valeur probante du scan | Le scan complète, ne remplace pas l'original | Une copie numérique peut-elle remplacer l'original ? Conditions (signature, horodatage) ? | Juriste | Bloquant 5b |
| R7 | Mentions après délivrance | Étiquette / mention paramétrable | Mentions à porter sur l'ordonnance (cachet, date, quantités, n° d'ordre) | Ordre | Bloquant 5b |
| R8 | Identifiant professionnel du médecin | Champ facultatif, désactivable | Existence d'un identifiant public, format, droit de l'enregistrer | Ordre des médecins, INPDP | Avant production |
| R9 | Statistiques nominatives par prescripteur | Usage interne de gestion uniquement, aucune cession | Licéité de la collecte et des états par médecin ; limites déontologiques | Juriste, Ordres | Avant production |
| R10 | Hébergement des données de santé | Selon D2 | Obligation de localisation en Tunisie ou d'agrément ? Conditions de transfert vers un hébergeur étranger | INPDP, ministère de la Santé | Bloquant 5b |
| R11 | Ordonnance électronique | `support = ELECTRONIQUE` prévu | Calendrier, format, signature, raccordement | Ministère de la Santé | Futur |
| R12 | Tiers payant CNAM | Hors v1 (D37) | Conditions de conventionnement et de télétransmission | CNAM | Futur |
| R13 | Autres pays | Profil de conformité par pays ; RGPD pour l'UE | Règles locales pour chaque pays ouvert | Juristes locaux | Futur |

## 2. Nouveaux points issus de l'audit (R14 à R22)

| # | Sujet | Hypothèse de conception | Question précise à poser | Interlocuteurs | Priorité |
|---|---|---|---|---|---|
| R14 | Retour de médicaments par un patient | Retour accepté selon l'état ; réglementés jamais remis en vente, quarantaine | Un médicament délivré peut-il être repris et remis en vente ? Règles spécifiques aux réglementés (destruction, déclaration) ? | Ordre, DPM | Bloquant 5b |
| R15 | Effet d'un retour sur l'ordonnance | Neutralisation par défaut (D42) | Après un retour, le pharmacien peut-il délivrer à nouveau sur la même ordonnance ? Mention à porter ? | Ordre | Bloquant 5b |
| R16 | Numérisation et OCR | Service hébergé APEX ; prestataire externe exclu sans contrat | Le traitement automatisé d'images d'ordonnances par un sous-traitant est-il autorisé ? Conditions (contrat, localisation, durée de conservation des images et extractions) | INPDP, juriste | Bloquant 5c |
| R17 | Quantités et durées maximales par classe | Contrôle bloquant par classe (`duree_max_prescription_j`) | Durées maximales de prescription et de délivrance par tableau ; fractionnement obligatoire ? | DPM | Bloquant 10 |
| R18 | Registre électronique des réglementés | Registre numérique généré dans la transaction | Le registre électronique est-il légalement accepté seul ? Forme, mentions, signature, export, conservation | DPM, Ordre | Bloquant 10 |
| R19 | Présence et qualification pour dispenser | Pharmacien pointé pour réglementés (D24) | Quelles catégories de personnel peuvent dispenser quels produits, en présence ou non d'un pharmacien ? | Ordre, Code de la santé | Bloquant 5b |
| R20 | Accès du patient à ses données et rectification | Export sur demande, rectification tracée | Délais et modalités de réponse ; peut-on refuser la suppression pendant la durée légale de conservation ? | INPDP | Avant production |
| R21 | Statistiques et réidentification | k = 5 et contrôles anti-croisement (D34) | Existe-t-il un seuil de référence imposé ou recommandé ? Les états par médecin à faible effectif sont-ils des données à caractère personnel du prescripteur ? | INPDP | Avant production |
| R22 | Transmission à un LLM externe | Aucune donnée nominative ou médicale ; agrégats uniquement | Des agrégats d'officine (sans patient) envoyés à un fournisseur étranger constituent-ils un transfert soumis à autorisation ? | INPDP, juriste | Avant production (lot 13) |

## 3. Synthèse par échéance

| Échéance | Points |
|---|---|
| **Avant le lot 5b** | R1, R2, R3, R4, R5, R6, R7, R10, R14, R15, R19 |
| **Avant le lot 5c** | R16 |
| **Avant le lot 10** | R17, R18 (et D5 du dossier principal) |
| **Avant la production** | R8, R9, R20, R21, R22 |
| **Futur** | R11, R12, R13 |

Recommandation : regrouper les questions par interlocuteur (INPDP : R1, R10, R16, R20–R22 ; Ordre des pharmaciens /
DPM : R2–R7, R14, R15, R17–R19) dans deux dossiers de consultation uniques.
