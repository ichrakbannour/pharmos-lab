# PHARM'OS — Décisions du Product Owner

Ce dossier centralise les décisions métier confirmées, les arbitrages en attente et les sources qui établissent leur portée. Les documents de proposition ne valent pas décision ; une décision métier ne vaut pas autorisation de réalisation.

## Sources actuellement présentes dans le dossier canonique

- **[Clarification PO TVA du 27/11/2026](CLARIFICATION_PO_TVA_20261127.md)** — champ TVA présent dès l'origine ; 7 % prérempli ; taux modifiable par produit par l'ADMIN-PHARMACIEN titulaire, y compris 0 % ; historisation et taux figé sur chaque vente. Clarification sans création de D87 ; R44 réglementaire reste à confirmer.

- **[Matrice D1–D86 v1.3.5](MATRICE_DECISIONS_D1_D86_v1.3.5.md)** — référence PO courante ; correctif du contrôle de cohérence des compteurs ; arbitrage INC-06 option B (D6, D37, D44) ; 20 arbitrées, 61 proposées, 3 à réexaminer, 2 caduques.
- [Addendum PO INC-06 — option B](INC-06_ARBITRAGE_PO_OPTION_B_20261127.md) — décision du 27/11/2026 : modèle complet ; périmètre v1 limité au remboursement par l'assuré et aux conventions privées ; aucune convention active par défaut ; tiers payant CNAM/mutuelles exclu de la v1 ; activation CNAM ultérieure sous vérification officielle et validation PO distincte.
- [Matrice D1–D86 v1.3.3](MATRICE_DECISIONS_D1_D86_v1.3.3.md) — version antérieure, conservée sans modification.
- [Matrice D1–D86 v1.2](MATRICE_DECISIONS_D1_D86_v1.2.md) — historique du 03/11/2026 ; ne remplace pas la matrice courante v1.3.5.
- [P81 — Registre officiel des décisions PO STOCK](P81_REGISTRE_DECISIONS_PO_STOCK.md) — source originale publiée depuis l'archive ; empreinte Git blob identique à l'original (17 719 octets).
- [P82 — Spécifications finales STOCK](P82_SPECIFICATIONS_FINALES_STOCK.md) — propositions sources, confirmées seulement dans la mesure consignée par P83.
- [P86 — Arbitrage consolidé des 13 points](P86_ARBITRAGE_CONSOLIDE_13_POINTS.md) — propositions techniques ; la réponse PO est consignée dans P87.

## Sources retrouvées dans l'export du 27/11/2026

L'archive jointe EXPORT_SOURCES_AUDIT_PHARMOS_20261127.zip a été contrôlée : 42 entrées, 498 326 octets, SHA-256 b3a4d228927300c6c12b18b5082fb4c797599fc762102a9e08a97fd47d0f9fa5, aucune erreur au test ZIP.

- Matrice D1–D86 : 14 versions, jusqu'à v1.3.3 (12/11/2026). Le différentiel est consigné dans le rapport v0.6 ; la v1.3.3 ne modifie aucun statut D1–D86.
- P81 : registre officiel original, 17 719 octets, SHA-256 dec4d6420c5e5ecf54d4e31410c4776a45b3f63e160dae7193a958cd41c2232c. Il est publié dans le dossier canonique ; taille et Git blob SHA contrôlés contre l'original.
- EXT03 v1.1 : document et six pièces d'audit d'origine retrouvés. LISEZMOI.md est un ajout effectué lors de la copie. EXT03 v1.1 demeure une proposition en attente de validation ; R1–R22 sont des questions réglementaires à confirmer.
- P83 : addendum PO confirmant Q-1 à Q-12, H-1 à H-6 et C-P82-1 à C-P82-3. Q-12 traite O-11 pour la forme (table de référence) ; O-5, O-6, O-9 et O-10 restent ouverts.
- P85 : addendum PO confirmant A-T1, A-T2 et A-T3 ; phase 2 suspendue.
- P87 : référence officielle des arbitrages PO P86-1 à P86-13 et précautions A/B. V-3 reste NON APPROUVÉ.
- P82, P84, P86, P88, P89 et P91 : plans, propositions et rapports d'exécution/diagnostic/préparation. Lire leur statut exact ; ne pas transformer une proposition en décision ou une préparation en exécution.

**Publication des sources :** les 42 fichiers originaux sont publiés individuellement et contrôlés dans le miroir d'archive [07_SOURCES_ORIGINALES/EXPORT_SOURCES_AUDIT_PHARMOS_20261127](../07_SOURCES_ORIGINALES/EXPORT_SOURCES_AUDIT_PHARMOS_20261127/). La matrice v1.3.3 est désormais également copiée dans le dossier canonique ; son Git blob SHA est identique à celui du fichier source dans le miroir. Les autres sources originales ne sont pas toutes recopiées individuellement dans le dossier canonique 04_DECISIONS_PO. Ne pas confondre miroir d'archive et documents canoniques.

## Lacunes et précautions

- P90 : aucun document autonome P90_* retrouvé. P88 contient toutefois une section MISE À JOUR P90 détaillant la reprise Option A et l'arrêt de R0d. Ne pas reconstituer un rapport autonome.
- P81 n'existe qu'à un emplacement source connu ; préserver l'empreinte et prévoir une sauvegarde indépendante.
- Les empreintes P82 à P86 n'ont pas toutes été comparées aux manifestes M0 de P88/P87.
- O-5, O-6, O-9 et O-10 restent ouverts. O-11 est traité pour la forme par P83 Q-12 ; V-3 reste NON APPROUVÉ.
- Les décisions opérationnelles P81–P91 sont distinctes des décisions D1–D86 de la matrice.

Voir le [rapport d'audit global v0.13](../03_RAPPORTS_AUDIT/AUDIT_DOCUMENTAIRE_GLOBAL_PHARMOS_v0.13.md) pour les conclusions récentes.

## Inventaire vérifiable des originaux

- [Manifeste SHA-256 des 42 sources exportées](../07_SOURCES_ORIGINALES/MANIFESTE_SHA256_EXPORT_20261127.md) — tailles et empreintes recalculées sur le ZIP joint ; le manifeste distingue les sources vérifiées dans l'archive de celles effectivement publiées dans GitHub.