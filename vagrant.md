
## création et démarrage
```sh
vagrant init <box, eg ubuntu/focal64>
vagrant up
```

## connexion à la VM
```sh
vagrant ssh-config  # affiche les infos

vagrant ssh
# ou
ssh vagrant@127.0.0.1 -p 2222 -i .vagrant/machines/default/virtualbox/private_key 
```
