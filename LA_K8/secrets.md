# Secrets
Create a secret using a yaml definition like this. It is a good idea to delete the yaml file containing the sensitive data after the secret object has been created in the cluster.
```
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
stringData:
  myKey: myPassword
```

Once a secret is created, pass the sensitive data to containers as an environment variable:
```
apiVersion: v1
kind: Pod
metadata:
  name: my-secret-pod
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', "echo Hello, Kubernetes! && sleep 3600"]
    env:
    - name: MY_PASSWORD
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: myKey
```