
## Commands
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

## Pitfalls

### VBoxManage: error: Code E_ACCESSDENIED

Stackoverflow : https://stackoverflow.com/questions/69728426/e-accessdenied-when-creating-a-host-only-interface-on-virtualbox-via-vagrant \
`/etc/vbox/networks.conf` manpage : https://www.virtualbox.org/manual/ch06.html#network_hostonly

Create `/etc/vbox/networks.conf` to allow IP range : 
```
* 192.168.98.0/21
* 192.168.60.0/21
```
