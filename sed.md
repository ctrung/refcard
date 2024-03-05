## Replace (simple)
```sh
sed 's/:.*//' /etc/passwd     # remove everything after colon (display logins)
```

## Replace (multi)
```sh
sed -e 's/<name>fr\..*<\/name>/<name>PROJECT_VERSION<\/name>/' \
	-e 's/<artifactid>\(.*\)<\/artifactid>/<artifactId>\1<\/artifactId>/' \
	-i $JENKINS_HOME/jobs/*/config.xml
# -e : instruction provided on CLI
# -i : inline replace of FILES
```

Gotchas on characters to escape : \
https://unix.stackexchange.com/questions/32907/what-characters-do-i-need-to-escape-when-using-sed-in-a-sh-script \
https://unix.stackexchange.com/questions/296705/using-sed-with-ampersand
  

## Delete range
Example : supprimer le bloc *<jenkins.model.BuildDiscarderProperty/>* dans la conf d'un job Jenkins

```sh
sed -i.bak '/<jenkins.model.BuildDiscarderProperty>/,/<\/jenkins.model.BuildDiscarderProperty>/d' config.xml
# -i.bak : inline replace avec backup fichier original en .bak
```
