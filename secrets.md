## Secrets

### Create a Secret from Literal and Display Its Details
```
$ kubectl create secret generic my-password --from-literal=password=mysqlpassword
$ kubectl get secret my-password -o yaml
$ kubectl describe secret my-password
```

### Create a Secret Manually
**ENCODE**
```
$ echo -n 'mysqlpassword' | base64
bXlzcWxwYXNzd29yZAo=
```
**DECODE**
```
$ echo -n 'bXlzcWxwYXNzd29yZAo=' | base64 --decode
mysqlpassword
```
```
apiVersion: v1
kind: Secret
metadata:
  name: my-password
type: Opaque
data:
  password: bXlzcWxwYXNzd29yZAo=
```
With stringData maps, there is no need to encode the value of each sensitive information field. The value of the sensitive field will be encoded when the my-password Secret is created:

```
apiVersion: v1
kind: Secret
metadata:
  name: my-password
type: Opaque
stringData:
  password: mysqlpassword
```

### Create a Secret from a File and Display Its Details
First, we encode the sensitive data and then we write the encoded data to a text file:
```
$ echo mysqlpassword | base64
 bXlzcWxwYXNzd29yZAo=

$ echo -n 'bXlzcWxwYXNzd29yZAo=' > password.txt
```
Now we can create the Secret from the password.txt file:

```
$ kubectl create secret generic my-file-password --from-file=password.txt
$ kubectl get secret my-file-password -o yaml
$ kubectl describe secret my-file-password
```

### Use Secrets Inside Pods
#### Using Secrets as Environment Variables
```
spec:
  containers:
  - image: wordpress:4.7.3-apache
    name: wordpress
    env:
    - name: WORDPRESS_DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: my-password
          key: password
```

#### Using Secrets as Files from a Pod
```
spec:
  containers:
  - image: wordpress:4.7.3-apache
    name: wordpress
    volumeMounts:
    - name: secret-volume
      mountPath: "/etc/secret-data"
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: my-password
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
        - secretRef:
            name: my-password
```