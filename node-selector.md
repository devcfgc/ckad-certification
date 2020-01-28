## Step One: Attach label to the node
```
$ kubectl label nodes <node-name> <label-key>=<label-value>
$ kubectl label nodes kubernetes-foo-node-1.c.a-robinson.internal disktype=ssd
$ kubectl get nodes --show-labels
```

## Step Two: Add a nodeSelector field to your pod configuration
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  nodeSelector:
    disktype: ssd
```