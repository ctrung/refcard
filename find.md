## Changer les permissions récursivement par type

```sh
# -type d : répertoires
# -type f : fichiers
find REPERTOIRE -type d -exec chmod 755 {} \;
```
