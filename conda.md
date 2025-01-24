# Conda

```bash
# install
yum install -y bzip2

# install anaconda
wget https://repo.continuum.io/archive/Anaconda3-5.0.1-Linux-x86_64.sh
bash Anaconda3-5.0.1-Linux-x86_64.sh
export PATH=/root/anaconda3/bin/:$PATH

# create new environment aws with python 3.6
conda create -n tensorflow python=3.6

# show environment list
conda env list

# use aws
conda activate aws
source activate tensorflow

# show installed packages inside
conda list

# exit aws env
source deactivate
conda deactivate

# remove env
conda env remove --name aws

# add tsinghua
conda config --show-sources
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/

# rename env
conda create --name new_name --clone old_name
conda env remove --name old_name
```

## .condarc

```text
channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/msys2/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
  - defaults
show_channel_urls: true
```
