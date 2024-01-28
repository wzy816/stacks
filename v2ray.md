# v2ray

## server

```bash
# tutorial at
# https://github.com/233boy/v2ray/wiki/V2Ray%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85%E8%84%9A%E6%9C%AC

# 1. install
bash <(curl -s -L https://git.io/v2ray-setup.sh)

# 2. edit /etc/v2ray/config.json
# change port to non-default
# change streamSettings.network to tcp, delete kcp
# change settings.clients.alterId to 64
vim /etc/v2ray/config.json

# 3. create systemd
touch /etc/systemd/system/v2ray.service.d

[Service]
ExecStart=
ExecStart=/usr/local/bin/v2ray -config /etc/v2ray/config.json
User=root

# 4. enable
sudo systemctl enable v2ray
sudo systemctl disable v2ray
sudo systemctl start v2ray
sudo systemctl stop v2ray
sudo systemctl restart v2ray

# check status
systemctl status v2ray

# check log
ll /var/log/v2ray/
tail /var/log/v2ray/error.log

# 5. stop firewall
systemctl stop firewalld
systemctl disable firewalld

# 6. update ECS firewall rule to allow port
```

## client

```bash
# download clashX for mac
https://github.com/yichengchen/clashX/releases

# download new yaml file and add to config folder
https://v2raytech.com/clash_template2.yaml

# modify vmess config
# don't change v2ray name in yaml
- name: "v2ray"
  type: vmess
  server: 1.2.3.4
  port: xxxx
  uuid: xxx
  alterId: 0
  cipher: auto
```
