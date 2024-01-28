# influxdb

## install

```bash
# download
wget https://dl.influxdata.com/influxdb/releases/influxdb-1.7.9.x86_64.rpm

# install
sudo yum localinstall influxdb-1.7.9.x86_64.rpm

# config
vim /etc/influxdb/influxdb.conf

# change permission /var/lib/influxdb or custom folder
chown -R influxdb:influxdb /data/influxdb

# start
sudo systemctl start influxdb

# restart
sudo systemctl restart influxdb

# show status
sudo systemctl status influxdb
```

## log
```bash
# show log
sudo journalctl -u influxdb.service

# or
tail /var/log/messages
```

## cli
```bash
# launch
influx

# launch with auth
influx -username admin -password '<password>'

# execute
influx -execute 'SHOW DATABASES'

```

## influxQL

### user
```sql

-- create
CREATE USER admin WITH PASSWORD '<password>' WITH ALL PRIVILEGES

--  set pwd
SET PASSWORD FOR <username> = '<password>'

-- create and grant
CREATE USER test with PASSWORD 'test'
GRANT READ on mydb to test

-- show grant
SHOW GRANTS FOR test

-- revoke
REVOKE ALL ON mydb FROM test

```
### database
```sql
-- creaet
CREATE DATABASE mydb

-- use
use mydb

-- show
SHOW DATABASES
```

### query
```sql
SELECT * FROM <measurement> LIMIT 10

DELETE FROM <measurement>
```

### tag
```sql
-- show all tags
SHOW TAG VALUES ON "<my_db>" FROM "<my_measurement>" WITH KEY in (<my_tag>)

SHOW TAG KEYS
```

### series
```bash
# count total number of series
influx -username admin -password '<password>' -database '<db>' -execute 'SHOW SERIES' | grep -v "name,_id,host" | wc -l
```

```sql
-- count cardinality
SHOW SERIES exact cardinality
```

### measurements
```sql
SHOW measurements
```

### field
```sql
SHOW FIELD KEYS on <my_db>
```
