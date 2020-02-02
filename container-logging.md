## POD logging
```
apiVersion: v1
kind: Pod
metadata:
  name: simple-webapp-pod
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
```

```
$ kubectl logs -f <POD-NAME> <CONTAINER-NAME>
$ kubectl logs -f simple-webapp-pod log-agent
```