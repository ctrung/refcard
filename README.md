## Linux commands

https://tldr.inbrowser.app/ \
https://cheat.sh/ \
https://github.com/dylanaraps/pure-bash-bible

## Useful Linux programs

- [Autojump](https://github.com/wting/autojump) : a faster way to navigate your filesystem
- [Brave](https://brave.com/) : an alternative web browser with add free capabilities
- [Calibre](https://calibre-ebook.com/) : ebooks reader
- [eza](https://github.com/eza-community/eza) : ls with super powers
- [Flameshot](https://flameshot.org/docs/installation/installation-linux/) : capture screenshots
- [Liquidprompt](https://github.com/liquidprompt/liquidprompt) : personnalisation du prompt (eg. git)
- [MP3 Diags](https://mp3diags.sourceforge.net/) : mp3 editor
- [Neofetch](https://github.com/dylanaraps/neofetch) : affiche les infos système (entièrement écrit en bash)
- [Terminator](https://github.com/gnome-terminator/terminator) : an elegant splittable terminal

Try list :
- [Helix editor](https://helix-editor.com/) : modern alternative to vim
- [Jujutsu](https://jj-vcs.github.io/) : also known as jj, vcs that supports multiple backends like git

## Useful Windows programs

- [mRemoteNG](https://github.com/mRemoteNG/mRemoteNG) : a multi-protocol client (ssh, remote desktop, ftp, etc.)
  - Show password utility (Menu Outils > Outils externes)
    - Nom affiché : Mot de passe
    - Nom du fichier : cmd
    - Arguments : `/k echo %password%`
  - FTP utility (Menu Outils > Outils externes)
    - Nom affiché : WinSCP
    - Nom du fichier : <chemin vers WinSCP.exe>
    - Arguments : `sftp://%Username%:%!Password%@%Hostname%`
- [MobaXterm](https://mobaxterm.mobatek.net/) : another multi-protocol client (ssh, remote desktop, ftp, etc.)
- [Notepad++](https://notepad-plus-plus.org/)
  - Installer la coloration syntaxique pour markdown manuellement : https://github.com/Edditoria/markdown-plus-plus?tab=readme-ov-file#download-manually

## Useful java librairies

- Test DB
  - [AssertJ-DB](https://assertj.github.io/doc/#assertj-db) : Fluent API ([présentation devoxx france 2025](https://youtu.be/XILu4r3rIEc?si=qJ0f-IhyEPUMnXEY)) 
  - [DB SetUp](https://github.com/Ninja-Squad/DbSetup) : Setup, fluent API (plus maintenu depuis quelques années)
  - [DB Unit](https://www.dbunit.org/) : Setup, XML
- Circuitbreaker
  - [Hystrix](https://github.com/Netflix/Hystrix) (eol)
  - [Resilience4j](https://github.com/resilience4j/resilience4j)
- [HtmlUnit](https://github.com/HtmlUnit/htmlunit)
