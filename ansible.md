### Reference materials

[Ansible for DevOps - Server and configuration management for humans - 2nd Ed.](https://www.ansiblefordevops.com/)


### Introduction

- Configuration management tool
- Idempotent
- Declarative config
- Agentless (SSH)

### Config file

`/etc/ansible/hosts` : default

### Commands

`ansible` options : 
  - `-a [ARGS]`   : args
- `-i [INI_FILE]` : ini file
- `-m [MODULE]`   : module
- `-u [USER]`     : SSH login, assume passwordless (key-based) login for SSH
- `--ask-pass ( -k )` : ask for SSH password (sshpass package has to be installed) - only use if key-based login for SSH is not setup

```sh
# --- NB ---
# example == group in ini file

ansible -i hosts.ini example -m ping -u [username]         # exec module
ansible -i hosts.ini example -a "free -h" -u [username]    # exec a command


ansible --list-hosts all            # list host from inventory
ansible --list-hosts localhost
```


### Playbooks

```yml
---
- hosts: all
  become: yes

  tasks:
  - name: Ensure chrony (for time synchronization) is installed.
    yum:
      name: chrony
      state: present

  - name: Ensure chrony is running.
    service:
      name: chronyd
      state: started
      enabled: yes
```

Compact
```yml
---
- hosts: all
  become: yes
  tasks:
    - yum: name=chrony state=present
    - service: name=chronyd state=started enabled=yes
```


### Modules

Options :
* -m, --module-name
* -a, --args
* -b, --become : when connecting with an unprivileged user (non-admin), become an admin user before executing commands 

Debug : affiche les variables \
`ansible -m debug -a "var=http_port" all`

Hostname : modify hostname \
`ansible -b -m hostname -a "name=newer-name" <HOST>`

Ping : check connectivity \
`ansible -m ping all`

Shell : run a shell command \
`ansible -m shell -a hostname all`

### Targets

```
ansible --list-hosts group1          # hosts of a group
ansible --list-hosts group1:group2   # hosts from either groups (logical OR)
ansible --list-hosts group1:&group2  # hosts from both groups (logical AND)
ansible 'all:!group1' --list-hosts   # exclude with !

ansible --list-hosts host-*          # wildcard

ansible '~^(web|lb)*' --list-hosts   # regex are prefixed with ~
```

### Inventory

#### File 

Eg. hosts file :

(INI format) 
```ini
[group_1]
host-001.local
host-002        ansible_host=192.168.xxx.xxx ansible_port=22      # host variables
host-0[01:10]                                                     # regex

[group2]
host-002                                                          # using inventory hostname allows for DRY

[group3:children]                                                 # group of groups with the :children header
group1
group2

[group3:vars]                                                     # setting attributes to a group with the :vars header
http_port=8080
```

(YAML format) : 
```yaml
webservers:
  hosts:
    web-001.local:
    web-002:
      ansible_host: 192.168.98.112
  vars:
    http_port: 8080

germany:
  hosts:
    web-de-00[1:2]:
france:
  hosts:
    web-fr-001:
spain:
  hosts:
    web-es-00[1:3]:
usa:
  hosts:
    web-us-00[1:4]:

europe:
  children:
    germany:
    france:
    spain:
```

Utiliser l'option `-i` pour spécifier le fichier d'inventaire : `ansible -i hosts --list-hosts all`

### Directory

Utiliser l'option `-i` pour spécifier le fichier d'inventaire : `ansible -i 'inventory/' --list-hosts all`

A savoir : 
- Tous les fichiers de l'arborescence sont parsés par ordre alphabétique.
- La dernière définition l'emporte sur les précédentes.

# Configuration 
Fichier `ansible.cfg` 

```ini
[defaults]
host_key_checking = false
inventory   = hosts            # file based
#inventory  = inventory/       # directory based
```

