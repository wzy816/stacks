# ssh

## basic

```bash
# key permission are too open
chmod 400 ./.ssh/your_key
```

## ~/.ssh/config

```txt
Host *
  ServerAliveInterval 5
  UseKeychain yes
  IdentityFile ~/.ssh/github
  IdentityFile ~/.ssh/id_rsa
  StrictHostKeyChecking no

```

## /Users/[user]/Library/LaunchAgents/sync.git.plist

```bash
# check validity
plutil /Users/[user]/Library/LaunchAgents/sync.git.plist

# load
launchctl load /Users/[user]/Library/LaunchAgents/sync.git.plist

# unload
launchctl unload /Users/[user]/Library/LaunchAgents/sync.git.plist

# start
launchctl start sync.git
```

```plist
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
<key>Label</key>
<string>sync.git</string>
<key>Program</key>
<string>/Users/[user]/sync.sh</string>
<key> StartCalendarInterval</key>
<dict>
<key>Hour</key>
<integer>10</integer>
<key>Minute</key>
<integer>40</integer>
</dict>
</plist>
```

### use proxy

```bash
ssh -i '/path/to/your/pem'  -o 'ProxyCommand=/usr/local/bin/ncat --proxy <proxy_ip>:<proxy_port> --proxy-type http %h %p' <user_name>@<ip>
```

### linux ssh login with key

```bash
# 1. generate or reuse local key pair
ssh-keygen
# private key -> ~/.ssh/id_rsa
# publick key -> ~/.ssh/id_rsa.pub

# 2. login to remote server with password

# 3. copy pub key to file ~/.ssh/authorized_keys

# 4. update /etc/ssh/sshd_config
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
PasswordAuthentication no

# 5. restart service
sudo service sshd restart

# 6. login again with key
ssh username@<ip>
ssh -i /path/to/private/key username@<ip>

# 8. check log
journalctl -u sshd.service -e

```

### mac ssh to remote ip with key

```bash
# 1. reuse local key pair
# private key -> ~/.ssh/id_rsa
# publick key -> ~/.ssh/id_rsa.pub

# 2. copy public key string to remote ip ~/.ssh/authorized_keys

# 3. update local ~/.ssh/config

Host [ip]
  HostName [ip]
  User root
  PreferredAuthentications publickey
  IdentityFile "/path/to/private/key"

```

### mac ssh to windows openssh with key

```bash
# 1. copy mac id_ed25519.pub to win C:\Users\{username}\.ssh\authorized_keys

# 2. win settings, 应用\可选功能, 添加openssh

# 3. edit win "C:\ProgramData\ssh\sshd_config"
uncomment
PubkeyAuthentication yes
AuthorizedKeysFile	.ssh/authorized_keys
PasswordAuthentication no

comment out
#Match Group administrators
#       AuthorizedKeysFile __PROGRAMDATA__/ssh/administrators_authorized_keys

# 4. win, win+R services.msc, 启动 openssh 服务

# 5. win, open shell, use ipconfig get ipv4 ip

# 6. login, mac, ssh {username}@{win ipv4 ip}

# 查看日志
# win, win+R eventvwr, 应用程序和服务日志找到OpenSSH
# mac 用verbose，ssh -vvv {username}@{win ipv4 ip}

# 服务中无法启动 openssh 调试
# win 用admin打开shell
# 从服务找到openssh安装目录
cd C:\Windows\System32\OpenSSH
sshd.exe -d
```
