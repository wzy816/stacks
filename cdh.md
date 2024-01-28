# cdh

```bash

# https://docs.cloudera.com/documentation/enterprise/latest/topics/cdh_admin_performance.html#cdh_performance__section_xpq_sdf_jq
# show swapness
cat /proc/sys/vm/swappiness
# set swapness
sudo sysctl vm.swappiness=1
# save swapness
echo "vm.swappiness=1" >> /etc/sysctl.conf
```
