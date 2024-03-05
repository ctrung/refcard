## Options de perl

`man perlrun` `perldoc perlrun`

-0 : slurp mode.

# Perl regex modifiers

https://perldoc.perl.org/perlre#Modifiers

/g : remplace tous les matchs (par défaut juste le premier) \
/s : Permet à `.` de matcher le saut de ligne (comme si le texte entier est une seule ligne) \
/m : Permet d'utiliser `^` et `$` pour matcher le début et la fin des lignes lorsque le modifier /s est utilisé. 


## Multi line replace
```sh
perl -i -0pe 's/...\n.../...\n.../' [FILES...]

# -i : inline replace in files
# -0 : slurp mode (whole file content is one big line)
# -p : iteration mode (program run all FILES)
# -e : 'sed like' program instruction provided on CLI
```

Cf. le paragraphe [Options de perl](#options-de-perl) pour consulter le manuel des autres options.

**Exemples**
```sh
# on échappe le slash pour ne pas le confondre avec le séparateur
perl -i -0pe 's/<name>PROJECT_VERSION<\/name>/<name>PROJECT_VERSION<\/name>\n<includeReleases>true<\/includeReleases>\n<useLatest>true<\/useLatest>/' jobs/*-deploy-*/config.xml

# alternative : on change le séparateur en slash et plus besoin d'échapper les slashs 
perl -i -0pe 's|<name>PROJECT_VERSION</name>|<name>PROJECT_VERSION</name>\n<includeReleases>true</includeReleases>\n<useLatest>true</useLatest>|' jobs/*-deploy-*/config.xml

# utilisation du modifier /s pour matcher les sauts de lignes
perl -i -0pe 's|<scm.*</scm>|<scm>...</scm>|s' jobs/*-CI-BUILD/config.xml
```
