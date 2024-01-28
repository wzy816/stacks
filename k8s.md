# k8s

## install

```bash
# install kubectl on mac
brew install kubectl

# check version
kubectl version
kubectl version --client

# install minikube on mac
brew install minikube

```

## minikube

```bash
# start
minikube start --driver=docker

# expose port
kubectl expose deployment hello-minikube --type=NodePort --port=8080
# expose port

# dashboard
# need to remove /usr/local/bin/k9s
minikube dashboard

#
minikube pause
minikube unpause
minikube stop

#
minikube delete --all

# show env
minikube docker-env

# point shell to minikube daemon
eval $(minikube docker-env)
# then build image

# see minikube docker daemon images
minikube ssh
docker images

```

## kubectl

```bash
# create deployment
kubectl create deployment hello-minikube --image=ealen/echo-server

# describe
kubectl describe deployments hello-minikube

# get service
kubectl get service hello-minikube

# show all pods
kubectl get pods -A

# describe pod
kubectl describe pod/[pod name]

# show replicaset
kubectl get replicaset

# forward port
kubectl port-forward service/hello-minikube 7080:8080

# get all ns
kubectl get namespace

# get all pods in ns
kubectl get pods --namespace=default

# get all running pods
kubectl get pods -A --field-selector=status.phase==Running

# run w/ ns
kubectl run nginx --image=nginx --namespace=default

# set current ns
kubectl config set-context --current --namespace=default

# check resources in a namespace
kubectl api-resources --namespaced=true

# check resources NOT in a namespace
kubectl api-resources --namespaced=false

# get service
kubectl get serviceaccount

# delete resources
kubectl delete pod,service,deployment -n default --all

# check api version
kubectl api-versions

# get cronjob
kubectl get cronjob
```

## node

```bash
# describe node
kubectl describe node minikube
```

## secret

```bash
# create
kubectl create secret docker-registry [secret_name] --docker-server=[ip]:[port] --docker-username=[username] --docker-password=["password"]

# edit
kubectl edit secret [secret_name]

# get
kubectl get secret [secret_name]

# delete
kubectl delete secret [secret_name]
```
