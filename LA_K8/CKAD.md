# CKAD
## K8S objects
```
kubectl api-resources -o name
kubectl get pods -n kube-system
kubectl get nodes
kubectl get nodes $node_name
kubectl get nodes $node_name -o yaml
kubectl describe node $node_name
```

## Basic Container Configuration
You can specify custom commands for your containers.
```
apiVersion: v1
kind: Pod
metadata:
  name: my-command-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['echo']
  restartPolicy: Never
```

You can also add custom arguments like so:
```
apiVersion: v1
kind: Pod
metadata:
  name: my-args-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['echo']
    args: ['This is my custom argument']
  restartPolicy: Never
```

Here is a pod with a containerPort:
```
apiVersion: v1
kind: Pod
metadata:
  name: my-containerport-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: nginx
    ports:
    - containerPort: 80
```

Another example:
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: web
  labels:
    app: myapp
spec:
  containers:
  - name: myapp
    image: nginx
    command: ['nginx']
    args: ["-g", "daemon off;", "-q"]
    ports:
    - containerPort: 80
```