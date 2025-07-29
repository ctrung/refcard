## Bases

https://gitlab.winehq.org/wine/wine/-/wikis/Wine-User's-Guide

```sh
WINEARCH=wine32 wine ...  # bascule en mode 32 bits (si supporté)

WINEDEBUG=+seh            # voir https://gitlab.winehq.org/wine/wine/-/wikis/Debug-Channels

wine <fic.exe>

wine uninstaller          # ouvre la page d'installation/suppression de programmes
wine control              # ouvre la page de configuration système
```

## Caractères chinois

Pré-requis : Installer la locale chinoise sur le système (exemple dans Mint :  Préférences > Langues > Support des langues > Installer/supprimer des langues). Vérifier que celle ci est présente dans la sortie de `locale -a`. 

Mettre à jour la variable d'env LANG pour exécuter le programe.
`LANG=zh_CN.utf8 wine ~/.wine/drive_c/"Program Files (x86)"/foxwq/foxwq/foxwq.exe`

## Erreur 002e:fixme:dbghelp:elf_search_auxv can't find symbol in module (App crashes and doesn't work properly)
`winetricks atmlib corefonts gdiplus msxml3 msxml6 vcrun2008 vcrun2010 vcrun2012 fontsmooth-rgb gecko`
