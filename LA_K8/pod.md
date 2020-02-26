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