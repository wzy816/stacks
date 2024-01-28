# yarn

## resourcemanager

```bash
sudo stop hadoop-yarn-resourcemanager
sudo start hadoop-yarn-resourcemanager
```

## nodemanager

```bash
sudo stop hadoop-yarn-nodemanager
sudo start hadoop-yarn-nodemanager
```

## timeline server

```bash
sudo stop hadoop-yarn-timelineserver
sudo start hadoop-yarn-timelineserver
```

## web proxy

yarn web api, use default port 8088 as rm, to avoid web attack use proxy

```bash
sudo stop hadoop-yarn-proxyserver
sudo start hadoop-yarn-proxyserver
```

## yarn-site.xml

```xml
<!-- rm memory -->
<property>
  <name>yarn.scheduler.minimum-allocation-mb</name>
  <value>128</value>
</property>
<property>
  <name>yarn.scheduler.maximum-allocation-mb</name>
  <value>28672</value>
</property>
<property>
  <name>yarn.nodemanager.vmem-pmem-ratio</name>
  <value>200</value>
</property>

<!-- nm memory -->
<property>
  <name>yarn.nodemanager.resource.memory-mb</name>
  <value>28672</value>
</property>

<!-- rm recovery -->
<property>
  <name>yarn.resourcemanager.recovery.enabled</name>
  <value>true</value>
</property>

<property>
  <name>yarn.resourcemanager.store.class</name>
  <value>org.apache.hadoop.yarn.server.resourcemanager.recovery.ZKRMStateStore</value>
</property>

<property>
  <name>yarn.resourcemanager.zk-address</name>
  <value>ip:2181</value>
</property>
```
