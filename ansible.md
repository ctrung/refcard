### Introduction

- configuration management tool
- idempotent
- declarative config
- agentless (ssh)

### Commandes

```sh
ansible --list-hosts all
ansible --list-hosts localhost

# modules (-m/--module-name, -a/--args, -b/--become)
ansible -m ping localhost                

ansible -m shell -a hostname localhost
ansible -m shell -a false localhost
ansible -b -m shell -a "hostname new-name" localhost    # bad : not declarative
ansible -b -m hostname -a "name=newer-name"             # good : declarative
```

### Inventory file 

Créer un fichier `hosts` contenant chaque host sur une ligne :
```
host-001.local
host-002.local
```

Utiliser l'option `-i, --inventory, --inventory-file` pour l'exploiter :
```sh
ansible -i hosts --list-hosts all
ansible -i hosts -m ping all
```

# Configuration 
Fichier `ansible.cfg` 

```ini
[defaults]
host_key_checking = false
inventory = hosts            # option -i
```
