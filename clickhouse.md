# clickhouse

## install

```bash
# https://clickhouse.tech/docs/en/getting-started/install/

# add repo
sudo yum install yum-utils
sudo rpm --import https://repo.clickhouse.tech/CLICKHOUSE-KEY.GPG
sudo yum-config-manager --add-repo https://repo.clickhouse.tech/rpm/stable/x86_64

# install
sudo yum install clickhouse-server clickhouse-client

```

## config

```bash
# edit paths to config.xml
# change data path with /var/lib/clickhouse/ to /data/clickhouse/
# change log path /var/log/clickhouse-server/ to /data/log/clickhouse/
vim /etc/clickhouse-server/config.xml

# update permission
chown clickhouse:clickhouse /data/clickhouse/
chown clickhouse:clickhouse /data/log/clickhouse/
```

## start/stop

```bash
# service will be started with clickhouse:clickhouse
# see /etc/systemd/system/clickhouse-server.service

# start server single node
systemctl start clickhouse-server

# stop
systemctl stop clickhouse-server

# reload config
sudo systemctl reload clickhouse-server

# check status
systemctl status clickhouse-server

# check full systemctl log
journalctl -u clickhouse-server
systemctl -l status clickhouse-server
```

## start client

```bash
# client from local
clickhouse-client

# multiple line client
clickhouse-client -m
```

## http

```bash
curl 'http://<ip>:8123'
```

## compile

build 23.8 on ubuntu 22.04, official [doc](https://github.com/ClickHouse/ClickHouse/blob/23.8/docs/en/development/developer-instruction.md)

```bash
sudo apt-get update
sudo apt-get install git cmake ccache python3 ninja-build nasm yasm gawk lsb-release wget software-properties-common gnupg
sudo apt-get install build-essential
sudo apt-get install software-properties-common
sudo add-apt-repository -y ppa:ubuntu-toolchain-r/test
sudo apt-get update && sudo apt-get upgrade

# method 1:
# wget https://apt.llvm.org/llvm.sh
# chmod u+x llvm.sh
# sudo ./llvm.sh 16
# method 2:
sudo apt-get install clang-16
clang-16 --version

export CC=clang-16
export CXX=clang++-16
ln -sf /usr/bin/clang++-16 /usr/bin/c++
ln -sf /usr/bin/clang-16 /usr/bin/cc

# clone
git clone --recursive --shallow-submodules --branch 23.8 https://github.com/ClickHouse/ClickHouse.git
# or from custom repo
# git clone git://[]/ClickHouse.git

# if not behind proxy, run
# git config --global --unset https.proxy
cd ClickHouse
git submodule update --init
git submodule update --init --force

# build
cd ClickHouse
mkdir build
cmake -S . -B build
cmake --build build -j 16

# cp binary to deploy dir
mkdir /data/clickhouse/server -p
cp /data/ClickHouse/build/programs/clickhouse /data/clickhouse/server
cp /data/ClickHouse/programs/server/config.xml /data/clickhouse/server
cp /data/ClickHouse/programs/server/users.xml /data/clickhouse/server

# deploy
/data/clickhouse/server/clickhouse server --config /data/clickhouse/server/config.xml
/data/clickhouse/server/clickhouse client

# mysql client
mysqlsh -u default -h localhost -P 9004 --sql
```
