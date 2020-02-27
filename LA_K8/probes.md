# Liveness and Readiness Probes
Here is a pod with a liveness probe that uses a command:

my-liveness-pod.yml:
```
apiVersion: v1
kind: Pod
metadata:
  name: my-liveness-pod
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', "echo Hello, Kubernetes! && sleep 3600"]
    livenessProbe:
      exec:
        command:
        - echo
        - testing
      initialDelaySeconds: 5
      periodSeconds: 5
```

Here is a pod with a readiness probe that uses an http request:

my-readiness-pod.yml:
```
apiVersion: v1
kind: Pod
metadata:
  name: my-readiness-pod
spec:
  containers:
  - name: myapp-container
    image: nginx
    readinessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5
```

## Example

- Container Health Issues

The first issue is caused by application instances entering an unhealthy state and responding to user requests with error messages. Unfortunately, this state does not cause the container to stop, so the Kubernetes cluster is not able to detect this state and restart the container. Luckily, the application has an internal endpoint that can be used to detect whether or not it is healthy. This endpoint is /healthz on port 8081. Your first task will be to create a probe to check this endpoint periodically. If the endpoint returns an error or fails to respond, the probe will detect this and the cluster will restart the container.

- Container Startup Issues

Another issue is caused by new pods when they are starting up. The application takes a few seconds after startup before it is ready to service requests. As a result, some users are getting error message during this brief time. To fix this, you will need to create another probe. To detect whether the application is ready, the probe should simply make a request to the root endpoint, /, on port 80. If this request succeeds, then the application is ready.

There is already a pod descriptor in the home directory: ~/candy-service-pod.yml. Edit this file to add the probes, then create the pod in the cluster to test it.

`vi ~/candy-service-pod.yml`
```
apiVersion: v1
kind: Pod
metadata:
  name: candy-service
spec:
  containers:
  - name: candy-service
    image: linuxacademycontent/candy-service:2
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8081
    readinessProbe:
      httpGet:
        path: /
        port: 80
```
