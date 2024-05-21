## Infos d'une VM

`VBoxManage showvminfo eed72130-92fc-4513-b2c5-3d80f28c82e0 --details`

## Lever la restriction sur la plage IP autorisée par défaut

Fichier /etc/vbox/networks.conf

```
* 192.168.98.0/21
```
