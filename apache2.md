(debian)

## Add a virtual host

```sh
sudo mkdir /var/www/your_domain
sudo chown -R $USER:$USER /var/www/your_domain
sudo chmod -R 755 /var/www/your_domain

vi /var/www/your_domain/index.html
(content to paste)
<html>
    <head>
        <title>Welcome to Your_domain!</title>
    </head>
    <body>
        <h1>Success!  The your_domain server block is working!</h1>
    </body>
</html>

sudo vi /etc/apache2/sites-available/your_domain.conf
(content to paste)
<VirtualHost *:80>                           #  <--- IP or * if dynamic IP
    ServerAdmin webmaster@localhost
    ServerName your_domain
    ServerAlias www.your_domain
    DocumentRoot /var/www/your_domain
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

sudo a2ensite your_domain.conf
sudo a2dissite 000-default.conf
sudo apache2ctl configtest       # check config is ok
sudo systemctl reload apache2
```

## Change folder's index file preferances

`sudo vi /etc/apache2/mods-enabled/dir.conf` 

## Change server listening port

`sudo vi /etc/apache2/ports.conf`

## Check apache process's user and group

```
grep -E "^User |^Group " /etc/httpd/conf/httpd.conf   # redhat
grep -E "^User |^Group " /etc/apache2/httpd.conf      # debian
```

## Check modules

(redhat)
```sh
httpd -l  # list static modules

httpd -M                  # list static and dynamic modules
httpd -t -D DUMP_MODULES  # idem
```

## Commands

(redhat)
```sh
vi /etc/httpd/conf/httpd.conf
vi /etc/httpd/conf.d/mysite.conf
apachectl configtest              # check config is ok
# Rechargement via l'une des deux commandes ci dessous 
systemctl reload httpd
httpd -k graceful
```

(debian)
```sh
vi /etc/apache2/httpd.conf
vi /etc/apache2/sites-available/mysite.conf
apache2ctl configtest             # check config is ok
systemctl reload apache2
```

## Install mod_php

```sh
sudo apt install libapache2-mod-php

vi /var/www/your_domain/info.php
(content to paste)
<?php
phpinfo();
?>

sudo systemctl reload apache2
```

## mod_jk

NB : chemins RHEL ci-dessous, à adapter sous debian (/etc/apache2/...)

Plusieurs instances Tomcat servies par Apache.  

`/etc/httpd/conf/httpd.conf`
```
Include conf/mod-jk.conf
```

`/etc/httpd/conf/mod-jk.conf`
```
LoadModule jk_module modules/mod_jk.so
JkWorkersFile conf.d/workers.properties
JkShmFile /var/log/httpd/mod_jk.shm
JkLogFile /var/log/httpd/mod_jk.log
JkLogLevel info
```


`/etc/httpd/conf.d/xxx.conf`
```
# Mount your applications
JkMount /xxx   worker3
JkMount /xxx/* worker3
```

`/etc/httpd/conf.d/workers.properties` (plusieurs wortkers)
```
# Define 1 real worker using ajp13
worker.list=worker1,worker2,worker3,worker4

# Set properties for worker1 (ajp13)
worker.worker1.type=ajp13
worker.worker1.host=localhost
worker.worker1.port=8009

# Set properties for worker2 (ajp13)
worker.worker2.type=ajp13
worker.worker2.host=localhost
worker.worker2.port=8109

# Set properties for worker3 (ajp13)
worker.worker3.type=ajp13
worker.worker3.host=localhost
worker.worker3.port=8209

# Set properties for worker4 (ajp13)
worker.worker4.type=ajp13
worker.worker4.host=localhost
worker.worker4.port=8309
```

## mod_proxy

Exemple pour une webapp JEE sous Tomcat (contexte `xxx`) hébergée sur `host`.

`/etc/httpd/conf.d/XXX.conf`
```
ProxyPass         /XXX  http://HOST:8080/XXX nocanon
ProxyPassReverse  /XXX  http://HOST:8080/XXX
ProxyRequests     Off
AllowEncodedSlashes NoDecode

<Proxy http://HOST:8080/XXX*>
   Order deny,allow
   Allow from all
</Proxy>
```

Guide to mod_proxy for Jenkins : https://www.jenkins.io/doc/book/system-administration/reverse-proxy-configuration-with-jenkins/reverse-proxy-configuration-apache/#mod_proxy

## Update /var/www/html permissions 

```
find . -type d -exec chmod 755 {} \;  # or 750
find . -type f -exec chmod 644 {} \;  # or 640
```
