## Repos

-	Fichier /etc/yum.conf
-	Répertoire /etc/yum.repos.d (fichier .repo, attribut `enabled` pour activer)

Utilitaire de configuration : yum-config-manager (package yum-utils)
`yum-config-manager --enable <repo>`

Activation/désactivation à la demande
```
yum --enablerepo remi,remi-php55 install php
yum --disablerepo remi,remi-php55 install php
```

## Commandes

```
yum list <package>   # liste un paquet déjà installé
yum search <package> # recherche un paquet
yum info <paquet>    # info d'un paquet
yum repolist         # liste les repos reconnus
  
yum install <paquet>
yum install <fichier rpm

yum-complete-transaction --cleanup-only  # nettoie l'état d'une précédente transaction annulée

yum remove <paquet>
yum erase <paquet>    # équivalent
```

## Lister le contenu d'un package rpm

```sh
# qu'il soit déjà installé ou non, commande unique
dnf repoquery -l <package>
```
