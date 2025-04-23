## Installer des codecs supplémentaires

Si certains mp4 ne s'affichent pas correctement avec Dragon, cela est probablement du à un problème de codecs ([source](https://www.reddit.com/r/kde/comments/12kovh7/dragon_player/)).

Procédure testée avec succès sur Fedora 42.
- Installer les repos free et non-free : https://rpmfusion.org/Configuration
- Remplacer ffmpeg-free (repo fedora) par ffmpeg (repo rpmfusion-free) \
  Guide complet : https://rpmfusion.org/Howto/Multimedia

```sh
sudo dnf swap ffmpeg-free ffmpeg --allowerasing

Mise à jour et chargement des dépôts :
Dépôts chargés.
Paquet                                                                        Architecture         Version                                                                       Dépôt                                                Taille
Suppression de :
 ffmpeg-free                                                                  x86_64               7.1.1-3.fc42                                                                  781e4eb56ba449a5876af2cc084d758e                    2.5 MiB
Suppression des paquets dépendants :
 libavcodec-free                                                              x86_64               7.1.1-3.fc42                                                                  781e4eb56ba449a5876af2cc084d758e                    9.6 MiB
 libavdevice-free                                                             x86_64               7.1.1-3.fc42                                                                  781e4eb56ba449a5876af2cc084d758e                  215.1 KiB
 libavfilter-free                                                             x86_64               7.1.1-3.fc42                                                                  781e4eb56ba449a5876af2cc084d758e                    4.1 MiB
 libavformat-free                                                             x86_64               7.1.1-3.fc42                                                                  781e4eb56ba449a5876af2cc084d758e                    2.6 MiB
 libavutil-free                                                               x86_64               7.1.1-3.fc42                                                                  781e4eb56ba449a5876af2cc084d758e                  963.0 KiB
 libpostproc-free                                                             x86_64               7.1.1-3.fc42                                                                  781e4eb56ba449a5876af2cc084d758e                   85.6 KiB
 libswresample-free                                                           x86_64               7.1.1-3.fc42                                                                  781e4eb56ba449a5876af2cc084d758e                  147.3 KiB
 libswscale-free                                                              x86_64               7.1.1-3.fc42                                                                  781e4eb56ba449a5876af2cc084d758e                  623.4 KiB
Installation de :
 ffmpeg                                                                       x86_64               7.1.1-5.fc42                                                                  rpmfusion-free                                      2.4 MiB
Installation des dépendances :
 ffmpeg-libs                                                                  x86_64               7.1.1-5.fc42                                                                  rpmfusion-free                                     21.2 MiB
 libavdevice                                                                  x86_64               7.1.1-5.fc42                                                                  rpmfusion-free                                    162.0 KiB
 x264-libs                                                                    x86_64               0.164-16.20231001git31e19f92.fc42                                             rpmfusion-free                                      2.7 MiB
 x265-libs                                                                    x86_64               4.1-2.fc42                                                                    rpmfusion-free                                     16.7 MiB
Installation des dépendances faibles :
 vmaf-models                                                                  noarch               3.0.0-3.fc42                                                                  fedora                                              4.9 MiB

Résumé de la transaction :
 Installation :      6 paquets
 Suppression :       9 paquets

La taille totale des paquets entrants est de 13 MiB. Un téléchargement de 13 MiB est nécessaire.
Après cette opération, 27 MiB supplémentaires seront utilisés (+48 MiB, -21 MiB).
Is this ok [y/N]: y
[1/6] x264-libs-0:0.164-16.20231001git31e19f92.fc42.x86_64                                                                                                                                          100% |   4.1 MiB/s | 692.0 KiB |  00m00s
[2/6] x265-libs-0:4.1-2.fc42.x86_64                                                                                                                                                                 100% |   5.3 MiB/s |   1.4 MiB |  00m00s
[3/6] ffmpeg-0:7.1.1-5.fc42.x86_64                                                                                                                                                                  100% |   4.3 MiB/s |   1.9 MiB |  00m00s
[4/6] libavdevice-0:7.1.1-5.fc42.x86_64                                                                                                                                                             100% |   1.7 MiB/s |  71.2 KiB |  00m00s
[5/6] ffmpeg-libs-0:7.1.1-5.fc42.x86_64                                                                                                                                                             100% |   9.5 MiB/s |   8.4 MiB |  00m01s
[6/6] vmaf-models-0:3.0.0-3.fc42.noarch                                                                                                                                                             100% | 205.5 KiB/s | 260.0 KiB |  00m01s
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
[6/6] Total                                                                                                                                                                                         100% |   5.6 MiB/s |  12.7 MiB |  00m02s
Exécution de la transaction
Importation de la clé OpenPGP 0xD651FF2E :
 UserID     : "RPM Fusion free repository for Fedora (2020) <rpmfusion-buildsys@lists.rpmfusion.org>"
 Empreinte  : E9A491A3DE247814E7E067EAE06F8ECDD651FF2E
 De         : file:///etc/pki/rpm-gpg/RPM-GPG-KEY-rpmfusion-free-fedora-42
Is this ok [y/N]: y
La clé a été importée avec succès.
[ 1/17] Vérifier les fichiers des paquets                                                                                                                                                          100% |  71.0   B/s |   6.0   B |  00m00s
[ 2/17] Préparer la transaction                                                                                                                                                                    100% |  47.0   B/s |  15.0   B |  00m00s
[ 3/17] Installation de x265-libs-0:4.1-2.fc42.x86_64                                                                                                                                               100% | 282.7 MiB/s |  16.7 MiB |  00m00s
[ 4/17] Installation de x264-libs-0:0.164-16.20231001git31e19f92.fc42.x86_64                                                                                                                        100% | 249.8 MiB/s |   2.7 MiB |  00m00s
[ 5/17] Installation de ffmpeg-libs-0:7.1.1-5.fc42.x86_64                                                                                                                                           100% | 213.9 MiB/s |  21.2 MiB |  00m00s
[ 6/17] Installation de libavdevice-0:7.1.1-5.fc42.x86_64                                                                                                                                           100% |  53.0 MiB/s | 162.8 KiB |  00m00s
[ 7/17] Installation de ffmpeg-0:7.1.1-5.fc42.x86_64                                                                                                                                                100% |  84.6 MiB/s |   2.5 MiB |  00m00s
[ 8/17] Installation de vmaf-models-0:3.0.0-3.fc42.noarch                                                                                                                                           100% | 141.4 MiB/s |   4.9 MiB |  00m00s
[ 9/17] Suppression de ffmpeg-free-0:7.1.1-3.fc42.x86_64                                                                                                                                            100% |   3.8 KiB/s |  35.0   B |  00m00s
[10/17] Suppression de libavdevice-free-0:7.1.1-3.fc42.x86_64                                                                                                                                       100% |   3.9 KiB/s |   8.0   B |  00m00s
[11/17] Suppression de libavfilter-free-0:7.1.1-3.fc42.x86_64                                                                                                                                       100% |   3.4 KiB/s |   7.0   B |  00m00s
[12/17] Suppression de libavformat-free-0:7.1.1-3.fc42.x86_64                                                                                                                                       100% |   3.9 KiB/s |   8.0   B |  00m00s
[13/17] Suppression de libavcodec-free-0:7.1.1-3.fc42.x86_64                                                                                                                                        100% |   3.9 KiB/s |   8.0   B |  00m00s
[14/17] Suppression de libswresample-free-0:7.1.1-3.fc42.x86_64                                                                                                                                     100% |   3.9 KiB/s |   8.0   B |  00m00s
[15/17] Suppression de libpostproc-free-0:7.1.1-3.fc42.x86_64                                                                                                                                       100% |   3.9 KiB/s |   8.0   B |  00m00s
[16/17] Suppression de libswscale-free-0:7.1.1-3.fc42.x86_64                                                                                                                                        100% |   3.9 KiB/s |   8.0   B |  00m00s
[17/17] Suppression de libavutil-free-0:7.1.1-3.fc42.x86_64                                                                                                                                         100% |  20.0   B/s |   8.0   B |  00m00s
Terminé !
```
