# vmware

## vmware tools

```bash
# don't use vm default
sudo apt update && sudo apt -y install open-vm-tools
```

## hgfs

```bash
# shared folders not shown under /mnt/hgfs, mount manually
sudo vmhgfs-fuse .host:/ /mnt/hgfs/ -o allow_other -o uid=1000
```
