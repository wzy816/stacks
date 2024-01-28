# hive

## install

()[https://docs.cloudera.com/documentation/enterprise/5-6-x/topics/cdh_ig_hive_installation.html]

## metastore

```bash
# avoid plain text password create jerk
hadoop credential create javax.jdo.option.ConnectionPassword -provider jceks://file/usr/lib/hive/conf/hive.jceks
# then remove javax.jdo.option.ConnectionPassword in xml

# start metastore
sudo service hive-metastore start
hive --service metastore
```

### hiveserver2

```bash
# start hiveserver2
sudo service hive-server2 start
$HIVE_HOME/bin/hiveserver2
$HIVE_HOME/bin/hive --service hiveserver2

# stop hiveserver2
sudo service hive-server2 stop
```

## beeline

```bash
# start cli
/usr/lib/hive/bin/beeline

# connect to db
!connect jdbc:hive2://localhost:10000 <javax.jdo.option.ConnectionUserName> <javax.jdo.option.ConnectionPassword>  org.apache.hive.jdbc.HiveDriver

# verify hiveserver2 is running
show tables;
```

## shell

```bash
# enter shell
hive

# single line
hive –e “show tables;”

# show all partitions
show partitions my_db.my_table

# show partition location
SHOW TABLE EXTENDED FROM my_db LIKE 'my_table' PARTITION(datetime='latest')

```

## security

don't use default Hive Authorization

### Storage Based Authorization via metastore

control hcatalog access (MapReduce，impala，pig，Spark SQL，hive Command line)

via control user's hdfs permission
()[https://cwiki.apache.org/confluence/display/Hive/Storage+Based+Authorization+in+the+Metastore+Server]

()[https://cwiki.apache.org/confluence/display/Hive/HCatalog+Authorization]

```bash
# grant user test
hadoop fs -setfacl -m user:test:rwx /user/hive/warehouse
```

## udf

https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF

## hive-site.xml

```xml
<!-- fs -->
<property>
  <name>fs.defaultFS</name>
  <value>hdfs://ip:8020</value>
</property>

<!-- dir -->
<property>
  <name>hive.exec.scratchdir</name>
  <value>/user/hive/tmp</value>
</property>
<property>
  <name>hive.metastore.warehouse.dir</name>
  <value>/user/hive/warehouse</value>
</property>
<property>
  <name>hive.querylog.location</name>
  <value>/user/hive/log</value>
</property>

<!-- exec -->
<property>
  <name>hive.execution.engine</name>
  <value>spark</value>
</property>
<property>
   <name>hive.exec.parallel</name>
   <value>true</value>
   <description></description>
</property>
<property>
   <name>hive.exec.compress.intermediate</name>
   <value>true</value>
   <description>intermediate files btw hive mr jobs are compressed</description>
</property>

<!-- enable lock -->
<property>
  <name>hive.zookeeper.quorum</name>
  <value>ip</value>
</property>

<property>
  <name>hive.support.concurrency</name>
  <value>true</value>
</property>

<!-- mapred -->
<property>
   <name>hive.mapred.mode</name>
   <value>strict</value>
   <description></description>
</property>
```

## hivemetastore-site.xml

```xml
<!-- mysql -->
<property>
  <name>hive.metastore.uris</name>
  <value>thrift://ip:9083</value>
  <description>JDBC connect string for a JDBC metastore</description>
</property>

<property>
  <name>javax.jdo.option.ConnectionURL</name>
  <value>jdbc:mysql://ip:3306/hive?createDatabaseIfNotExist=true</value>
  <description></description>
</property>

<property>
  <name>javax.jdo.option.ConnectionDriverName</name>
  <value>org.mariadb.jdbc.Driver</value>
  <description></description>
</property>

<property>
  <name>javax.jdo.option.ConnectionUserName</name>
  <value>hive</value>
  <description>username to use against metastore database</description>
</property>

<property>
  <name>hadoop.security.credential.provider.path</name>
  <value>jceks://file/usr/lib/hive/conf/hive.jceks</value>
</property>
<!-- <property>
  <name>javax.jdo.option.ConnectionPassword</name>
  <value></value>
  <description>password to use against metastore database</description>
</property> -->

<!-- enable storage based authorization -->
<property>
  <name>hive.metastore.pre.event.listeners</name>
  <value>org.apache.hadoop.hive.ql.security.authorization.AuthorizationPreEventListener</value>
</property>

<property>
  <name>hive.security.metastore.authorization.manager</name>
  <value>org.apache.hadoop.hive.ql.security.authorization.StorageBasedAuthorizationProvider</value>
</property>

<property>
  <name>hive.security.metastore.authenticator.manager</name>
  <value>org.apache.hadoop.hive.ql.security.HadoopDefaultMetastoreAuthenticator</value>
</property>
```

## hiveserver2-site.xml

```xml
<!-- thrift -->
<property>
   <name>hive.server2.thrift.port</name>
   <value>10000</value>
</property>

<property>
   <name>hive.server2.thrift.http.port</name>
   <value>10001</value>
</property>

<!-- enabled SQL Standards Based Authorization -->
<property>
   <name>hive.server2.enable.doAs</name>
   <value>true</value>
</property>

<property>
  <name>hive.security.authorization.enabled</name>
  <value>true</value>
</property>

<property>
  <name>hive.security.authorization.createtable.owner.grants</name>
  <value>ALL</value>
</property>

<property>
  <name>hive.users.in.admin.role</name>
  <value>hdfs</value>
</property>
```

### using custom

```xml
<property>
  <name>hive.server2.authentication</name>
  <value>CUSTOM</value>
</property>

<property>
    <name>hive.server2.custom.authentication.class</name>
    <value>org.apache.hadoop.hive.contrib.auth.CustomPasswdAuthenticator</value>
</property>
```

### using kerboros

```xml
<property>
  <name>hive.server2.authentication</name>
  <value>KERBEROS</value>
</property>

<property>
  <name>hive.server2.authentication.kerberos.principal</name>
  <value>hive/_HOST@YOUR-REALM.COM</value>
</property>

<property>
  <name>hive.server2.authentication.kerberos.keytab</name>
  <value>/etc/hive/conf/hive.keytab</value>
</property>
```

### using LDAP

```xml
<property>
  <name>hive.server2.authentication</name>
  <value>ldap[s]://host:port</value>
</property>

<property>
  <name>hive.server2.authentication.ldap.url</name>
  <value>LDAP_URLL</value>
</property>

<property>
  <name>hive.server2.authentication.ldap.Domain</name>
  <value>AD_DOMAIN_ADDRESS</value>
</property>
```
