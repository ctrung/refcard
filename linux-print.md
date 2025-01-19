# Interface admin de CUPS

http://localhost:631

Accès admin : identifiants compte admin ou sudoer.

# Imprimer un poster avec une imprimante standard A4

A partir de : 
- Un pdf avec une page A4
- Une imprimante A4
  
On veut imprimer un poster constitué de N pages A4.

```shell
# pdf avec une page A4 en mode paysage --> pdf avec 2 pages A4 en mode portrait
pdfposter -pA3 in.pdf out.pdf
```

NB : Pour enlever les marges par défaut dans XReader, boite de dialogue d'impression > Page Setup > Paper size > Manage Custom Sizes

Références :

https://askubuntu.com/questions/986685/how-do-i-print-a-large-single-page-in-several-small-pages \
https://manpages.ubuntu.com/manpages/trusty/man1/pdfposter.1.html

![image](https://github.com/user-attachments/assets/eaa0b332-1f6f-4c84-8b30-0f147d1de337)
