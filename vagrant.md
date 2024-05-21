
## Création, démarrage, statut, arrêt
```sh
vagrant init <box, eg ubuntu/focal64>
vagrant up

vagrant status
vagrant status <vm>

vagrant halt
vagrant halt <vm> 
```

## Connexion à la VM
```sh
vagrant ssh-config  # affiche les infos

vagrant ssh <vm>
# ou
ssh vagrant@127.0.0.1 -p 2222 -i .vagrant/machines/default/virtualbox/private_key 
```
