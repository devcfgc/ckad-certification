# Service Accounts
Creating a ServiceAccount looks like this: `kubectl create serviceaccount my-serviceaccount`

Use the serviceAccountName attribute in the pod spec to specify which ServiceAccount the pod should use:
```
apiVersion: v1
kind: Pod
metadata:
  name: my-serviceaccount-pod
spec:
  serviceAccountName: my-serviceaccount
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', "echo Hello, Kubernetes! && sleep 3600"]
```