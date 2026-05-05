# whisper

# install on windows w/ gpu + cuda11.8

```bash
conda create -n whisper python=3.10

# model installed on "C:\Users\[user]\.cache\whisper"

python -m pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
python -m pip install -U openai-whisper
```
