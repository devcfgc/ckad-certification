## Example GC
```
apiVersion: v1 
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  accessModes:
      - ReadWriteOnce
  storageClass: google-storage
  resources:
     requests:
       storage: 500Mi
```
```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: google-storage
provisioner: kubernetes.io/gce-pd
parameters:
  type: pd-standard
  replication-type: none
```