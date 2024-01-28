# airflow

## basic

```bash
# on centos
pip install cryptography
# generate frenet key for docker
python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"

# on mac
brew install mysql
pip install apache-airflow[all]
pip install mysqlclient
```

## cli

```bash
# create Admin
airflow create_user -r Admin -u <user_name> -e <email> -f <first_name> -l <last_name> -p <password>

# initialize database
airflow initdb

# show dags
airflow list_dags

# show tasks
airflow list_tasks tutorial
airflow list_tasks tutorial --tree

# create connection
airflow connections -a --conn_id <ftp> --conn_uri "ftp://username:password@ip:port"
airflow connections -a --conn_id <mysql> --conn_type "mysql" --conn_host "ip" --conn_login "username" --conn_password "password" --conn_port "3306"

# test job
airflow test my_dag main 20181029

# postgresql
# delete dags
delete from public.dag where dag_id = 'tutorial2';
```

## worker

```bash
# assume /data/airflow is the directory

# https://github.com/puckel/docker-airflow/blob/master/README.md#custom-airflow-plugins
# 1. edit /data/airflow/requirements.txt and mount it like
# /data/airflow/requirements.txt:/requirements.txt
# ex. add following two lines:
# pymongo
# boto3

# 2. add an airflow worker docker instance
# hostname is the ip address of host machine so that the remote log is accessible
# docker command is worker -q

# 3. clone my dag files
cd /data/airflow/dags
git clone airflow_dag_project

# 4. add plugins
cd /data/airflow/plugins
git clone https://github.com/airflow-plugins/mongo_plugin

# 5. edit permission of airflow/logs
cd /data/airflow
chmod -R 757 logs
```

## docker-compose.yml

```yml
version: "2"
services:
  scheduler:
    image: puckel/docker-airflow:1.10.0-2
    environment:
      LOAD_EXAMPLES: n
      EXECUTOR: Celery
      FERNET_KEY: <FERNET_KEY>
      POSTGRES_USER: airflow
      POSTGRES_PASSWORD: airflow
      POSTGRES_DB: airflow
      POSTGRES_PORT: "5432"
      POSTGRES_HOST: postgres
      REDIS_PASSWORD: airflowpassrequired
      REDIS_HOST: redis
      REDIS_PORT: "6379"
      AIRFLOW__WEBSERVER__RBAC: "True"
      AIRFLOW__CORE__SQL_ALCHEMY_CONN: postgresql+psycopg2://airflow:airflow@postgres:5432/airflow
      AIRFLOW_HOME: /usr/local/airflow
      AIRFLOW__SMTP__SMTP_HOST: smtp.example.com
      AIRFLOW__SMTP__SMTP_PORT: "25"
      AIRFLOW__SMTP__SMTP_ MAIL_FROM: <airflow@example.com>
      AIRFLOW__SMTP__SMTP_SSL: "False"
      AIRFLOW__SMTP__SMTP_STARTTLS: "False"
      AIRFLOW__WEBSERVER__BASE_URL: http://<ip1>:8080
    stdin_open: true
    volumes:
      - /data/airflow/dags:/usr/local/airflow/dags
      - /data/airflow/logs:/usr/local/airflow/logs
      - /data/airflow/requirements.txt:/requirements.txt
      - /data/airflow/plugins:/usr/local/airflow/plugins
    tty: true
    links:
      - redis:redis
      - postgres:postgres
    command:
      - scheduler
    labels:
      io.rancher.container.pull_image: always
      io.rancher.scheduler.affinity:host_label: airflow=true
  worker1:
    image: puckel/docker-airflow:1.10.0-2
    hostname: <ip1>
    environment:
      LOAD_EXAMPLES: n
      EXECUTOR: Celery
      FERNET_KEY: <FERNET_KEY>
      POSTGRES_USER: airflow
      POSTGRES_PASSWORD: airflow
      POSTGRES_DB: airflow
      POSTGRES_PORT: "5432"
      POSTGRES_HOST: postgres
      REDIS_PASSWORD: airflowpassrequired
      REDIS_HOST: redis
      REDIS_PORT: "6379"
      AIRFLOW__WEBSERVER__RBAC: "True"
      AIRFLOW__CORE__SQL_ALCHEMY_CONN: postgresql+psycopg2://airflow:airflow@postgres:5432/airflow
      AIRFLOW_HOME: /usr/local/airflow
      AIRFLOW__SMTP__SMTP_HOST: smtp.example.com
      AIRFLOW__SMTP__SMTP_PORT: "25"
      AIRFLOW__SMTP__SMTP_ MAIL_FROM: airflow@example.com
      AIRFLOW__SMTP__SMTP_SSL: "False"
      AIRFLOW__SMTP__SMTP_STARTTLS: "False"
      AIRFLOW__WEBSERVER__BASE_URL: http://<ip1>:8080
    stdin_open: true
    volumes:
      - /data/airflow/dags:/usr/local/airflow/dags
      - /data/airflow/logs:/usr/local/airflow/logs
      - /data/airflow/plugins:/usr/local/airflow/plugins
      - /data/airflow/requirements.txt:/requirements.txt
    tty: true
    links:
      - redis:redis
      - postgres:postgres
    ports:
      - 8793:8793/tcp
    command:
      - worker
    labels:
      io.rancher.container.pull_image: always
      airflow-worker: "true"
  webserver:
    image: puckel/docker-airflow:1.10.0-2
    environment:
      LOAD_EXAMPLES: n
      EXECUTOR: Celery
      FERNET_KEY: <FERNET_KEY>
      POSTGRES_USER: airflow
      POSTGRES_PASSWORD: airflow
      POSTGRES_DB: airflow
      POSTGRES_PORT: "5432"
      POSTGRES_HOST: postgres
      REDIS_PASSWORD: airflowpassrequired
      REDIS_HOST: redis
      REDIS_PORT: "6379"
      AIRFLOW__WEBSERVER__RBAC: "True"
      AIRFLOW__CORE__SQL_ALCHEMY_CONN: postgresql+psycopg2://airflow:airflow@postgres:5432/airflow
      AIRFLOW_HOME: /usr/local/airflow
      AIRFLOW__SMTP__SMTP_HOST: smtp.example.com
      AIRFLOW__SMTP__SMTP_PORT: "25"
      AIRFLOW__SMTP__SMTP_ MAIL_FROM: airflow@example.com
      AIRFLOW__SMTP__SMTP_STARTTLS: "False"
      AIRFLOW__SMTP__SMTP_SSL: "False"
      AIRFLOW__WEBSERVER__BASE_URL: http://<ip1>:8080
    stdin_open: true
    volumes:
      - /data/airflow/dags:/usr/local/airflow/dags
      - /data/airflow/logs:/usr/local/airflow/logs
      - /data/airflow/plugins:/usr/local/airflow/plugins
      - /data/airflow/requirements.txt:/requirements.txt
    tty: true
    links:
      - postgres:postgres
      - redis:redis
    ports:
      - 8080:8080/tcp
    command:
      - webserver
    labels:
      io.rancher.container.pull_image: always
      io.rancher.scheduler.affinity:host_label: airflow=true
  worker2:
    image: puckel/docker-airflow:1.10.0-2
    hostname: <ip2>
    environment:
      LOAD_EXAMPLES: n
      EXECUTOR: Celery
      FERNET_KEY: <FERNET_KEY>
      POSTGRES_USER: airflow
      POSTGRES_PASSWORD: airflow
      POSTGRES_DB: airflow
      POSTGRES_PORT: "5432"
      POSTGRES_HOST: postgres
      REDIS_PASSWORD: airflowpassrequired
      REDIS_HOST: redis
      REDIS_PORT: "6379"
      AIRFLOW__WEBSERVER__RBAC: "True"
      AIRFLOW__CORE__SQL_ALCHEMY_CONN: postgresql+psycopg2://airflow:airflow@postgres:5432/airflow
      AIRFLOW_HOME: /usr/local/airflow
      AIRFLOW__SMTP__SMTP_HOST: smtp.example.com
      AIRFLOW__SMTP__SMTP_PORT: "25"
      AIRFLOW__SMTP__SMTP_ MAIL_FROM: airflow@example.com
      AIRFLOW__SMTP__SMTP_SSL: "False"
      AIRFLOW__SMTP__SMTP_STARTTLS: "False"
      AIRFLOW__WEBSERVER__BASE_URL: http://<ip1>:8080
    stdin_open: true
    volumes:
      - /data/airflow/dags:/usr/local/airflow/dags
      - /data/airflow/logs:/usr/local/airflow/logs
      - /data/airflow/plugins:/usr/local/airflow/plugins
      - /data/airflow/requirements.txt:/requirements.txt
    tty: true
    links:
      - redis:redis
      - postgres:postgres
    ports:
      - 8793:8793/tcp
    command:
      - worker
      - -q
      - <ip2>
    labels:
      io.rancher.container.pull_image: always
      airflow-worker: "true"
  postgres:
    image: postgres:9.6
    environment:
      POSTGRES_USER: airflow
      POSTGRES_PASSWORD: airflow
      POSTGRES_DB: airflow
      PGDATA: /var/lib/postgresql/data/pgdata
      POSTGRES_HOST: airflow
    stdin_open: true
    volumes:
      - /data/airflow/pgdata:/var/lib/postgresql/data/pgdata
    tty: true
    ports:
      - 5432:5432/tcp
    labels:
      io.rancher.container.pull_image: always
      io.rancher.scheduler.affinity:host_label: airflow=true
  redis:
    image: redis:latest
    stdin_open: true
    volumes:
      - /data/redis:/data
    tty: true
    ports:
      - 6379:6379/tcp
    command:
      - redis-server
      - --requirepass
      - airflowpassrequired
    labels:
      io.rancher.container.pull_image: always
      io.rancher.scheduler.affinity:host_label: airflow=true
  flower:
    image: puckel/docker-airflow:1.10.0-2
    environment:
      EXECUTOR: Celery
      REDIS_PASSWORD: airflowpassrequired
      REDIS_HOST: redis
      REDIS_PORT: "6379"
      AIRFLOW__SMTP__SMTP_MAIL_FROM: airflow@example.com
    stdin_open: true
    tty: true
    links:
      - redis:redis
    ports:
      - 5555:5555/tcp
    command:
      - flower
    labels:
      io.rancher.container.pull_image: always
      io.rancher.scheduler.affinity:host_label: airflow=true
```

## rancher-compose.yml

```yml
version: '2'
services:
  scheduler:
    scale: 1
    start_on_create: true
  worker1:
    scale: 1
    start_on_create: true
  webserver:
    scale: 1
    start_on_create: true
  worker2:
    scale: 1
    start_on_create: true
  postgres:
    scale: 1
    start_on_create: true
  redis:
    scale: 1
    start_on_create: true
  flower:
    scale: 1
    start_on_create: true

```
