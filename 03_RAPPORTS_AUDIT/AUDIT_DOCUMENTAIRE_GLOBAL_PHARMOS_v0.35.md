# PHARM'OS — Audit documentaire global v0.35

**Date :** 27/11/2026  
**Périmètre :** réconciliation documentaire des décisions STOCK P81 à P87, avec contrôle des conséquences sur le plan de réalisation P86 et de leur maintien dans la chaîne P88–P91.  
**Nature :** audit documentaire en lecture seule des sources GitHub. Aucun code, test, build, migration, base, service, DEMO, fichier système ou processus n'a été modifié.

## 1. Sources contrôlées

Sources originales dans `07_SOURCES_ORIGINALES/EXPORT_SOURCES_AUDIT_PHARMOS_20261127/` :
- P81 — registre officiel des décisions PO STOCK ;
- P82 — spécifications finales et plan de réalisation 0022 ;
- P83 — addendum confirmant Q-1 à Q-12, H-1 à H-6 et C-P82-1 à C-P82-3 ;
- P85 — addendum A-T1, A-T2 et A-T3 ;
- P86 — arbitrage consolidé des 13 points et plan unique de réalisation ;
- P87 — addendum officiel des arbitrages P86 et rapport d'exécution ;
- P88 (mise à jour P90), P89 et P91 — rapports d'exécution, diagnostic et préparation déjà examinés dans v0.34.

**Limite de lecture :** contrôle ciblé des sections décisionnelles et des correspondances. Ce rapport ne prétend pas à une relecture ligne par ligne de tous les documents P82, P84, P85 et P86 ni du plan historique P81. Les limites de lecture de P86 et P87 restent applicables. Les constats ci-dessous sont limités aux passages effectivement recoupés.

## 2. Chaîne d'autorité documentaire

| Niveau | Document | Portée retenue |
|---|---|---|
| Décisions initiales | P81 | DP-1 à DP-6, C1 à C4, points ouverts et limites d'autorisation |
| Spécifications proposées | P82 | Traduction technique proposée ; Q/H et règles nouvelles ne valent pas décision avant confirmation |
| Confirmation métier | P83 | Q-1 à Q-12 et H-1 à H-6 confirmés ; précautions C-P82-1 à C-P82-3 enregistrées |
| Autorisations futures bornées | P85 | A-T1 approuvé, A-T2 phase 1 conditionnelle / phase 2 suspendue, A-T3 R7a strictement borné ; aucune mission lancée |
| Arbitrage technique soumis | P86 | 13 recommandations et plan unique ; propositions non décisionnelles avant réponse PO |
| Référence PO post-arbitrage | P87 | P86-1 à P86-13 confirmés, précautions A/B enregistrées ; aucune autorisation technique nouvelle |

**Règle de priorité :** P87 prévaut sur les mentions historiques « proposition », « en attente » ou « à confirmer » des documents P86 pour les seuls points qu'il enregistre. Les documents historiques ne sont pas réécrits. P87 ne transforme pas les autorisations futures P85 en autorisation de lancer la réalisation.

## 3. Traçabilité des 13 arbitrages P86

Le tableau P85 §7 et le tableau de traçabilité du rapport P87 établissent la correspondance suivante. Les 13 points sont tous représentés, sans renumérotation ni omission :

| P86 | Référence P85 | Résultat consigné par P87 | Contrôle d'audit |
|---|---|---|---|
| 1 | A1-L1 | C1–C8 révisés, C7 scindé, C9 ajouté | décision confirmée ; critère de promotion conservé |
| 2 | A1-L2 | adaptation de schema_parite, p33, p34 sans affaiblissement | principe confirmé ; modification des tests non autorisée par P87 |
| 3 | A1-L3 | noms et formats fixés en fin de R1 par l'équipe et rapportés | décision confirmée ; choix techniques précis non encore matérialisés |
| 4 | A2-L2 | seuils documentés et mesurés en R0 ; planchers durcissables, jamais assouplis sans accord PO | décision confirmée ; seuils ne valent pas garantie de capacité |
| 5 | A2-L1 | report de phase 2 ; porte PC-1 conditionnelle | décision confirmée ; phase 2 demeure suspendue |
| 6 | §3.6 P85 | C7a obligatoire ; C7b D1 local en réserve / non vérifié | décision confirmée ; D1 réel ne peut être déclaré PASS |
| 7 | R7a-L1 | G6 conditionnel avec contrôles obligatoires et verrou d'exposition | décision confirmée ; visuel/RTL/mobile reportables seulement sous les conditions écrites |
| 8 | R7a-L2 | confirmation livrée, pas de point d'entrée PERTE dans V1/V3 avant mission d'interface | décision confirmée ; renforcée par précaution A |
| 9 | A3-L2 | V1 et V3 ; contenus fonctionnels équivalents, clés FR/EN/AR propres | décision confirmée ; contrôles impossibles listés par interface |
| 10 | A3-L3 | formulation neutre de prudence ; MSG-4 complet reporté | décision confirmée ; texte exact reste à définir en réalisation |
| 11 | §6.3 P85 | R4e → R7a → R5 → PC-1 → R6 → G6 | décision confirmée ; R7a appartient à la fenêtre de gel |
| 12 | O-5, O-6, O-9, O-10 | maintien explicite ouverts | aucun point n'est clos ou arbitré sur le fond |
| 13 | V-3 | maintien NON APPROUVÉ ; écriture CAT-11 et exemples chiffrés à soumettre avant G6 | aucune approbation implicite par les tests CAT-11 |

Aucun écart de numérotation n'a été relevé entre P85 §7, P86 et P87 sur ces 13 points.

## 4. Réconciliation des décisions antérieures

### 4.1 P81 → P82 → P83

- P81 retient quatre états de stock, le suivi par lot obligatoire pour les médicaments, les réservations par produit, les règles de sorties et la liste fermée des entrées physiques.
- P82 présente les règles Q-1 à Q-12 et H-1 à H-6 comme propositions soumises au PO, sans autoriser la réalisation.
- P83 confirme explicitement Q-1 à Q-12 et H-1 à H-6 conformément aux recommandations, ainsi que C-P82-1 à C-P82-3.
- Q-12 retient la table de référence pour les motifs normalisés. L'ancien O-11 du registre P81 ne doit donc pas être présenté comme encore en attente sur ce choix technique précis.
- O-5, O-6, O-9 et O-10 restent ouverts. V-3 demeure non approuvé selon P87-13.

Aucune modification rétroactive des documents P81/P82 n'est requise : les addenda datés portent les décisions ultérieures.

### 4.2 P85 → P86 → P87 : deux adaptations de plan toujours nécessaires

**RES-1 — passage en PERTE et API**

- Le plan P86 R4b prévoit un service et une route HTTP de passage en PERTE, avec paramètre de confirmation portant la quantité ; les scénarios de succès PER-04/PER-05 et R7a-E6 supposent un passage confirmé.
- La précaution A de P87 exige qu'aucun passage en PERTE ne soit possible par API tant que le parcours utilisateur complet et sa confirmation explicite ne sont pas disponibles ; elle interdit aussi tout contournement de la confirmation en l'absence d'écran.
- P87 qualifie expressément la tension de « contradiction apparente avec le plan P86, non avec les décisions PO ». La lecture littérale et ses effets sur les scénarios PER-* restent à confirmer par le PO avant R4b.
- **État :** plan P86 non révisé sur ce point ; aucune des deux lectures n'est autorisée à être implémentée par P87.

**RES-2 — suivi obligatoire par lot à la création/adoption**

- P86-V1 proposait un traitement transitoire côté service avec message générique alors que les écrans de création/adoption ne permettaient pas encore de corriger les données.
- La précaution B de P87 rejette explicitement le message technique générique et toute suspension générale lorsque la conformité peut être corrigée. Le comportement attendu est un refus compréhensible, un motif expliqué et une correction possible dans le parcours utilisateur, à intégrer avant R4e.
- Cette correction suppose des changements d'écrans hors du périmètre exclusif R7a. P87 ne fixe ni le lot, ni sa position exacte par rapport au gel R4a→G6, ni les fichiers, ni l'effet sur le manifeste et les contrôles visuels.
- P87 indique que le plan P86 §13 doit être révisé ; cette révision n'a pas été faite par P87.
- **État :** résultat métier décidé ; lot, séquencement et autorisation technique encore à arbitrer.

Ces deux réserves ne sont pas des contradictions entre décisions PO. Elles sont des écarts de traduction entre décisions PO confirmées et plan de réalisation proposé.

## 5. Garde-fous et statuts à préserver

| Sujet | Statut documentaire sûr |
|---|---|
| Autorisation de lancer R0–R6 | absente ; autorisation distincte du PO requise |
| R7a | périmètre futur borné ; développement non autorisé |
| P86-V1 / précaution B | résultat décidé ; développement non autorisé ; plan à réviser |
| Parcours PERTE / précaution A | précaution confirmée ; développement non autorisé ; RES-1 à clarifier |
| Migration 0022 | non créée ; aucune application |
| compose.ts, schema_parite, p33, p34 | modifications non autorisées par P87 en l'absence de mission lancée |
| Phase 2, D1 local, navigateur, PC-1 | suspendus / non exécutés |
| G6 / G7 | non autorisées ; aucune promotion ni application DEMO |
| O-5, O-6, O-9, O-10 | ouverts |
| V-3 | non approuvé ; CAT-11 ne vaut pas approbation |
| V-4, V-5, V-6 | vigilance, non requalifiées ici |

Les rapports P88–P91 ne lèvent aucune de ces réserves : P88 préflight bloqué, P90 essai interrompu sans verdict, P89 diagnostic en lecture seule, P91 préparation sans exécution de palier. Aucun résultat de test STOCK PASS n'est établi par cette chaîne.

## 6. Anomalies documentaires et corrections nécessaires avant réalisation

| ID | Action documentaire requise | Condition de clôture |
|---|---|---|
| DOC-STK-01 | Réviser le plan unique P86 pour intégrer la précaution A, expliciter la lecture retenue par le PO et aligner R4b, routes, scénarios PER-* et R7a-E6 | décision PO sur RES-1 puis plan révisé et traçable |
| DOC-STK-02 | Réviser le plan P86 pour intégrer le traitement correctif de P86-V1 avant R4e, hors R7a, sans suspension générale | arbitrage PO sur RES-2 : lot, place par rapport au gel, périmètre et autorisation |
| DOC-STK-03 | Mettre à jour les index documentaires afin de pointer vers P87 comme autorité courante et de signaler les réserves RES-1/RES-2 | liens et libellés vérifiés |
| DOC-STK-04 | Préserver la distinction décision métier / proposition technique / autorisation d'exécution dans tout futur rapport | aucun GO ni PASS déduit d'un addendum |

Aucune modification des sources historiques P81–P87 n'est recommandée. Les corrections doivent prendre la forme d'un nouvel addendum ou d'une nouvelle version du plan, sans écraser les pièces historiques.

## 7. Conclusion

La chaîne décisionnelle P81–P87 est traçable : les 13 arbitrages P86 sont enregistrés dans P87, les décisions Q/H sont confirmées par P83, et les autorisations futures P85 restent bornées. **Aucune contradiction entre décisions PO n'a été établie.**

En revanche, le plan P86 n'est pas prêt à être exécuté sans révision : RES-1 (PERTE/API) et RES-2 (création/adoption avec suivi par lot) doivent être résolues et intégrées au plan. Le point 2 de P86 §14 — autorisation explicite de lancer la réalisation — demeure absent.

**Conclusion opérationnelle : aucune réalisation STOCK, aucun test, aucune migration 0022, aucun changement DEMO/PM2/service, aucune promotion G6/G7 et aucun nettoyage ne sont autorisés par le présent audit.**

---
**Fin de l'audit v0.35.**
