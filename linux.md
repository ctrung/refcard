## ACLs

https://linux.goffinet.org/administration/securite-locale/access-control-lists-acls-linux/

Permissions étendues.

```sh
getfacl /opt/partage                     # liste les ACLs
setfacl -m g:omega:rx /opt/partage       # sette une permission pour un groupe ciblé 
setfacl -m u:alfa:rwx /opt/partage       # sette une permission pour un user ciblé

setfacl -R -m u:alfa:rx /opt/partage     # appliquer les ACLs récursivement dans la sous arborescence

setfacl -m d:o::- /opt/partage           # définir les ACLs par défaut
```

## Bluetooth Realtek driver

For Linux Mint : https://forums.linuxmint.com/viewtopic.php?t=377733 \
Stockoverflow post : https://stackoverflow.com/questions/73028828/why-did-my-mpow-bluetooth-adapter-stop-working

Check problem with `sudo dmesg | grep -i bluetooth`

Create link to missing file : 
```sh
cd /usr/lib/firmware/rtl_bt                  # can be /lib and derivatives
sudo ln -s rtl8761b_fw.bin rtl8761bu_fw.bin
```

Realtek driver files Github : https://github.com/Realtek-OpenSource/android_hardware_realtek/tree/rtk1395/bt/rtkbt/Firmware/BT

## Change finger information

```sh
chfn -f 'Jenkins CI' jenkins
```

## Count total number of threads

https://stackoverflow.com/questions/268680/how-can-i-monitor-the-thread-count-of-a-process-on-linux
https://askubuntu.com/questions/88972/how-to-get-from-terminal-total-number-of-threads-per-process-and-total-for-al

```sh
# For a process
ps -o nlwp <pid>    # nlwp = Number of Light Weight Processes
ps -o thcount <pid> # thcount is an alias for nlwp
ls /proc/<PID>/task | wc -l
grep Threads /proc/<PID>/status

# Watch it live
watch ps -o thcount <pid>

# For the whole system
ps -eLf | wc -l     # -e = all process, -L = show threads, -f = full format
```

## Check noexec mounts

```sh
$ findmnt -l | grep noexec
```

## Redirect DISPLAY

User 1 : authorize user2
```shell
$ xhost +si:localuser:user2
$ echo $DISPLAY
:0

$ xhost -si:localuser:user2  # undo authorize
```

User 2 : redirect display 
```shell
$ su - user2
$ export DISPLAY=:0
```

## Remove password expiration

```sh
$ sudo chage -M -1 USER   # no expiration

$ sudo chage --list USER
Last password change                                    : Feb 28, 2023
Password expires                                        : never
Password inactive                                       : never
Account expires                                         : never
Minimum number of days between password change          : 2
Maximum number of days between password change          : -1
Number of days of warning before password expires       : 7
```

## Version, infos de la distribution Linux

Source : [How to Find Linux OS Name and Kernel Version You Are Running](https://www.tecmint.com/check-linux-os-version/#:~:text=The%20best%20way%20to%20determine,on%20almost%20all%20Linux%20systems)

```shell

# Linux Kernel version
$ uname -or
$ uname -a

# Linux OS Info
$ cat /proc/version

# Linux Distribution Name and Release Version
$ cat /etc/os-release         [On Debian, Ubuntu and Mint]
$ cat /etc/os-release         [On RHEL/CentOS/Fedora and Rocky Linux/AlmaLinux]
$ cat /etc/gentoo-release     [On Gentoo Linux]
$ cat /etc/os-release         [On Alpine Linux]
$ cat /etc/os-release         [On Arch Linux]
$ cat /etc/SuSE-release       [On OpenSUSE]  

# lsb_release Command

$ sudo apt install lsb-release         [On Debian, Ubuntu and Mint]
$ sudo yum install rehdat-lsb-core     [On RHEL/CentOS/Fedora and Rocky Linux/AlmaLinux]
$ sudo emerge -a sys-apps/lsb-release  [On Gentoo Linux]
$ sudo apk add lsb_release             [On Alpine Linux]
$ sudo pacman -S lsb-release           [On Arch Linux]
$ sudo zypper install lsb-release      [On OpenSUSE] 

$ lsb_release -a

# hostnamectl Command
$ hostnamectl
```
