1)
```
$ kubectl run my-webapp --image=nginx --labels='tier=frontend' --replicas=2
$ kubectl expose deployment my-webapp --name=front-end-service --type=NodePort --target-port=80 --port=80 --dry-run -oyaml > front-end-service.yaml
$ vi front-end-service.yaml
$ kubectl create -f front-end-service.yaml
```

2)
```
$ kubectl get no
$ kubectl taint nodes node01 'app_type=alpha:NoSchedule'
$ kubectl get no -o=jsonpath='{.items[*].spec.taints}'
$ kubectl get no -o=jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.taints}{"\n"}{end}'

$ kubectl run alpha --image=redis --generator=run-pod/v1 --dry-run -oyaml > alpha.yaml
$ vim alpha.yaml

apiVersion: v1
kind: Pod
metadata:
  name: alpha
spec:
  containers:
  - name: alpha
    image: redis
  tolerations:
  - key: "app_type"
    value: "alpha"
    operator: "Equal"
    effect: "NoSchedule"

$ kubectl create -f alpha.yaml
$ kubectl get po alpha -o wide
$ kubectl describe po alpha
```

3)
```
$ kubectl label no node02 app_type=beta
$ kubectl get no node02 --show-labels
$ kubectl run beta-apps --image=nginx --replicas=3 --dry-run -oyaml > beta.yaml

affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: app_type
          operator: In
          values:
          - beta
$ kubectl create -f beta.yaml
```

4)
```
$ kubectl explain ingress --recursive | less
$ kubectl explain ingress --recursive | grep VERSION
$ kubectl explain ingress --recursive | grep rules
$ vim ingress.yaml

apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: my-video-service
spec:
  rules:
  - host: ckad-mock-exam-solution.com
    http:
      paths:
      - path: /video
        backend:
          serviceName: my-video-service
          servicePort: 8080

$ kubectl create -f ingress.yaml
$ kubectl describe ingress my-video-service
$ curl -v http://ckad-mock-exam-solution.com:30093/video
```

5)
```
$ kubectl get po pod-with-rprobe -oyaml > pod-with-rprobe.yaml
$ vim pod-with-rprobe.yaml

readinessProbe:
  httpGet:
    path: /ready
    port: 8080

$ kubectl delete po pod-with-rprobe
$ kubectl create -f pod-with-rprobe.yaml
$ kubectl describe po pod-with-rprobe
```

6)
```
$ kubectl create job --image=docker/whalesay whalesay --dry-run -oyaml > whalesay.yaml
$ vim whalesay.yaml

apiVersion: batch/v1
kind: Job
metadata:
  name: whalesay
spec:
  template:
    spec:
      containers:
      - name: whalesay
        image: docker/whalesay
        command: ["cowsay"]
        args: ["I am going to ace CKAD!"]
      restartPolicy: Never
  backoffLimit: 6
  completions: 10

$ kubectl create -f whalesay.yaml
$ kubectl get jobs
```

7)
```
$ kubectl run multi-pod --image=nginx --generator=run-pod/v1 --dry-run -oyaml > multi-pod.yaml
$ vim multi-pod.yaml

apiVersion: v1
kind: Pod
metadata:
  name: multi-pod
spec:
  containers:
  - name: jupiter
    image: nginx
    env:
    - name: type
      value: planet
    command: ["sleep", "4800"]
  - name: europa
    image: busybox
    env:
    - name: type
      value: moon
    command: ["sleep", "4800"]

$ kubectl create -f multi-pod.yaml
$ kubectl get po
```

8)
```
$ vim pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: custom-volume
spec:  
  accessModes:    
    - ReadWriteMany  
  capacity:    
    storage: 50Mi
  persistentVolumeReclaimPolicy: Retain
  hostPath:    
    path: /opt/data
```