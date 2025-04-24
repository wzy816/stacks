# huggingface

```bash
# huggingface-cli needs python > 3.7

# install
pip install --upgrade huggingface_hub

# login
huggingface-cli login

# upload file to root
huggingface-cli upload [repo] [local_file_path]

# upload file to path
huggingface-cli upload [repo] [local_file_path] [path_in_repo]

# cannot download model or slow
export HF_ENDPOINT=https://hf-mirror.com
export HF_HUB_DOWNLOAD_TIMEOUT=30

# set local dir
export HUGGINGFACE_HUB_CACHE=/mnt/.cache/
export TRANSFORMERS_CACHE=/mnt/.cache/
export HF_HOME=/mnt/.cache/

# download model (no hf_transfer)
huggingface-cli download [username/repo_name]
```
