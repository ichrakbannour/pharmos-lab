# PHARM'OS — Décisions du Product Owner

Ce dossier centralise les décisions métier confirmées, les arbitrages en attente et les sources qui établissent leur portée. Les documents de proposition ne valent pas décision ; une décision métier ne vaut pas autorisation de réalisation.

## Sources actuellement présentes dans GitHub

- [Matrice D1–D86 v1.2](MATRICE_DECISIONS_D1_D86_v1.2.md) — historique du 03/11/2026 ; ne remplace pas la v1.3.3.
- [P82 — Spécifications finales STOCK](P82_SPECIFICATIONS_FINALES_STOCK.md) — propositions sources, confirmées seulement dans la mesure consignée par P83.
- [P86 — Arbitrage consolidé des 13 points](P86_ARBITRAGE_CONSOLIDE_13_POINTS.md) — propositions techniques ; la réponse PO est consignée dans P87.

## Sources retrouvées dans l'export du 27/11/2026, publication GitHub à effectuer

L'archive jointe `EXPORT_SOURCES_AUDIT_PHARMOS_20261127.zip` a été contrôlée : 42 entrées, 498 326 octets, SHA-256 `b3a4d228927300c6c12b18b5082fb4c797599fc762102a9e08a97fd47d0f9fa5`, aucune erreur au test ZIP.

- Matrice D1–D86 : 14 versions, jusqu'à v1.3.3 (12/11/2026). La v1.3.3 est la plus récente retrouvée ; elle reste un document de travail soumis à validation et ne crée aucun statut nouveau.
- P81 : registre officiel original, 17 719 octets, SHA-256 `dec4d6420c5e5ecf54d4e31410c4776a45b3f63e160dae7193a958cd41c2232c`. Cette empreinte correspond à la source exigée ; ne pas confondre avec l'ancienne pièce jointe de taille/hash différents.
- EXT03 v1.1 : document et six pièces d'audit d'origine retrouvés. `LISEZMOI.md` est un ajout effectué lors de la copie, pas une pièce de l'archive initiale. EXT03 v1.1 demeure une proposition en attente de validation.
- P83 : addendum PO confirmant Q-1 à Q-12, H-1 à H-6 et C-P82-1 à C-P82-3.
- P85 : addendum PO confirmant A-T1, A-T2 et A-T3 ; phase 2 suspendue.
- P87 : référence officielle des arbitrages PO P86-1 à P86-13 et précautions A/B.
- P82, P84, P86, P88, P89 et P91 : plans, propositions et rapports d'exécution/diagnostic/préparation. Lire leur statut exact ; ne pas transformer une proposition en décision ou une préparation en exécution.

**Limite de publication :** les sources ci-dessus ont été inspectées dans l'archive jointe, mais les 42 fichiers ne sont pas encore publiés individuellement dans ce dépôt. Le rapport d'audit v0.5 documente les contrôles et leurs conclusions. Aucune source ne doit être annoncée comme « intégrée à GitHub » avant vérification de sa présence dans l'arbre du dépôt.

## Lacunes et précautions

- P90 : aucun document autonome `P90_*` retrouvé. Le rapport P88 contient toutefois une section « MISE À JOUR P90 » détaillant la reprise Option A et l'arrêt de R0d. Ne pas reconstituer un rapport autonome.
- P81 n'existe qu'à un emplacement source connu ; préserver l'empreinte et prévoir une sauvegarde indépendante.
- Les empreintes P82 à P86 n'ont pas toutes été comparées aux manifestes M0 de P88/P87.
- O-5, O-6, O-9, O-10 et O-11 du registre P81 restent ouverts.
- Les décisions opérationnelles P81–P91 sont distinctes des décisions D1–D86 de la matrice.

Voir le [rapport d'audit global v0.5](../03_RAPPORTS_AUDIT/AUDIT_DOCUMENTAIRE_GLOBAL_PHARMOS_v0.5.md) pour les conclusions détaillées.
