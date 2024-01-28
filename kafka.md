# kafka

## basic

```bash
# install command line tool
brew install kafka

# linux
useradd kafka -m
passwd kafka
wget https://downloads.apache.org/kafka/2.7.0/kafka_2.12-2.7.0.tgz
tar -xvzf kafka_2.12-2.7.0.tgz
# add kafka_2.12-2.7.0/bin to PATH
```

## SSL

```bash
kafka-topics.sh --bootstrap-server <ip:port> --list --command-config /path/to/config.properties
```

### config.properties

```bash
java.security.auth.login.config=/path/to/kafka_client_jaas.conf
ssl.truststore.location=/path/to/truststore.jks
security.protocol=SSL
```

### kafka_client_jaas.conf

```
KafkaClient {
  org.apache.kafka.common.security.plain.PlainLoginModule required
  username="<username>"
  password="<password>";
};
```

## topic

```bash
# create topic
kafka-topics.sh --create --bootstrap-server <ip:port> --replication-factor 3 --partitions 1 --topic <topic>

# topic list
kafka-topics.sh --bootstrap-server <ip:port> --list

# describe topic
kafka-topics.sh --describe --bootstrap-server <ip:port> --topic <topic>

# get topic offset in all partitions
kafka-run-class.sh kafka.tools.GetOffsetShell --bootstrap-server <ip:port> --topic <topic>

# increase topic partitions
kafka-topics.sh --bootstrap-server <ip:port> --topic <topic> --alter --partitions 10
 
# delete topic
kafka-topics.sh --delete --bootstrap-server <ip:port> --topic <topic>
```

## consumer

```bash
# consume message
kafka-console-consumer.sh --bootstrap-server <ip:port> --from-beginning --topic <topic> --consumer.config <path_to_jaas> --group <consumer_group> --property print.key=true --property print.value=true

# consume transaction state message with formatter
kafka-console-consumer.sh --bootstrap-server <ip:port> --from-beginning --topic <topic>  --group <consumer_group> --formatter "kafka.coordinator.transaction.TransactionLog\$TransactionLogMessageFormatter"  --property print.key=true --property print.value=true

# describe group
kafka-consumer-groups.sh --all-groups --describe  --bootstrap-server <ip:port>

# reset group offset
kafka-consumer-groups.sh  --bootstrap-server <ip:port> --group <consumer_group> --reset-offsets --to-earliest --all-topics --execute
```

## docker-compose.yml

```yml
version: "2"
services:
  kafka:
    image: wurstmeister/kafka
    environment:
      KAFKA_ZOOKEEPER_CONNECT: zoo1:2181,zoo2:2181,zoo3:2181
      KAFKA_AUTO_CREATE_TOPICS_ENABLE: "false"
      KAFKA_LISTENERS: PLAINTEXT://:9092
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://_{HOSTNAME_COMMAND}:9092
      HOSTNAME_COMMAND: "docker info | grep ^Name: | cut -d' ' -f 2"
    stdin_open: true
    external_links:
      - zookeeper/zoo2:zoo2
      - zookeeper/zoo3:zoo3
      - zookeeper/zoo1:zoo1
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    tty: true
    ports:
      - 9092:9092/tcp
    labels:
      io.rancher.container.pull_image: always
      io.rancher.scheduler.affinity:host_label: kafka=true
```

## rancher-compose.yml

```yml
version: "2"
services:
  kafka:
    scale: 3
    start_on_create: true
```
