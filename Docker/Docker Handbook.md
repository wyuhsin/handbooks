# Docker Cheatsheet

## Compose

```bash
# create and run containers in the foreground
docker compose up

# create and run containers in the background
docker compose up -d

# list running containers
docker compose ps

# list all containers
docker compose ps --all
docker compose ps -a

# stop containers
docker compose stop

# stop and remove containers, networks created by up
docker compose down

# pull images defined in a compose file
docker compose pull

# create a container using docker compose
docker compose create

# build and start containers using docker compose
docker compose up --build
docker compose up -d --build

# scale services defined in a compose file
docker compose up -d --scale {{SERVICE_NAME}}={{SCALE_NUMBER}}

# view the logs of services defined in a compose file
docker compose logs
docker compose logs {{SERVICE_NAME}}

# execute a command in a running container defined in a compose file
docker compose exec {{SERVICE_NAME}} {{COMMAND}}

# run a one-off command in a service container defined in a compose file
docker compose run {{SERVICE_NAME}} {{COMMAND}}

# remove stpped containers created by up
docker compose rm

```

## Container

```bash
# list running containers
docker ps
docker container list
docker container ls

# list all containers
docker ps -all
docker ps -a
docker container list -all
docker container ls --all

# run a container from my-image:1.0
docker run my-image:1.0
docker container run my-image:1.0

# run a container from my-image:1.0 with the name www
docker run --name www my-image:1.0

# run a container from my-image:1.0 exposing internal port 3000 to external port 80
docker run --name www -p 80:3000 my-image:1.0

# stop a container by the name www
docker stop www
docker container stop www

# stop containers by the name www1 www2 ...
docker stop www1 www2
docker container stop www1 www2

# stop all the containers that match a keyword example
docker stop $(docker ps -a | grep "example" | awk '{print $1}')

# kill a container by the name www
docker kill www
docker container kill www

# kill containers by the name www1 www2 ...
docker kill www1 www2
docker container kill www1 www2

# remove containers by the name www1 www2 ...
docker rm www1 www2
docker container rm www1 www2

# remove all the containers that match a keyword example
docker rm $(docker ps -a | grep "example" | awk '{print $1}')

# runs id in www container
docker exec www id
docker container exec www id

# run an interactive bash shell on the container
docker exec -it {{CONTAINER}} bash
docker container exec -it {{CONTAINER}}} bash

# run a container in the background from my-image:1.0
docker run -d my-image:1.0
docker container run -d my-image:1.0

# attach to a running container named www
docker attach www
docker container attach www

# inspect a container named www1
docker inspect www
docker container inspect www

# list logs of a container named www1
docker logs www
docker container logs www

# start a stopped container named www
docker start www
docker container start www

# restart a running container named www
docker restart www
docker container restart www

# pause a running container named www
docker pause www
docker container pause www

# unpause a paused container named www
docker unpause www
docker container unpause www

# rename a container named www to web
docker rename www web
docker container rename www web

```

## Image

```bash
# build a image with the name my-image and tag 1.0
docker build -t my-image:1.0 .

# list local images
docker images ls

# delete local image by the name:tag my-image:1.0
docker image rm my-image:1.0

# build an image in the current directory with the name my-image
docker build -t my-image .

# pull image example from a registry
docker pull example
docker image pull example
docker pull example:1.0
docker image pull example:1.0

# push image my-image to repository myrepo under a registry
docker push myrepo/my-image:2.0
docker image push myrepo/my-image:2.0

# dangling image
docker image --filter "dangling=true"

```

## Network

```bash
# list Network
docker network ls

# create a network with the name my-network
docker network create my-network

# remove a network with the name my-network
docker network rm my-network

# inspect a network with the name my-network
docker network inspect my-network

```

## System

```bash
# remove all unused containers, networks, images(both dangling and unreferenced)
docker system prune

# remove all unused containers, networks, images(both dangling and unreferenced), and volumes.
docker system prune --volumes

# remove all unused containers, networks, all unused images, and volumes.
docker system prune --all --volumes

```

## Volume

```bash
# list volumes
docker volume ls

# create a volume
docker volume create {{VOLUME_NAME}}

# remove all unused local volumes
docker volume prune

# backup my_volume to my_backup.tar
docker run --rm --volume my_volume:/mount_bkp --volume $(pwd):/backup ubuntu tar cvf /backup/my_backup.tar /mount_bkp

# restore my_backup.tar to my_volume
docker run --rm --volume my_volume:/mount_bkp --volume $(pwd):/backup ubuntu tar xvf /backup/my_backup.tar -C /mount_bkp --strip 1

# dangling volume
docker volume --filter "dangling=true"

# remove all danling volume(docker volume prune)
docker volume rm $(docker volume -qf "dangling=true")

```