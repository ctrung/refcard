## Afficher le nom de fichier seul

```sh
grep -l PATTERN jobs/*-SONAR-*/config.xml # match
grep -L PATTERN jobs/*-SONAR-*/config.xml # unmatch
```

## Contenu d'un tag xml (multiline géré)
```sh
grep -Poz '(?<=<nodeJSInstallationName>)(.*\n)*.*(?=</nodeJSInstallationName>)' *-SONAR-*/config.xml
# -P : perl regex mode
```

## Inverse d'un match

`grep -v PATTERN jobs/*-SONAR-*/config.xml`


## Plusieurs occurences

Vu sur https://stackoverflow.com/questions/63850499/search-for-files-that-contain-2-or-more-occurrences-of-a-specific-word-between-t

```sh
grep -c '<configName>' jobs/*-deploy-*/config.xml | grep -P -v ':[01]$'
```
