
## Création, démarrage, statut, arrêt
```sh
vagrant box add geerlingguy/centos7    # download vm image
vagrant init geerlingguy/centos7       # load image in virtualbox and generate Vagrantfile
vagrant up                             # start
vagrant halt                           # stop
vagrant destroy                        # delete

vagrant ssh                 # ssh inside vm
vagrant ssh-config          # display infos

ssh vagrant@127.0.0.1 \     # ssh inside vm
    -p 2222 \
    -i .vagrant/machines/default/virtualbox/private_key 

vagrant status
vagrant status <vm>
```
