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

## cli

```bash

# db
create database -db my_db
delete database -db my_db
use database -db my_db

# collection
create collection -c my_collection -f text_field:VARCHAR:10000 -f ts_field:INT64:ts_field -f vector_field:FLOAT_VECTOR:512 -f  -p text_field -s 16
create index

delete collection -c my_collection

show collection -c my_collection

# query
load collection -c my_collection

query
my_collection
text_field == "test"
text_field, ts_field, vector_field

release collection -c my_collection
```
