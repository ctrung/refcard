### Introduction

About Ansible :
- Configuration management tool
- Idempotent
- Declarative config
- Agentless (SSH)

Reading : \
[Ansible for DevOps - Server and configuration management for humans - 2nd Ed.](https://www.ansiblefordevops.com/)


### Config file

`/etc/ansible/hosts` : default inventory file

`ansible.cfg` : default config file
```ini
[defaults]
host_key_checking = false
inventory   = hosts            # file based
#inventory  = inventory/       # directory based
```


### Usage

`ansible` options : 
- `-a [ARGS]`   : args
- `-b, --become` : sudo user 
- `-i [INI_FILE]` : inventory file
- `-f [NB_THREADS]` : fork threads
- `-m [MODULE]`   : module
- `-u [USER]`     : SSH login, assume passwordless (key-based) login for SSH
- `--ask-pass ( -k )` : ask for SSH password (sshpass package has to be installed) - only use if key-based login for SSH is not setup

#### Inventory file (explicit)

```sh
# example = group in inventory file
ansible -i hosts.ini example -m ping -u [username]         # exec module
ansible -i hosts.ini example -a "free -h" -u [username]    # exec a command
```

#### Commands (manual)
```sh
ansible multi -a "hostname"
ansible multi -a "hostname" -f 1                  # -f = fork threads (1 == sequentially)
ansible multi -a "df -h"
ansible multi -a "date"
```

#### Check package and service
```sh
ansible multi -b -m yum -a "name=chrony state=present"                    # check package 
ansible multi -b -m service -a "name=chronyd state=started enabled=yes"   # check service is up
```
Generic package module that works for redhat, debian, etc... : `ansible app -b -m package -a "name=git state=present"`

#### Django env
```sh
ansible app -b -m yum -a "name=python3-pip state=present"
ansible app -b -m pip -a "name=django<4 state=present"
ansible app -a "python -m django --version"
```

#### Mariadb
```sh
ansible db -b -m yum -a "name=mariadb-server state=present"
ansible db -b -m service -a "name=mariadb state=started enabled=yes"
ansible db -b -m yum -a "name=firewalld state=present"
ansible db -b -m service -a "name=firewalld state=started enabled=yes"
ansible db -b -m firewalld -a "zone=database state=present permanent=yes"
ansible db -b -m firewalld -a "source=192.168.60.0/24 zone=database state=enabled permanent=yes"
ansible db -b -m firewalld -a "port=3306/tcp zone=database state=enabled permanent=yes"
ansible db -b -m yum -a "name=python3-PyMySQL state=present"  # mysql_* modules need python3-PyMySQL package installed on targeted hosts
ansible db -b -m mysql_user -a "name=django host=% password=12345 priv=*.*:ALL state=present"
```

#### Limit hosts in a group
```sh
ansible app -b -a "service chronyd restart" --limit "192.168.60.4"   # --limit : target specific hosts
ansible app -b -a "service ntpd restart" --limit "*.4"               # target with a wildcard
ansible app -b -a "service ntpd restart" --limit ~".*\.4"            # target with a regex
```

#### Groups and users
https://docs.ansible.com/ansible/latest/collections/ansible/builtin/group_module.html#ansible-collections-ansible-builtin-group-module \
https://docs.ansible.com/ansible/latest/collections/ansible/builtin/user_module.html#ansible-collections-ansible-builtin-user-module
```sh
ansible app -b -m group -a "name=admin state=present"                  # create group

# optional params : generate_ssh_key=yes uid=[uid] shell=[shell] password=[encrypted-password]
ansible app -b -m user -a "name=johndoe group=admin createhome=yes"    # create user

ansible app -b -m user -a "name=johndoe state=absent remove=yes"       # delete user
```

#### File system management

```sh
ansible multi -m stat -a "path=/etc/environment"           # read file info

ansible multi -m copy -a "src=/etc/hosts dest=/tmp/hosts"  # local copy on hosts

# fetch from hosts to controller (eg. /tmp/192.168.60.6/etc/hosts, etc...)
ansible multi -b -m fetch -a "src=/etc/hosts dest=/tmp"    

ansible multi -m file -a "dest=/tmp/test mode=644 state=directory"       # create dir
ansible multi -m file -a "src=/src/file dest=/dest/symlink state=link"   # create link
ansible multi -m file -a "dest=/tmp/test state=absent"                   # delete
```

#### Config
```sh
ansible --list-hosts all            # list host from inventory
ansible --list-hosts localhost
```


### Playbooks

See examples at : 

- https://github.com/geerlingguy/ansible-for-devops


#### Usage

```sh
ansible-playbook playbook.yml

ansible-playbook playbook.yml --limit webservers      # --limit : CLI limit option, eq. to hosts directive in the playbook file
ansible-playbook playbook.yml --list-hosts            # print hosts only

ansible-playbook playbook.yml --user=johndoe
ansible-playbook playbook.yml --become --become-user=janedoe --ask-become-pass

# other options :
#  --inventory=PATH ( -i PATH ),
#  --verbose ( -v )
#  --extra-vars=VARS ( -e VARS ) : "key=value,key=value" format
#  --forks=NUM ( -f NUM )
#  --connection=TYPE ( -c TYPE )
#  --check : dry run mode, tasks are checked but not run

```

**Multilines string**
Use `command: >` syntax
```yml
    - name: Copy configuration files.
      command: >
        cp httpd.conf /etc/httpd/conf/httpd.conf
```

**Variables (global)**
Use `{{ item. }}` syntax to reference variable
```yml
- hosts: all
  become: yes

  vars:
    node_apps_location: /usr/local/opt/node
```

**Loop in a task**
Use `with_items` directive with `{{ item. }}` syntax
```yml
- name: Copy configuration files.
  copy:
    src: "{{ item.src }}"
    dest: "{{ item.dest }}"
    owner: root
    group: root
    mode: 0644
  with_items:
    - src: httpd.conf
      dest: /etc/httpd/conf/httpd.conf
    - src: httpd-vhosts.conf
      dest: /etc/httpd/conf/httpd-vhosts.conf

- name: "Start Apache, MySQL, and PHP."
  service: "name={{ item }} state=started enabled=yes"
  with_items:
    - apache2
    - mysql
```



#### Examples

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

##### Install a repo
```yml

# Yum
- name: Install EPEL repo.
  yum: name=epel-release state=present

- name: Install Remi repo.
  yum:
    name: "https://rpms.remirepo.net/enterprise/remi-release-7.rpm"
    state: present

# Apt

# Python libs are needed for apt_repository module to work
# https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_repository_module.html
- name: Get software for apt repository management.
  apt:
    state: present
    name:
      - python3-apt
      - python3-pycurl

- name: Add ondrej repository for later versions of PHP.
  apt_repository: repo='ppa:ondrej/php' update_cache=yes
```

##### Import repo key (RHEL)
```yml
- name: Import Remi GPG key.
  rpm_key:
    key: "https://rpms.remirepo.net/RPM-GPG-KEY-remi"
    state: present
```

##### Install a package
```yml
# Yum
- name: Install Node.js and npm.
  yum: name=npm state=present enablerepo=epel

# Apt
- name: Get software for apt repository management.
  apt:
    state: present
    name:
      - python3-apt
      - python3-pycurl
```

##### Install [Forever](https://www.npmjs.com/package/forever)
```yml
- name: Install Forever (to run our Node.js app).
  npm: name=forever global=yes state=present
```

##### Build project (npm install)
```yml
- name: Install app dependencies defined in package.json.
  npm: path={{ node_apps_location }}/app 
```

##### Condition
```yml
- name: Start example Node.js app.
  command: "forever start {{ node_apps_location }}/app/app.js"
  when: "forever_list.stdout.find(node_apps_location + '/app/app.js') == -1"
```

##### Variables file
```yml
---
- hosts: all
  become: yes
  vars_files:
    - vars.yml
```

##### Pre-tasks
```yml
pre_tasks:
  - name: Update apt cache if needed.
    apt: update_cache=yes cache_valid_time=3600
```
NB : see `post_tasks` too

##### Handlers
Run at the end of a play if task executes successfully
```yml
tasks:
  - name: Enable Apache rewrite module (required for Drupal).
    apache2_module: name=rewrite state=present
    notify: restart apache   # <--- notify

handlers:                    # <--- handler
  - name: restart apache
    service: name=apache2 state=restarted

# notify multiple handlers
- name: Rebuild application configuration.
  command: /opt/app/rebuild.sh
  notify:
    - restart apache
    - restart memcached

# chaining handlers
handlers:
  - name: restart apache
    service: name=apache2 state=restarted
    notify: restart memcached

  - name: restart memcached
    service: name=memcached state=restarted

```

Handlers are run at the end of a playbook. To run handlers in the middle of a playbook, use the `meta` module to do so (e.g. - meta: flush_handlers)

Use `--force-handlers` to run handlers even if a play failed.  


##### Enable an apache module
```yml
- name: Enable Apache rewrite module (required for Drupal).
  apache2_module: name=rewrite state=present
  notify: restart apache
```

##### Add an apache virtualhost
```yml
- name: Add Apache virtualhost for Drupal.
  template:
    src: "templates/drupal.test.conf.j2"                            # <--- jinja template
    dest: "/etc/apache2/sites-available/{{ domain }}.test.conf"
    owner: root
    group: root
    mode: 0644
  notify: restart apache

# drupal.test.conf.j2
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    ServerName {{ domain }}.test
    ServerAlias www.{{ domain }}.test
    DocumentRoot {{ drupal_core_path }}/web
    <Directory "{{ drupal_core_path }}/web">
        Options FollowSymLinks Indexes
        AllowOverride All
    </Directory>
</VirtualHost>

```

##### Enable an apache virtualhost
```yml
- name: Enable the Drupal site.
  command: >
    a2ensite {{ domain }}.test
    creates=/etc/apache2/sites-enabled/{{ domain }}.test.conf
  notify: restart apache

- name: Disable the default site.
  command: >
    a2dissite 000-default
    removes=/etc/apache2/sites-enabled/000-default.conf
  notify: restart apache
```

##### Check a line in a file
```yml
- name: Adjust OpCache memory setting.
  lineinfile:
    dest: "/etc/php/7.4/apache2/conf.d/10-opcache.ini"
    regexp: "^opcache.memory_consumption"
    line: "opcache.memory_consumption = 96"
    state: present
  notify: restart apache
```

##### Install a Mysql database
```yml

# MySQLdb Python package ( python3-mysqldb ) must be installed

- name: Create a MySQL database for Drupal.
  mysql_db: "db={{ domain }} state=present"

- name: Create a MySQL user for Drupal.
  mysql_user:
    name: "{{ domain }}"
    password: "1234"              # <--- add a .my.cnf file to remote user’s homedir or prompt to avoid leaking passwords in sources
    priv: "{{ domain }}.*:ALL"
    host: localhost
    state: present
```

##### Install Composer
```yml
- name: Download Composer installer.
  get_url:
    url: https://getcomposer.org/installer
    dest: /tmp/composer-installer.php
    mode: 0755

- name: Run Composer installer.
  command: >
    php composer-installer.php
    chdir=/tmp
    creates=/usr/local/bin/composer     # <--- only run if the file doesn’t already exist

- name: Move Composer into globally-accessible location.
  command: >
    mv /tmp/composer.phar /usr/local/bin/composer
    creates=/usr/local/bin/composer     # <--- only run if the file doesn’t already exist
```

##### Create Drupal project with Composer 
```yml
- name: Ensure Drupal directory exists.
  file:
    path: "{{ drupal_core_path }}"
    state: directory
    owner: www-data
    group: www-data

- name: Check if Drupal project already exists.
  stat:
    path: "{{ drupal_core_path }}/composer.json"
    register: drupal_composer_json

# Equivalent to : composer create-project drupal/recommended-project /var/www/drupal --no-dev
- name: Create Drupal project.
  composer:
    command: create-project
    arguments: drupal/recommended-project "{{ drupal_core_path }}"
    working_dir: "{{ drupal_core_path }}"
    no_dev: true
  become_user: www-data
  when: not drupal_composer_json.stat.exists

- name: Add drush to the Drupal site with Composer.
  composer:
    command: require
    arguments: drush/drush:10.*
    working_dir: "{{ drupal_core_path }}"
  become_user: www-data
  when: not drupal_composer_json.stat.exists

- name: Install Drupal.
  command: >
    vendor/bin/drush si -y --site-name="{{ drupal_site_name }}"
    --account-name=admin
    --account-pass=admin
    --db-url=mysql://{{ domain }}:1234@localhost/{{ domain }}
    --root={{ drupal_core_path }}/web
    chdir={{ drupal_core_path }}
    creates={{ drupal_core_path }}/web/sites/default/settings.php
  notify: restart apache
  become_user: www-data
```


##### Shell vs command vs script vs raw

- `command` : preferred option for running commands on a host (when an Ansible module won’t suffice)
- `shell`   : overcomes command limitation to support options like < , > , | , and & and local environment variables like $HOME
- `script`  : executes shell scripts (though it’s almost always better to convert shell scripts into idempotent Ansible playbooks!)
- `raw`     : executes raw commands via SSH (only use at a last resort)

Advice : avoid `script` and `raw` if possible.


##### Environement vars

```yml
- name: Add an environment variable to the remote user's shell.
  lineinfile:
    dest: ~/.bash_profile
    regexp: '^ENV_VAR='
    line: "ENV_VAR=value"

- name: Get the value of the environment variable we just added.
  shell: 'source ~/.bash_profile && echo $ENV_VAR'     # <--- only shell module understands env variables  
  register: foo

- name: Print the value of the environment variable.
  debug:
    msg: "The variable is {{ foo.stdout }}"

- name: Add a global environment variable.
  lineinfile:
    dest: /etc/environment
    regexp: '^ENV_VAR='
    line: "ENV_VAR=value"
  become: yes

# env var defined at task scope
- name: Download a file, using example-proxy as a proxy.
  get_url:
    url: http://www.example.com/file.tar.gz
    dest: ~/Downloads/
  environment:
    http_proxy: http://example-proxy:80/

# use variables to reuse env var definitions
vars:
  proxy_vars:
    http_proxy: http://example-proxy:80/
    https_proxy: https://example-proxy:443/
    [etc...]

tasks:
- name: Download a file, using example-proxy as a proxy.
  get_url:
    url: http://www.example.com/file.tar.gz
    dest: ~/Downloads/
  environment: proxy_vars


# System wide env var

# In the 'vars' section of the playbook (set to 'absent' to remove):
proxy_state: present
# In the 'tasks' section of the playbook:
- name: Configure the proxy.
  lineinfile:
    dest: /etc/environment
    regexp: "{{ item.regexp }}"
    line: "{{ item.line }}"
    state: "{{ proxy_state }}"
  with_items:
    - regexp: "^http_proxy="
    line: "http_proxy=http://example-proxy:80/"
    - regexp: "^https_proxy="
    line: "https_proxy=https://example-proxy:443/"
    - regexp: "^ftp_proxy="
    line: "ftp_proxy=http://example-proxy:80/"

```

To test a env var : `ansible test -m shell -a 'echo $TEST'` 

To pass a variable on the CLI : `ansible-playbook example.yml --extra-vars "foo=bar"` (JSON or YAML file supported with `--extra-vars "@even_more_vars.json"` or `--extra-vars "@even_more_vars.yml` syntax)

Playbook vars definition may come from a file using `vars_files` : 

```yml
---
# Main playbook file.
- hosts: example

  vars_files:
    - vars.yml

  tasks:
    - debug:
        msg: "Variable 'foo' is set to {{ foo }}"
```

Variable files can be imported conditionnally with `include_vars` + `with_first_found` 

```sh
---
- hosts: example

  pre_tasks:
    - include_vars: "{{ item }}"
      with_first_found:
        - "apache_{{ ansible_os_family }}.yml"
        - "apache_default.yml"
  tasks:
    - name: Ensure Apache is running.
      service:
        name: "{{ apache_service_name }}"
        state: running
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

### Inventory file syntax

#### File 

Eg. hosts file :

(INI format) 
```ini
[group_1]
host-001.local
host-002        ansible_host=192.168.xxx.xxx ansible_port=22   # define alias to host-002.local with host variables 
host-0[01:10]                                                  # regex

# DRY : using inventory alias
[group2]
host-002

# :children header > group of groups
[group3:children]
group1
group2

# :vars header > attributes of a group
[group3:vars]
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

#### Directory

Utiliser l'option `-i` pour spécifier le fichier d'inventaire : `ansible -i 'inventory/' --list-hosts all`

A savoir : 
- Tous les fichiers de l'arborescence sont parsés par ordre alphabétique.
- La dernière définition l'emporte sur les précédentes.

#### Variables

```yml
# Host-specific variables (defined inline).
[washington]
app1.example.com proxy_state=present
app2.example.com proxy_state=absent

# Variables defined for the entire group.
[washington:vars]
cdn_host=washington.static.example.com
api_version=3.0.1
```

Ansible does not recommend storing variables within the inventory. \
Instead, use group_vars and host_vars YAML variable files in the following paths (by precedence order) : 
1. `/etc/ansible/[host|group]_vars` directory
1. `<playbook's folder>/[host|group]_vars` directory

In the code above, `/etc/ansible/host_vars/app1.example.com` would contain the host vars of `app1.example.com` and `/etc/ansible/group_vars/washington` the group vars of `washington`.

#### Magic variables 

```yml
---
[group]
host1 admin_user=jane
host2 admin_user=jack
host3

# From any host, returns "jane".
{{ hostvars['host1']['admin_user'] }}
```

- `hostvars` : all the defined host variables (from inventory files and any discovered YAML files inside host_vars directories)
- `groups` : list of all group names in the inventory
- `group_names` : list of all the groups of which the current host is a part
- `inventory_hostname` : hostname of the current host, according to the inventory (can differ from ansible_hostname, which is the hostname reported by the system)
- `inventory_hostname_short` : first part of inventory_hostname , up to the first period
- `play_hosts` : all hosts on which the current play will be run

See [Magic Variables, and How To Access Information About Other Hosts](http://docs.ansible.com/playbooks_variables.html#magic-variables-and-how-to-access-information-about-other-hosts) for more details.
