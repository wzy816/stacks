# zeppelin

## using docker
```
docker run -d -p 8080:8080 --rm -v /data/zeppelin/logs:/logs -v /data/zeppelin/notebook:/notebook  --name zeppelin apache/zeppelin:0.9.0
```

## using binary
```bash
# download
wget https://mirror-hk.koddos.net/apache/zeppelin/zeppelin-0.9.0-preview2/zeppelin-0.9.0-preview2-bin-all.tgz

# unzip
tar xzf ./zeppelin-0.9.0-preview2-bin-all.tgz

# start
bin/zeppelin-daemon.sh start

# restart
bin/zeppelin-daemon.sh restart
```
## config 

1. create conf/zeppelin-site.xml from template
```xml
<!-- add -->
<name>zeppelin.anonymous.allowed</name>
<value>false</value>
<description>Anonymous user allowed by default</description>
</property>

<!-- update -->
<name>zeppelin.interpreter.dep.mvnRepo</name>
<value>http://insecure.repo1.maven.org/maven2/</value>
</property>

```

2. create conf/shiro.ini from template add, ex.
```
[users]
user = pwd, admin

[roles]
admin = *
```