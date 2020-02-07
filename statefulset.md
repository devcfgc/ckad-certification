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

## example dynamic provisioning

```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
  labels:
      app: mysql
spec:
  replicas: 3
  selector:
     matchLabels:
        app: mysql
  serviceName: mysql1-h
  podManagementPolicy: Parallel
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql
        volumeMounts:
         - mounthPath: /var/lib/mysql
           name: data-volume
volumeClaimTemplate:
- metadata:
    name: data-volume
  spec:
   accessModes:
      - ReadWriteOnce
   storageClassName: google-storage
   resources:
      requests:
        storage: 500Mi
```