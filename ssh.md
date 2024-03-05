## Version de OpenSSH utilisée
```sh
$ ssh -V
```

## Aide sur les options de configuration
```sh
$ man ssh_config
```

## Récupérer l'empreinte de ma clé publique 

```sh
# -l : lister (au lieu de créer), -f : fichier
> ssh-keygen -lf /home/user/.ssh/id_rsa.pub        # sha256
> ssh-keygen -E md5 -lf /home/user/.ssh/id_rsa.pub # md5
```

## Passer par un serveur de rebond

```sh
## en ligne de commande
> ssh -J user1@proxy user2@serveur
```

```sh
## ~/.ssh/config
Host rebond
  HostName 10.188.0.206
  User rmo

Host 30
  HostName 10.1.8.30
  User jboss
  ProxyJump rebond

Host 29
  HostName 10.1.8.29
  User rmo
  ProxyJump rebond

Host 67
  HostName 10.1.8.67
  User jboss
  ProxyJump rebond
```

## SSH proxy
https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Proxies_and_Jump_Hosts

## Où se trouve la configuration permanente ?
* globale : `/etc/ssh/ssh_config`
* locale : `$HOME/.ssh/config`

## Configuration du nom de domaine
`/etc/resolv.conf`

## Utiliser une autre configuration
```sh
> ssh -F test-config avarice
```

## Voir les options de configuration utilisée par ssh (dryrun)
```sh
> ssh -G xyz@example.com
```

## Passer une option de configuration permanente (utile si celle ci n'a pas d'équivalent en ligne de commande)
```sh
> ssh -o Port=2222 sloth # ce qui est équivalent à ssh -p 2222 sloth
```

## Hasher les entrées existantes du fichier known_hosts
```sh
> ssh-keygen -H
```

## Hasher les nouvelles entrées du fichier known_hosts
```sh
# ~/.ssh/config
HashKnownHosts yes
```

## Récupérer le hash d'une entrée du fichier known_hosts
```sh
> ssh-keygen -F avarice.mwl.io
```


## Utiliser une autre clé privée
```sh
> ssh -i $HOME/specialkey sloth
```

## Supprimer une entrée du fichier known_hosts
```sh
$ ssh-keygen -R avarice.mwl.io
```

## Générer une paire de clés
```sh
# -t : type d'algorithme
# -f : fichier
# -N : passphrase (conseillé pour les user keys, la plupart du temps vide pour les host keys)
$ ssh-keygen -t ecdsa -f ssh_host_ecdsa_key -N ''
```


## Agent forwarding
Conditions d'utilisation :
* à activer avant d'entrer dans la session ssh
* doit être activée côté client (ForwardAgent yes) et côté serveur (AllowAgentForwarding yes)

```sh
client $ ssh -o ForwardAgent=yes rebond
rebond $ echo $SSH_AUTH_SOCK # si de la forme /tmp/ssh-zOeUnDTnkb/agent.2513, alors ça fonctionne
rebond $ ssh dest            # on peut alors se logger avec la clé privée qui est restée sur client 
```


## X forwarding

Rappel notions :
* X server = tourne côté client, s'occupe de l'affichage
* X client = tourne côté serveur distant, envoie les instructions d'affichage du programme graphique

Pré-requis :
* Serveur distant SSH : xauth installé et X11Forwarding yes dans sshd_config
* Client SSH : ForwardX11 yes, voire ForwardX11Trusted yes pour plus de privilèges dans ssh_config

```sh 
## ex. de config côté client autorisant le X forwading seulement pour un serveur donné
ForwardX11 no
Host pride
ForwardX11 yes
ForwardX11Trusted yes
Compression yes  # utile pour les éviter des latences
```

```sh
# en ligne de commande 
# -C = Compression yes
# -X = ForwardX11 yes
# -Y = ForwardX11Trusted yes
$ ssh -CX pride 
$ ssh -CY pride
```

To check XForwarding is working, inside ssh session $DISPLAY variable should be set and be equal to 
xserver-host:display-number(.screen-number) (TCP display)
or :display-number(.screen-number) (local display)

```sh
# encore plus pratique, 
# -f lance le programme X à distance et permet de récupérer le prompt (ssh tourne alors en arrière plan)
$ ssh -f wrath xterm
```

Liens utiles
* https://unix.stackexchange.com/questions/17255/is-there-a-command-to-list-all-open-displays-on-a-machine


## Port forwarding

(Mémo) \
client : localIP, localport \
serveur ssh : remoteIP, remoteport

### Local
Mapper un accès du serveur côté client.
Exemple d'usage : chiffrer un traffic non sécurisé.

```sh
$ ssh -L localIP:localport:remoteIP:remoteport hostname

# si on ommet localIP, 127.0.0.1 est pris par défaut
$ ssh -L localport:remoteIP:remoteport hostname
```

```sh
# ~/.ssh/config, 
# attention, il n'y a pas de deux points au milieu entre la partie local et remote
Host envy.mwl.io
LocalForward localIP:localport remoteIP:remoteport
```

### Remote
C'est l'inverse du local port forwarding. 
Exemple d'usage : donner accès à un service côté client depuis l'extérieur 

```sh
$ ssh -R remoteIP:remoteport:localIP:localport hostname

# si on ommet remoteIP, 127.0.0.1 est pris par défaut
$ ssh -R remoteport:localIP:localport hostname
```

```sh
# ~/.ssh/config, 
# attention, il n'y a pas de deux points au milieu entre la partie local et remote
Host envy.mwl.io
RemoteForward remoteIP:remoteport localIP:localPort
```

### Dynamic
Plus large, il s'agit de monter un proxy SOCKS sur le client. 
Tout traffic TCP/IP passant par ce proxy a alors accès au réseau auquel a accès le serveur.

```sh
$ ssh -D localaddress:localport hostname
# si on ommet localaddress, 127.0.0.1 est pris par défaut
$ ssh -D 9999 sloth
```

```sh
# ~/.ssh/config, 
DynamicForward host:port
```

### Mode background
```sh
# exemple avec un local port forwarding
# -N : pas d'allocation de terminal
# -f : mode background
$ ssh -DfN localaddress:localport hostname
```

## Bloquer le port forwarding
AllowTcpForwarding(sshd_config) yes(defaut)|no|local|remote


## Autoriser n'importe quelle IP pour le port forwarding (autre que 127.0.0.1)
GatewayPorts(ssh_config, sshd_config) no(defaut)|yes|clientspecified

NB : sshd_config pour remote port forwarding, ssh_config pour local port forwarding

## Restreindre les IPs/ports utilisés dans le port forwarding
PermitOpen(sshd_config) hostname1:port1 hostname2:port2 ...

NB : les IPs sont séparés par un espace

## Ne même pas laisser la session SSH se faire si le port forwarding ne fonctionne pas

ExitOnForwardFailure(ssh_config) no(defaut)|yes


## Keep alive

```sh
## côté client, envoie un paquet toute les 5 mins, 2 échecs autorisé
Host *
    ServerAliveInterval 300
    ServerAliveCountMax 2
```

```sh
## côté serveur, envoie un paquet toute les 5 mins, 2 échecs autorisé
ClientAliveInterval 300
ClientAliveCountMax 2
```


## Tunnels

PermitTunnel no(defaut)|yes|point-to-point|ethernet

(sshd_config)
```sh
Match Address avarice.mwl.io
PermitRootLogin prohibit-password # il faut autoriser le login de root car  il faut les droits pour monter des devices tunnel et changer les routes ! 
PermitTunnel point-to-point
```

La commande client complète 
```sh
ssh -i keyfile -f -wclientTunnelNumber:serverTunnelNumber servername true
``` 

ou 

```sh
# (ssh_config)
Host gluttony.mwl.io
Tunnel point-to-point
TunnelDevice 0:0
IdentityFile /root/.ssh/tunnelkey
IdentitiesOnly yes

# la commande est alors plus simple
ssh -f gluttony.mwl.io true
```

