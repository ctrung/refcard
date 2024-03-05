
# Création lien symbolique vers un fichier/dossier

`ln -s <fichier/dir> <lien>`

-s, --symbolic
       make symbolic links instead of hard links


# Mise à jour lien symbolique vers un fichier

`ln -sf <fichier> <lien>`

-f, --force
       remove existing destination files

L'option --force est obligatoire pour remplacer un lien existant.

# Mise à jour lien symbolique vers un dossier

`ln -sfn <dir> <lien>`

 -n, --no-dereference
       treat destination that is a symlink to a directory as if it were a normal file

L'option --no-dereference est obligatoire car sans celle ci le lien est interprété comme le répertoire pointé. 
On se retrouve alors avec un ajout de lien dans le répertoire pointé au lieu de la mise à jour attendue.
