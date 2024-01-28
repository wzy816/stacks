# elk

## install

```bash
# in production env
sudo sysctl -w vm.max_map_count=262144

# elasticsearch data volumes permission
chmod 777 /data/elasticsearch/data/

# setup password
curl -XPUT -u elastic 'my_ip:9200/_xpack/security/user/elastic/_password'  -H "Content-Type: application/json" -d '{"password" : "my_password"}'
```

## ES REST

```bash
# get all indices
/_cat/indices?v

# get mapping
/<index>/mapping
/_all/_mapping
/_mapping
```

## Kibana

### Timelion

    .es(index=my_index,timefield=timestamp,metric=max:soc),
    .es(q= field:2,index=my_index,timefield=timestamp,metric=max:soc),
    .es(index=my_index,timefield=timestamp,metric=max:soc).label("soc").color(#ee1122)

## run

```bash
# elasticsearch
docker run -d \
    --name elasticsearch \
    -p 9200:9200 \
    -p 9300:9300 \
    --env ES_JAVA_OPTS="-Xmx256m -Xms256m" \
    --env discovery.type=zen \
    --env discovery.zen.ping.unicast.hosts=elasticsearch \
    --env xpack.security.enabled=false \
    -v /data/elasticsearch/data:/usr/share/elasticsearch/data \
    docker.elastic.co/elasticsearch/elasticsearch:6.4.2
```

## service

```bash
# elasticsearch
docker service create \
  --network isa \
  --replicas 1 \
  --name elasticsearch \
  -p 9200:9200 \
  -p 9300:9300 \
  --env ES_JAVA_OPTS="-Xmx256m -Xms256m" \
  --env discovery.type=zen \
  --env discovery.zen.ping.unicast.hosts=elasticsearch \
  --env xpack.security.enabled=false \
  --constraint node.hostname==<my_host> \
  --mount type=bind,source=/data/elasticsearch/data,destination=/usr/share/elasticsearch/data \
  docker.elastic.co/elasticsearch/elasticsearch:6.4.2

# logstash
docker service create \
  --network isa \
  --replicas 1 \
  --name logstash \
  -p 5000:5000 \
  -p 9600:9600 \
  --env LS_JAVA_OPTS="-Xmx256m -Xms256m" \
  --env elasticsearch.url=http://elasticsearch:9200 \
  --constraint node.hostname==<my_host> \
  docker.elastic.co/logstash/logstash:6.4.2

docker service update \
  --mount-add type=bind,source=/data/logstash/logstash.conf,destination=/usr/share/logstash/pipeline/logstash.conf \
  logstash

# kibana
docker service create \
  --network isa \
  --replicas 1 \
  --name kibana \
  -p 5601:5601 \
  --env elasticsearch.url=http://elasticsearch:9200 \
  --constraint node.hostname==<my_host> \
  docker.elastic.co/kibana/kibana:6.4.2

# logspout
docker service create \
  --network isa \
  --mode global \
  --restart-condition "on-failure" \
  --restart-delay 30s \
  --name logspout \
  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock \
  --env-add RAW_FORMAT="{{ toJSON .Data }}\n" \
  gliderlabs/logspout:latest \
  multiline+raw://<kibana_host>:5000
```

## docker-compose.yml

```yml
version: "2"
services:
  logstash:
    image: docker.elastic.co/logstash/logstash:6.4.2
    environment:
      LS_JAVA_OPTS: -Xmx256m -Xms256m
      elasticsearch.url: http://elasticsearch:9200
    stdin_open: true
    tty: true
    links:
      - elasticsearch:elasticsearch
    ports:
      - 5000:5000/tcp
      - 9600:9600/tcp
    labels:
      io.rancher.scheduler.affinity:host_label: elk=true
      logstash: "true"
      io.rancher.container.pull_image: always
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:6.4.2
    environment:
      ES_JAVA_OPTS: -Xms512m -Xmx512m
      cluster.name: docker-cluster
      discovery.type: zen
      discovery.zen.ping.unicast.hosts: elasticsearch
      xpack.security.enabled: "false"
    stdin_open: true
    volumes:
      - /data/elasticsearch/data:/usr/share/elasticsearch/data
    tty: true
    ports:
      - 9200:9200/tcp
      - 64497:9300/tcp
    labels:
      io.rancher.scheduler.affinity:host_label: elk=true
      elasticsearch: "true"
      io.rancher.container.pull_image: always
      io.rancher.scheduler.affinity:container_label_ne: elasticsearch=true
  kibana:
    image: docker.elastic.co/kibana/kibana:6.4.2
    environment:
      elasticsearch.url: http://elasticsearch:9200
    stdin_open: true
    tty: true
    links:
      - elasticsearch:elasticsearch
    ports:
      - 5601:5601/tcp
    labels:
      io.rancher.scheduler.affinity:host_label: elk=true
      io.rancher.container.pull_image: always
      kibana: ""
```

## rancher-compose.yml

```yml
version: "2"
services:
  logstash:
    scale: 1
    start_on_create: true
  elasticsearch:
    scale: 1
    start_on_create: true
  kibana:
    scale: 1
    start_on_create: true
```
