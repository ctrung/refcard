# Repos

-	Fichier /etc/yum.conf
-	Répertoire /etc/yum.repos.d (fichier .repo, attribut `enabled` pour activer)

Utilitaire de configuration : yum-config-manager (package yum-utils)
`sudo yum-config-manager --enable <repo>`

Activation/désactivation à la demande
```
sudo yum --enablerepo remi,remi-php55 install php
sudo yum --disablerepo remi,remi-php55 install php
```

# Commandes

```
sudo yum list <package>   # liste un paquet déjà installé
sudo yum search <package> # recherche un paquet
sudo yum info <paquet>    # info d'un paquet
sudo yum repolist         # liste les repos reconnus
  
sudo yum install <paquet>
sudo yum install <fichier rpm
```
