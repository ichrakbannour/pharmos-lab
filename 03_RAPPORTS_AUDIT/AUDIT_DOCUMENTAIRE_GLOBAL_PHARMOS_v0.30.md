# PHARM'OS — Audit documentaire global v0.30

**Date :** 27/11/2026  
**Périmètre :** correction approfondie de R44 (TVA) après recoupement des lois de finances publiées au JORT ; maintien de la revue R42–R51.  
**Nature :** audit documentaire ; aucune décision PO modifiée et aucun paramétrage fiscal changé.

## 1. Correction importante de R44

Le constat provisoire du rapport v0.29 (FAQ ministérielle à 6 % contre cadrage PO à 7 %) était incomplet. Le recoupement des textes postérieurs montre que la FAQ à 6 % n'est pas une base suffisante pour déterminer le taux actuel applicable à la vente en officine.

### Chronologie documentaire

1. **Loi de finances 2018** : le taux du tableau B est porté de 6 % à 7 % par l'article 43 de la loi n°2017-66.
2. **Loi de finances 2020** : l'article 30 de la loi n°2019-78 fixe l'application de la taxation des médicaments à compter du 01/01/2020.
3. **Loi de finances 2021** : l'article 25 de la loi n°2020-46 ajoute l'exclusion des médicaments et produits pharmaceutiques au stade du commerce de gros et du commerce de détail et abandonne les rappels de TVA antérieurs sur ces ventes.
4. **Ministère des Finances — page actuelle sur les secteurs soumis à la TVA** : le commerce de gros et le commerce de détail sont indiqués comme exclus pour les médicaments et produits pharmaceutiques.
5. **FAQ ministérielle encore publiée** : elle affiche toujours 6 % depuis 2016. Cette réponse est incohérente avec les textes postérieurs et la page actuelle sur les secteurs soumis ; elle ne doit pas être utilisée seule.

### Conclusion R44

- Pour les **ventes de médicaments et produits pharmaceutiques au stade du gros et du détail**, les textes postérieurs et la page actuelle du Ministère indiquent une exclusion de TVA ; la FAQ 6 % est obsolète/incohérente sur ce point.
- Le taux de **7 %** a bien été introduit pour le tableau B et la taxation des médicaments a été programmée à compter du 01/01/2020, mais la LF 2021 a ensuite modifié le champ des ventes de médicaments et produits pharmaceutiques.
- Cela ne permet pas de conclure que tous les produits vendus en officine sont exonérés : dispositifs médicaux, accessoires, parapharmacie, cosmétiques, compléments, prestations, importations et opérations particulières doivent être qualifiés séparément.
- Le cadrage PO antérieur prévoyant un taux par défaut de 7 % doit donc être **réconcilié** avec le champ légal d'exclusion. Le présent audit ne modifie pas cette décision PO et n'autorise aucun changement automatique.

**Statut : R44 — ÉCART JURIDIQUE À ARBITRER / CONFIRMATION FISCALE ÉCRITE REQUISE avant paramétrage.**

## 2. Sources principales

- [JORT n°104 du 27 décembre 2019 — loi n°2019-78, article 30](https://lake.jort.tn/journal-officiel/fr/2019/104.pdf) : fixe l'application de la taxation des médicaments à compter du 01/01/2020.
- [JORT n°128 du 25 décembre 2020 — loi n°2020-46 portant loi de finances pour 2021](https://www.jort.tn/view/journal-officiel/fr/2020/128/) : article 25 relatif à l'exclusion des ventes de médicaments et produits pharmaceutiques au stade du gros et du détail et à l'abandon des rappels antérieurs.
- [Ministère des Finances — secteurs soumis à la TVA](https://www.finances.gov.tn/fr/node/890) : indique actuellement l'exclusion des médicaments et produits pharmaceutiques du commerce de gros et du commerce de détail.
- [FAQ du Ministère des Finances](https://www.finances.gov.tn/fr/faq?page=7) : conserve une réponse indiquant 6 % depuis 2016 ; cette réponse doit être considérée comme incohérente avec les textes postérieurs tant qu'elle n'est pas mise à jour.
- [Loi de finances 2018 — article 43, JORT n°101 du 19/12/2017](https://lake.jort.tn/journal-officiel/fr/2017/101.pdf) : modification du taux réduit du tableau B à 7 %.

Les textes publiés au JORT prévalent sur les FAQ administratives et les commentaires secondaires. La traduction française du JORT est informative lorsque le texte arabe fait foi.

## 3. Conséquences documentaires

- EXT06 R44 a été corrigée : la FAQ 6 % est signalée comme incohérente avec les textes postérieurs ; l'exclusion des ventes de médicaments/produits pharmaceutiques au gros et au détail est mentionnée ; le taux PO 7 % reste à réconcilier.
- La règle « aucun taux par défaut » dans EXT06 est maintenue tant que la matrice fiscale par catégorie/opération n'est pas confirmée.
- Le rapport v0.29 reste conservé comme photographie antérieure ; sa conclusion provisoire R44 est remplacée par le présent rapport v0.30.
- Les autres constats R42, R43, R45–R51 restent partiellement documentés ou ouverts comme indiqué au rapport v0.29.

## 4. Décision / confirmation encore nécessaire

Obtenir une confirmation écrite d'un expert-comptable ou de l'administration fiscale sur :
1. le traitement TVA actuel des ventes de médicaments et produits pharmaceutiques au détail par une officine ;
2. le traitement des achats/reventes en gros et des importations ;
3. le traitement des dispositifs médicaux, accessoires, parapharmacie, cosmétiques, compléments et services ;
4. le taux par défaut à conserver dans PHARM'OS et les droits de modification du titulaire.

Aucune décision PO antérieure n'est annulée par l'audit. Le taux 7 % n'est ni effacé ni appliqué automatiquement à toutes les catégories.

## 5. Garanties

- Aucun taux TVA n'a été changé dans le produit.
- Aucune décision PO n'a été modifiée.
- Aucun paramétrage, code, schéma, base, test, migration ou déploiement n'a été touché.

---
**Fin du rapport v0.30.**
