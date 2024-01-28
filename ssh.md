# ssh

## basic

```bash
# key permission are too open
chmod 400 ./.ssh/your_key
```

## ~/.ssh/config

    Host *
      ServerAliveInterval 5
      UseKeychain yes
      IdentityFile ~/.ssh/github
      IdentityFile ~/.ssh/id_rsa
      StrictHostKeyChecking no

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

### login with key

```bash
# 1. generate or reuse local key pair
ssh-keygen

# 2. login to remote server with password

# 3. copy local pub key to file /<username>/.ssh/authorized_keys

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
