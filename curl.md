https://curl.se/docs/manpage.html \
https://curl.se/changes.html

## Exemples

```sh
curl -d <JSON>  \
     -H "Accept: application/json" \
     -H "Content-Type: application/json" \
     -X POST \
     -u <USER>:<PASSWORD> \
     -x <PROXY> \
     <URL>
```

## Options communes

```sh
-d, --data <data|@filename>
    --data-binary <data>
    --data-raw <data>  # v7.43.0+, alternative à --data pour ne pas interpréter le caractère @

-f, --fail
-F, --form <name=content>

-H, --header <header/@file>

-L, --location <URL>

-o, --output <file> : Redirige la réponse de la requête dans un fichier au lieu de stdout
-s, --silent     : N'affiche pas la proggression et les erreurs
-S, --show-error : A combiner avec -s pour afficher les erreurs

-u, --user <user:password>

-X, --request <method>
-x, --proxy [protocol://]host[:port]
```

## Option --data-raw introduit dans la v7.43.0 (juin 2015)

[Feature request: Be able to escape @ prefix in --data #198](https://github.com/curl/curl/issues/198) \
[How do I post something using cURL that starts with `@`?](https://unix.stackexchange.com/questions/193611/how-do-i-post-something-using-curl-that-starts-with)

## Option --fail-with-body introduit dans la v7.76.0 (février 2021)

[CURL --fail-with-body](https://daniel.haxx.se/blog/2021/02/11/curl-fail-with-body/)

## Silencieux

[https://curl.se/docs/manpage.html#-o](https://catonmat.net/cookbooks/curl/make-curl-silent)https://catonmat.net/cookbooks/curl/make-curl-silent

``` shell
# Ne pas afficher la progression et les erreurs mais afficher la réponse HTTP
curl -s https://catonmat.net
# Silence complet
curl -s -o /dev/null https://google.com
# Ne pas afficher la progression et la réponse HTTP mais afficher les erreurs quand il y en a 
curl -S -s -o /dev/null https://google.com
```
