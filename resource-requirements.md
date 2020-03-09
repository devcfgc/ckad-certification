## POD definition
```
apiVersion: v1
kind: Pod
metadata:
  name: frontend
spec:
  containers:
  - name: db
    image: mysql
    env:
    - name: MYSQL_ROOT_PASSWORD
      value: "password"
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
  - name: wp
    image: wordpress
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

## Note on default resource requirements and limits
When a pod is created the containers are assigned a default CPU request of .5 and memory of 256Mi. For the POD to pick up those defaults you must have first set those as default values for request and limit by creating a LimitRange in that namespace.
```
apiVersion: v1
kind: LimitRange
metadata:
  name: mem-limit-range
spec:
  limits:
  - default:
      memory: 512Mi
    defaultRequest:
      memory: 256Mi
    type: Container
```
https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/


```
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-limit-range
spec:
  limits:
  - default:
      cpu: 1
    defaultRequest:
      cpu: 0.5
    type: Container
```
https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/cpu-default-namespace/

References: https://kubernetes.io/docs/tasks/configure-pod-container/assign-memory-resource