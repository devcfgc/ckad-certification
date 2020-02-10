```
---
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-storage
provisioner: kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer # Wait to create until Pod making PVC is created. Default is immediate (create once PVC is created)

---
apiVersion: v1 
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 10Gi
  volumeMode: Block
  accessModes:
  - ReadWriteOnce
  storageClassName: local-storage
  local:
    path: /data/storage
  nodeAffinity:
    required:
      modeSelectorTerms:
      - matchExpressions:
        - key: kubernetes.io/hostname
          operator: In
          values:
          - <node-name>

---
apiVersion: v1 
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClass: local-storage
  resources:
     requests:
       storage: 1Gi

---
apiversion: apps/v1
kind: [Pod | StatefulSet | Deployment]
...
  spec:
    volumes:
    - name: my-volume
      persistentVolumeClaim:
        claimName: my-pvc
```