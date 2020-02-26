# POD
my-pod.yml:
```
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600']
```

Create a pod from the yaml definition file: `kubectl create -f my-pod.yml`

Edit a pod by updating the yaml definiton and re-applying it: `kubectl apply -f my-pod.yml`

You can also edit a pod like this: `kubectl edit pod my-pod`

You can delete a pod like this: `kubectl delete pod my-pod`

## Multicontainer
```
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  containers:
  - name: nginx
    image: nginx:1.15.8
    ports:
    - containerPort: 80
  - name: busybox-sidecar
    image: busybox
    command: ['sh', '-c', 'while true; do sleep 30; done;']
```

### Ambassador pattern
Create a YAML definition file called fruit-service-ambassador-config.yml.
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: fruit-service-ambassador-config
data:
  haproxy.cfg: |-
    global
        daemon
        maxconn 256

    defaults
        mode http
        timeout connect 5000ms
        timeout client 50000ms
        timeout server 50000ms

    listen http-in
        bind *:80
        server server1 127.0.0.1:8775 maxconn 32
```

Create a YAML definition file for the pod called fruit-service.yml.
```
apiVersion: v1
kind: Pod
metadata:
  name: fruit-service
spec:
  containers:
  - name: legacy-fruit-service
    image: linuxacademycontent/legacy-fruit-service:1
  - name: haproxy-ambassador
    image: haproxy:1.7
    ports:
    - containerPort: 80
    volumeMounts:
    - name: config-volume
      mountPath: /usr/local/etc/haproxy
  volumes:
  - name: config-volume
    configMap:
      name: fruit-service-ambassador-config
```

Create the pod in the cluster. `kubectl apply -f fruit-service.yml`
If everything is working correctly, you should be able to access fruit-service from another pod.
You can create a busybox pod to use for testing with a file called busybox.yml.
```
apiVersion: v1
kind: Pod
metadata:
  name: busybox
spec:
  containers:
  - name: myapp-container
    image: radial/busyboxplus:curl
    command: ['sh', '-c', 'while true; do sleep 3600; done']
```

Create the busybox testing pod. `kubectl apply -f busybox.yml`

Use the busybox pod to test the legacy service on port 80. This command uses a subcommand to get the cluster's IP address for the pod and executes a curl command in the busybox pod to access the legacy service on port 80. `kubectl exec busybox -- curl $(kubectl get pod fruit-service -o=custom-columns=IP:.status.podIP --no-headers):80`
