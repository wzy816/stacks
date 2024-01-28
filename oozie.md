# oozie

## basic

```bash
# check version
oozie version

# start/stop
sudo stop oozie
sudo start oozie

# restart
sudo restart oozie

# update sharelib
sudo -u oozie oozie admin -sharelibupdate
# list sharelib
oozie admin -oozie http://$(hostname -f):11000/oozie -shareliblist

# kill job
oozie job -oozie http://<ip>:11000/oozie -auth KERBEROS -kill <job_id>
```

## error

```bash
# web ui org.apache.jasper.JasperException: PWC6033: Error in Javac compilation for JSP
cd /opt/cloudera/parcels/CDH/lib/oozie/embedded-oozie-server/dependency/
ls -ltr
unlink ./javax.servlet.jsp-2.3.2.jar
# restart oozie

# when web ui is disabled
# find libext, should be /var/lib/oozie/
echo "$OOZIE_HOME/libext"
# download
cd wget http://archive.cloudera.com/gplextras/misc/ext-2.2.zip
unzip ext-2.2.zip
# check
ls -ltr /var/lib/oozie/ext-2.2/
# restart oozie in cdh manager
```
