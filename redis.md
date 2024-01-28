# redis

## commands

```bash
# test connection
PING

# set key
SET runoobkey redis

# del key
DEL runoobkey

# get max number of client
config get maxclients
```

## .conf

```text
# 需要不同服务器的节点连通，就不能设置为 127.0.0.1）
bind [ip]
# 需要不同服务器的节点连通，这个就要设置为 no
protected-mode no
port 7001
tcp-backlog 511
timeout 0
tcp-keepalive 300
# 设置后台运行 redis
daemonize yes
supervised no
pidfile /var/run/redis_7001.pid
loglevel notice
logfile /var/log/redis/redis_7001.log
databases 16
always-show-logo yes
save 900 1
save 300 10
save 60 10000
stop-writes-on-bgsave-error yes
rdbcompression yes
rdbchecksum yes
dbfilename dump_7000.rdb
dir ./
slave-serve-stale-data yes
slave-read-only yes
repl-diskless-sync no
repl-diskless-sync-delay 5
repl-disable-tcp-nodelay no
slave-priority 100
lazyfree-lazy-eviction no
lazyfree-lazy-expire no
lazyfree-lazy-server-del no
slave-lazy-flush no
appendonly yes
appendfilename "appendonly_7001.aof"
appendfsync everysec
no-appendfsync-on-rewrite no
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
aof-load-truncated yes
aof-use-rdb-preamble no
lua-time-limit 5000
cluster-enabled yes
cluster-config-file nodes-7001.conf
cluster-node-timeout 5000
slowlog-log-slower-than 10000
slowlog-max-len 128
latency-monitor-threshold 0
notify-keyspace-events ""
hash-max-ziplist-entries 512
hash-max-ziplist-value 64
list-max-ziplist-size -2
list-compress-depth 0
set-max-intset-entries 512
zset-max-ziplist-entries 128
zset-max-ziplist-value 64
hll-sparse-max-bytes 3000
activerehashing yes
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit slave 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60
hz 10
aof-rewrite-incremental-fsync yes
```

## redis-cli

```bash
# download src
wget http://download.redis.io/releases/redis-6.0.7.tar.gz
tar xzf redis-6.0.7.tar.gz
cd redis-6.0.7
make

# use
src/redis-cli -h <host> -p <port> -a "mypass"
```
