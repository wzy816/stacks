# hbase

## docker-compose.yml

```yml
version: "2"
services:
  hbase-master:
    image: bde2020/hbase-master:1.0.0-hbase1.2.6
    environment:
      HBASE_CONF_hbase_rootdir: hdfs://namenode:8020/hbase
      HBASE_CONF_hbase_cluster_distributed: "true"
      HBASE_CONF_hbase_zookeeper_quorum: zoo1,zoo2,zoo3
      HBASE_CONF_hbase_master: hbase-master:60000
      HBASE_CONF_hbase_master_hostname: hbase-master
      HBASE_CONF_hbase_master_port: "60000"
      HBASE_CONF_hbase_master_info_port: "60010"
      HBASE_CONF_hbase_regionserver_port: "60020"
      HBASE_CONF_hbase_regionserver_info_port: "60030"
      HBASE_MANAGES_ZK: "false"
    stdin_open: true
    external_links:
      - zookeeper/zoo1:zoo1
      - zookeeper/zoo2:zoo2
      - zookeeper/zoo3:zoo3
      - hadoop/datanode:datanode
      - hadoop/namenode:namenode
    tty: true
    links:
      - hbase-regionserver:hbase-regionserver
    ports:
      - 60000:60000/tcp
      - 60010:60010/tcp
    labels:
      io.rancher.scheduler.affinity:host_label: hbase=true
      hbase-master: "true"
      io.rancher.container.pull_image: always
  hbase-regionserver:
    image: bde2020/hbase-regionserver:1.0.0-hbase1.2.6
    environment:
      HBASE_CONF_hbase_rootdir: hdfs://namenode:8020/hbase
      HBASE_CONF_hbase_cluster_distributed: "true"
      HBASE_CONF_hbase_zookeeper_quorum: zoo1,zoo2,zoo3
      HBASE_CONF_hbase_master: hbase-master:60000
      HBASE_CONF_hbase_master_hostname: hbase-master
      HBASE_CONF_hbase_master_port: "60000"
      HBASE_CONF_hbase_master_info_port: "60010"
      HBASE_CONF_hbase_regionserver_port: "60020"
      HBASE_CONF_hbase_regionserver_info_port: "60030"
      HBASE_MANAGES_ZK: "false"
    stdin_open: true
    external_links:
      - zookeeper/zoo1:zoo1
      - zookeeper/zoo2:zoo2
      - zookeeper/zoo3:zoo3
      - hadoop/datanode:datanode
      - hadoop/namenode:namenode
    tty: true
    links:
      - hbase-master:hbase-master
    ports:
      - 60020:60020/tcp
      - 60030:60030/tcp
    labels:
      io.rancher.scheduler.affinity:host_label: hbase=true
      hbase-regionserver: "true"
      io.rancher.container.pull_image: always
      io.rancher.scheduler.affinity:container_label_ne: hbase-regionserver=true
  hbase-thrift:
    image: bde2020/hbase-base:1.0.0-hbase1.2.6
    environment:
      HBASE_CONF_hbase_rootdir: hdfs://namenode:8020/hbase
      HBASE_CONF_hbase_cluster_distributed: "true"
      HBASE_CONF_hbase_zookeeper_quorum: zoo1,zoo2,zoo3
      HBASE_CONF_hbase_master: hbase-master:60000
      HBASE_CONF_hbase_master_hostname: hbase-master
      HBASE_CONF_hbase_master_port: "60000"
      HBASE_CONF_hbase_master_info_port: "60010"
      HBASE_CONF_hbase_regionserver_port: "60020"
      HBASE_CONF_hbase_regionserver_info_port: "60030"
      HBASE_MANAGES_ZK: "false"
    stdin_open: true
    external_links:
      - zookeeper/zoo1:zoo1
      - zookeeper/zoo2:zoo2
      - zookeeper/zoo3:zoo3
      - hadoop/datanode:datanode
      - hadoop/namenode:namenode
    tty: true
    links:
      - hbase-regionserver:hbase-regionserver
    ports:
      - 9090:9090/tcp
      - 9095:9095/tcp
    command:
      - /opt/hbase-1.2.6/bin/hbase
      - thrift
      - start
    labels:
      io.rancher.scheduler.affinity:host_label: hbase=true
      hbase-thrift: "true"
      io.rancher.container.pull_image: always
      io.rancher.scheduler.affinity:container_label: hbase-master=true
```

## rancher-compose.yml

```yml
version: "2"
services:
  hbase-master:
    scale: 1
    start_on_create: true
  hbase-regionserver:
    scale: 3
    start_on_create: true
  hbase-thrift:
    scale: 1
    start_on_create: true
```
