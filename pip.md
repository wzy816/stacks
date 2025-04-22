# pip

```bash
# install on centos 7.9
sudo yum install epel-release
yum -y install python-pip

# update outdated packages
pip list --outdated --format=freeze | grep -v '^\-e' | cut -d = -f 1  | xargs -n1 pip install -U

# install from requirements.txt
pip install -r requirements.txt

# add tsinghua repo
pip install pip -U
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

# uninstall w/ deps
pip install pip-autoremove

#
# echo "cython<3.0" > c.txt
PIP_CONSTRAINT=c.txt pip install
```
