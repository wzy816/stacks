# jupyter

## basic

```bash
source activate my_env

# if jupyter not recognize my_env
conda install jupyter
conda install nb_conda_kernels

# activate notebook
jupyter notebook

# install extension
pip install jupyter_contrib_nbextensions
jupyter contrib nbextension install --user

# conda env not show in notebook
# in base env
conda install nb_conda_kernels
# in target env
conda install ipykernel
# restart notebook, target env will show
```

## lab

```python
# show module
help()

# show system
import platform
platform.uname()
```

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

## support c++

```bash
conda create -n cling
conda activate cling
conda install jupyter notebook
conda install xeus-cling -c conda-forge
jupyter kernelspec list
jupyter notebook
```
