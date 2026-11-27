# PHARM'OS — Audit documentaire global v0.12

**Date :** 27/11/2026  
**Complément de :** audits documentaires globaux v0.10 et v0.11.  
**Périmètre :** conception PHARM'OS v1.2.1, matrice PO D1–D86 v1.3.3, arborescence GitHub et plan de lots §16.2.  
**Nature :** contrôle de fraîcheur des statuts et de traçabilité des sources. Aucun code, aucune migration, aucun test, aucune opération système.

## 1. Verdict

La conception v1.2.1 reste utile comme base consolidée, mais son tableau de statuts (§18) n'est plus synchronisé avec la matrice PO v1.3.3 publiée dans l'archive des sources.

L'écart principal porte sur D40 :
- conception v1.2.1 §18 : D40 figure parmi les décisions proposées ; total affiché de 16 arbitrées et 63 proposées ;
- matrice v1.3.3 : D40 est arbitrée depuis le 10/11/2026 ; total de 17 arbitrées et 62 proposées.

Le dépôt conserve ainsi deux instantanés documentaires de statuts différents, sans avertissement explicite dans la conception v1.2.1. Il faut préserver l'historique de cette version, mais afficher clairement que son tableau de statuts est historique et que la matrice PO v1.3.3 fait foi pour le statut actuel.

## 2. Écart D40 et compteurs de décisions

### 2.1 Conception v1.2.1 §18

Le tableau de synthèse affiche :
- 16 décisions arbitrées ;
- 63 proposées ;
- 5 à réexaminer ;
- 2 caduques.

La liste des décisions proposées inclut D40 dans la plage D38–D43.

### 2.2 Matrice PO v1.3.3

La matrice source v1.3.3 :
- indique explicitement que D40 a été arbitrée par le PO le 10/11/2026 ;
- remplace la réservation courte « 48 h » par une réservation sans avance, avec expiration par défaut à deux jours comptables, configurable par l'ADMIN ;
- exige la traçabilité des modifications d'échéance, une notification avant expiration et interdit l'annulation silencieuse ou la confiscation automatique ;
- affiche 17 décisions arbitrées et 62 proposées, avec 5 à réexaminer et 2 caduques.

### 2.3 Conclusion

Le tableau §18 de la conception v1.2.1 est un instantané antérieur à l'arbitrage D40. Il ne doit pas être utilisé comme état courant des décisions.

Correction documentaire recommandée :
1. conserver le tableau historique pour ne pas falsifier la photographie de la v1.2.1 ;
2. ajouter une note de fraîcheur explicite renvoyant à la matrice PO v1.3.3 ;
3. publier ou référencer cette matrice v1.3.3 dans le dossier canonique 04_DECISIONS_PO, avec un lien stable depuis le README ;
4. vérifier que les références de D40 dans les documents de conception et les plans de lots renvoient à la règle arbitrée, et non à l'ancienne proposition de 48 heures.

## 3. Traçabilité de la matrice de référence

La section 18 de la conception v1.2.1 indique comme matrice complète : MATRICE_DECISIONS_D1_D86_v1.2.1.md.

Contrôle de l'arborescence GitHub :
- le dossier canonique 04_DECISIONS_PO contient MATRICE_DECISIONS_D1_D86_v1.2.md ;
- la matrice v1.2.1 est conservée dans l'archive des sources originales ;
- la matrice v1.3.3, utilisée ici comme état PO actuel, est également conservée dans cette archive ;
- aucune copie canonique v1.3.3 n'a été identifiée dans 04_DECISIONS_PO lors du contrôle de l'arborescence.

Il existe donc une rupture de traçabilité entre la référence historique citée par la conception, la matrice canonique actuellement présente et la dernière matrice source vérifiée.

Action recommandée : faire pointer le dossier Décisions PO vers la matrice v1.3.3 vérifiée, sans supprimer ni remplacer les versions historiques. Le fichier source de l'archive doit rester intact.

## 4. Cohérence du plan de lots §16.2

Le plan §16.2 cite des décisions et exigences comme prérequis de lots. Ce tableau est un plan conditionnel, pas une preuve que les décisions ou exigences sont validées.

Points à préserver :
- Lot 5b reste conditionné à la validation d'EXT03 v1.1 et aux décisions D27–D43 ainsi qu'aux exigences R1–R13 ;
- Lot 7b reste conditionné à la validation d'EXT04 et aux décisions D44–D58 et exigences R23–R33 ;
- Lot 7b ne peut pas être considéré libéré tant que INC-06 n'est pas arbitré ;
- le Lot 5 doit appliquer D40 dans sa version arbitrée, et non l'ancienne valeur de 48 heures ;
- le Lot 5d doit appliquer la règle D74 et la permission dédiée décrites dans la conception v1.2.1, sous réserve de validation de la traduction technique marquée RECO.

Le libellé « INC-06 tranché » du Lot 7b demeure contradictoire avec D37/D44 et a déjà été enregistré sous AUD-056 dans le rapport v0.11. Il n'est pas réouvert ici ; il reste à corriger.

## 5. Registre des constats

| ID | Constat | État |
|---|---|---|
| AUD-056 | §16.2 indique « INC-06 tranché » alors que D37/D44 restent ouverts | Ouvert — voir v0.11 |
| **AUD-057** | Conception v1.2.1 §18 conserve D40 comme proposée et affiche 16/63, contrairement à la matrice v1.3.3 (17/62) | Ouvert — instantané de statuts périmé |
| **AUD-058** | La référence de matrice citée en §18 et la matrice canonique du dossier 04_DECISIONS_PO ne correspondent pas à la dernière matrice PO v1.3.3 vérifiée | Ouvert — traçabilité documentaire |

## 6. Actions recommandées

1. Ajouter une note de fraîcheur à la conception v1.2.1 §18 : tableau historique, D40 arbitrée ultérieurement, matrice v1.3.3 de référence pour les statuts courants.
2. Publier une copie canonique ou un index stable de la matrice v1.3.3 dans 04_DECISIONS_PO, sans modifier l'archive source.
3. Mettre à jour 04_DECISIONS_PO/README.md pour distinguer clairement la matrice canonique courante et les matrices historiques.
4. Recontrôler les documents de conception qui utilisent encore les valeurs historiques de D40.
5. Maintenir INC-06 ouvert jusqu'à arbitrage PO explicite ; aucune activation du tiers payant ne découle du présent audit.

Ces actions sont des recommandations. Aucun fichier source n'a été modifié dans cette mission.

## 7. Limites

- Contrôle documentaire et d'arborescence uniquement.
- Les statuts PO sont déterminés à partir de la matrice v1.3.3 de l'archive source vérifiée.
- La matrice source n'a pas été déplacée ni modifiée.
- Aucun arbitrage, test, prototype, code, migration, déploiement ou comportement DEMO n'a été exécuté ou validé.

## 8. Sources vérifiées

- 01_CONCEPTION/CONCEPTION_PHARMOS_v1.2.1.md — §§16.2, 18.
- 04_DECISIONS_PO/MATRICE_DECISIONS_D1_D86_v1.2.md — copie canonique actuelle dans le dossier PO.
- 04_DECISIONS_PO/README.md — index du dossier PO.
- 07_SOURCES_ORIGINALES/EXPORT_SOURCES_AUDIT_PHARMOS_20261127/1_MATRICE_D1_D86/MATRICE_DECISIONS_D1_D86_v1.3.3.md — D40, synthèse des statuts et historique v1.2.3.
