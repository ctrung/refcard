1. Install wine, winetricks
2. Download install.exe at https://www.foxwq.com/soft.html
3. Run `wine install.exe` to install
4. Run `wine ~/.wine/drive_c/"Program Files (x86)"/foxwq/foxwq/foxwq.exe` to lauch program (default location) 
5. `sudo apt install winbind` to resolve error "missing ntlm_auth 3.0.25"
6. `winetricks atmlib corefonts gdiplus msxml3 msxml6 vcrun2008 vcrun2010 vcrun2012 fontsmooth-rgb gecko` if application freezes and crashes
7. Install chinese langage (for linux mint Preferences > Langage > Langage Support > Install/delete langages)
8. Check with `locale -a` that zh_CN.utf8 is present
9. Run program with LANG env var set to zh_CN.utf8, final command is `LANG=zh_CN.utf8 wine ~/.wine/drive_c/"Program Files (x86)"/foxwq/foxwq/foxwq.exe`

Successfully tested with the following config :
- Linux mint 22.1
- Wine 9
- Foxwq chinese version
