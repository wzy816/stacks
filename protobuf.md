# protobuf

## install protoc
```bash
# download
wget https://github.com/protocolbuffers/protobuf/releases/download/v3.17.3/protoc-3.17.3-linux-x86_64.zip

# unzip
unzip protoc-3.17.3-linux-x86_64.zip -d $HOME/.local

# add path
export PATH="$PATH:$HOME/.local/bin"

# check version
protoc --version
```

## protoc
```bash
# compile to python
# ex. protoc --proto_path=src --python_out=build/gen src/bar/baz.proto
# will read src/bar/baz.proto
# and produce build/gen/bar/baz_pb2.py
protoc --proto_path=<proto_file_dir> --python_out=<output_dir> <proto_file>
```
