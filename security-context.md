## Docker security context
```
$ kubectl exec <POD-NAME> whoami # get user used to execute the processes within the pod
$ docker run --user=1001 ubuntu sleep 3600
$ docker run --cap-add MAC_ADMIN ubuntu
```

## Pod Security Policies
To automate the enforcement of security contexts, you can define [PodSecurityPolicies](https://kubernetes.io/docs/concepts/policy/pod-security-policy/) (PSP). A PSP is defined via a standard Kubernetes manifest following the PSP API schema. An example is presented below.

These policies are cluster-level rules that govern what a pod can do, what they can access, what user they run as, etc.

For instance, if you do not want any of the containers in your cluster to run as the root user, you can define a PSP to that effect. You can also prevent containers from being privileged or use the host network namespace, or the host PID namespace.

You can see an example of a PSP below:
```
apiVersion: extensions/v1beta1
kind: PodSecurityPolicy
metadata:
  name: restricted
spec:
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: MustRunAsNonRoot
  fsGroup:
    rule: RunAsAny
```

For Pod Security Policies to be enabled, you need to configure the admission controller of the controller-manager to contain PodSecurityPolicy. These policies make even more sense when coupled with the RBAC configuration in your cluster. This will allow you to finely tune what your users are allowed to run and what capabilities and low level privileges their containers will have.

See the [PSP RBAC example](https://github.com/kubernetes/examples/blob/master/staging/podsecuritypolicy/rbac/README.md) on GitHub for more details.

### What is the user used to execute a process within the pod?
```
kubectl exec POD-NAME whoami
```

### POD security-context example
```
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
  volumes:
  - name: sec-ctx-vol
    emptyDir: {}
  containers:
  - name: sec-ctx-demo
    image: busybox
    command: [ "sh", "-c", "sleep 1h" ]
    volumeMounts:
    - name: sec-ctx-vol
      mountPath: /data/demo
    securityContext:
      allowPrivilegeEscalation: false
---
apiVersion: v1
kind: Pod
metadata:
  name: web-pod
spec:
  securityContext:
    runAsUser: 1000
  containers:
  - name: ubuntu
    image: ubuntu
    command: [ "sleep", "3600" ]
---
apiVersion: v1
kind: Pod
metadata:
  name: web-pod
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command: [ "sleep", "3600" ]
    securityContext:
      runAsUser: 1000
      capabilities:  # Capabilities are only supported at the container level and not at the POD level
          add: ["MAC_ADMIN"]
---
apiVersion: v1
kind: Pod
metadata:
  name: multi-pod
spec:
  securityContext:
    runAsUser: 1001
  containers:
  -  image: ubuntu
     name: web
     command: ["sleep", "5000"]
     securityContext:
      runAsUser: 1002

  -  image: ubuntu
     name: sidecar
     command: ["sleep", "5000"]
```