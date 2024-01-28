# pip

```bash
# update outdated packages
pip list --outdated --format=freeze | grep -v '^\-e' | cut -d = -f 1  | xargs -n1 pip install -U

# install from requirements.txt
pip install -r requirements.txt

# add tsinghua repo
pip install pip -U
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

# uninstall w/ deps
pip install pip-autoremove

```
