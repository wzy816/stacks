# milvus

## standalone

```bash
mkdir /mnt/milvus/docker -p
cd milvus

wget https://github.com/milvus-io/milvus/releases/download/v2.3.3/milvus-standalone-docker-compose.yml -O docker-compose.yml

export DOCKER_VOLUME_DIRECTORY=/mnt/milvus/docker

sudo docker compose up -d
sudo docker compose ps
```
