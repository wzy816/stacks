# emr

## system

```bash
# timezone
cat /etc/sysconfig/clock
ls /usr/share/zoneinfo
```

## aws

```bash
# show all cluster
aws emr list-clusters --active

# describe cluster
aws emr describe-cluster --cluster-id <id>
```

## error

### libm.so.6 link broken

```bash
# when run hadoop version
# Error: failed /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.201.b09-0.43.amzn1.x86_64/jre/lib/amd64/server/libjvm.so, because libm.so.6: symbol __strtof128_nan, version GLIBC_PRIVATE not defined in file libc.so.6 with link time reference

# find link broken
ll /lib64/libm.so.6

# fix link
ldconfig

# 2020-06-07 02:58:31,082 [dispatcher-event-loop-4] WARN  org.apache.spark.scheduler.cluster.YarnSchedulerBackend$YarnSchedulerEndpoint  - Requesting driver to remove executor 2 for reason Container from a bad node: container_e01_1590244610345_18020_02_000003 on host: ... Exit status: 1. Diagnostics: Exception from container-launch.
# Container id: container_e01_1590244610345_18020_02_000003
# Exit code: 1
# Stack trace: ExitCodeException exitCode=1:
# 	at org.apache.hadoop.util.Shell.runCommand(Shell.java:972)
# 	at org.apache.hadoop.util.Shell.run(Shell.java:869)
# 	at org.apache.hadoop.util.Shell$ShellCommandExecutor.execute(Shell.java:1170)
# 	at org.apache.hadoop.yarn.server.nodemanager.DefaultContainerExecutor.launchContainer(DefaultContainerExecutor.java:235)
# 	at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:299)
# 	at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:83)
# 	at java.util.concurrent.FutureTask.run(FutureTask.java:266)
# 	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
# 	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
# 原因：历史文件冲突，该节点下的历史 jar 包和文件导致，清理即可
stop hadoop-yarn-nodemanager
cd /mnt/yarn
rm -rf filecache/ usercache/
sudo start hadoop-yarn-nodemanager

```

## service

```bash
# list services
initctl list

# restart
sudo stop hadoop-yarn-resourcemanager
sudo start hadoop-yarn-resourcemanager
sudo status hadoop-yarn-resourcemanager
```
