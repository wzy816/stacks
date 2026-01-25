# v2ray

## server

```bash
# tutorial at
# https://github.com/233boy/v2ray/wiki/V2Ray%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85%E8%84%9A%E6%9C%AC

# 1. install
bash <(curl -s -L https://git.io/v2ray-setup.sh)

# 2. update /etc/v2ray/config.json

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

### server /etc/v2ray/config.json

```json
{
    "log": {
        "access": "/var/log/v2ray/access.log",
        "error": "/var/log/v2ray/error.log",
        "loglevel": "warning"
    },
    "inbounds": [
        {
            "port": 1234,
            "protocol": "vmess",
            "settings": {
                "clients": [
                    {
                        "id": "xxxx",
                        "level": 1,
                        "alterId": 0
                    }
                ]
            },
            "streamSettings": {
                "network": "tcp"
            },
            "sniffing": {
                "enabled": true,
                "destOverride": [
                    "http",
                    "tls"
                ]
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "freedom",
            "settings": {
                "domainStrategy": "UseIP"
            },
            "tag": "direct"
        },
        {
            "protocol": "blackhole",
            "settings": {},
            "tag": "blocked"
        }
    ],
    "dns": {
        "servers": [
            "https+local://dns.google/dns-query",
            "8.8.8.8",
            "1.1.1.1",
            "localhost"
        ]
    },
    "routing": {
        "domainStrategy": "IPOnDemand",
        "rules": [
            {
                "type": "field",
                "ip": [
                    "geoip:private"
                ],
                "outboundTag": "blocked"
            },
            {
                "type": "field",
                "domain": [
                    "domain:epochtimes.com",
                    "domain:epochtimes.com.tw",
                    "domain:epochtimes.fr",
                    "domain:epochtimes.de",
                    "domain:epochtimes.jp",
                    "domain:epochtimes.ru",
                    "domain:epochtimes.co.il",
                    "domain:epochtimes.co.kr",
                    "domain:epochtimes-romania.com",
                    "domain:erabaru.net",
                    "domain:lagranepoca.com",
                    "domain:theepochtimes.com",
                    "domain:ntdtv.com",
                    "domain:ntd.tv",
                    "domain:ntdtv-dc.com",
                    "domain:ntdtv.com.tw",
                    "domain:minghui.org",
                    "domain:renminbao.com",
                    "domain:dafahao.com",
                    "domain:dongtaiwang.com",
                    "domain:falundafa.org",
                    "domain:wujieliulan.com",
                    "domain:ninecommentaries.com",
                    "domain:shenyun.com"
                ],
                "outboundTag": "blocked"
            },
            {
                "type": "field",
                "protocol": [
                    "bittorrent"
                ],
                "outboundTag": "blocked"
            }
        ]
    },
    "transport": {
        "kcpSettings": {
            "uplinkCapacity": 100,
            "downlinkCapacity": 100,
            "congestion": true
        }
    }
}
```

## client on mac

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
  port: 1234
  uuid: xxx
  alterId: 0
  cipher: auto
```

## share clash in windows to device via windows hotspot, no clash TUN

1. make sure windows clash working
2. in clash, turn on allow WLAN, get port (default=7890)
3. turn on windows mobile hotspot, share to WLAN
4. cmd ipconfig, get ip of network adapter local area connection (ip sim. to 192.168.?.?, reset adapter if necessary)
5. device WLAN connect to windows hotspot
6. on device WLAN, config proxy to manual, set server to 192.168.?.? and port to 7890
7. done

## client on unbuntu ECS

```bash
# install v2ray
# download v2ray-linux-64.zip from https://github.com/v2ray/v2ray-core/releases to ECS
# download https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh to ECS
# in install-release.sh, update TMP_DIRECTORY to where zip file is, remove download_v2ray to skip download
bash install-release.sh

# update /usr/local/etc/v2ray/config.json

# /etc/environment, add
http_proxy="http://127.0.0.1:1080/"
https_proxy="http://127.0.0.1:1080/"
ftp_proxy="http://127.0.0.1:1080/"
no_proxy="localhost,127.0.0.1,localaddress,.localdomain.com"

# restart machine to allow process to use proxy inbounds

sudo systemctl restart v2ray
sudo systemctl status v2ray
```

### client /usr/local/etc/v2ray/config.json

```json
{
    "log": {
        "loglevel": "warning"
    },
    "inbounds": [
        {
            "port": 1080,
            "listen": "127.0.0.1",
            "protocol": "http",
            "settings": {
                "udp": true
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "vmess",
            "settings": {
                "vnext": [
                    {
                        "address": "1.2.3.4",
                        "port": 1234,
                        "users": [
                            {
                                "id": "xxxx",
                                "alterId": 0,
                                "security": "auto",
                                "level": 0
                            }
                        ]
                    }
                ]
            },
            "tag": "proxy"
        },
        {
            "protocol": "freedom",
            "tag": "direct"
        }
    ],
    "routing": {
        "domainStrategy": "IPOnDemand",
        "rules": [
            {
                "type": "field",
                "ip": [
                    "geoip:private",
                    "geoip:cn"
                ],
                "outboundTag": "direct"
            }
        ]
    }
}
```

## error common/drain: common/drain: unable to drain connection > EOF > proxy/vmess/encoding: invalid user

https://www.v2fly.org/guide/faq.html#vmess-invalid-user

```bash
- change AlterID to 0
- if clash for windows, check windows system time alignment
```
