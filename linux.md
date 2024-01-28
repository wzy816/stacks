# linux

## basic

```bash
# check disk
df -h

# check mounted
df -HT

# check dir size
du -h my_dir
du -h --max-depth=1 | sort -h

# count files
ls -1 | wc -l

# check port
netstat -tulpn

# check cpu
cat /proc/cpuinfo | grep processor
lscpu

# check memory
cat /proc/meminfo
#  show in gb
awk '{ printf "%.2f", $2/1024/1024 ; exit}' /proc/meminfo

# check kernel
uname -a

# check ip
hostname -i

# show ip
ip addr show

# show filesystem hierachy
man hier

# list of all available encodings
iconv --list

# find file
find / -name <file_name_prefix>*.jar

# find by pattern and print in one line separated with space
find $PWD/Astro* | tr '\n' ' '

# find large files
find . -type f -size +100M

# process
ps aux --sort=-pcpu | head -n 10
# kill process
kill -9 <PID>

#  show all env
printenv | more

# check system version
cat /etc/os-release

# check domain
ping <domain>

# show network status
ifconfig -a

# open network interface
vi /etc/netplan
netplan apply

# check current user
who

# list wildcard
ls f*
ls file?

# show one-line description
whatis who

# show manual
man cp

# match name in manual
apropos cp

# copy folder
cp -avr <source> <target>

# move folder or multiple folders
mv <source>.* <target>

# remove all except one folder
mv exceptionFolder ..
rm -rf *
mv ../exceptionFolder .

# rename files
for f in <file_regex>; do mv -v "$f" "${f/<old_string>/<new_string>}";done;

# remove all contents, not the folder
rm -r /path/to/dir/*

# remove all contents and the folder
rm -r /path/to/dir

# remove by regex
find . -type d -name "name*" -exec rm -rf {} \;

# print file w/ line number
cat -n <file>

# show file1 and file2 content
cat file1 file2

# copy file1 to file2
cat file1 > file2

# append file1 to the end of file2
cat file1 >> file2

# join file1 and file2 to create bigfile
cat file1 file2 > bigfile

# omit the repeated empty
cat -s <file>

# display TAB
cat -T <file>

# display end-of-line
cat -e <file>

# merge files
cat *.csv>merged.csv

# download file from ssh
ssh ... 'cat /path/on/remote' > /path/on/local

# print file1 to screen continuously
tail -f file1

# print first 5 lins
head -5 file1

# change permision
chmod [ugoa][+-][rwx] file1

# show  all user
less /etc/passwd

# show all suspend and background jobs
jobs

# add & to start job in the background
sleep 1 &

# forground job 1
fg %1

# show process statuses
ps

# check os
echo $OSTYPE

# check all env variables
printenv | less

# show shell history
echo $history
```

## grep

general regular expression print

```bash
# invert search result
grep -v <pattern> <file>

# show line number
grep -n <pattern> <file>

# recursive search, skip symlinks, show only filenames
grep -lr <pattern> <file_pattern>

# multiple search pattern, use basic regex joined by \|
grep 'pattern1\|pattern2' <file>

# basic regex feature with meta-characters
# ^   start of line
# $   end of line
# .   any single character
# []  any single character enclosed
# [^] any single character except enclosed
# \  escape meta character
grep "^patt.[^r]n$" <file>

# extended regex
grep -E "pattern1|pattern2" <file>

# search lines end with ,
grep ',[[:cntrl:]]*$' <file>

# basic regex
# char * . ^ $ [] [^]
grep <file>

# extended regex
# ? + () {} |
grep -E <file>
```

## hexdump

display file contents in hexadecimal, decimal, octal, or ascii

```bash
# 0a -> LF
# 0d -> CR
hexdump -e '"%08_ad (0x%08_ax)    "8/1 "%02x ""   "8/1 "%02x "' -e '"    "8/1 "%_p""|"8/1 "%_p""\n"' -s 0 <file>
```

## curhl

```bash
# download file
curl -O <address>

# post
curl <address> -H "Content-Type: application/json" -X POST -d '{"key1":"value1", "key2":"value2"}'
curl <address> -H "Content-Type: application/json" -X POST -d '@data.json'
```

## http server

```bash
python3 -m http.server
```

## zip

```bash
unzip my.zip
unzip my.zip -d /path/to/dir
```

## yum

```bash
# clean /var/cache/yum
yum clean packages
yum clean headers
yum clean all
```

## systemctl, hostnamectl

```bash
# show version
systemctl --version

# start
sudo systemctl start application.service

# stop
sudo systemctl stop application.service

# status
systemctl status application.service

# show full log
journalctl -u application.service

# show hostname
hostnamectl
```

## sar

```bash
# install sar
yum install sysstat
# on mac
brew install sysstat

# show config
cat /etc/sysconfig/sysstat
cat /etc/cron.d/sysstat

# check log dir
ls /var/log/sa

# check report
cat /var/log/sa/sar14

# show report
# -f use file
# -d : block device
# -F : mounted filesystem
# -H : hugepages
# -n DEV: network
# -r : memory
# -S : swap space
# -u : cpu
# -v : inode
# -W : swapping
cat -u -f /var/log/sa/sar14
```

## timeout

```bash
timeout 7d [command] > [output file] &
```

## nvme

```bash
# install
yum install nvme-cli

# list all devices
nvme list

# check log
nvme smart-log /dev/nvme0n1
```

## screen

```bash
# list
screen -ls

# create new session
screen
screen -S [session]

# attach
screen -r -d
screen -r [session id]

# exit current session
cmd+a d

# delete window
cmd+a k

# create window
cmd+a c
```

## iperf

```bash
iperf3 -c 172.18.136.245   -b 40G -f M -P 8 -t 1
```

## socat

```bash
# install
yum install socat

# forward local PORT to IP:PORT
socat TCP4-LISTEN:<PORT>,fork TCP4:<IP>:<PORT>
```

## timezone

```bash
# check timezone
ls -al /etc/localtime

# list timezone
ls /usr/share/zoneinfo
ls /usr/share/zoneinfo/Asia

# set timezone
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

## wget

```bash
# download in the background continously
wget -c -b [url]

# check download
tail wget-log
```
