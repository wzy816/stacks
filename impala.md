# impala

## impala-shell
```bash
# in conda
pip install sasl
pip install impala-shell

# resolve sasl.h
yum -y install cyrus-sasl cyrus-sasl-devel cyrus-sasl-lib 

# connect and executre
impala-shell -i [ip]:21000 -d [database]  -q 'show tables;'

# connect
impala-shell -i [ip]:21000 -d [database]
# show info
profile
```