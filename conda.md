# Conda

```bash
# install
yum install -y bzip2

# install anaconda
wget https://repo.continuum.io/archive/Anaconda3-5.0.1-Linux-x86_64.sh
bash Anaconda3-5.0.1-Linux-x86_64.sh
export PATH=/data/anaconda3/bin:$PATH
export PATH=/data/miniconda3/bin:$PATH

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
conda remove --name old_name --all # or conda env remove --name old_name
```

## .condarc

```text
channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```
