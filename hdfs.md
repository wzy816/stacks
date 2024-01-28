# FS Shell

## install

```bash
# install on mac
brew search hadoop

# install on linux
wget http://apache.claz.org/hadoop/common/hadoop-2.8.5/hadoop-2.8.5.tar.gz
tar -xvf hadoop-2.8.5.tar.gz
mv hadoop-2.8.5 /usr/local/hadoop

# find java path
readlink -f /usr/bin/java | sed "s:bin/java::"

# set java_home
# in ~/.bashrc, add
export JAVA_HOME=$(readlink -f /usr/bin/java | sed "s:bin/java::")
export HADOOP_CONF_DIR='/etc/hadoop/conf'
export HADOOP_HOME='/usr/local/hadoop'
export HADOOP_CLASSPATH=$(find $HADOOP_HOME -name '*.jar' | xargs echo | tr ' ' ':')

# check java_home
echo $JAVA_HOME
```

## basic

```bash
# check version
hadoop version

# checksum
hadoop fs -checksum <URI>

# show free space
hadoop fs -df <path>

# count
hadoop fs -count <path>>
```

## file & directory

```bash
# list files
hadoop fs -ls <URI>

# list s3
/usr/local/hadoop/bin/hadoop fs \
  -Dfs.s3a.access.key=<access_key> \
  -Dfs.s3a.secret.key=<secret_key> \
  -Dfs.s3a.impl=org.apache.hadoop.fs.s3a.S3AFileSystem \
  -Dfs.s3a.endpoint=s3.<region>.amazonaws.com.cn \
  -ls s3://<URI>

# put file
hadoop fs -put <localsrc> <dst>

# rm file
hadoop fs -rm <URI>

# rm directory
hadoop fs -rm -r <path>

# create dir
hadoop fs -mkpath -p <path>

# find file name that match a pattern
hadoop fs -find <path> -name <pattern> -print

# add acl
hdfs dfs -setfacl -R -m user:group:rwx hdfs://path/
```

## snapshot

```bash
# Admin ops require superuser privilege
# allow
hdfs dfsadmin -allowSnapshot <path>

# disallow
hdfs dfsadmin -disallowSnapshot hdfs://<ip>/temp/

# User ops
# create
hdfs dfs -createSnapshot <path> [<snapshotName>]

# delete
hdfs dfs -deleteSnapshot <path> [<snapshotName>]
```

## core-site.xml

```bash
# update
vim /etc/hadoop/conf/core-site.xml

# apply change
su - hdfs
hdfs dfsadmin -refreshNodes
```

## docker-compose.yml

```yml
version: "2"
services:
  datanode:
    image: uhopper/hadoop-datanode
    environment:
      CLUSTER_NAME: hadoop
      CORE_CONF_fs_defaultFS: hdfs://10.132.144.39:8020
      HDFS_CONF_dfs_namenode_datanode_registration_ip___hostname___check: "false"
    stdin_open: true
    network_mode: host
    volumes:
      - /data/hadoop/datanode:/hadoop/dfs/data
    tty: true
    links:
      - namenode:namenode
    ports:
      - 50010:50010/tcp
    labels:
      io.rancher.scheduler.affinity:host_label: hadoop-datanode=true
      io.rancher.container.hostname_override: container_name
      io.rancher.container.pull_image: always
      io.rancher.scheduler.affinity:container_label_ne: hadoop-datanode=true
      hadoop-datanode: "true"
  namenode:
    image: uhopper/hadoop-namenode
    hostname: namenode
    environment:
      CLUSTER_NAME: hadoop
      CORE_CONF_fs_defaultFS: hdfs://10.132.144.39:8020
      HDFS_CONF_dfs_namenode_datanode_registration_ip___hostname___check: "false"
    stdin_open: true
    network_mode: host
    volumes:
      - /data/hadoop/namenode:/hadoop/dfs/name
    tty: true
    ports:
      - 50070:50070/tcp
      - 8020:8020/tcp
    labels:
      hadoop-namenode: "true"
      io.rancher.scheduler.affinity:host_label: hadoop-namenode=true
      io.rancher.container.pull_image: always
```

## rancher-compose.yml

```yml
version: "2"
services:
  datanode:
    scale: 3
    start_on_create: true
  namenode:
    scale: 1
    start_on_create: true
```
