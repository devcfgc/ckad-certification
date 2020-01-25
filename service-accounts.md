### Create a Service-Account
```
$ kubectl create serviceaccount SERVICEACCOUNT-NAME
$ kubectl get serviceaccount
$ kubectl describe serviceaccount SERVICEACCOUNT-NAME
$ kubectl describe secret SERVICEACCOUNT-NAME-TOKEN
```

### Each namespace has a default serviceaccount
When a POD is created the default token is mounted at /var/run/secrets/kubernetes.io/serviceaccount
If you run the following command:
```
$ kubectl exec -it POD-NAME ls /var/run/secrets/kubernetes.io/serviceaccount
ca.crt  namespace  token

$ kubectl exec -it POD-NAME cat /var/run/secrets/kubernetes.io/serviceaccount/token
```

If you do not want to have mounted the default token
```
apiVersion: v1
kind: Pod
metadata:
 name: my-pod
spec:
 containers:
   - name: my-pod
     image: my-pod
 automountServiceAccountToken: false
```

### Use a specific service account in a POD
```
$ kubectl create serviceaccount SERVICEACCOUNT-NAME

---
apiVersion: v1
kind: Pod
metadata:
 name: my-pod
spec:
 containers:
   - name: my-pod
     image: my-pod
 serviceAccount: SERVICEACCOUNT-NAME
```

### Give permissions to serviceaccount using RBAC

#### Create serviceaccount
```
$ kubectl create serviceaccount dashboard-sa
```

#### Create Role
```
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: pod-readerrules:
- apiGroups:
  - ''  resources:
  - pods
  verbs:
  - get
  - watch
  - list
```

#### Role binding
```
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: ServiceAccount
  name: dashboard-sa # Name is case sensitive
  namespace: default
roleRef:
  kind: Role #this must be Role or ClusterRole
  name: pod-reader # this must match the name of the Role or ClusterRole you wish to bind to
  apiGroup: rbac.authorization.k8s.io
```