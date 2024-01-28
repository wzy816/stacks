# nvidia

## nvidia-smi

```bash
# query params of all GPU
nvidia-smi -q

# query params of index 0 GPU
nvidia-smi -q -i 0

nvidia-smi -l

# query status every 15s and save to csv
nvidia-smi --query-gpu=timestamp,pci.bus_id,pstate,pcie.link.gen.max,pcie.link.gen.current,temperature.gpu,utilization.gpu,utilization.memory,memory.total,memory.free,memory.used --format=csv -l 15 > log.csv &

```
