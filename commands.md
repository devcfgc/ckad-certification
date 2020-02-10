# Imperative commands

```
$ kubectl cluster-info
kubectl get all
kubectl run [container-name] --image=[image-name]
kubectl port-forward [pod] [ports]
```

## Create a some resources
```
$ kubectl create namespace ny-namespace
$ kubectl create configmap nginx-configuration --namespace my-namespace
$ kubectl create serviceaccount ingress-serviceaccount --namespace my-namespace
$ kubectl get roles,rolebindings --namespace my-namespace


$ kubectl run --generator=run-pod/v1 nginx --image=nginx
$ kubectl run --generator=run-pod/v1 redis --image=redis:alpine -l tier=db

$ kubectl get po --show-labels
$ kubectl get deploy --show-labels

$ kubectl exec webapp cat /log/app.log # Execute commands inside a POD
$ kubectl exec webapp cat /log/app.log

$ kubectl create deployment --image=nginx nginx --save-config
$ kubectl create deployment webapp --image=example/webapp-color --save-config
$ kubectl create deployment demo --image=nginx --dry-run -oyaml > deployment.yaml
$ kubectl run blue --image=nginx --replicas=6
$ kubectl get deployments --show-labels
$ kubectl get deployments -l app=nginx
$ kubectl set image deployment/nginx nginx=nginx:1.9.1 --record
$ kubectl scale deployment/webapp --replicas=3
```

## Get POD definition into YAML
```
$ kubectl get pod webapp-color -oyaml > temp1.yaml
```

## Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)
```
$ kubectl run --generator=run-pod/v1 nginx --image=nginx --dry-run -o yaml
```

## Set POD date
`$ kubectl exec -it POD-NAME -- date -s '19 APR 2012 11:14:00'`

## Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)

`$ kubectl run --generator=deployment/v1beta1 nginx --image=nginx --dry-run -o yaml`

Or

`$ kubectl create deployment --image=nginx nginx --dry-run -o yaml`

## Generate Deployment YAML file (-o yaml). Don't create it(--dry-run) with 4 Replicas (--replicas=4)
`$ kubectl run --generator=deployment/v1beta1 nginx --image=nginx --dry-run --replicas=4 -o yaml`

> `kubectl create deployment` does not have a `--replicas` option. You could first create it and then scale it using the kubectl scale command.

## Save it to a file - (If you need to modify or add some other details)
`$ kubectl run --generator=deployment/v1beta1 nginx --image=nginx --dry-run --replicas=4 -o yaml > nginx-deployment.yaml

## Create a Service of type NodePort and expose it:
```
$ kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run -o yaml
$ kubectl expose deployment webapp --type=NodePort --port=8080 --name=webapp-service --dry-run -o yaml > webapp-service.yaml
```

## Create a service to expose port
`$ kubectl expose pod redis --port=6379 --name redis-service`

## Port forward a service
`$ kubectl port-forward svc/demo 8080:8080`
