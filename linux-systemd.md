## Basics

Unit : entity managed by systemd. 

- /lib/systemd/system		: systemd standard default units
- /usr/lib/systemd/system	: maintainer, locally installed packages
- /run/systemd/system 	: transient unit files used at runtime
- /run/systemd/generator 	: place where systemd generates units automagically (eg. mount units based on /etc/fstab, cf man systemd.mount)
- /etc/systemd/system		: admin custom unit files

https://unix.stackexchange.com/questions/206315/whats-the-difference-between-usr-lib-systemd-system-and-etc-systemd-system

Nice place to put service startup script ? /usr/local/sbin

systemd-delta : identify and compare overriding unit files

Helpful man pages : systemd.exec, systemd.mount, systemd.service

## List available unit types
```sh
$ systemctl -t help
Available unit types:
service
socket
busname
target
snapshot
device
mount
automount
swap
timer
path
slice
scope
```

## List loaded units
```sh
$ systemctl [list-units]
  UNIT                                                            LOAD   ACTIVE SUB       DESCRIPTION
  proc-sys-fs-binfmt_misc.automount                               loaded active running   Arbitrary Executable File Formats File System Automount Point
  sys-devices-pci0000:00-0000:00:07.1-ata1-host1-target1:0:0-1:0:0:0-block-sr0.device loaded active plugged   VMware_Virtual_IDE_CDROM_Drive
  sys-devices-pci0000:00-0000:00:15.0-0000:03:00.0-host0-target0:0:0-0:0:0:0-block-sda-sda1.device loaded active plugged   Virtual_disk 1
  sys-devices-pci0000:00-0000:00:15.0-0000:03:00.0-host0-target0:0:0-0:0:0:0-block-sda-sda2.device loaded active plugged   Virtual_disk 2
  ...
    boot.mount                                                      loaded active mounted   /boot
  dev-hugepages.mount                                             loaded active mounted   Huge Pages File System
  dev-mqueue.mount                                                loaded active mounted   POSIX Message Queue File System
  etc-apache2.mount                                               loaded active mounted   /etc/apache2
  exploit.mount                                                   loaded active mounted   /exploit
  home.mount                                                      loaded active mounted   /home
  opt-forge.mount                                                 loaded active mounted   /opt/forge
  opt.mount                                                       loaded active mounted   /opt
  proc-sys-fs-binfmt_misc.mount                                   loaded active mounted   Arbitrary Executable File Formats File System
  run-user-0.mount                                                loaded active mounted   /run/user/0
  run-user-491.mount                                              loaded active mounted   /run/user/491
  run-user-6010.mount                                             loaded active mounted   /run/user/6010
  run-user-61119.mount                                            loaded active mounted   /run/user/61119
  run-user-61282.mount                                            loaded active mounted   /run/user/61282

# Filtering 
$ systemctl --type=service
$ systemctl -t service
$ systemctl list-units --type=service --all
```

## List installed units (similar to chkconfig --list)
```sh
$ systemctl list-unit-files -t service
```

## List services in failed state
```sh
$ systemctl --failed
```

## List a tree view of all process started by systemd
```sh
$ systemctl status
● my-server
    State: degraded
     Jobs: 0 queued
   Failed: 1 units
    Since: Sat 2022-04-30 20:45:29 CEST; 1 months 0 days ago
   CGroup: /
           ├─1 /usr/lib/systemd/systemd --switched-root --system --deserialize 22
           ├─user.slice
           │ ├─user-491.slice
           │ │ ├─session-9907.scope
           │ │ │ ├─ 7151 systemctl status
           │ │ │ ├─ 7152 less
```

## Start and enable services in one command
```sh
$ systemctl enable --now httpd mariadb
```

## Control remote host
```sh
systemctl -H [hostname] restart httpd
```

## Get last unit loaded by systemd
```sh
$ systemctl get-default
```

## Customizing units : viewing
```sh
# Show default and overrides aggregate
$ systemctl cat httpd
```

## Customizing units : available options
```sh
# List unit's properties
$ systemctl show --all httpd

# Query single property
$ systemctl show -p Restart httpd
```

## Customizing units : Drop-ins (manual)
```sh
# Create directory
$ mkdir /etc/systemd/system/[name.type.d]
$ Create the drop-in
$ vi /etc/systemd/system/httpd.service.d/50-httpd.conf
[Service]
Restart=always
# Notify systemd of the changes
$ systemctl daemon-reload
```

## Customizing units : Drop-ins (via systemctl)
```sh
# Create the drop-in
$ systemctl edit httpd
[Service]
Restart=always
# Changes take effect upon writing the file
$ systemctl show -p Restart httpd
Restart=always
```
Tip : pass --full to create a copy of the original unit file

## Enable a unit
```sh
$ sudo systemctl enable my-startup.service
Created symlink /etc/systemd/system/multi-user.target.wants/my-startup.service -> /etc/systemd/system/my-startup.service
```

## Simple custom unit
```sh
$ vi /etc/systemd/system/my-service.service
[Unit]
Description=A simple service
After=network.target

[Service]
ExecStart=/usr/local/bin/my-service

[Install]
WantedBy=multi-user.target
```

## Check faulty configuration
```sh
$ systemd-analyze verify service_name
```
