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

## EXAMPLES
### POD + SECRET + CONFIGMAP
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: candy-service-config
  data:
    candy.cfg: |-
      candy.peppermint.power=100000000
      candy.nougat-armor.strength=10
      candy.lemon.acceptability=0
---
apiVersion: v1
kind: Secret
metadata:
  name: db-password
stringData:
  password: Kub3rn3t3sRul3s!
---
apiVersion: v1
kind: Pod
metadata:
   name: candy-service
spec:
  securityContext:
    fsGroup: 2000
  serviceAccountName: candy-svc
  containers:
  - name: candy-service
    image: linuxacademycontent/candy-service:1
    volumeMounts:
      - name: config-volume
        mountPath: /etc/candy-service
    env:
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: db-password
          key: password
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
  volumes:
  - name: config-volume
    configMap:
      name: candy-service-config
```