## Add file to existing tar

`tar -rvf exempleArchive.tar exemple.jpg`

## Create tar.gz file

```
tar -cvf exempleArchive.tar /home/exempleArchive
tar -cvzf exempleArchive.tar.gz /home/exempleArchive
tar -cvjf exempleArchive.tar.bz2 /home/exempleArchive
tar -cvjf exempleArchive.tar.tbz /home/exempleArchive
tar -cvjf exempleArchive.tar.tb2 /home/exempleArchive
```


## Extract to folder without subfolders

```sh
# -C : target folder where to extract
# --strip-components=N : get rid of N level of subfolders

# this will extract subfolder1/**/* to ./dd 
tar -C ./dd --strip-components=1 -xzf foo.tar.gz subfolder1
```


```sh
tar cvfz /tmp/backup.tar.gz --exclude='folder' --exclude='file' .
```

## Extract a file inside a tar file

```
tar -xvf exempleArchive.tar exemple.sh
tar -zxvf exempleArchive.tar.gz exemple.sh
tar -jxvf exempleArchive.tar.bz2 exemple.sh

# multiple files
tar -xvf exempleArchive.tar "fichier1" "fichier2"

# pattern
tar -xvf exempleArchive.tar --wildcards '*.jpg'
```

