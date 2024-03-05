https://svnbook.red-bean.com/index.en.html

## Commands

### authentication

Les sous commandes svn supportent toutes les options `--username` et `--password` si besoin de surcharger le cache credentials ou utilisation au sein d'un script.

Pour supprimer une entrée du cache des credentials :
```sh
ls ~/.subversion/auth/svn.simple/
# après identification de la bonne entrée, suppression... 
rm -f ~/.subversion/auth/svn.simple/CACHE_ENTRY
```

### client configuration files

User configuration folder 

*Linux*   : `~/.subversion/config` or `/etc/subversion/config` (global) \
*Windows* : `%appdata%\subversion\config`

Files : 
* config
* README.txt
* servers

### properties

* svn:author
* svn:date
* svn:mergeinfo

### revision keywords

* HEAD : dernière révision au sein du repo
* BASE : révision avant les modifications locales  
* COMMITTED : révision du dernier commit en local 
* PREV : révision du précédent commit en local (eq. COMMITTED-1)

Exemples : 

```sh
# shows the last change committed to foo.c
svn diff -r PREV:COMMITTED foo.c

# shows log message for the latest repository commit
svn log -r HEAD

# compares your working copy (with all of its local changes) to the latest version of that tree in the repository
svn diff -r HEAD

# compares the unmodified version of foo.c with the latest version of foo.c in the repository
svn diff -r BASE:HEAD foo.c

# shows all commit logs for the current versioned directory since you last updated
svn log -r BASE:HEAD

# rewinds the last change on foo.c, decreasing foo.c's working revision
svn update -r PREV foo.c

# compares the unmodified version of foo.c with the way foo.c looked in revision 14
svn diff -r BASE:14 foo.c
```

### revision dates

Exemples :

```sh
svn update -r {REVISION_DATE}

où {REVSION_DATE} peut être de la forme :

{2006-02-17}
{15:30}
{15:30:00.200000}
{"2006-02-17 15:30"}
{"2006-02-17 15:30 +0230"}
{2006-02-17T15:30}
{2006-02-17T15:30Z}
{2006-02-17T15:30-04:00}
{20060217T1530}
{20060217T1530Z}
{20060217T1530-0500}
```

### server configuration

```sh

# svn://HOST:PORT/LOCAL/PATH/TO/REPO
svnserve -d                                      # -d (--daemon) : daemon (port 3690 et toutes les interfaces réseau par défaut)
svnserve --listen-port PORT --listen-host HOST
svnserve -d -r /var/svn                          # -r (-root) : racine des répertoires à servir
```

### svnadmin
(admin command)
```sh
svnadmin --version

svnadmin create /var/svn/repos                   # par défaut --fs-type fsfs
svnadmin create --fs-type fsfs /var/svn/repos
svnadmin create --fs-type bdb /var/svn/repos

svnadmin dump /var/svn/repos > dumpfile
svnadmin dump oldrepos | svnadmin load newrepos  # dump et load grâce à un pipe

svnadmin help
svnadmin help SUBCOMMAND

svnadmin load /var/svn/newrepos < dumpfile

svnadmin lslocks /var/svn/repos  # liste les locks

svnadmin lstxns /var/svn/repos   # liste les transactions inachevées 

svnadmin pack /var/svn/repos     # utile pour défragmenter le repo

svnadmin rmlocks /var/svn/repos /project/raisin.jpg  # supprime un lock

svnadmin rmtxns /var/svn/repos TXN
svnadmin rmtxns /var/svn/repos `svnadmin lstxns /var/svn/repos`  # supprime toutes les transactions
```

### svnlook
(admin command)
```sh
svnlook --version

svnlook help
svnlook help SUBCOMMAND

svnlook info /var/svn/repos           # eq. -r HEAD par défaut
svnlook info /var/svn/repos -r REV    # info sur une révision   , -r == --revision
svnlook info /var/svn/repos -t TXN    # info sur une transaction, -t == --transaction

svnlook youngest /var/svn/repos       # display youngest revision
```

### svn

```sh
svn --version

svn add FILE      # changement structurel : ajout d'un fichier

svn cat -r REV FILE                      # affiche le contenu du fichier à la révision REV (-r == --revision)
svn cat URL/PATH/TO/FILE@PEGREV > FILE   # réssusciter un fichier d'une révision (sans conservation de l'historique svn log)

svn changelist LABEL FILES...     # ajoute un fichier à un changelist
svn changelist --remove FILES...  # supprime un fichier d'un changelist
svn changelist NEWLABEL --changelist OLDLABEL --depth infinity .  # renomme un changelist
svn changelist --remove --changelist LABEL --depth infinity .     # supprime un changelist

svn checkout http://svn.example.com/svn/repo/trunk [FOLDER]
svn checkout http://svn.example.com/svn/repo/trunk/subfolder [FOLDER]

svn cleanup     # nettoie les locks internes de SVN (utile si commit interrompu en plein milieu) 

svn commit -m MESSAGE
svn commit -m --force-log MESSAGE                  # utile pour éviter les ambiguités quand le message et le nom d'un fichier du repo est le même
svn commit -F FILE_MESSAGE
svn commit -m MESSAGE --with-revprop "PROP=VALUE"  # setter un revprop
svn commit -m MESSAGE --changelist LABEL           # limiter aux fichiers d'un changelist
svn commit -m MESSAGE --changelist LABEL \
                      --keep-changelists           # limiter aux fichiers d'un changelist tout en le conservant après le commit

svn copy FOO BAR                                     # changement structurel en local : copie d'un fichier
svn copy URL_TRUNK URL_NEW_BRANCH -m MESSAGE         # création d'une branche au sein du repo
svn copy URL/PATH/TO/FILE@PEGREV FILE                # réssusciter un fichier d'une révision (historique svn log conservé !)
svn copy URL/TO/BRANCH@REV URL/TO/BRANCH -m MESSAGE  # réssusciter une branche

svn delete FILE          # changement structurel : suppr fichier
svn delete --force FILE  # changement structurel : forcage suppr fichier lors de la résolution d'un conflit

svn diff                             # voir les changements locaux
svn diff -r REV [FILE]               # comparer avec une version du repo (-r = --revision)
svn diff -r REV_FROM:REV_TO [FILE]   # comparer deux versions du repo (-r = --revision)
svn diff -c REV                      # comparer une version avec la précédente, équivalent à -r REV:REV-1 (-c = --change)
svn diff --changelist LABEL          # limiter aux fichiers d'un changelist
svn diff --depth=empty /path/to/merge/target  # utile après un merge pour voir le mergeinfo


svn help
svn help SUBCOMMAND

svn import /path/to/mytree http://svn.example.com/svn/repo/some/project -m "Initial import"  # Envoyer des fichiers non versionnés sur le repo SVN (pas de checkout)

svn info FILE
svn info FILE | grep URL

svn list http://svn.example.com/svn/repo/some/project   # ls sur le repo

svn lock FILE -m MESSAGE
svn lock --force FILE  # vole le lock d'un autre (eq. unlock --force + lock de manière atomique)

svn log [FILE]
svn log -v [FILE]              # -v == --verbose : affiche le contenu des commits
svn log -q [FILE]              # -q == --quiet   : n'affiche pas le commentaire des commits
svn log -r REV [FILE]          # -r == --revision
svn log -r REV_X:REV_Y [FILE]
svn log --diff [FILE]          # diff du commit
svn log --with-all-revprops --xml FILE
svn log -v -r 390 -g           # -g == --use-merge-history : affiche aussi les changesets issus d'un merge au sein du changeset courant

svn merge URL       # merge une branche dans l'espace de travail 
svn merge -c 9238   # merge changeset r9238 into the working copy
svn merge URL/TO/FILE PATH/TO/FILE -c REVISION  # merge un subtree pour une révision donnée
svn merge URL . -c REVISION                     # merge tout la branche pour une révision donnée
svn merge --reintegrate URL/BRANCH              # remerger la branche sur le tronc

svn merge -c -REVISION URL                      # undo d'un change (eq. à git revert), attention à bien mettre le moins devant la révision pour avoir un "reverse patch"

svn merge URL_FROM URL_TO WORKING_COPY          # full syntax avec les trois parties explicitement nommées, applique le diff entre deux URLs au répertoire de travail
svn merge -r REVFROM:REVTO URL WORKING_COPY     # version plus courte : applique le diff entre deux révisions au répertoire de travail
svn merge -r REVFROM:REVTO URL                  # version encore plus courte : applique le diff entre deux révisions au répertoire courant

svn merge -c REV --record-only URL              # usecase 1 : bloquer un changeset (fait croire à mergeinfo que celui ci a déjà été mergé)
                                                # usecase 2 : continuer à rendre une branche réintegrée utilisable

svn mergeinfo URL_TRUNK                       # connaitre les changements déjà mergés
svn mergeinfo URL_TRUNK --show-revs eligible  # connaitre les changements à venir

svn mkdir FOO     # changement structurel : création d'un répertoire

svn move FOO BAR  # changement structurel : déplacement d'un fichier

svn propedit PROPERTY FILE
svn propedit PROPERTY -rREVISION --revprop # édite une propriété de révision

svn propdel PROPERTY FILE
svn propdel PROPERTY -rREVISION --revprop # suppr une propriété de révision

svn propget PROPERTY FILE
svn propget PROPERTY -rREVISION --revprop # affiche une propriété de révision
svn propget svn:mergeinfo --recursive     # affiche tous les mergeinfo de la branche

svn proplist FILE
svn proplist --verbose FILE        # affiche aussi les valeurs des properties
svn proplist -rREVISION --revprop  # affiche les propriétés de révision

svn propset PROPERTY VALUE [FILE]
svn propset PROPERTY -F PATH_TO_PROPERTY_VALUE_FILE [FILE]
svn propset PROPERTY VALUE -rREVISION --revprop    # set une propriété de révision

svn resolve --accept=base        FILE  # met le fichier tel qu'il était avant les modifications locales
svn resolve --accept=mine-full   FILE  # met le fichier avec les modifications locales seulement
svn resolve --accept=theirs-full FILE  # met le fichier avec les modifications du repo seulement 
svn resolve --accept=working     FILE  # met le fichier tel quel (eg. suite à un merge manuel)

svn revert FILE   # annule les modifications sur un fichier
svn revert . -R   # tout annuler (eg. après un merge pour recommencer)

svn status                 # voir le statut des objets
svn status FILE
svn status --verbose       # voir le statut des objets (même ceux non modifiés)
svn status --show-updates  # voir le statut désynchronisé des objets

svn switch URL/TO/BRANCH   # change de branche au sein du répertoire de travail courant

svn unlock FILE
svn unlock --force URL # casse le lock d'un autre, nécessite un lock manuel derrière


svn update         # met à jour la copie locale depuis la dernière version du repo
svn update -r REV  # met à jour la copie locale depuis la version REV du repo (-r = --revision)
```

