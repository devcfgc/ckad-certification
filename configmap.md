## Create a ConfigMap
```
$ kubectl create configmap my-config --from-literal=key1=value1 --from-literal=key2=value2 
configmap/my-config created
$ kubectl get configmaps my-config -o yaml
```
### Create a ConfigMap from a file
First we need to create a file `permission-reset.properties`with the following configuration data:
```
permission=read-only
allowed="true"
resetCount=3
```
We can then create the ConfigMap with the following command:

```
$ kubectl create configmap permission-config --from-file=<path/to/>permission-reset.properties
```

### Use ConfigMaps Inside Pods
#### As Environment Variables
```
  containers:
  - name: myapp-specific-container
    image: myapp
    env:
    - name: SPECIFIC_ENV_VAR1
      valueFrom:
        configMapKeyRef:
          name: my-config
          key: SPECIFIC_DATA
    - name: SPECIFIC_ENV_VAR2
      valueFrom:
        configMapKeyRef:
          name: my-config-2
          key: SPECIFIC_INFO
```

#### As Volumes
```
  containers:
  - name: myapp-vol-container
    image: myapp
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: my-config
```

#### Example
```
kind: Pod 
apiVersion: v1 
metadata:
  name: pod-env-var 
spec:
  containers:
    - name: env-var-configmap
      image: nginx:1.7.9 
      envFrom:
        - configMapRef:
            name: my-config
```