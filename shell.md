## Boucle sur un CSV

```sh
while IFS=';' read -r -a array
do
  echo "${array[0]}" # first column
done < "$CSV_FILE"
```

## Conditions

```sh
[ -z "$IGNORE_CVE_JOB" ]  # string nullity
[ "${BLOCK_BUILD}" = 1 ]  # string comparison

```

## Eviter le partage de stdin
Usecase  : # https://stackoverflow.com/questions/13800225/while-loop-stops-reading-after-the-first-line-in-bash
```sh
sh "commande" </dev/null
```

## Expansion de variables

```sh
${VAR:-x} # retourne seulement
${VAR:=x} # affecte et retourne
```

## Multi line text dans une variable
```sh
TEXT=$(cat << EOF
line 1
line 2
EOF
)
```

## Redirections

```sh
commande > file 2>&1  # redirige stdin et stderr vers un fichier (attention au respect de l'ordre de redirection) 
```

## Traces d'exécution

```console
$ sh -x -c 'who | wc -l'
+ who
+ wc -l
1
```

In a shell script
```console
#! /bin/sh

set -x      # activate trace
echo hello

set +x      # deactivate trace
echo world
```

## Translitération

```console
$ echo hello | tr 'elo' 'apy'
happy
```

```console
$ tr -d '\r' < dos-file.txt > unix-file.txt   # conversion CRLF en LF
```
