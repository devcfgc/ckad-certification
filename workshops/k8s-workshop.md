```
$ kubectl run --generator=run-pod/v1 nginx-448839 --image=nginx:alpine

$ k create ns apx-z993845

$ kubectl create deployment httpd-frontend --image=httpd:2.4-alpine
$ kubectl scale deployment/httpd-frontend --replicas=3

$ kubectl run --generator=run-pod/v1 messaging --image=redis:alpine -l tier=msg
$ kubectl create deployment messaging --image=redis:alpine --dry-run -o yaml > deploy1.yaml

$ kubectl get rs rs-d33393 -oyaml > rs1.yaml

$ kubectl expose deployment redis --port=6379 --name messaging-service --namespace marketing
$ kubectl get svc messaging-service -n marketing -oyaml > svc1.yaml # Add selector name=redis-pod

$ k get po webapp-color -oyaml > pod1.yaml

$ kubectl create configmap cm-3392845 --from-literal=DB_NAME=SQL3322 --from-literal=DB_HOST=sql322.mycompany.com --from-literal=DB_PORT=3306

$ kubectl create secret generic db-secret-xxdf --from-literal=DB_Host=sql01 --from-literal=DB_User=root --from-literal=DB_Password=password123

$ kubectl get po app-sec-kff3345 -oyaml > pod2.yaml

$ kubectl logs e-com-1123 -n e-commerce > /opt/outputs/e-com-1123.logs


apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-analytics
spec:
  capacity:
    storage: 100Mi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: "/pv/data-analytics"
```