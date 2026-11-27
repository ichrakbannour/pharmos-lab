# PHARM'OS — Audit documentaire global v0.29

**Date :** 27/11/2026  
**Périmètre :** revue des sources officielles R42–R51 d'EXT06 ; contrôle des implications pour le catalogue, la fiscalité, la comptabilité, les services pharmaceutiques, la paie et les prix.  
**Nature :** audit documentaire ; aucune validation juridique/comptable, aucun changement de décision PO, aucune modification applicative.

## 1. Résultat exécutif

La recherche a permis de trouver des sources officielles utiles pour R42, R43, R44, R45, R46, R47, R50 et R51. Elles documentent des éléments précis, mais **ne ferment pas globalement R42–R51**.

Le point le plus sensible est **R44 — TVA** :
- la FAQ actuellement publiée par le Ministère des Finances indique un taux de **6 %** pour les médicaments et produits pharmaceutiques depuis le 01/01/2016, avec une exception de suspension pour certains médicaments sans similaire local ;
- le cadrage PO antérieur de PHARM'OS retient un taux de **7 %** pour le contexte de vente ;
- cette divergence doit être arbitrée sur la base d'une confirmation fiscale à jour. Le présent audit ne remplace pas le taux PO, ne le modifie pas et ne valide pas le 6 % comme règle universelle.

## 2. État des exigences R42–R51

| Exigence | Source/constat vérifié | Statut |
|---|---|---|
| **R42 — catégories réglementaires** | La DPM publie les listes AMM et des décisions de reclassement ; certains produits sont explicitement requalifiés en dispositifs médicaux dans les publications 2026. Cela confirme l'intérêt d'une séparation produit/médicament/DM, mais pas une taxonomie exhaustive des accessoires, cosmétiques, compléments et autres catégories. | **PARTIELLEMENT DOCUMENTÉ — OUVERT** |
| **R43 — classification et statut AMM** | La DPM publie les AMM en vigueur, supprimées et suspendues, avec listes par DCI, laboratoire et classe thérapeutique ; mise à jour affichée au 07/10/2026. Le droit/licence d'usage d'ATC et les identifiants d'interopérabilité restent à confirmer. | **PARTIELLEMENT DOCUMENTÉ — OUVERT** |
| **R44 — TVA et fiscalité** | La FAQ du Ministère affiche 6 % pour les médicaments et produits pharmaceutiques depuis 2016, avec exception de suspension pour certains produits ; le taux PO antérieur de 7 % doit être réconcilié. Les taux des autres catégories, services, timbres et facturation électronique restent à confirmer. | **ÉCART À ARBITRER — BLOQUANT AVANT PARAMÉTRAGE FISCAL** |
| **R45 — immobilisations** | Le Ministère recense NC 05 (immobilisations corporelles) et NC 06 (incorporelles) dans le système comptable des entreprises. NCE 02 est une norme des comptes de l'État et ne doit pas être transposée automatiquement à une officine privée. Les seuils, durées et méthodes propres au cas de l'officine restent à valider par expert-comptable. | **PARTIELLEMENT DOCUMENTÉ — OUVERT** |
| **R46 — pré-comptabilité / plan de comptes** | Loi 96-112 et NC 01 structurent le système comptable des entreprises. Le Ministère indique que les personnes tenant leur comptabilité sur ordinateur doivent déposer le programme initial/modifié auprès du bureau de contrôle fiscal et signaler matériel, lieu et changements. Il reste à déterminer si PHARM'OS, qualifié « Comptabilité à titre indicatif » par D80, entre dans ce champ selon son usage réel. | **PARTIELLEMENT DOCUMENTÉ — OUVERT** |
| **R47 — services pharmaceutiques** | La loi 73-55, art. 17, et l'arrêté du 27/07/1974 encadrent notamment les injections. Cette source ne valide pas toutes les prestations envisagées (mesure, dépistage, premiers soins, conseils, vaccinations et leurs conditions). | **PARTIELLEMENT DOCUMENTÉ — OUVERT** |
| **R48 — administrateur / délégation** | Le CNOPT publie l'arrêté du 07/10/2010 relatif au remplacement des pharmaciens d'officine. Ce texte de remplacement ne suffit pas à établir le droit de déléguer l'administration du logiciel ou des actes financiers. | **OUVERT** |
| **R49 — annulation de vente** | Les obligations générales de facturation sont documentées, mais aucune règle officielle précise sur le délai d'annulation, les avoirs, les tickets de caisse et la numérotation n'a été confirmée dans les sources consultées. D75 reste une règle PO, pas une validation fiscale. | **OUVERT** |
| **R50 — données de paie** | Les règles générales INPDP de déclaration préalable des traitements de données personnelles s'appliquent à examiner ; aucune règle spécifique complète sur les champs, accès, conservation et transferts de données de paie n'a été établie. | **PARTIELLEMENT DOCUMENTÉ — OUVERT** |
| **R51 — prix réglementés et marges** | La PCT met à disposition un jeu de prix de médicaments de juillet 2026 (prix de gros, prix officine, prix public) via le portail public Idaraty ; la politique des prix du Ministère décrit l'homologation administrative. Les circulaires officielles, dates d'effet, marges et règles par catégorie doivent être conservées et contrôlées à chaque import. | **PARTIELLEMENT DOCUMENTÉ — OUVERT** |

## 3. Sources officielles consultées

- **DPM — AMM et catégories** : [Liste des médicaments à usage humain](https://dpm.tn/medicament/humain/liste-des-medicaments), avec listes en vigueur, supprimées et suspendues, par DCI, laboratoire et classe thérapeutique ; [décisions du CTSP du 30 avril 2026](https://dpm.tn/pharmacie/renouvellement-d-inscription-sur-les-listes-d-attente/suivi-des-demandes-de-renouvellement-d-inscription-sur-les-listes-d-attente/pharmaciens-n-ayant-pas-regularises-leurs-situations/28-banner/472-comite-technique-des-specialites-pharmaceutiques-du-30-avril-2026).
- **TVA** : [FAQ du Ministère des Finances sur le taux de TVA des médicaments](https://www.finances.gov.tn/fr/faq?page=7) ; [aperçu fiscal du Ministère](https://www.finances.gov.tn/fr/fiscalite-service). La FAQ indique 6 % depuis le 01/01/2016 ; elle doit être confrontée au texte consolidé à jour et au cadrage PO 7 %.
- **Comptabilité / immobilisations** : [Conseil National de la Comptabilité](https://www.finances.gov.tn/fr/conseil-national-de-la-comptabilite-cnc) (loi 96-112, NC 01, NC 05 et NC 06) ; [obligations comptables en régime réel](https://www.finances.gov.tn/fr/node/951) ; [NCE 02](https://www.finances.gov.tn/fr/document/nce02immobilisations-corporelles), norme des comptes de l'État.
- **Exercice pharmaceutique** : [textes CNOPT sur la profession pharmaceutique](https://cnopt.tn/profession-pharmaceutique/) ; [textes applicables à l'officine](https://cnopt.tn/pharmacie-dofficine-2/) ; [arrêté du 27/07/1974 sur les injections](https://cnopt.tn/espace-juridique/arrete-du-27-juillet-1974-fixant-les-conditions-de-pratique-des-injections-par-les-pharmaciens/).
- **Données personnelles** : [INPDP — textes](https://www.inpdp.tn/Texte.html) et [manuel de procédures](https://www.inpdp.tn/Manuel_procedures_INPDP.pdf).
- **Prix** : [politique des prix du Ministère du Commerce](https://commerce.gov.tn/politique-des-prix_11_23) ; [jeu de données PCT — prix des médicaments, juillet 2026](https://www.idaraty.tn/public/index.php/fr/organisations/pharmacie-centrale-tunisie/datasets).

## 4. Effet sur EXT06

EXT06 §9 a été enrichie avec :
- les sources DPM pour R42/R43 ;
- le taux de 6 % indiqué par le Ministère et l'écart avec le cadrage PO 7 % pour R44 ;
- la distinction NC 05/NC 06 et NCE 02 pour R45 ;
- les obligations de logiciel de comptabilité informatisée à confirmer pour R46 ;
- les textes d'injection et les limites de preuve pour R47/R48 ;
- les formalités INPDP générales pour R50 ;
- la source PCT/Idaraty pour R51.

Aucun statut de décision PO n'est modifié. R42–R51 restent ouverts ou partiellement documentés.

## 5. Points nécessitant une décision ou confirmation

1. **R44 / TVA** : obtenir une confirmation fiscale écrite et arbitrer l'écart entre le taux PO 7 % et la FAQ ministérielle 6 % pour les médicaments/produits pharmaceutiques. Aucun taux global ne doit être corrigé par l'audit.
2. **R46 / logiciel comptable** : confirmer auprès de l'administration fiscale si la pré-comptabilité indicative D80 déclenche les formalités prévues pour la comptabilité tenue sur ordinateur.
3. **R47 / services** : obtenir une liste officielle actuelle et les conditions détaillées de chaque service avant le lot 9c.
4. **R48 / délégation** : obtenir un avis écrit sur la délégation d'administration logicielle et financière par le titulaire.
5. **R51 / prix** : obtenir les circulaires PCT officielles et conserver une version datée, les dates d'effet et les empreintes de chaque import.

## 6. Garanties

- Aucun taux de TVA n'a été changé.
- Aucune décision PO n'a été modifiée ou présumée.
- Aucun service pharmaceutique n'a été activé.
- Aucune importation de prix, AMM ou données de référence n'a été exécutée.
- Aucun code, schéma, base, test, migration, déploiement, service ou environnement DEMO n'a été modifié.

---
**Fin du rapport v0.29.**
