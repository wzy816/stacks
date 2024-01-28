# flink

- scala 2.12.15
- flink 1.14.2

## deployment non-ha session mode on k8s

[1.14 setup](https://nightlies.apache.org/flink/flink-docs-release-1.14/docs/deployment/resource-providers/standalone/kubernetes/)

```bash
kubectl create namespace flink-cluster

# create service account
kubectl create serviceaccount flink -n flink-cluster

# create cluster role binding
kubectl create clusterrolebinding flink-role-binding-flink \
  --clusterrole=edit \
  --serviceaccount=flink-cluster:flink

# create configmap
kubectl create -f flink-configuration-configmap.yaml

# create jobmanager service
kubectl create -f jobmanager-service.yaml
kubectl get service flink-jobmanager

# create jobmanager deployment
kubectl create -f jobmanager-session-deployment-non-ha.yaml

# create task manager deployment
kubectl create -f taskmanager-session-deployment.yaml
kubectl get pods

# enable Nginx Ingress Controller plugin

# create ingress config for flink-cluster
# endpoint_ip flink-jobmanager.your.domain > flink-jobmanager 8081

# edit local /etc/hosts
endpoint_ip  flink-jobmanager.your.domain

# open dashboard at flink-jobmanager.your.domain

```
