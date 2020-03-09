## Taint - Node
```
$ kubectl taint nodes node-name key=value:taint-effect
```
**taint-effect**: What happens to PODs that DO NOT TOLERATE this taint? NoSchedule | PreferNoSchedule | NoExecute

```
$ kubectl taint nodes node1 app=blue:NoSchedule
```

## Tolerations - Node
```
apiVersion: v1
kind: Pod
metadata:
  name: frontend
spec:
  containers:
  - name: nginx-container
    image: nginx
  tolerations:
  - key: "app"
    operator: "Equal"
    value: "blue"
    effect: "NoSchedule"
---
apiVersion: v1
kind: Pod
metadata:
  name: bee
spec:
  containers:
  - image: nginx
    name: bee
  tolerations:
  - key: spray
    value: mortein
    effect: NoSchedule
    operator: Equal
```

## Review Taint on the master node
```
$ kubectl describe pod kubemaster | grep Taint
```

## Remove Taint on master node example
```
$ kubectl taint nodes master node-role.kubernetes.io/master:NoSchedule-
```