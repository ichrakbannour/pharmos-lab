# PHARM'OS — P89 — Rapport de diagnostic mémoire et tmpfs (lecture seule)

Date : 25/11/2026 (mesures du 25/11/2026, 13:57 à 14:05 UTC). Hôte : noyau 6.1.155, 2 cœurs, 2 039 440 kB de RAM, sans swap.

**Statut : diagnostic terminé. Aucune action corrective effectuée. La reprise de P88 reste SUSPENDUE jusqu'à décision explicite du PO.**

---

## 0. Résumé

1. **FAIT VÉRIFIÉ** : `/tmp` est un tmpfs plein (1 019 720 kB utilisés sur 1 019 720 kB, soit exactement 50 % de la RAM). Il occupe **1 020 544 kB de RAM (50,0 %)**, non récupérables car il n'y a pas de swap.
2. **FAIT VÉRIFIÉ** : cette occupation explique pourquoi la mémoire disponible est structurellement basse (≈ 0,6 Go au lieu d'environ 1,6 Go sans ce contenu, calcul arithmétique).
3. **FAIT VÉRIFIÉ** : elle **n'explique pas les variations** observées. Le contenu de `/tmp` est constant (`Shmem` identique à chaque relevé, aucun fichier modifié depuis le 25/11 00:56:34), alors que `MemAvailable` a varié de 489 648 à 606 724 kB, soit 117 Mo.
4. **CORRECTION de l'hypothèse P88** : P88 attribuait la baisse de ≈ 125 Mo au cache ou à `Shmem` du tmpfs. Cette part est **infirmée** (`Shmem` constant). Seule la contrainte structurelle est confirmée.
5. **OBSERVATION** : au dernier relevé (14:04:53), `MemAvailable` = 600 148 kB, soit au-dessus du plancher de 500 000 kB. Le blocage P88 était donc **transitoire**, mais la marge est faible (≈ 100 Mo) et la cause des variations est **NON IDENTIFIÉE**.
6. **FAIT VÉRIFIÉ** : aucun processus ne détient de fichier sous `/tmp`. Aucun fichier n'est « actif ».
7. **OBSERVATION** : le plus gros fichier (`/tmp/backup/webapp_backup_ir3ywwou.tar.gz`, 633 524 224 o, 60,7 % de `/tmp`) a une **origine non documentée** et n'a pas été ouvert. Son statut est INCONNU.
8. Aucune suppression n'est recommandée sans vérification préalable de copies durables et sans autorisation PO (§9 et §10).

---

## 1. Cadre et effets de bord déclarés

Respecté : aucun fichier supprimé, déplacé ou modifié ; `/tmp` non nettoyé ; aucun service ou processus redémarré ou arrêté ; seuils inchangés ; aucun test, build ni migration ; DEMO non touchée ; Git, code et documents P81–P88 non modifiés ; aucun secret ouvert ; aucun contenu d'archive ouvert ; aucun fichier créé hors de ce rapport.

Effets de bord de lecture à déclarer par honnêteté :
- **`sudo -n` en lecture seule** (au moins 7 appels) pour `lsof`, `/proc/slabinfo` et `dmesg`. Ces appels ont produit des lignes d'audit (PAM) dans le journal noyau. Aucun état n'a été modifié.
- **Dates d'accès (atime)** : la lecture de fichiers sous `relatime` a pu mettre à jour l'atime de métadonnées, notamment `/tmp/p77_pkg/p77_sauvegarde_historique_v2.tar` (hachage de 32 Mo) et des répertoires parcourus. Les contenus et les dates de modification sont inchangés.
- **`git status`** a pu rafraîchir l'index sans modifier de contenu.
- **Erreur de manipulation** : la vérification des empreintes de docs, lancée une première fois depuis le mauvais répertoire, a signalé de faux « FAILED ». Refaite depuis `/home/user/webapp` : 19/19 et 2/2 OK.

---

## 2. Mesures mémoire horodatées (UTC, 25/11/2026)

### 2.1 Relevé instantané 13:57:34 (`free -k` et `/proc/meminfo`)
| Poste | kB |
|---|---|
| MemTotal | 2 039 440 |
| MemFree | 93 908 |
| **MemAvailable** | **582 092** |
| « used » (procps : total − available) | 1 457 348 |
| Buffers | 95 928 |
| Cached (dont Shmem) | 1 361 884 |
| **Shmem** | **1 020 544** |
| AnonPages | 185 208 |
| Active(file) / Inactive(file) | 181 580 / 255 684 |
| Active(anon) / Inactive(anon) | 400 364 / 805 300 |
| Slab (SReclaimable / SUnreclaim) | 264 024 (238 996 / 25 028) |
| KernelStack / PageTables / Percpu | 1 592 / 3 016 / 568 |
| Swap (total / utilisé) | 0 / 0 |
| Dirty / Writeback | 916 / 0 |
| Mapped | 51 512 |
| CommitLimit / Committed_AS | 1 019 720 / 1 413 868 |

Paramètres noyau lus : `vm.swappiness`=60 (sans effet, pas de swap), `overcommit_memory`=0, `min_free_kbytes`=45 056, `vfs_cache_pressure`=100. Pas de `/proc/pressure/memory` (PSI indisponible : les blocages mémoire ne sont pas mesurables).

Contrôle de cohérence : Active(anon) + Inactive(anon) = 1 205 664 ≈ AnonPages + Shmem (1 205 752). La RAM des fichiers de `/tmp` est donc bien comptée dans l'anonyme du noyau.

### 2.2 Séries
| Heure | MemAvailable (kB) | Note |
|---|---|---|
| 13:57:43 → 13:58:13 (7 relevés, pas de 5 s) | 582 220 | stable |
| 13:58:18, 13:58:23 | 581 968 | |
| 13:58:28 → 13:58:38 | 581 716 | −504 kB sur 55 s |
| 13:59:29 (vmstat, 5 s) | MemFree 99 116 → 99 392 ; si/so = 0 ; CPU 97–99 % inactif ; wa = 0 | |
| 14:00:53 | 582 352 | série 2 |
| 14:00:57 → 14:01:21 | 582 420 | stable |
| **14:02:04** | **606 724** | saut de +24 Mo sans cause identifiée |
| 14:03:43 → 14:03:55 | 600 488 puis 600 460 | |
| 14:04:49 → 14:04:53 | 600 172 puis 600 148 | |

Pendant la série 1, `Shmem` est resté à 1 020 544 kB sur les 12 relevés. Pendant la série 2, il est resté à 1 020 544 kB sur les 8 relevés. Entre les deux séries, `SReclaimable` est passé de 238 996 à 224 040 kB et le cache fichier (Active+Inactive file) de 437 392 à 451 600 kB.

### 2.3 Relevés P88 (rappel, heures exactes non relevées : ≈ 13:48–13:54 UTC)
619 840 (début P88) → 494 764 / 494 260 / 499 736 → **489 648** (minimum observé) → 497 900 → 497 432–497 488. LIMITE : ces valeurs n'étaient pas horodatées.

**Plage totale observée** : 489 648 à 606 724 kB, soit une amplitude de 117 076 kB (5,7 % de la RAM), sans modification du contenu de `/tmp`.

Disque `/` : 1 129 200 Ko libres (P88) puis 1 129 156–1 129 160 Ko (P89), soit une variation de ≈ 40 Ko, 97 % utilisé.

---

## 3. Bilan de la RAM (13:57:34)

| Poste | kB | % RAM | Récupérable ? |
|---|---|---|---|
| tmpfs `/tmp` (Shmem) | 1 020 544 | 50,0 | **Non** (pas de swap) |
| Mémoire anonyme des processus | 185 208 | 9,1 | Non |
| Cache fichiers hors tmpfs + buffers | 437 268 | 21,4 | Oui, en principe |
| Slab (dont réclamable 238 996) | 264 024 | 12,9 | En partie |
| Libre | 93 908 | 4,6 | — |
| Autre, non détaillé (pile noyau, tables de pages, etc.) | 38 488 | 1,9 | — |
| **Total** | **2 039 440** | 100 | |

Décomposition de `MemAvailable` (série 2, 14:00:53) : MemFree 94 856 + cache fichier 451 600 + slab réclamable 224 040, moins les décotes et réserves du noyau. Un recalcul approché de P89 reproduit la valeur mesurée (582 420) avec une réserve implicite d'environ 75 Mo. HYPOTHÈSE de modèle : la réserve exacte n'a pas été lue.

**Point d'attention** : seuls 94 Mo sont libres pour de bon. Environ 80 % de la mémoire dite « disponible » est du cache fichier et du slab, qui doivent être effectivement réclamés sous pression. Le slab comprend notamment `ext4_inode_cache` ≈ 174 Mo et `dentry` ≈ 24 Mo, hérités de parcours d'arborescence. Leur réclamation réelle n'a pas été testée (test = pression mémoire, non autorisé).

---

## 4. Processus consommateurs (aucun processus interrompu)

73 processus, somme des RSS = 271 264 kB (doubles comptages possibles).

| PID | Processus | Utilisateur | RSS (kB) | Détail |
|---|---|---|---|---|
| 5638 | node (DEMO, `src/entry/ui.main.ts`) | user | 86 260 | Pss 80 714, dont anonyme 72 056 ; parent PID 1 ; hors PM2 |
| 347 | envd | root | 81 512 | agent sandbox, non inspectable sans droits |
| 613 | PM2 God Daemon | user | 44 820 | Pss 37 274 ; **`pm2 jlist` renvoie `[]` : aucune application gérée** |
| 278 | systemd-journald | root | 22 296 | |
| 1 | systemd | root | 9 968 | |
| 310 | systemd-networkd | | 5 976 | |

Les autres processus pèsent moins de 4 Mo chacun.

**OBSERVATION** : la mémoire anonyme totale des processus (185 Mo) est faible. Les processus ne sont pas la cause de la rareté de mémoire. La cause principale est le tmpfs.

**OBSERVATION (OOM)** : `oom_score` de DEMO (5638) = 694, `oom_score_adj` = 0 ; PM2 God : 0 avec `adj` −1000 ; PID 1 : 0. La valeur 694 ne correspond pas à une estimation fondée sur la RSS (≈ 43 pour 86 Mo sur 2 Go) : **NON EXPLIQUÉ**. Prudence : en cas de pression extrême, DEMO pourrait figurer parmi les victimes probables. Aucun événement OOM n'a été trouvé dans `dmesg` (voir §7, limite du tampon).

Cgroup : le processus d'observation relève de `/system.slice/envd.service`. Les fichiers `memory.max` et `memory.current` n'étaient pas lisibles ou n'existent pas : **la limite de cgroup n'est pas vérifiée**.

---

## 5. `/tmp`

### 5.1 Montage et capacité
- `/tmp` : tmpfs, options `rw,nosuid,nodev,nr_inodes=1048576` ; taille 1 019 720 kB (995,8 Mio) = **exactement 50 % de MemTotal** (valeur par défaut). Utilisé 1 019 720 kB, disponible 0, **100 %**.
- Inodes : 2 244 utilisés sur 1 048 576 (1 %), non limitant.
- Autres tmpfs : `/dev/shm` 0 utilisé ; `/run` 56 kB ; `/run/lock` 0.
- Contenu : 2 035 fichiers, 208 répertoires, 0 lien ; 1 039 507 493 o cumulés ; 29 entrées de niveau 1.
- Racine `/tmp` : `drwxrwxrwt root:root`, dernière modification 25/11 00:54:36.
- Le système a démarré le 24/11 à 13:40:16 : tout le contenu a été créé depuis ce démarrage (les dates antérieures sont des dates conservées par extraction d'archives).

### 5.2 Répartition par répertoire (`du`, kB)
| Entrée | kB | % de /tmp | Fichiers | Dates de modification |
|---|---|---|---|---|
| `/tmp/backup` | 618 676 | 60,7 | 1 | 25/11 00:56 |
| `/tmp/p78_restore` | 98 768 | 9,7 | 1 275 | 24/11 21:14–21:15 |
| `/tmp/p77_dl` | 93 856 | 9,2 | 29 | 24/11 20:25 |
| `/tmp/p78_tartest` | 62 712 | 6,1 | 30 | 24/11 21:15 |
| `/tmp/p77_pkg` | 62 492 | 6,1 | 8 | 24/11 20:25 |
| `/tmp/p73_verif` | 41 256 | 4,0 | 61 | 15/11 à 24/11 (extraction) |
| `/tmp/p77_restore` | 31 368 | 3,1 | 22 | 24/11 20:24 |
| `/tmp/node-compile-cache` | 2 644 | 0,3 | 566 | 24/11 17:19 |
| `/tmp/p73kit` | 420 | 0,04 | 28 | 24/11 15:06 |
| Fichiers de niveau 1 (13 listes `p77_*`/`p78_*` ≈ 1 545 699 o ; `p73_bases_essentielles.tar.gz` 6 018 490 o ; `p73_kit_reprise.tgz` 105 727 o) | ≈ 7 528 | 0,7 | 15 | 24/11 |
| 5 répertoires système root (`.X11-unix`, etc., chrony) | 0 | 0 | 0 | 24/11 13:21 |

### 5.3 Plus gros fichiers
| Taille (o) | Fichier | Modifié |
|---|---|---|
| 633 524 224 | `/tmp/backup/webapp_backup_ir3ywwou.tar.gz` | 25/11 00:56 |
| ≈ 31 887 260 à 31 989 760 (×9) | packs et archives P77/P78 (`p77_sauvegarde_historique_v2.tar`, `retour.tar`, `*.pack` dans `p77_*`/`p78_*`) | 24/11 20:24–21:15 |
| 10 858 496 (×2), 10 846 208 | bases de sauvegarde DEMO extraites (`p73_verif/x/backups/…`) | 23/11–24/11 |
| 6 018 490 (×2) | `p73_bases_essentielles.tar.gz`, `p73_verif/bases.tar.gz` | 24/11 15:09–15:10 |

### 5.4 Propriétaires et dates
- **Propriétaires** : les 2 035 fichiers appartiennent à `user` (1 039 507 493 o). Seuls 5 répertoires vides appartiennent à root.
- **Dates (fichiers, octets)** : 15/11 (5, 786 397) ; 17/11 (5, 959 008) ; 22/11 (1, 450) ; 23/11 (7, 22 432 515) ; 24/11 (2 016, 381 804 899) ; 25/11 (1, 633 524 224).
- **Plus récent** : `/tmp/backup/webapp_backup_ir3ywwou.tar.gz` (25/11 00:56:34). Aucun fichier de `/tmp` n'a été modifié depuis (contrôle réitéré à 14:03).

### 5.5 Processus susceptibles d'utiliser ces fichiers
- **FAIT VÉRIFIÉ** : `lsof` en root, sous deux formes (`lsof -w /tmp` et `lsof -w +D /tmp`, 14:00:52), renvoie **aucune ligne** (code 1). Aucun fichier de `/tmp` n'est ouvert, y compris aucun fichier supprimé encore ouvert.
- **FAIT VÉRIFIÉ** : le parcours de `/proc/*/fd`, `cwd`, `root` et `maps` (13:59:08) ne trouve aucune référence à `/tmp`.
- Aucune des lignes documentaires (docs P73–P78) ne désigne un processus actif sur ces fichiers.

---

## 6. Classification des contenus de `/tmp`

Aucune suppression n'est décidée ni recommandée ici. Les catégories décrivent l'état des preuves.

**A. Fichiers temporaires actifs : AUCUN.** Aucun processus ne les détient (§5.5). Le cache `node-compile-cache` (2 644 kB, régénérable) n'est référencé par aucun processus ; il n'est pas démontré qu'il soit inutile pour la DEMO au redémarrage.

**B. Fichiers inconnus**
- `/tmp/backup/webapp_backup_ir3ywwou.tar.gz` (618 676 kB).
  - Origine : **non documentée** (0 occurrence dans `docs/*.md`).
  - HYPOTHÈSE (certitude moyenne) : produit par un mécanisme de sauvegarde de la plateforme (nom de motif `webapp_backup_<id>.tar.gz`, date proche de la fin de P87).
  - Contenu : **non ouvert**. Copie durable : NON VÉRIFIÉ. Envoi éventuel vers un stockage : NON VÉRIFIÉ.

**C. Dérivés de contrôle, candidats à décision PO sous conditions de vérification** (documentés comme copies de restauration ou de test)
- Dérivés P78 : `p78_restore` et `p78_tartest` (161 480 kB). Doc P78 §3.3 : restauration de contrôle. Originaux présents dans `work/p78` : pack, archive `.tar` et `.sha` ; **hachages non recalculés par P89** (P78 annonce pack `0391879e…` et archive `bc41a0aa…`).
- Dérivés P77 : `p77_dl` et `p77_restore` (125 224 kB). Doc P77 : re-téléchargement et restauration de contrôle.
- Listes `p77_*.txt` et `p78_*.txt` (13 fichiers, ≈ 1 546 kB) : sorties de contrôle.

**D. Archives de référence ou copies non établies : à conserver sauf décision PO**
- `p77_pkg` (62 492 kB) : contient l'archive v2. Son hachage actuel (`703705862bbc848f…`) est **identique à celui du rapport P77** (FAIT VÉRIFIÉ). `work/p77` ne contient aucune archive `.tar*` (5 fichiers). Le pack équivalent existe sous `work/p76` (même taille 31 887 260 o, hachage non comparé). Copie sur AI Drive : annoncée par P77, non vérifiée par P89.
- `p73_verif`, `p73kit`, `p73_bases_essentielles.tar.gz`, `p73_kit_reprise.tgz` (≈ 47 656 kB) : contiennent des sauvegardes de bases DEMO extraites. `work/p73` (24 fichiers) ne contient pas d'archive `.tar*` ou `.tgz` à profondeur ≤ 2. Doc P73 : « volatile, non supprimée ». Copie durable : **NON ÉTABLIE**.
- **Règle permanente** : « préserver archives P76/P77/P78 ». Toute action sur les catégories C et D concerne cette règle et exige une décision explicite du PO.

---

## 7. Analyse : la saturation de `/tmp` explique-t-elle la baisse ?

### 7.1 Distinctions
| Notion | Valeur | Rôle |
|---|---|---|
| Capacité du tmpfs | 1 019 720 kB (50 % RAM, défaut) | plafond d'écriture ; un `/tmp` « à 100 % » signifie surtout que les écritures échouent (ENOSPC) |
| Mémoire réellement consommée | 1 020 544 kB (`Shmem`) | RAM occupée par les fichiers, non récupérable sans swap |
| Cache récupérable | ≈ 437 Mo fichier + ≈ 224 Mo slab réclamable | peut être rendu sous pression |

Ce n'est pas le taux de remplissage (100 %) qui coûte de la mémoire, mais le volume stocké (≈ 1 Go).

### 7.2 Conclusions
| N° | Conclusion | Étiquette | Certitude |
|---|---|---|---|
| 1 | `/tmp` immobilise 1 020 544 kB (50 %) de RAM non récupérable (`Shmem` ≈ utilisé tmpfs 1 019 720 ; écart 824 kB = autres tmpfs) | FAIT VÉRIFIÉ | Haute |
| 2 | Sans ce contenu, la mémoire disponible serait d'environ 1,6 Go (582 092 + 1 019 720 ≈ 1 601 812 kB) | Calcul arithmétique, effet réel non testé | Moyenne à haute |
| 3 | La contrainte structurelle explique le niveau bas de `MemAvailable` et l'étroitesse de la marge vis-à-vis du plancher de 500 Mo | FAIT VÉRIFIÉ | Haute |
| 4 | **Les variations (489 648 ↔ 606 724 kB) ne s'expliquent pas par `/tmp`** : `Shmem` constant, aucun fichier modifié après 00:56:34 | FAIT VÉRIFIÉ | Haute |
| 5 | **L'hypothèse P88 (baisse de ≈ 125 Mo due au tmpfs) est infirmée** pour la baisse ; confirmée seulement comme contrainte structurelle | CORRECTION | Haute |
| 6 | Cause des variations : **NON IDENTIFIÉE** | OUVERT | Faible |

### 7.3 Autres causes possibles des variations (HYPOTHÈSES, non confirmées)
- Dynamique du cache fichier et du slab (variation observée de ±15 Mo en 3 minutes ; `ext4_inode_cache` ≈ 174 Mo hérité de parcours d'arborescence).
- Consommation transitoire de processus (dont ceux de P88 : hachage récursif, `tar`, `find` ; ceux de la plateforme en fin de tour, comme l'auto-backup Git, dont le commit 631a900 est postérieur au rapport P88).
- Activité de l'agent sandbox `envd` (root, non inspectable).
- Les deux sauts à la hausse observés (+85 Mo entre ≈ 13:53 et 13:57:34 ; +24 Mo à 14:02) n'ont pas de cause identifiée. Aucune de mes commandes de lecture n'a pu libérer de la mémoire.

### 7.4 Limites de l'analyse
- `dmesg` est lisible, mais le tampon est saturé de lignes d'audit (dont les miennes). L'absence d'événement OOM, « no space left » ou tmpfs dans ce tampon ne prouve pas l'absence passée d'OOM (couverture temporelle du tampon non mesurée).
- PSI indisponible (pas de mesure de blocage mémoire). Limite de cgroup non lue.
- Aucune charge de test n'a été mesurée (**NON MESURÉ** : consommation réelle d'une suite `node --test`).

---

## 8. Risques d'une intervention (non réalisée)

| Risque | Détail |
|---|---|
| **Irréversibilité** | Un `unlink` sur tmpfs libère les pages immédiatement, sans corbeille. |
| **Copie unique possible** | `/tmp/backup` (origine inconnue) ; archives et bases P73 (copie durable non établie) ; archive P77 (copie AI Drive non revérifiée). |
| **Règle permanente P76/P77/P78** | Une suppression dans ces groupes contredit la règle « préserver archives » sans dérogation PO explicite. |
| **Mécanisme de sauvegarde plateforme** | `/tmp/backup` pourrait être attendu par un flux plateforme (HYPOTHÈSE). |
| **Impossible de déplacer vers le disque** | Disque à 97 %, 1 129 160 Ko libres ; déplacer `/tmp/backup` (618 676 kB) ferait passer le disque sous le seuil d'arrêt de 800 000 Ko. |
| **Redémarrage ou réinitialisation du bac à sable** | Contenu de `/tmp` perdu (nature volatile du tmpfs ; comportement exact d'un snapshot non vérifié) et DEMO arrêtée. |
| **`drop_caches`** | Modification de l'état noyau ; sans effet sur le tmpfs ; ralentissement transitoire. Non recommandé. |
| **Arrêt de PM2 God (45 Mo, sans application)** | Gain ≈ 37 Mo ; risque de perturber l'outillage ; arrêt de processus interdit sans autorisation. |
| **Lecture des archives** | Lister ou hacher exige de la CPU et du temps ; lister `/tmp/backup` pourrait faire apparaître des noms sensibles. |

**Risque en cas de reprise sans intervention** : marge d'environ 100 Mo au-dessus du plancher, variations de ±60 Mo observées, consommation d'une suite non mesurée, DEMO avec un `oom_score` élevé. Seuil d'arrêt P84 (350 000 kB) inchangé.

---

## 9. Recommandations de reprise (PROPOSITIONS, aucune appliquée)

**Principe** : ne rien supprimer avant d'avoir vérifié une copie durable, par comparaison de hachages, de chaque élément concerné.

### Option A : reprise de P88 sans libération, avec garde renforcée
- Conditions proposées avant chaque suite : au moins 5 relevés de `MemAvailable` espacés de 60 s, tous ≥ 500 000 kB ; relevé juste avant le lancement ; chien de garde toutes les 5 s pendant la suite avec arrêt immédiat sous 350 000 kB ; une seule suite à la fois ; `TMPDIR` sur disque (`/home/user/work/p88/tmp`) ; phase 2 (D1 local, navigateur) toujours suspendue.
- État actuel : 600 148 kB à 14:04:53, donc plancher respecté **à cet instant**.
- Risque : marge fine et variations non expliquées ; l'écart entre le plancher (500 Mo) et l'arrêt (350 Mo) est de 150 Mo seulement.
- Conséquence : les contrôles visuels FR/EN/AR/RTL/mobile de G6 (phase 2) ne sont pas possibles dans ce mode ; ce point reste à trancher.

### Option B : libération par paliers, après autorisation PO et vérifications
Ordre croissant de risque. Gains attendus (HYPOTHÈSE d'effet : la mémoire disponible augmente d'un montant voisin) :
| Palier | Contenu | Gain (kB) | Préconditions |
|---|---|---|---|
| B1 | `p78_restore` + `p78_tartest` | 161 480 | hachage des originaux de `work/p78` (pack, `.tar`, `.sha`) conforme à P78 ; décision PO |
| B2 | `p77_dl` + `p77_restore` | 125 224 | hachage de `p77_sauvegarde_historique_v2.tar` conforme (déjà vérifié sur la copie de `/tmp/p77_pkg`) ; présence confirmée d'une copie durable (AI Drive ou `work`) ; décision PO |
| B3 | listes `p77_*.txt` / `p78_*.txt` | ≈ 1 510 | décision PO ; gain négligeable |
| B4 | `p77_pkg` | 62 492 | copie durable de l'archive v2 vérifiée ; dérogation PO à la règle de préservation |
| B5 | `/tmp/backup` | 618 676 | identification de l'origine et de l'état d'envoi ; vérification d'une copie durable ; décision PO |
| B6 | groupe P73 | ≈ 47 656 | copie durable établie ; décision PO |

B1 + B2 seuls libéreraient ≈ 286 704 kB et porteraient `MemAvailable` d'environ 600 à environ 880 Mo. B5 seul libérerait ≈ 618 Mo.

### Option C : abaisser le plancher
**Non recommandée.** Les seuils P84 sont des planchers durcissables, jamais assouplis.

### Recommandations complémentaires
- Décider après coup d'une politique pour `/tmp` (taille maximale du tmpfs, usage réservé aux fichiers courts) afin que les prochaines missions ne retombent pas sur la même contrainte.
- Formaliser le critère PRQ-3 comme « N relevés consécutifs » plutôt que comme un relevé instantané.
- Documenter dans le chantier que la mémoire disponible dépend du contenu de `/tmp` : toute nouvelle archive en tmpfs réduit la marge.

---

## 10. Actions nécessitant une autorisation explicite du PO

Aucune de ces actions n'a été effectuée.

1. Supprimer, déplacer ou copier un fichier ou répertoire de `/tmp`, par groupe : P78 dérivés (B1) ; P77 dérivés (B2) ; listes (B3) ; `p77_pkg` (B4) ; `/tmp/backup` (B5) ; groupe P73 (B6) ; `node-compile-cache`.
2. Dérogation à la règle permanente « préserver archives P76/P77/P78 ».
3. Vérifications préalables ordonnées pour clore P89 : recalcul de hachages des archives de `work/p78` et `work/p76`/`p77` ; vérification de la copie AI Drive de P77 ; lecture ou listage du contenu de `/tmp/backup` (noms seulement ou en-tête).
4. Création de copies durables sur disque (consommation de disque, sous le seuil d'arrêt 800 000 Ko).
5. Arrêt ou redémarrage de tout processus ou service (PM2 God, DEMO, envd), y compris `ResetSandbox`.
6. Modification d'un paramètre système : `drop_caches`, taille ou remontage du tmpfs, création de swap.
7. Modification ou abaissement d'un seuil P84 (non recommandé ; seul un durcissement est conforme).
8. Reprise de P88 selon l'option A ou B, lancement de la ligne de base R0d, de builds, de tests ou de migrations.
9. Toute mission de nettoyage, de purge ou de réparation de système de fichiers.

---

## 11. Vérification de non-modification (état au 25/11/2026, 14:04)

- `/tmp` : 2 035 fichiers, 1 039 507 493 o, plus récente modification 25/11 00:56:34 : **identique** au premier relevé ; tmpfs toujours à 100 %.
- Code et configuration : agrégats `src`, `tests`, `tools`, `migrations`, `ui/public/on-premise`, `ui/saas-src/src` **identiques à M0** (6/6) ; `package.json`, `tsconfig.json`, `src/entry/compose.ts` : préfixes d'empreinte identiques.
- Docs P81–P86 : 19/19 OK ; docs P87 : 2/2 OK ; rapport P88 : 8 820 o, empreinte `a07c5d6081726adf…` inchangée.
- Migrations : 21 officielles ; `migrations_candidates` absent ; `core` : 750 096 384 o.
- Git : HEAD `631a900` (auto-backup), 0 tag, 0 stash, aucune opération Git manuelle. Seul fichier ajouté : ce rapport.
- DEMO : PID 5638 active, `/health` → `{"status":"ok"}` ; PM2 God (613) actif ; aucun service redémarré.
- Aucun test, build ni migration lancé ; `/home/user/work/p88/tmp` vide (0 entrée).

---

## 12. Anomalies, limites, non vérifié

- OUVERT : cause des variations de `MemAvailable` (489–607 Mo) et des deux sauts à la hausse.
- NON EXPLIQUÉ : `oom_score` 694 de DEMO.
- NON VÉRIFIÉ : origine et statut d'envoi de `/tmp/backup/webapp_backup_ir3ywwou.tar.gz` ; copies durables des archives P73 et P77 ; limite de cgroup ; couverture du tampon `dmesg`.
- NON MESURÉ : consommation mémoire d'une suite de tests ; effet réel d'une libération de `/tmp` ; réclamabilité effective du slab.
- LIMITE : relevés P88 non horodatés ; heures P88 approximatives.
- LIMITE : les chiffres de §3 (réserve noyau ≈ 75 Mo) sont un calcul de modèle, non une lecture.
- Aucun nettoyage effectué, même sur des fichiers apparemment inutilisés.

**La reprise de P88 reste suspendue jusqu'à décision explicite du PO.**
