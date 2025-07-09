# postgresql

## install

```bash
sudo yum install postgresql-server
```

## psql

```bash
# login
psql -h <host> -U <username> -d <password>

# show tables
\dt

# select
select * from <table>;
```

## ubuntu

```bash
# install
sudo apt install postgresql

# change password
sudo -u postgres psql template1
ALTER USER postgres with encrypted password 'your_password';

# start
sudo systemctl restart postgresql

# check
sudo -u postgres psql -c "select * from pg_stat_replication;"

# stop
sudo systemctl stop postgresql

```
