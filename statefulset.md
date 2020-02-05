## example
```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
  labels:
      app: mysql
spec:
  template:
    metadata:
      labels:
        app: mysql
  spec:
    containers:
    - name: mysql
      image: mysql
  replicas: 3
  selector:
     matchLabels:
        app: mysql
  serviceName: mysql1-h
  podManagementPolicy: Parallel
```