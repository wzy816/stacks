# zookeeper

## mac

```bash
# install on mac
brew install zookeeper

# connect to a zk
zkCli -server <ip>
```

## install

0.  export ZOOKEEPER_HOME=/usr/lib/zookeeper/

1.  download zookeeper at /usr/lib/zookeeper/

2.  setup conf/zoo.cfg

```cfg
maxClientCnxns=0
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/var/lib/zookeeper
clientPort=2181
autopurge.purgeInterval=24

server.0=<ip>:2888:3888
server.1=<ip>:2888:3888
```

3.  create myid at dataDir

```bash
cd /var/lib/zookeeper
touch myid
cat 1 > myid
```

4.  start server

```bash
/usr/lib/zookeeper/bin/zkServer.sh start
```

5.  see status

```bash
/usr/lib/zookeeper/bin/zkCli.sh -server 127.0.0.1:2181
```

6.  check connection

```bash
/usr/lib/zookeeper/bin/zkCli.sh -server 127.0.0.1:2181
```

## docker-compose.yml

```yml
version: "2"
services:
  zoo3:
    image: zookeeper
    environment:
      ZOO_MY_ID: "3"
      ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=zoo2:2888:3888 server.3=0.0.0.0:2888:3888
    stdin_open: true
    tty: true
    ports:
      - 2181:2181/tcp
    labels:
      io.rancher.scheduler.affinity:host_label: zookeeper=true
      io.rancher.container.pull_image: always
      io.rancher.scheduler.affinity:container_label_ne: zookeeper=true
      zookeeper: "true"
  zoo2:
    image: zookeeper
    environment:
      ZOO_MY_ID: "2"
      ZOO_SERVERS: server.1=zoo1:2888:3888 server.2=0.0.0.0:2888:3888 server.3=zoo3:2888:3888
    stdin_open: true
    tty: true
    ports:
      - 2181:2181/tcp
    labels:
      io.rancher.scheduler.affinity:host_label: zookeeper=true
      io.rancher.container.pull_image: always
      io.rancher.scheduler.affinity:container_label_ne: zookeeper=true
      zookeeper: "true"
  zoo1:
    image: zookeeper
    environment:
      ZOO_MY_ID: "1"
      ZOO_SERVERS: server.1=0.0.0.0:2888:3888 server.2=zoo2:2888:3888 server.3=zoo3:2888:3888
    stdin_open: true
    tty: true
    ports:
      - 2181:2181/tcp
    labels:
      io.rancher.scheduler.affinity:host_label: zookeeper=true
      io.rancher.container.pull_image: always
      io.rancher.scheduler.affinity:container_label_ne: zookeeper=true
      zookeeper: "true"
```

## rancher-compose.yml

```yml
version: "2"
services:
  zoo3:
    scale: 1
    start_on_create: true
  zoo2:
    scale: 1
    start_on_create: true
  zoo1:
    scale: 1
    start_on_create: true
```
