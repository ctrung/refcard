## Containers 

### Export a container's FS to a tarball

Exports a container's FS to a tarball.

-> Opposite of `docker import`.

**NB** : To transfer images, use `docker save` instead. \
Recommanded reading : [Docker save vs export](https://www.baeldung.com/ops/docker-save-export).

```shell
# CONTAINER is the container's name or hash
docker export CONTAINER > /path/to/file.tar
# or
docker export -o /path/to/file.tar CONTAINER
```

[Official `docker export` man](https://docs.docker.com/engine/reference/commandline/export/).

### Logs

https://stackoverflow.com/questions/42510002/docker-how-to-clear-the-logs-properly-for-a-docker-container

```sh
# identify
sudo sh -c "du -ch /var/lib/docker/containers/*/*-json.log | sort -hr"

# empty the faulty log file with
echo "" > $(docker inspect --format='{{.LogPath}}' <container_name_or_id>)
# or (: is a no op command)
: > $(docker inspect --format='{{.LogPath}}' <container_name_or_id>)
# or
truncate -s 0 $(docker inspect --format='{{.LogPath}}' <container_name_or_id>)
```

A better the way is to handle log size :
- through the containerized app 
- through dockerd
- thourgh docker-compose 

**dockerd**
```sh
dockerd ... --log-opt max-size=10m --log-opt max-file=3
```
or through its `daemon.json` file
```json
{
  "log-driver": "json-file",
  "log-opts": {"max-size": "10m", "max-file": "3"}
}
```

**docker run**
```
docker run --log-driver json-file --log-opt max-size=10m --log-opt max-file=3 ...
```

**docker-compose**
```
version: '3.7'
services:
  app:
    image: ...
    logging:
      options:
        max-size: "10m"
        max-file: "3"
```

### Removal

Remove all stopped containers
```sh
docker ps --filter “status=exited” -q | xargs -r docker rm --force
```

Remove all the containers
```sh
$ docker ps -a -q | xargs -r docker rm -- force
```

## Disk usage

```sh
$ docker system df
TYPE                SIZE                RECLAIMABLE
Images              10.17GB             7.155GB (70%)
Containers          354B                354B (100%)
Local Volumes       1.105GB             151.1MB (13%)
Build Cache         3.409GB             3.409GB
```

## Images

### Create an image from a FS tarball

Creates one image from one tarball which is not an image "stricto sensu" (just a filesystem to import as an image). 

-> Opposite of `docker export`.

**NB** : To import one or many "real" images, use `docker load` instead. \
Recommanded reading : [Docker load vs import](https://medium.com/bb-tutorials-and-thoughts/docker-import-vs-load-91d418f0073c).

```shell
docker import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]
```

[Official `docker import` man](https://docs.docker.com/engine/reference/commandline/import/).

### Create an image from an image tarball

Creates one or more images from a tarball containing one or more images. 

-> Opposite of `docker save`.

**NB** : To create an image from a FS, use `docker import` instead. \
Recommanded reading : [Docker load vs import](https://medium.com/bb-tutorials-and-thoughts/docker-import-vs-load-91d418f0073c).

```shell
docker load < busybox.tar.gz
# or
docker load --input fedora.tar
```

[Official `docker load` man](https://docs.docker.com/engine/reference/commandline/load/).

### Removal

Remove dangling images
```sh
# méthode 1
$ docker images -f “dangling=true” -q | xargs -r docker rmi -f
# ou méthode 2 
$ docker image prune
```

### Save one or multiples images to a tarball

Useful to transfer one or multiples images between hosts. 

**NB** : To export a container FS to a tarball for image creation, use `docker export` instead. \
Recommanded reading : [Docker save vs export](https://www.baeldung.com/ops/docker-save-export).

```shell
# IMAGE can be FQN (eg. ghcr.io/xyz/my-application:1.2.3) or hash
docker save IMAGE [IMAGE...] > /path/to/file.tar
# or
docker save -o /path/to/file.tar IMAGE [IMAGE...]
```

[Official `docker save` man](https://docs.docker.com/engine/reference/commandline/save/).

## System "cleaning"
```sh
# Clean the Docker builder cache
$ DOCKER_BUILDKIT=1 docker builder prune --all --force
# Remove networks
$ docker network prune --force
# Remove all stopped containers, unused networks, dangling images and/or unused images, dangling volumes, builder cache.
$ docker system prune
```

## Volumes

### Removal

Remove dangling images
```sh
# méthode 1
$ docker volume ls -qf dangling=true | xargs -r docker volume rm
# ou méthode 2 
$ docker volume prune --force
```
