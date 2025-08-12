# Docker

## Basic

```bash
# install on centos
# https://docs.docker.com/install/linux/docker-ce/centos/#install-docker-ce

sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce-18.03.0.ce

# modifier image dir
touch /etc/docker/daemon.json

# add following lines assuming /data is sufficient
{
    "data-root": "/data/docker"
}

# move images
mv /var/lib/docker/* /data/docker/ -f

# start
sudo systemctl start docker

# stop
sudo systemctl stop docker

# restart
sudo systemctl daemon-reload && sudo systemctl restart docker

# login
docker login <registry> -u <username> -p <password>

# pull image
docker pull <image>

# check port
docker port <container_name>
```

## container

```bash
# show containers
docker container ls -a

# run image
docker run -d --name <container_name> --restart=always -v <server_dir>:<container_dir>  -p <server_port>:<container_port> <image>

# run image iteractive
docker run -it <image_name> sh

# remove container
docker container stop <container_name> && docker container rm <container_name>

# remove stopped containers
docker container prune

# remove
docker container rm <container_id>

# check ip
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container_name_or_id>

# inspect
docker container inspect <container_name>
```

## image

```bash
# save image to file
docker save -o [ouput/file/path] golang:1.13.3

# load image from file
docker load -i [path/to/image/file]
```

## logs

```bash

docker logs <container_name>

# show log strea
docker logs -f <container_name>

docker logs <container_name> --tail 100
```

## swarm

### init

```bash
# prepare
# 1. install docker
# 2. check ip
# 3. check port 2377, 7946, 4789

# init a swarm
docker swarm init --advertise-addr <manager_ip>

# show how to join
docker swarm join-token worker
docker swarm join-token manager

# join a swarm as a worker
docker swarm join \
  --token <token> \
  <manager_ip>:2377

# check swarm state
docker info

# remove from swarm, at leave node
docker swarm leave
```

### node

```bash
# check node
docker node ls

# drain a node to prevent it from receiving tasks
docker node update --availability drain <node_name>

# inspect a node
docker node inspect --pretty <node_name>

# add label
docker node update --label-add <label_key>=<label_value> <node_name>
```

### service

```bash
# create service
docker service create \
  --replicas 1 \
  --env MYVAR=myvalue \
  --name <service_name> \
  --network <network_name> \
  --publish published=<HOST_PORT>,target=<CONTAINER_PORT> \
  --constraint node.labels.<label_key>==<label_value> \
  --constraint node.hostname==<host_name> \
  --mount type=bind,source=/path/on/host,destination=/path/in/container \
  # --mount type=bind,source=/data/elasticsearch/data,destination=/usr/share/elasticsearch/data \
  <image> \
  <command>

# show service
docker service ls

# inspect service
docker service inspect --pretty <service_name>
docker service inspect --format="{{json .Endpoint.Spec.Ports}}" <service_name>

# check nodes that are running the service
docker service ps <service_name>

# scale a service
docker service scale <service_name>=<number>

# delete a service
docker service rm <service_name>

# update a service
docker service update \
  # update image
  --image <new_image> \
  # env
  --env-add MYVAR=myvalue \
  # update port
  --publish-add published=<PUBLISHED-PORT>,target=<CONTAINER-PORT> \
  --publish-add published=<PUBLISHED-PORT>,target=<CONTAINER-PORT>,protocol=udp \
  -p <PUBLISHED-PORT>:<CONTAINER-PORT> \
  -p <PUBLISHED-PORT>:<CONTAINER-PORT>/udp \
  # mount
  --mount-add type=volume,source=other-volume,target=/somewhere-else \
  --mount-rm /somewhere \
  #
  <service_name>

# restart a service
docker service update --force <id>

# show log
docker service logs <service>
```

### network

#### basic

```bash
# show network
docker network ls
```

#### overlay

```bash
# create network
docker network create --driver overlay <network_name>

# attach service when create
docker service create \
  --network <network_name> \
  --name my-web \
  nginx

# attach service by updating
docker service update \
  --network-add <network_name> \
  <service_name>

# detach
docker service update \
  --network-rm <network_name> \
  <service_name>

# change network
docker service update \
  --network-add <new_network_name> \
  --network-rm <old_network_name> \
  <service_name>
```

### Monitor

```bash
# show usage
docker system df

# show status
docker stats

# show containers
docker ps -a
```

### clean

```bash
# prune
docker image prune
docker container prune
docker volume prune
docker network prune

# remove all containers
docker rm -vf $(docker ps -aq)

# remove all images
docker rmi -f $(docker images -aq)
```

## build.sh

```bash
#!/bin/bash
PUSH_REGISTRY=docker-registry.com
PULL_REGISTRY=docker.com
GIT_COMMIT_ID=`git rev-parse HEAD`
PROJECT=my-project
IMAGE_TAG=${PUSH_REGISTRY}/${PROJECT}:${GIT_COMMIT_ID}

docker build -t ${IMAGE_TAG} .
docker push ${IMAGE_TAG}
```

## Dockerfile

### node

```Dockerfile
FROM node:8.10

RUN mkdir -p /my-group/my-project

COPY . /my-group/my-project

WORKDIR /my-group/my-project

RUN npm install --registry=https://registry.npm.taobao.org

EXPOSE 3000

CMD [ "npm", "start" ]
```

## daemon.json

```json
{
  "data-root": "/data/docker",
  "log-driver": "gelf",
  "log-opts": {
    "gelf-address": "udp://ip:port"
  },
  "metrics-addr": "127.0.0.1:9323",
  "experimental": true
}
```

## add daocloud

```bash
 curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://f1361db2.m.daocloud.io

# IMPORTANT! this will exit running containers
sudo systemctl restart docker

# start exited container
docker start $(docker ps -a -q --filter "status=exited")
```

## exec

```bash
# show env
docker exec <container> env
```
