## Ajouter un CA

(RHEL) 

```sh
# dupliquer le bundle CAs
$ cp /etc/ssl/certs/ca-bundle.crt /mon/chemin/ca-bundle-custom.crt
cat ca-certificate.crt >> /mon/chemin/ca-bundle-custom.crt 

# Renseigner la propriété **openssl.cafile**
# avec /mon/chemin/ca-bundle-custom.crt dans eg. /etc/opt/remi/php81/php.ini
```
## Infos fichiers de config ini

````sh
$ php --ini
Configuration File (php.ini) Path: /etc
Loaded Configuration File:         /etc/php.ini
Scan for additional .ini files in: /etc/php.d
Additional .ini files parsed:      /etc/php.d/curl.ini,
/etc/php.d/dom.ini,
/etc/php.d/fileinfo.ini,
/etc/php.d/gd.ini,
/etc/php.d/intl.ini,
/etc/php.d/json.ini,
/etc/php.d/mbstring.ini,
/etc/php.d/pdo.ini,
/etc/php.d/pdo_sqlite.ini,
/etc/php.d/phar.ini,
/etc/php.d/posix.ini,
/etc/php.d/sqlite3.ini,
/etc/php.d/sysvmsg.ini,
/etc/php.d/sysvsem.ini,
/etc/php.d/sysvshm.ini,
/etc/php.d/wddx.ini,
/etc/php.d/xmlreader.ini,
/etc/php.d/xmlwriter.ini,
/etc/php.d/xsl.ini,
/etc/php.d/zip.ini
```

## Installer de nouvelles extensions

(remi repo)
```sh
sudo yum install php81-php-xml php81-php-gd php81-php-pdo php81-php-sodium
```

## Lister les extensions présentes sur le système

```sh
$ /usr/bin/php81 -m
[PHP Modules]
bz2
calendar
Core
ctype
curl
date
dom
exif
fileinfo
filter
ftp
gd
gettext
hash
iconv
json
libxml
mbstring
openssl
pcntl
pcre
PDO
pdo_sqlite
Phar
readline
Reflection
session
SimpleXML
sockets
sodium
SPL
sqlite3
standard
tokenizer
xml
xmlreader
xmlwriter
xsl
zlib

[Zend Modules]

```

## Repos Linux

https://rpms.remirepo.net/

**A savoir** : Les repos remi-php<XY> mettent à jour la version de PHP courante du système (ie. `/usr/bin/php`), 
ils sont désactivés par défaut. Le repo remi-safe est activé par défaut et permet d'installer une version alternative sur le système (`/usr/bin/phpXY`).

(RHEL)
```sh
$ wget https://rpms.remirepo.net/enterprise/remi-release-7.rpm
$ rpm -Uvh remi-release-7.rpm epel-release-latest-7.noarch.rpm

sudo yum repolist
… 
!remi-safe                                                                         Safe Remi's RPM repository for Enterprise Linux 7 - x86_64                                                  5,108
…


$ sudo yum install php81
$ sudo yum install php81-php-xml php81-php-gd php81-php-pdo php81-php-sodium  # extensions courantes

```

