## MINIKUBE
```
$ minikube addons enable metrics-server
```

## OTHERS
```
$ git clone https://github.com/kubernetes-incubator/metrics-server.git
$ kubectl create -f deploy/1.8+/
$ kubectl top node
$ kubectl top pod
```