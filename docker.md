## Disk usage information

```sh
$ docker system df
TYPE                SIZE                RECLAIMABLE
Images              10.17GB             7.155GB (70%)
Containers          354B                354B (100%)
Local Volumes       1.105GB             151.1MB (13%)
Build Cache         3.409GB             3.409GB
```

## Remove containers

Remove all stopped containers
```sh
docker ps --filter “status=exited” -q | xargs -r docker rm --force
```

Remove all the containers
```sh
$ docker ps -a -q | xargs -r docker rm — force
```

## Remove images

Romve dangling images
```sh
# méthode 1
$ docker images -f “dangling=true” -q | xargs -r docker rmi -f
# ou méthode 2 
$ docker image prune
```


## Remove volumes

Romve dangling images
```sh
# méthode 1
$ docker volume ls -qf dangling=true | xargs -r docker volume rm
# ou méthode 2 
$ docker volume prune --force
```

## Autres
```sh
# Clean the Docker builder cache
$ DOCKER_BUILDKIT=1 docker builder prune --all --force
# Remove networks
$ docker network prune --force
# Remove all stopped containers, unused networks, dangling images and/or unused images, dangling volumes, builder cache.
$ docker system prune
```
