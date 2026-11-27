# PHARM'OS — P91 — Préparation contrôlée de l'Option B (libération mémoire)

Autorisation PO : 25/11/2026. Mission exclusivement préparatoire.
Statut : **rapport livré. Aucun palier exécuté. Aucun fichier de `/tmp` supprimé, déplacé ou modifié.**
Horodatage des derniers relevés : 15:18 UTC (25/11/2026). Données fictives uniquement ; aucun contenu sensible ouvert ; `codes.json` ni ouvert, ni haché, ni déplacé.

Étiquettes : FAIT VÉRIFIÉ / OBSERVATION / HYPOTHÈSE / PROPOSITION / NON TESTÉ / NON VÉRIFIÉ / LIMITE / CONDITION / OUVERT.

---

## 1. Synthèse

| Point | Constat |
|---|---|
| Contrainte | FAIT VÉRIFIÉ : `/tmp` est un tmpfs de 1 019 720 kB (50 % de MemTotal 2 039 440 kB), plein à 100 %, sans swap. Shmem = 1 020 544 kB. Ces pages ne sont récupérables que par suppression. |
| Mémoire | FAIT VÉRIFIÉ : MemAvailable = 590 564 kB à 15:18:52 (593 180 kB à 15:16:51). Plage observée en P89–P91 : 470 860 – 606 724 kB, sans changement de `/tmp`, cause NON IDENTIFIÉE. |
| Contenu de `/tmp` | FAIT VÉRIFIÉ : 2 035 fichiers, 1 039 507 493 o ; 25 entrées de premier niveau. Propriétaire `user` partout (sauf un dossier système root, vide, illisible). Dernière modification : 25/11 00:56:34. Aucune écriture récente. |
| Plus gros poste | `/tmp/backup/webapp_backup_ir3ywwou.tar.gz` : 618 676 kB (60,7 %). Archive **tronquée**, origine non documentée, aucune copie durable. |
| Éléments P76/P77/P78 | ≈ 351 000 kB. Les archives et packs sont **à préserver absolument** (règle PO). |
| Gain théorique | Palier 1 : ≈ 8,6 Mo. Palier 2 : ≈ 43 Mo cumulés. Palier 3 : ≈ 300 Mo cumulés. Palier 4 : ≈ 919 Mo cumulés (valeurs de taille, pas de RAM garantie ; voir §6). |
| Recommandation | PROPOSITION : aucun palier n'est « sans risque » en gain significatif. Le premier palier utile (≥ 250 Mo) est le palier 3, qui exige une **dérogation** PO. Le palier 4 exige une décision d'identification préalable. |

---

## 2. Résidu P90 : `/home/user/work/p88/tmp/pharmos-s2-w950Bg`

Examen limité aux métadonnées. Empreintes consignées dans `/home/user/work/p91/residu_sha.txt` (hors dépôt, mode 700) ; `codes.json` exclu.

| Élément | Valeur |
|---|---|
| Existence | FAIT VÉRIFIÉ : présent, inchangé entre le premier et le dernier relevé de P91 (21 fichiers). |
| Volume | 21 fichiers, 17 dossiers, 0 lien symbolique, 6 252 142 o (du : 6 216 kB). |
| Propriétaire / droits | `user:user` ; dossiers 700, fichiers 600. |
| Dates | Création 14:51:36 – 14:51:41 UTC le 25/11 (test P90 interrompu à 14:51:44). |
| Système de fichiers | FAIT VÉRIFIÉ : disque `/` (ext4), **pas tmpfs**. Il ne consomme **aucune RAM** et ne contribue pas au blocage mémoire. Il occupe ≈ 6,2 Mo de disque. |
| Contenu | Une base `control.db`, des bases `dbs/*.db` (en-têtes SQLite valides), des fichiers de travail, et `codes.json` (600, 1 273 o, mtime 14:51:38). Le contenu de `codes.json` n'a pas été lu. |
| Processus ouvrants | `lsof` : aucun processus n'a de fichier ouvert dans ce dossier (rc=1). |

**Précautions avant toute intervention future (PROPOSITION) :**
1. Ne jamais ouvrir, afficher, copier, hacher ni déplacer `codes.json`. Toute action sur ce dossier est une décision PO distincte.
2. Aucune libération de mémoire ne dépend de ce résidu. Il n'est pas candidat à l'Option B.
3. Si le PO décide un jour de le supprimer : suppression du dossier entier uniquement (pas de suppression partielle), après constat qu'aucun test n'est en cours, sans afficher le contenu des fichiers, après décision sur la conservation des empreintes (`residu_sha.txt`) comme preuve P90.
4. Conserver les droits 700/600 ; ne pas le placer dans une zone partagée ni dans le dépôt Git.
5. Ne pas lancer de test qui réutilise ce dossier. Les noms sont aléatoires (suffixe `w950Bg`), donc pas de collision attendue.
6. OBSERVATION : le disque est à 97 % (1 121 776 Ko libres) ; supprimer ce résidu ne libérerait que ≈ 6 Mo de disque et 0 de RAM.

---

## 3. Inventaire des candidats dans `/tmp`

Légende copie durable : **IDENTIQUE** = copie sur disque comparée par empreinte ; **NON VÉRIFIÉE** ; **ABSENTE**. Les empreintes sont tronquées à 16 caractères hexadécimaux (SHA-256) ; les empreintes complètes figurent dans `/home/user/work/p91/tmp_sha_full.txt`. Propriétaire de toutes les lignes : `user`. Les tailles en kB sont issues de `du -sk` (pour les dossiers) ou de la taille/1024 (pour les fichiers).

### 3.A Archive de sauvegarde (catégorie : archives de sauvegarde)

| Chemin | Taille | Origine | Date | Empreinte | Copie durable | Preuve de restauration | Risque |
|---|---|---|---|---|---|---|---|
| `/tmp/backup/webapp_backup_ir3ywwou.tar.gz` | 633 524 224 o (618 676 kB) | **Inconnue** (HYPOTHÈSE : mécanisme de sauvegarde de la plateforme ; non documenté dans les rapports) | 2026-11-25 00:56:34 | `0e247faf7c855435…` (complète : `0e247faf7c85543598581594e4ffc24cb5256f5c1332caf8a44d1270d52254b7`) | **ABSENTE**. Aucune copie de même taille ou empreinte hors `/tmp`. Le fichier homonyme `/home/user/uploaded_files/webapp_backup_ir3ywwou.tar.gz` (1 935 949 o, 09/11) est un **autre contenu**, plus ancien : ce n'est pas une copie. | **ABSENTE** : `gzip -t` rc=1 « unexpected end of file » (archive tronquée ; taille multiple de 4096 : HYPOTHÈSE de troncature par saturation disque/tmpfs). Aucune restauration complète n'est possible. | Perte définitive d'un instantané partiel du 25/11 00:56. Atténuation : 3 564 membres lisibles dont 2 560 environ présents dans Git ; 1 004 membres non suivis par Git (985 objets `.git`, hooks, config, un fichier docs à accent) ; 24 fichiers suivis par Git absents de l'archive (docs P81–P89 créés après). Pas de `pharmos/core`. |

OBSERVATION : l'archive contient les objets `.git` du 25/11 00:56. Leur présence dans le dépôt actuel n'a pas été vérifiée objet par objet (NON VÉRIFIÉ). Le dépôt vivant et la sauvegarde automatique SB-Git existent ; le dernier push vers SB-Git n'est pas vérifiable localement (fetch interdit).

### 3.B Archives de référence P73 (catégorie : archives de référence + éléments P73)

| Chemin | Taille | Origine | Date | Empreinte | Copie durable | Preuve de restauration | Risque |
|---|---|---|---|---|---|---|---|
| `/tmp/p73_bases_essentielles.tar.gz` | 6 018 490 o (5 878 kB) | P73 (connue) | 24/11 15:09:46 | `8543f99c70b89598…` | Disque : **ABSENTE** (`work/p73` ne contient que `bases.sha`, pas l'archive). Externe (`gsk upload`) : rapport P73 et `up_bases.json` statut ok, tailles concordantes ; **NON VÉRIFIABLE** aujourd'hui (AI Drive inaccessible : plan/crédit). | Présente : P73 a restauré et comparé : 24/24 empreintes OK. 10/10 `.db` identiques aux originaux de `.pharmos-private/backups/demo-env-*`. | Moyen : seule copie locale de l'archive ; le contenu `.db` existe aussi dans `.pharmos-private`. |
| `/tmp/p73_kit_reprise.tgz` | 105 727 o (103 kB) | P73 | 24/11 15:06:21 | `db8e0c4e89679b0b…` | Disque : **ABSENTE** (empreinte seule dans `work/p73/kit.sha`). Externe : NON VÉRIFIABLE. | Présente (vérifié en P73). | Faible à moyen : kit de reprise ; sa perte oblige à le reconstruire. |
| `/tmp/p73_verif/bases.tar.gz` | 5 880 kB | Re-téléchargement P73 | 24/11 | `8543f99c70b89598…` (= archive ci-dessus) | Doublon exact de `/tmp/p73_bases_essentielles.tar.gz`, donc l'identique subsiste en `/tmp`. | Idem | Nul pour le contenu tant que l'original reste ; ne constitue pas une copie durable. |
| `/tmp/p73_verif/kit.tgz` | 104 kB | Re-téléchargement P73 | 24/11 | `db8e0c4e89679b0b…` | Doublon exact de `/tmp/p73_kit_reprise.tgz`. | Idem | Idem |

### 3.C Dérivés de contrôle P73

| Chemin | Taille | Origine | Date | Empreinte agrégée | Copie durable | Preuve | Risque |
|---|---|---|---|---|---|---|---|
| `/tmp/p73_verif/x/` (extraction de contrôle : 5 sauvegardes `demo-env-*`) | 34 820 kB | P73 (connue) | 24/11 15:12 | voir `tmp_sha_full.txt` ; agrégat du dossier parent `/tmp/p73_verif` : `d5eb65f843dd075e…` | **IDENTIQUE** pour les 10 `.db` (comparés aux originaux de `/home/user/.pharmos-private/backups/demo-env-*/db`). | Présente (24/24 empreintes OK vs `ext_bases_sha256_attendu`). | Faible : dérivé reconstructible depuis l'archive ; la perte n'efface pas de preuve unique. |
| `/tmp/p73_verif/` autres fichiers | ≈ 452 kB (par différence) | P73 | 24/11 | `d5eb65f843dd075e…` (agrégat) | NON VÉRIFIÉE | — | Faible |
| `/tmp/p73kit/` (28 fichiers) | 420 kB | P73 | 24/11 15:06:21 | `24f65c652221afc7…` | Contenu = kit ; archive correspondante `/tmp/p73_kit_reprise.tgz` (voir 3.B). | Présente | Faible |

Total groupe P73 : ≈ 47 660 kB (`p73_verif` 41 256 + `p73kit` 420 + archives 5 878 + 103).

### 3.D Éléments P76/P77/P78

**Préservation obligatoire : aucune archive ou pack P76/P77/P78 n'est candidat.** Les dérivés ci-dessous ne sont listés que pour la proposition de dérogation (palier 3), parce que leur suppression retirerait aussi des copies de packs.

| Chemin | Taille | Contenu | Origine / date | Empreinte | Copie durable | Preuve | Risque |
|---|---|---|---|---|---|---|---|
| `/tmp/p77_pkg/` | 62 492 kB (8 f.) | Contient `p77_sauvegarde_historique_v2.tar` (31 989 760 o), le pack v2 et le dossier extrait | P77, 24/11 20:25:27 | tar `703705862bbc848f…` ; dossier `670532caae5d1c67…` | Pack v2 `77ba1f4ff516f8a1…` : **IDENTIQUE** à `work/p76/p76_historique_v2.pack`. **Tar v2 : ABSENTE sur disque** (work/p77 = 5 petits fichiers). Externe : P77 l'a déposé et re-téléchargé 11 s plus tard dans le même sandbox ; NON VÉRIFIABLE aujourd'hui. | Présente (P77). | **ÉLEVÉ : copie locale unique du tar v2. PRÉSERVATION ABSOLUE.** |
| `/tmp/p77_dl/retour.tar` | 31 240 kB | Copie re-téléchargée du tar v2 | P77, 24/11 20:25:55 | `703705862bbc848f…` (identique à p77_pkg) | Aucune copie disque. | Présente | Archive P77 : **PRÉSERVATION** (règle PO). |
| `/tmp/p77_dl/` (hors `retour.tar`) | ≈ 62 616 kB (par différence ; total 93 856 kB, 29 f.) | Extractions de contrôle (`x/`, `rest/`) incluant des copies du pack v2 | P77 | `b8be042347522b84…` (agrégat du dossier) | Pack : IDENTIQUE (work/p76). Autres fichiers : dérivés reconstructibles. | Présente | Moyen : retire 2 copies du pack v2 ; la copie disque `work/p76` subsiste. |
| `/tmp/p77_restore/` | 31 368 kB (22 f.) | Dépôt restauré de contrôle (contient une copie du pack v2) | P77, 24/11 20:24:46 | `b28d1e8748106492…` | Pack : IDENTIQUE (work/p76). | Présente (restauration P77) | Moyen (dérogation). |
| `/tmp/p78_restore/` | 98 768 kB (1 275 f.) | `r.git` 31 412 kB (copie du pack v3) ; `w/` 67 356 kB (1 253 f. : 1 252 identiques au dépôt actuel, 1 différent `pharmos/tests/ui3/stock_d1.test.ts` = version du commit 81fbb6d, HEAD diffère par le commit 4f58c09 du 25/11 00:35:56 : divergence attendue) | P78, 24/11 21:14:50 | `5473e8727d1ea69e…` | Pack v3 `0391879ee6395091…` : **IDENTIQUE** à `work/p78/p78_historique_v3.pack`. `w/` : reconstructible depuis le pack. | Présente (P78 : restauration vérifiée, 321 commits). | Moyen : preuve de restauration P78 (dérivée). Le tar v3 est conforme à `.tar.sha` dans `work/p78`. |
| `/tmp/p78_tartest/` | 62 712 kB (30 f.) | Extraction de contrôle du tar v3 (copies du pack v3) | P78, 24/11 21:15:53 | `82c6f5ef99c8d636…` | Pack : IDENTIQUE (work/p78). Tar v3 : présent dans `work/p78` (conforme). | Présente | Moyen (dérogation). |
| 13 listes `/tmp/p77_*.txt`, `/tmp/p78_*.txt` | ≈ 1 544 kB au total | Listes de chemins/contrôles | P77/P78, 24/11 | voir `niveau1.txt` | 4 listes ont une copie identique dans `work/p76` (`p77_ce` = `p78_ce` ; `p77_missing` = `p78_miss`). **9 sans copie identique** : `p77_15`, `p77_paths`, `p78_att`, `p78_chemins`, `p78_chemins_archives`, `p78_ext`, `p78_lt_local`, `p78_need`, `p78_paths` (`p78_att` = `p78_ext` entre elles). | Non applicable | Faible, mais éléments P77/P78 : dérogation. |

Packs P76/P77/P78 dans `/tmp` : pack v2 `77ba1f4ff516f8a1…` ×4 ; pack v3 `0391879ee6395091…` ×3 ; tous identiques aux copies disque (`work/p76`, `work/p78`). Archives non candidates.

### 3.E Fichiers temporaires

| Chemin | Taille | Origine | Date | Empreinte | Copie durable | Risque |
|---|---|---|---|---|---|---|
| `/tmp/node-compile-cache/` | 2 644 kB (566 f.) | Cache de compilation Node (connue : cache régénérable) | 24/11 17:19:42 | `4cd9f52a106d96d7…` | Sans objet (régénérable). | Très faible. OBSERVATION : le processus DEMO (PID 5638) n'a pas `NODE_COMPILE_CACHE` défini ; aucun lien établi avec lui (NON VÉRIFIÉ pour d'autres processus). |
| `/tmp/systemd-private-…-chrony.service-…` | vide (root) | Système | — | — | Hors périmètre : **NON AUTORISÉ**, jamais candidat. |

### 3.F Doublons observés

FAIT VÉRIFIÉ : 110 groupes de doublons exacts dans `/tmp`, 212 673 012 o redondants au total (essentiellement les packs/extractions P77/P78 et la double copie P73). Hors P77/P78/backup : 7 057 362 o (≈ 6 891 kB). Détail : `/home/user/work/p91/dups_hashes.txt`.

### 3.G Récapitulatif de couverture

| Groupe | kB (du) |
|---|---|
| `/tmp/backup` | 618 676 |
| Éléments P73 | ≈ 47 660 |
| Éléments P77 | 187 716 (62 492 + 93 856 + 31 368) |
| Éléments P78 | 161 480 (98 768 + 62 712) |
| Listes P77/P78 | ≈ 1 544 |
| `node-compile-cache` | 2 644 |
| **Total** | **≈ 1 019 720** (= taille du tmpfs ; cohérent avec Shmem 1 020 544 kB) |

---

## 4. État des copies durables

| Élément | État | Preuve / limite |
|---|---|---|
| `.db` P73 (10 fichiers) | **IDENTIQUE** | Comparés aux originaux sur disque ext4 (`.pharmos-private/backups/demo-env-*`). |
| Archives P73 (bases, kit) | Disque : **ABSENTE**. Externe : **NON VÉRIFIÉE** | Rapport P73 : aller-retour `gsk upload`/`download` vérifié par SHA-256 (déclaré, non rejouable). `gsk aidrive ls` renvoie une erreur de plan/crédit ; `/mnt/aidrive` est un simple répertoire vide. |
| Pack v2 (P76/P77) | **IDENTIQUE** | `work/p76/p76_historique_v2.pack` = `77ba1f4ff516f8a1…`. Le pack v1 (`c84aa4f8be3d7e58…`) est défectueux (rapport P76). |
| Tar v2 P77 | Disque : **ABSENTE**. Externe : **NON VÉRIFIÉE** | Copie locale unique dans `/tmp/p77_pkg` (et un doublon dans `p77_dl`). |
| Pack v3 + tar v3 P78 | **IDENTIQUE** (pack et tar) | `work/p78`. Le rapport P78 signale que la v3 est locale uniquement sur le volume `vda` jugé « endommagé » : OBSERVATION de risque de la copie durable elle-même. |
| Listes P77/P78 | 4 identiques, 9 sans copie | Voir 3.D. |
| `/tmp/backup/webapp_backup_ir3ywwou.tar.gz` | **ABSENTE** | Aucune copie ; le fichier homonyme d'`uploaded_files` est un autre contenu. |
| Sauvegarde automatique SB-Git | **NON VÉRIFIÉE** | Dernier commit local `288399f` (14:55:20) ; `refs/remotes/genspark/main` absent localement ; fetch interdit en P91. |

Cohérence avec les rapports historiques : tailles et empreintes P73, P77, P78 **concordent** avec les rapports (FAIT VÉRIFIÉ). L'archive `/tmp/backup` n'est mentionnée dans aucun rapport PHARM'OS (origine non documentée).

Rappel de la règle : aucune archive n'est jugée supprimable sur son nom ou son ancienneté. Dans ce rapport, « supprimable » signifie uniquement « conditions du palier remplies et autorisation PO donnée ».

---

## 5. Analyse mémoire

### 5.1 Relevés

| Heure (UTC, 25/11) | MemAvailable (kB) | Remarque |
|---|---|---|
| 14:51:44 (P90) | 470 860 | Arrêt du chien de garde pendant R0d |
| Pendant P91, lectures de `/tmp/backup` (≈ 633 Mo) | ≈ 572 000 – 576 000 | Baisse transitoire due au cache de pages |
| 15:16:51 | 593 180 | Après retour au calme |
| 15:18:52 | 590 564 | Dernier relevé avant rédaction |

Autres valeurs à 15:18 : MemTotal 2 039 440 kB ; MemFree 170 644 kB ; Buffers 250 004 kB ; Cached 1 108 248 kB (dont Shmem 1 020 544 kB) ; SReclaimable 271 404 kB ; swap 0.
OBSERVATION : le Cached inclut le tmpfs. Il n'est donc pas récupérable en dehors d'une suppression. MemAvailable reste surtout alimenté par Buffers, le cache fichier non-tmpfs et le slab réclamable.

### 5.2 Consommation par processus (RSS, `ps`)

| PID | Processus | RSS (kB) |
|---|---|---|
| 5638 | node (DEMO) | 84 136 |
| 347 | envd | 82 768 |
| 613 | PM2 God | 39 348 |
| 278 | systemd-journal | 15 168 |
| 1 | systemd | 9 408 |

FAIT VÉRIFIÉ : la somme des RSS des processus est d'environ 230 Mo, soit ≈ 11 % de MemTotal. Le tmpfs (≈ 1 020 Mo) est de loin le principal consommateur. Les processus ne sont pas la cause du blocage.

### 5.3 Capacité théorique récupérable

Principe : supprimer un fichier tmpfs libère ses pages immédiatement et de façon irréversible. La taille `du` est donc le **maximum théorique** de Shmem libéré. Elle n'est **pas** une mesure de RAM récupérée.

Justification de la prudence :
- Les pages libérées deviennent MemFree, donc comptées dans MemAvailable. Le gain réel attendu est proche de la taille supprimée, mais **NON TESTÉ**.
- MemAvailable varie de ±60 à 120 Mo sans action sur `/tmp` (cause non identifiée). Un gain inférieur à ≈ 100 Mo est donc **inobservable de façon fiable**.
- Les lectures de fichiers (hachage, `tar -t`) peuvent occuper temporairement du cache et fausser la mesure.
- Estimation prudente retenue : **80 % de la taille supprimée** (PROPOSITION, non mesurée).

| Palier | Taille cumulée (kB) | Gain prudent à 80 % (kB) | MemAvailable attendu (base 590 564 kB) | Seuil de lancement 500 000 kB |
|---|---|---|---|---|
| Aucun | 0 | 0 | ≈ 590 000 | franchi, mais marge instable (a atteint 470 860) |
| 1 | 8 628 | ≈ 6 900 | ≈ 597 000 | inchangé : sans effet décisif |
| 2 | 43 448 | ≈ 34 800 | ≈ 625 000 | marge faible, dans le bruit de mesure |
| 3 | 300 456 | ≈ 240 000 | ≈ 830 000 | marge nette |
| 4 | 919 132 | ≈ 735 000 | ≈ 1 325 000 (plafonné par MemTotal et autres consommations ; valeur indicative) | marge très large |

Détail des tailles cumulées : palier 1 = 2 644 + 5 880 + 104 ; palier 2 = palier 1 + 34 820 ; palier 3 = palier 2 + 257 008 (98 768 + 62 712 + 31 368 + 62 616 + 1 544) ; palier 4 = palier 3 + 618 676.

Le palier 3 est le premier qui modifie la marge de façon observable.

LIMITE : le palier 4 donnerait un gain théorique très supérieur à ce qu'exige P88. Les autres paliers peuvent suffire, ce qui rend le palier 4 non indispensable.

---

## 6. Proposition de libération par paliers (PROPOSITION : rien n'est exécuté)

Ordre prudent : du moins risqué au plus risqué. Chaque palier exige une décision PO écrite distincte.

### Palier 0 — Aucune intervention (option de référence)
- Contenu : aucun.
- Mémoire récupérée : 0.
- Risque : aucun fichier touché. Le blocage P90 (MemAvailable < 500 000 kB) peut se reproduire.
- Autorisation : aucune.

### Palier 1 — Sans risque identifié (gain très faible)
- Contenu :
  - `/tmp/node-compile-cache/` (2 644 kB) : cache régénérable ;
  - `/tmp/p73_verif/bases.tar.gz` (5 880 kB) et `/tmp/p73_verif/kit.tgz` (104 kB) : doublons exacts des archives P73 d'origine, qui restent dans `/tmp`.
- Mémoire potentiellement récupérable : ≈ 8 628 kB (≈ 6 900 kB prudents).
- Risques : le cache est reconstruit au prochain usage Node. Pour les doublons, perte de contenu nulle, mais la copie restante est la même archive non durable. Le gain reste dans le bruit de mesure.
- Preuve de conservation : empreintes identiques des originaux (`8543f99c70b89598…`, `db8e0c4e89679b0b…`) ; originaux inchangés.
- Conditions préalables : empreintes revérifiées juste avant l'action ; aucune ouverture en cours (`lsof`) ; relevé MemAvailable avant/après.
- Autorisation PO : décision explicite pour ces 3 éléments seulement.
- Réserve : ce palier ne justifie pas à lui seul l'Option B.

### Palier 2 — Nécessitant une copie durable
- Contenu : `/tmp/p73_verif/x/` (34 820 kB), extraction de contrôle P73. Les 10 `.db` sont déjà identiques aux originaux sur disque (FAIT VÉRIFIÉ). Les autres fichiers de ce dossier relèvent d'un contrôle d'empreinte à refaire avant action (NON VÉRIFIÉ).
- Mémoire potentiellement récupérable : ≈ 34 820 kB (≈ 27 900 kB prudents).
- Risques : perte d'un dérivé de contrôle reconstructible depuis l'archive P73.
- Preuve de conservation : `.db` identiques aux originaux ; liste des empreintes (24/24) conservée dans `work/p73`. Condition : les empreintes de tous les fichiers du dossier sont comparées avant suppression.
- Condition préalable (copie durable) : si le PO veut conserver aussi les **archives P73** avec une copie disque, une copie de 5 878 kB + 103 kB sur disque est à autoriser séparément (la mission P91 interdit toute création de copie). Disque : 1 121 776 Ko libres, marge de 321 776 Ko avant le seuil d'arrêt (800 000 Ko) ; ce volume est compatible.
- Autorisation PO : (a) suppression de `p73_verif/x` ; (b) éventuellement création de la copie disque des archives P73.

### Palier 3 — Nécessitant une dérogation (éléments P77/P78, hors archives)
- Contenu (dérivés uniquement) :
  - `/tmp/p78_restore/` (98 768 kB), `/tmp/p78_tartest/` (62 712 kB), `/tmp/p77_restore/` (31 368 kB) ;
  - `/tmp/p77_dl/` **sauf `retour.tar`** (≈ 62 616 kB) ;
  - 13 listes `/tmp/p77_*.txt` et `/tmp/p78_*.txt` (≈ 1 544 kB).
- Mémoire potentiellement récupérable : ≈ 257 008 kB (≈ 205 600 kB prudents) ; cumulé avec les paliers 1 et 2 : ≈ 300 456 kB.
- Pourquoi une dérogation : la règle PO préserve les éléments P76/P77/P78. Ces dossiers ne sont pas des archives, mais ils contiennent des **copies de packs** et la preuve de restauration de P77/P78. La suppression modifie donc l'état des preuves historiques, à autoriser expressément.
- Risques :
  - perte des copies de packs dans `/tmp` (les copies disque `work/p76` et `work/p78` subsistent, identiques) ;
  - perte de la preuve de restauration P78 déposée dans `p78_restore` (la restauration est reproductible depuis `work/p78`, NON REJOUÉE) ;
  - 9 listes n'ont **aucune copie identique** : leur perte est définitive (utilité résiduelle faible, mais non nulle pour la traçabilité) ;
  - rappel : le volume `vda` hébergeant `work/p78` est signalé « endommagé » par P78 ; les copies disque ne sont donc pas pleinement fiables (OBSERVATION).
- Preuve de conservation : empreintes des packs `77ba1f4ff516f8a1…` et `0391879ee6395091…` identiques à `work/` ; tar v3 conforme à `.tar.sha` ; à rejouer juste avant l'action.
- Conditions préalables :
  - dérogation PO écrite listant les chemins exacts ;
  - décision sur les 9 listes sans copie (conserver en les copiant sur disque, ou accepter la perte) ;
  - `lsof` sans accès ouvert ; vérification des empreintes immédiatement avant.
- Autorisation PO : dérogation à la règle de préservation P76/P77/P78 pour les seuls dérivés nommés.

### Palier 4 — Identification préalable + décision PO (plus gros gain)
- Contenu : `/tmp/backup/webapp_backup_ir3ywwou.tar.gz` (618 676 kB).
- Mémoire potentiellement récupérable : ≈ 618 676 kB (≈ 495 000 kB prudents) ; cumulé : ≈ 919 132 kB.
- Risques :
  - archive **tronquée**, origine **inconnue** (mécanisme plateforme supposé) ; aucune copie durable ; suppression irréversible ;
  - **déplacement vers le disque impossible en sécurité** : 1 121 776 − 618 676 ≈ 503 100 Ko libres, soit moins que le seuil d'arrêt disque de 800 000 Ko ;
  - tout accès à cette archive (`gzip -t`, `tar -t`) fait baisser MemAvailable de ≈ 20 à 30 Mo pendant la lecture ;
  - perte d'un instantané partiel, dont 1 004 membres non suivis par Git (objets `.git`, hooks, config).
- Preuve de conservation : aucune restauration complète possible ; contenu majoritairement présent dans Git (HEAD actuel) ; objets `.git` internes non vérifiés individuellement.
- Conditions préalables (CONDITION) :
  1. Le PO identifie l'origine de l'archive (génération par la plateforme ? usage attendu ?) ;
  2. Le PO accepte la perte du contenu partiel, ou demande une copie vers un volume externe (taille 633 Mo : incompatible avec le disque actuel) ;
  3. Les paliers 1 à 3 sont d'abord jugés insuffisants.
- Autorisation PO : décision explicite de suppression de cet unique fichier, après examen de ces conditions.

### Contenu à préserver absolument (aucun palier)
- Toutes les archives et tous les packs P76/P77/P78 : `/tmp/p77_pkg/p77_sauvegarde_historique_v2.tar` (**copie locale unique**), `/tmp/p77_dl/retour.tar`, ainsi que les packs v2 et v3 et leurs copies disque sous `work/`.
- Archives P73 `/tmp/p73_bases_essentielles.tar.gz` et `/tmp/p73_kit_reprise.tgz` tant qu'aucune copie disque n'existe.
- Le dump `pharmos/core` (750 096 384 o), `/home/user/.pharmos-private/**`, `/home/user/work/p88/**` (dont le résidu et `codes.json`), `/home/user/uploaded_files/**`.
- Le dossier système root de `/tmp` (hors périmètre).

---

## 7. Risques et réserves

1. **Effet réel non testé** : le gain RAM par palier est une estimation. MemAvailable fluctue de ±60 à 120 Mo sans action ; mesurer avant/après avec plusieurs relevés.
2. **Irréversibilité** : un tmpfs n'a ni corbeille ni swap. Toute suppression y est définitive.
3. **Copies durables fragiles** : AI Drive est inaccessible (plan/crédit) ; les copies « externes » des rapports P73/P77 ne sont pas vérifiables. Les copies disque dépendent d'un volume signalé « endommagé » et d'un disque plein à 97 %.
4. **Disque** : 1 121 776 Ko libres ; marge de 321 776 Ko avant l'arrêt à 800 000 Ko. Toute copie durable doit rester très inférieure à cette marge.
5. **Archive `/tmp/backup`** : origine inconnue, tronquée ; une suppression sans identification pourrait retirer un artefact attendu par la plateforme (HYPOTHÈSE, NON VÉRIFIÉE).
6. **Sauvegarde SB-Git** : l'état distant n'est pas vérifiable localement ; la copie durable du code repose sur elle (NON VÉRIFIÉ).
7. **Réapparition du blocage** : même après libération, MemAvailable peut baisser pendant un test (P90 : −117 Mo en 8 s). Les seuils de P84/P90 restent inchangés.
8. **Aucune réparation du système de fichiers** n'est proposée ; le disque à 97 % reste une contrainte indépendante.

## 8. Autorisations PO nécessaires (liste précise)

| Réf. | Autorisation | Palier | Obligatoire pour |
|---|---|---|---|
| A0 | Aucune (rester au palier 0) | 0 | — |
| A1 | Suppression de `/tmp/node-compile-cache`, `/tmp/p73_verif/bases.tar.gz`, `/tmp/p73_verif/kit.tgz` | 1 | gain ≈ 8,6 Mo |
| A2 | Suppression de `/tmp/p73_verif/x` après comparaison complète des empreintes | 2 | gain ≈ 34,8 Mo |
| A3 | (Optionnelle) création d'une copie disque des archives P73 (≈ 6 Mo) | 2 | conservation durable avant tout retrait futur des archives P73 |
| A4 | Dérogation à la règle P76/P77/P78 pour les dérivés `p77_dl` (hors `retour.tar`), `p77_restore`, `p78_restore`, `p78_tartest` | 3 | gain ≈ 255 Mo |
| A5 | Décision sur les 9 listes sans copie identique (conserver par copie disque, ou accepter la perte) et suppression des 13 listes | 3 | gain ≈ 1,5 Mo |
| A6 | Identification de l'origine de `/tmp/backup/webapp_backup_ir3ywwou.tar.gz` et décision de suppression | 4 | gain ≈ 618 Mo |
| A7 | Relevés mémoire avant/après et seuils d'arrêt d'une éventuelle exécution (reprendre ceux de P84/P90) | tous | contrôle |
| A8 | Toute action sur le résidu P90 (`pharmos-s2-w950Bg`, dont `codes.json`) | hors paliers | décision distincte, sans effet RAM |

NON AUTORISÉ sans décision séparée : toute action sur les archives P76/P77/P78, `pharmos/core`, `.pharmos-private`, et le relancement de P88.

## 9. Effets de bord et limites déclarés de P91

- Lectures de `/tmp` (find, stat, sha256sum, `gzip -t`, `zcat | tar -t`) : atime modifiés ; baisse transitoire de MemAvailable à ≈ 572 000 – 576 000 kB pendant la lecture de `/tmp/backup`, puis retour à ≈ 600 000 kB.
- `sudo -n` utilisé plusieurs fois pour `lsof` (lecture seule).
- `gsk aidrive ls` : erreur de plan/crédit (aucune écriture).
- `bc` absent : sommes recalculées à la main (écarts d'arrondi ≤ 1 kB par ligne possibles).
- Fichiers de travail créés hors dépôt : `/home/user/work/p91/` (mode 700 ; noms de fichiers et empreintes, aucun contenu sensible).
- Aucun test, build, migration, suppression, déplacement, arrêt de processus, ni opération Git manuelle.
- Non vérifié : effet réel de chaque suppression ; contenu des objets Git de l'archive tronquée ; copies externes AI Drive ; état distant SB-Git.

## 10. Clôture

Mission P91 : rapport livré. Aucun palier réalisé. P88 non relancée. P92 non créée. L'état de PR-0, de la migration 0022 (non créée), de G6 (non proposée) et de la phase 2 (suspendue) est inchangé. Toute intervention sur les fichiers requiert une décision PO distincte fondée sur ce rapport.
