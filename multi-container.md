## SIDECAR
The idea for a sidecar container is to add some functionality not present in the main container. Rather than bloating code, which may not be necessary in other deployments, adding a container to handle a function such as logging solves the issue, while remaining decoupled and scalable. Prometheus monitoring and Fluentd logging leverage sidecar containers to collect data.

```
---
apiVersion: v1
kind: Pod
metadata:
  name: simple-webapp
  labels: 
    name: simple-webapp
spec:
  containers:
  - name: simple-webapp
    image: simple-webapp
    ports:
      - containerPort: 8080
  - name: log-agent
    image: log-agent
---
apiVersion: v1
kind: Pod
metadata:
  name: app
  namespace: elastic-stack
  labels:
    name: app
spec:
  containers:
  - name: app
    image: example/event-simulator
    volumeMounts:
    - mountPath: /log
      name: log-volume

  - name: sidecar
    image: example/filebeat-configured
    volumeMounts:
    - mountPath: /var/log/event-simulator/ 
      name: log-volume

  volumes:  
    - name: log-volume
    hostPath:
      # directory location on host
      path: /var/log/webapp
      # this field is optional
      type: DirectoryOrCreate
```

### EXAMPLE
```
$ kubectl run busybox --image=busybox --restart=Never -o yaml --dry-run -- /bin/sh -c 'echo hello;sleep 3600' > pod.yaml
$ vi pod.yaml

containers:
  - args:
    - /bin/sh
    - -c
    - echo hello;sleep 3600
    image: busybox
    imagePullPolicy: IfNotPresent
    name: busybox
    resources: {}
  - args:
    - /bin/sh
    - -c
    - echo hello;sleep 3600
    image: busybox
    name: busybox2

$ kubectl create -f pod.yaml

# Connect to the busybox2 container within the pod
$ kubectl exec -it busybox -c busybox2 -- /bin/sh
ls
exit

# or you can do the above with just an one-liner
$ kubectl exec -it busybox -c busybox2 -- ls

# you can do some cleanup
$ kubectl delete po busybox
```

## ADAPTER
The basic purpose of an adapter container is to modify data, either on ingress or egress, to match some other need. Perhaps, an existing enterprise-wide monitoring tools has particular data format needs. An adapter would be an efficient way to standardize the output of the main container to be ingested by the monitoring tool, without having to modify the monitor or the containerized application. An adapter container transforms multiple applications to singular view.

## AMBASSADOR
Ambassador is, "an open source, Kubernetes-native API gateway for microservices built on Enjoy".
It allows for access to the outside world without having to implement a service or another entry in an ingress controller: proxy local connection, reverse proxy, limits HTTP requests, re-route from the main container to the outside world.​