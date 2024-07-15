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
- `-b, --become` : sudo user 
- `-i [INI_FILE]` : inventory file
- `-f [NB_THREADS]` : fork threads
- `-m [MODULE]`   : module
- `-u [USER]`     : SSH login, assume passwordless (key-based) login for SSH
- `--ask-pass ( -k )` : ask for SSH password (sshpass package has to be installed) - only use if key-based login for SSH is not setup

```sh
# --- NB ---
# example == group in ini file

ansible -i hosts.ini example -m ping -u [username]         # exec module
ansible -i hosts.ini example -a "free -h" -u [username]    # exec a command

ansible multi -a "hostname"
ansible multi -a "hostname" -f 1                  # -f = fork threads (1 == sequentially)
ansible multi -a "df -h"
ansible multi -a "date"

# check time daemon sync
ansible multi -b -m yum -a "name=chrony state=present"                    # check package 
ansible multi -b -m service -a "name=chronyd state=started enabled=yes"   # check service is up

# provision django app env
ansible app -b -m yum -a "name=python3-pip state=present"
ansible app -b -m pip -a "name=django<4 state=present"
ansible app -a "python -m django --version"

# provision mariadb
ansible db -b -m yum -a "name=mariadb-server state=present"
ansible db -b -m service -a "name=mariadb state=started enabled=yes"
ansible db -b -m yum -a "name=firewalld state=present"
ansible db -b -m service -a "name=firewalld state=started enabled=yes"
ansible db -b -m firewalld -a "zone=database state=present permanent=yes"
ansible db -b -m firewalld -a "source=192.168.60.0/24 zone=database state=enabled permanent=yes"
ansible db -b -m firewalld -a "port=3306/tcp zone=database state=enabled permanent=yes"
ansible db -b -m yum -a "name=python3-PyMySQL state=present"  # mysql_* modules need python3-PyMySQL package installed on targeted hosts
ansible db -b -m mysql_user -a "name=django host=% password=12345 priv=*.*:ALL state=present"

ansible app -b -a "service chronyd restart" --limit "192.168.60.4"   # --limit : target specific hosts
ansible app -b -a "service ntpd restart" --limit "*.4"               # target with a wildcard
ansible app -b -a "service ntpd restart" --limit ~".*\.4"            # target with a regex

# provision groups and users
# https://docs.ansible.com/ansible/latest/collections/ansible/builtin/group_module.html#ansible-collections-ansible-builtin-group-module
# https://docs.ansible.com/ansible/latest/collections/ansible/builtin/user_module.html#ansible-collections-ansible-builtin-user-module
ansible app -b -m group -a "name=admin state=present"
ansible app -b -m user -a "name=johndoe group=admin createhome=yes"  # additional params : generate_ssh_key=yes uid=[uid] shell=[shell] password=[encrypted-password]
ansible app -b -m user -a "name=johndoe state=absent remove=yes"

# generic package module that works for redhat, debian, etc...
ansible app -b -m package -a "name=git state=present"

# files and dirs management
ansible multi -m stat -a "path=/etc/environment"           # read file info

ansible multi -m copy -a "src=/etc/hosts dest=/tmp/hosts"  # local copy on hosts

ansible multi -b -m fetch -a "src=/etc/hosts dest=/tmp"    # fetch from hosts to controller (eg. /tmp/192.168.60.6/etc/hosts, etc...)

ansible multi -m file -a "dest=/tmp/test mode=644 state=directory"       # create dir
ansible multi -m file -a "src=/src/file dest=/dest/symlink state=link"   # create link
ansible multi -m file -a "dest=/tmp/test state=absent"                   # delete


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

