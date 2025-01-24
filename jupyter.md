# jupyter

## basic

```bash
source activate my_env

# install extension
pip install jupyter_contrib_nbextensions
jupyter contrib nbextension install --user

# activate notebook
jupyter notebook

```

## kernel

```bash
# create new kernel
conda create -n tf2
conda activate tf2
pip install ipykernel
python -m ipykernel install --user --name tf2 --display-name "Python (tf2)"

# show kernels
jupyter kernelspec list

# remove kernel
jupyter kernelspec uninstall <old_kernel>

# start jupyter lab
nohup jupyter lab >/dev/null 2>&1&
```

### env not show in notebook kernel

```bash
# in base env
conda install jupyter
conda install nb_conda_kernels
# in target env
conda install ipykernel
# restart notebook, target env will show
```

## notebook

```bash
# create config
jupyter notebook --generate-config

# /root/.jupyter/jupyter_notebook_config.py
# c.NotebookApp.ip = "0.0.0.0"
# c.NotebookApp.port = 8888

# start
jupyter notebook --allow-root
```

## lab

```python
# show module
help()

# show system
import platform
platform.uname()
```

## support c++

```bash
conda create -n cling
conda activate cling
conda install jupyter notebook
conda install xeus-cling -c conda-forge
jupyter kernelspec list
jupyter notebook
```
