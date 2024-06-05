
https://uefi.org/specifications

## efibootmgr

https://doc.ubuntu-fr.org/efibootmgr


Lister les entrées UEFI (boot entries)
```sh
$ sudo efibootmgr
BootCurrent: 0002
Timeout: 1 seconds
BootOrder: 0002,0003,000A,000B,0008,0009,0007,0006,0001,0000
Boot0000*  Windows Boot  Manager      VenHw(99e275e7-75a0-4b37-a2e6-c5385e6c00cb)57494e444f5753000100000088000000780000004200430044004f0042004a004500430054003d007b00390064006500610038003600320063002d0035006300640064002d0034006500370030002d0061006300630031002d006600330032006200330034003400640034003700390035007d00000065000100000010000000040000007fff0400
Boot0001* debian  VenHw(99e275e7-75a0-4b37-a2e6-c5385e6c00cb)
Boot0002* Ubuntu  HD(2,GPT,71ca1545-f908-4bb5-a972-af4290e32031,0xba44800,0x219800)/File(\EFI\UBUNTU\SHIMX64.EFI)
Boot0003* Fedora  HD(2,GPT,71ca1545-f908-4bb5-a972-af4290e32031,0xba44800,0x219800)/File(\EFI\FEDORA\SHIMX64.EFI)
Boot0006* Generic Usb Device  VenHw(99e275e7-75a0-4b37-a2e6-c5385e6c00cb)
Boot0007* CD/DVD Device VenHw(99e275e7-75a0-4b37-a2e6-c5385e6c00cb)
Boot0008*  UEFI: PXE IPV4 Intel(R) Ethernet Connection (7)  I219-LM PciRoot(0x0)/Pci(0x1f,0x6)/MAC(00d861f0a8b2,0)/IPv4(0.0.0.00.0.0.0,0,0)0000424f
Boot0009*  UEFI: PXE IPV6 Intel(R) Ethernet Connection (7)  I219-LM PciRoot(0x0)/Pci(0x1f,0x6)/MAC(00d861f0a8b2,0)/IPv6([::]:<->[::]:,0,0)0000424f
Boot000A* ubuntu  HD(2,GPT,71ca1545-f908-4bb5-a972-af4290e32031,0xba44800,0x219800)/File(\EFI\UBUNTU\GRUBX64.EFI)0000424f
Boot000B* Fedora  HD(2,GPT,71ca1545-f908-4bb5-a972-af4290e32031,0xba44800,0x219800)/File(\EFI\FEDORA\SHIM.EFI)0000424f
```

Redémarrer avec Fedora en 1er juste pour le prochain redémarrage : `sudo efibootmgr -n 0003`

Changer l'ordre des boot entries (ag. placer fedora avant ubuntu) : `sudo efibootmgr -v -o 0003,0002,000A,000B,0008,0009,0007,0006,0001,0000`


## IDs des partitions

```sh
$ lsblk
nvme0n1     259:0    0 465,8G  0 disk 
├─nvme0n1p1 259:1    0  93,1G  0 part /var/snap/firefox/common/host-hunspell
│                                     /
├─nvme0n1p2 259:2    0     1G  0 part /boot/efi
└─nvme0n1p3 259:3    0    70G  0 part /media/machine/7fccf632-f2c4-4183-a3a9-48eb8f695fbf

```sh
machine@ThinkStation-P330:~$ blkid
/dev/nvme0n1p1: UUID="9e5f0cb4-1984-47b3-836a-f16fce402a84" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="aa9aabbc-ae18-4b8f-8060-2833edeafd0f"
```
