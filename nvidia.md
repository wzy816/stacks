# nvidia

## cuda

```bash
# install
apt install nvidia-cuda-toolkit

# version
nvcc --version

# update nvcc
# https://developer.nvidia.com/cuda-downloads
sudo apt-get -y install cuda-toolkit-12-8
# add PATH="/usr/local/cuda-12.8/bin:$PATH" to ~/.bashrc
source ~/.bashrc
```

## driver

```bash
ubuntu-drivers list

ubuntu-drivers install

```

## nvidia-smi

```bash
# query params of all GPU
nvidia-smi -q

# query params of index 0 GPU
nvidia-smi -q -i 0

nvidia-smi -l

watch -n 2 nvidia-smi

# query status every 15s and save to csv
nvidia-smi --query-gpu=timestamp,pci.bus_id,pstate,pcie.link.gen.max,pcie.link.gen.current,temperature.gpu,utilization.gpu,utilization.memory,memory.total,memory.free,memory.used --format=csv -l 15 > log.csv &
```
