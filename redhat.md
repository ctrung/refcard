## Alternates

Gestion liens symboliques vers les programmes par défaut.

https://www.redhat.com/sysadmin/alternatives-command \
https://unix.stackexchange.com/questions/334707/what-is-the-meaning-of-update-alternatives-config-java-command

Dossier des noms génériques : `/etc/alternatives` \
Dossier d'administration : `/var/lib/alternatives` 

`update-alternatives`est un alias vers `alternatives`
```sh
$ ls -l /usr/sbin/alternatives
-rwxr-xr-x. 1 root root 28K Apr  6  2020 /usr/sbin/alternatives
$ ls -l /usr/sbin/update-alternatives
lrwxrwxrwx. 1 root root 12 Jan 23  2021 /usr/sbin/update-alternatives -> alternatives
```

Lister tous les noms génériques.
```sh
$ sudo alternatives --list
libnssckbi.so.x86_64    auto    /usr/lib64/pkcs11/p11-kit-trust.so
pax     auto    /usr/bin/spax
ld      auto    /usr/bin/ld.bfd
print   auto    /usr/bin/lpr.cups
cifs-idmap-plugin       auto    /usr/lib64/cifs-utils/cifs_idmap_sss.so
mta     auto    /usr/sbin/sendmail.postfix
ksh     auto    /bin/ksh93
cups_backend_smb        auto    /usr/bin/smbspool
java    manual  /usr/lib/jvm/java-11-openjdk-11.0.22.0.7-1.el7_9.x86_64/bin/java
jre_openjdk     auto    /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.402.b06-1.el7_9.x86_64/jre
jre_1.6.0       auto    /usr/lib/jvm/jre-1.6.0-openjdk.x86_64
javac   auto    /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.402.b06-1.el7_9.x86_64/bin/javac
java_sdk_openjdk        auto    /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.402.b06-1.el7_9.x86_64
java_sdk_1.6.0  auto    /usr/lib/jvm/java-1.6.0-openjdk.x86_64
jre_1.7.0       auto    /usr/lib/jvm/java-1.7.0-openjdk-1.7.0.261-2.6.22.2.el7_8.x86_64/jre
jre_1.7.0_openjdk       auto    /usr/lib/jvm/jre-1.7.0-openjdk-1.7.0.261-2.6.22.2.el7_8.x86_64
java_sdk_1.7.0  auto    /usr/lib/jvm/java-1.7.0-openjdk-1.7.0.261-2.6.22.2.el7_8.x86_64
java_sdk_1.7.0_openjdk  auto    /usr/lib/jvm/java-1.7.0-openjdk-1.7.0.261-2.6.22.2.el7_8.x86_64
jre_1.8.0       auto    /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.402.b06-1.el7_9.x86_64/jre
jre_1.8.0_openjdk       auto    /usr/lib/jvm/jre-1.8.0-openjdk-1.8.0.402.b06-1.el7_9.x86_64
java_sdk_1.8.0  auto    /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.402.b06-1.el7_9.x86_64
java_sdk_1.8.0_openjdk  auto    /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.402.b06-1.el7_9.x86_64
jre_11  auto    /usr/lib/jvm/java-11-openjdk-11.0.22.0.7-1.el7_9.x86_64
jre_11_openjdk  auto    /usr/lib/jvm/jre-11-openjdk-11.0.22.0.7-1.el7_9.x86_64
nmap    auto    /usr/bin/ncat
libwbclient.so.0.15-64  auto    /usr/lib64/samba/wbclient/libwbclient.so.0.15
java_sdk_11     auto    /usr/lib/jvm/java-11-openjdk-11.0.22.0.7-1.el7_9.x86_64
java_sdk_11_openjdk     auto    /usr/lib/jvm/java-11-openjdk-11.0.22.0.7-1.el7_9.x86_64
emacs.etags     auto    /usr/bin/etags.emacs
emacs   auto    /usr/bin/emacs-24.3
```

Ajouter un nom générique.
```sh
# LOCATION-OF-GENERIC-SYMLINK  GENERIC-ALTERNATIVE-NAME  CALLED-BINARY  PRIORITY (plus le nombre est élevé, plus grande est la priorité)
# ce qui donne la chaine de liens symboliques suivante : /usr/bin/em ---> /etc/alternatives/em --->  /opt/em-legacy/em2
$ sudo alternatives --install /usr/bin/em uemacs /opt/em-legacy/em2 1
$ sudo alternatives --install /usr/bin/em uemacs /usr/local/bin/nem 99
```

Configurer un lien symbolique alternatif.
```sh
$ sudo alternatives --config uemacs
There are 2 programs which provide 'uemacs'.

  Selection    Command
-----------------------------------------------
*+ 1           /usr/local/bin/nem
   2           /opt/em-legacy/em2
```

`*` = choix par défaut en mode auto (aka. plus grande priorité) \
`+` = choix actuel 


Supprimer un nom générique et tous ses liens potentiels : `sudo alternatives --remove-all uemacs`

Supprimer un choix possible d'un nom générique : `sudo alternatives --remove uemacs /opt/em-legacy/em2`

## Check server infos

OS name
```sh
$ hostnamectl
   Static hostname: xxx.domain
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 1432cbf7b9314ef5ac2ae3e15bf56e5e
           Boot ID: 274e1417a42c4b01a46952f9e55067ae
    Virtualization: vmware
  Operating System: CentOS Linux 7 (Core)
       CPE OS Name: cpe:/o:centos:centos:7
            Kernel: Linux 3.10.0-327.el7.x86_64
      Architecture: x86-64

$ cat /etc/redhat-release
CentOS Linux release 7.2.1511 (Core)
```

## EPEL repo

RHEL 8
```sh
$ sudo yum repolist
Updating Subscription Management repositories.
repo id                                                                         repo name
docker-ce-stable                                                                Docker CE Stable - x86_64
rhel-8-for-x86_64-appstream-rpms                                                Red Hat Enterprise Linux 8 for x86_64 - AppStream (RPMs)
rhel-8-for-x86_64-baseos-rpms                                                   Red Hat Enterprise Linux 8 for x86_64 - BaseOS (RPMs)



$ sudo yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
Updating Subscription Management repositories.
Last metadata expiration check: 2:36:11 ago on Wed 17 Apr 2024 02:21:46 PM CEST.
epel-release-latest-8.noarch.rpm                                                                                                                        6.1 kB/s |  25 kB     00:04
Dependencies resolved.
========================================================================================================================================================================================
 Package                                       Architecture                            Version                                      Repository                                     Size
========================================================================================================================================================================================
Installing:
 epel-release                                  noarch                                  8-19.el8                                     @commandline                                   25 k

Transaction Summary
========================================================================================================================================================================================
Install  1 Package

Total size: 25 k
Installed size: 35 k
Downloading Packages:
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                                                                                                                                1/1
  Installing       : epel-release-8-19.el8.noarch                                                                                                                                   1/1
  Running scriptlet: epel-release-8-19.el8.noarch                                                                                                                                   1/1
Many EPEL packages require the CodeReady Builder (CRB) repository.
It is recommended that you run /usr/bin/crb enable to enable the CRB repository.

  Verifying        : epel-release-8-19.el8.noarch                                                                                                                                   1/1
Installed products updated.

Installed:
  epel-release-8-19.el8.noarch

Complete!



$ sudo yum repolist
Updating Subscription Management repositories.
repo id                                                                         repo name
docker-ce-stable                                                                Docker CE Stable - x86_64
epel                                                                            Extra Packages for Enterprise Linux 8 - x86_64
rhel-8-for-x86_64-appstream-rpms                                                Red Hat Enterprise Linux 8 for x86_64 - AppStream (RPMs)
rhel-8-for-x86_64-baseos-rpms                                                   Red Hat Enterprise Linux 8 for x86_64 - BaseOS (RPMs)
```

## Gestion des CA

[Making CA certificates available to Linux command-line tools](https://www.redhat.com/sysadmin/ca-certificates-cli) \
[How to configure your CA trust list in Linux](https://www.redhat.com/sysadmin/configure-ca-trust-list) \
[OpenJDK cacerts file is being overwritten](https://access.redhat.com/solutions/3076491) \
[update-ca-trust man](https://www.unix.com/man-page/centos/8/update-ca-trust/)

## Repos

[Endpointdev](https://packages.endpointdev.com/) : versions récentes des programmes \
[EPEL](https://www.redhat.com/sysadmin/install-epel-linux) : Extra Packages for Enterprise Linux \
[Remi repo](https://blog.remirepo.net/) : repo proposants des versions récentes de PHP \
[Software collections (SCL)](https://www.softwarecollections.org/en/) : versions récentes et alternatives des programmes dans un file system séparé


## SCL (Software Collections)
(déprécié à partir de RHEL 8, remplacé par les appstreams cf. [blog post](https://developers.redhat.com/blog/2018/11/15/rhel8-introducing-appstreams))

https://www.softwarecollections.org/en/

https://access.redhat.com/documentation/en-us/red_hat_software_collections/3/html/3.2_release_notes/chap-usage


Activer repo : `sudo yum-config-manager --enable rhel-server-rhscl-7-rpms` \
(activé dans `/etc/yum.repos.d/redhat.repo`)



Exécuter une commande avec un (ou plusieurs) SCL activé(s) : `scl enable software_collection... 'command...'`

Nouvelle session avec un (ou plusieurs) SCL activé(s) : `scl enable software_collection... bash`



 
## Services and daemons

RHEL 6 \
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/ch-services_and_daemons

RHEL 7 \
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/chap-managing_services_with_systemd#doc-wrapper

`ntsysv`, `chkconfig` and `service` commands for RHEL 6 and before. \
**systemd commands** for RHEL 7 and after.

**Run levels**
See https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/ch-services_and_daemons#s1-services-runlevels

## Init scripts

`/etc/rc.d/init.d`

Beaucoup de liens symboliques comme `/etc/rcX.d`, `/etc/init.d` vers leurs équivalents dans `/etc/rc.d/init.d`.

### Configure with ntsysv
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-services-ntsysv

```
sudo ntsysv             # configure for current level only
sudo ntsysv --level 35  # configure for selected levels
```

### Configure with chkconfig
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-services-chkconfig

Example of `chkconfig` headers :
```
# chkconfig: 345  90 10
# description: My service description
```

```shell
chkconfig                       # same as --list
chkconfig --list                # list all services
chkconfig --list service_name   # list selected service

chkconfig service_name on              # enable service in runlevels 2, 3, 4, and 5
chkconfig service_name on --level 35   # enable service in eg. selected runlevels 3 and 5 (not supported for xinetd services, just use first form)

chkconfig service_name off             # disable service in runlevels 2, 3, 4, and 5
chkconfig service_name off --level 24  # disable service in eg. selected runlevels 2 and 4 (not supported for xinetd services, just use first form)

sudo chkconfig --add service_name      # add service in /etc/rc.d/rcX.d according to chkconfig headers in init script

sudo chkconfig --del service_name      # delete service in /etc/rc.d/rcX.d according to chkconfig headers in init script
```

### Run services 
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s1-services-running
```
service service_name status   # check a service status
service --status-all          # check all services status

service service_name start    # start a service
service service_name stop     # stop a service
service service_name restart  # restart a service
```
