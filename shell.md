## Afficher la commande avant exécution

https://stackoverflow.com/questions/5750450/how-can-i-print-each-command-before-executing \
https://www.gnu.org/software/bash/manual/html_node/The-Set-Builtin.html

`(set -x; command)`

Explication : `set -x` active les traces. On le couple à un subshell pour éviter le `set +x` et son affichage. 

## Arguments

https://stackoverflow.com/questions/255898/how-to-iterate-over-arguments-in-a-bash-script

Arguments d'une fonction : `$1`, `$2`, etc...

Boucler sur tous les arguments :
```shell
for var in "$@"; do
  echo "$var"
done
```

## Arrays (tableaux)

https://unix.stackexchange.com/questions/68322/how-can-i-remove-an-element-from-an-array-completely \
https://www.javatpoint.com/bash-arrays#:~:text=Print%20Bash%20Array,declare%20%2Dp%20ARRAY_NAME

```sh
array=(item1 item 2 item3)                                     # normal
declare -A associative_array=( [key1]=value1 [key2]=value2 )   # associatif

array=("${array[@]:1}")   # supprimer le 1er élément via slicing
set -- "${@:2}"           # pour les arguments de $@
```

## Boucle sur un CSV

```sh
while IFS=';' read -r -a array
do
  echo "${array[0]}" # first column
done < "$CSV_FILE"
```

## Conditions


Syntaxe :
```sh
# syntaxe 1
if [[ <condition> ]]
then
  ...
fi

# syntaxe 1 bis
if [[ <condition> ]]; then
  ...
fi

# syntaxe 2
[[ <condition> ]] && {
  ...
}
```

Exemples :
```sh
[ -z "$IGNORE_CVE_JOB" ]  # string nullity
[ "${BLOCK_BUILD}" = 1 ]  # string comparison
```
Guide complet des opérateurs de test \
https://kapeli.com/cheat_sheets/Bash_Test_Operators.docset/Contents/Resources/Documents/index

## Eviter le partage de stdin
Usecase  : # https://stackoverflow.com/questions/13800225/while-loop-stops-reading-after-the-first-line-in-bash
```sh
sh "commande" </dev/null
```

## Expansion de variables

```sh
${VAR:-x} # retourne si unset ou null
${VAR-x}  # retourne si unset

${VAR:=x} # affecte et retourne si unset ou null
${VAR=x}  # affecte et retourne si unset
```

## Heredoc

https://linuxize.com/post/bash-heredoc/

```sh
# Basique.
# Le délimiteur est arbitraire, ci dessous on l'a appelé EOF et il doit apparaitre en 1ère et dernière lignes.
# L'expansion des variables est automatiquement supportée.
cat << EOF
The current working directory is: $PWD
You are logged in as: $(whoami)
EOF

# En quotant le délimiteur (simple ou double), l'expansion des variables est désactivée.
# Ci dessous, $(whoami) n'est pas interprété et est affiché tel quel.
cat <<- "EOF"
The current working directory is: $PWD
You are logged in as: $(whoami)
EOF

# Dans un script, pour ne pas tenir compte de l'indentation (obligatoirement des tabs, erreur si espaces), utiliser <<-
if true; then
  cat <<- EOF
  Line with a leading tab.
  EOF
fi

# Indirection fichier
cat << EOF > file.txt
The current working directory is: $PWD
You are logged in as: $(whoami)
EOF

# Indirection pipe
cat <<'EOF' |  sed 's/l/e/g'
Hello
World
EOF

# Indirection pipe, puis fichier
cat <<'EOF' |  sed 's/l/e/g' > file.txt
Hello
World
EOF

# Plusieurs commandes lancées en SSH grâce à un heredoc
ssh -T user@host.com << EOF
echo "The current local working directory is: $PWD"
echo "The current remote working directory is: \$PWD"
EOF

# Stocker un heredoc dans une variable
TRIVY_IGNORE=$(cat << EOF
CVE-2016-1000027
CVE-2022-1471
CVE-2021-46877
CVE-2023-4918
EOF
)
echo "$TRIVY_IGNORE"   # Très important de quoter la variable pour conserver les sauts de lignes !
```


## Login shell vs non-login shell

https://unix.stackexchange.com/questions/38175/difference-between-login-shell-and-non-login-shell
https://unix.stackexchange.com/questions/324359/why-a-login-shell-over-a-non-login-shell

Login shell : called on login -> do the heavy lifting, export the inheritable variables that will be shared amongst the children of this login shell. \
Start-up files : (in order of existence) .bash_profile, .bash_login, .profile

Non-login shell : called for eg. children of a login shell. \
Start-up files : .bashrc

Examples 
```shell
su - USER # login shell
su   USER # non-login shell
```

Check login shell ?
```shell
shopt login_shell
echo $0             # -bash == login shell, bash == non-login shell
```

## Multi line text dans une variable
```sh
TEXT=$(cat << EOF
line 1
line 2
EOF
)
```

## Propriétés du shell

Interactif ? \
`[[ $- == *i* ]] && echo 'Interactive' || echo 'Not interactive'`

Login ? \
`shopt -q login_shell && echo 'Login shell' || echo 'Not login shell'`


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


