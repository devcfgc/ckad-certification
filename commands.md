# Imperative commands
## Create an Pod
```
$ kubectl run --generator=run-pod/v1 nginx --image=nginx
$ kubectl run --generator=run-pod/v1 redis --image=redis:alpine -l tier=db
```

## Get POD definition into YAML
`kubectl get pod webapp-color -o yaml > temp1.yaml`

## Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)
`kubectl run --generator=run-pod/v1 nginx --image=nginx --dry-run -o yaml`

## Set POD date
`kubectl exec -it POD-NAME -- date -s '19 APR 2012 11:14:00'`

## Create a deployment
`kubectl run --generator=deployment/v1beta1 nginx --image=nginx`

Or the newer recommended way:

```
kubectl create deployment --image=nginx nginx
kubectl create deployment webapp --image=kodekloud/webapp-color
```

## Change image from deployment
`kubectl set image deployment/nginx nginx=nginx:1.9.1 --record`

## Scale deployment
`kubectl scale deployment/webapp --replicas=3`

## Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)

`kubectl run --generator=deployment/v1beta1 nginx --image=nginx --dry-run -o yaml`

Or

`kubectl create deployment --image=nginx nginx --dry-run -o yaml`

## Generate Deployment YAML file (-o yaml). Don't create it(--dry-run) with 4 Replicas (--replicas=4)
`kubectl run --generator=deployment/v1beta1 nginx --image=nginx --dry-run --replicas=4 -o yaml`

> `kubectl create deployment` does not have a `--replicas` option. You could first create it and then scale it using the kubectl scale command.

## Save it to a file - (If you need to modify or add some other details)
`kubectl run --generator=deployment/v1beta1 nginx --image=nginx --dry-run --replicas=4 -o yaml > nginx-deployment.yaml`

## Create a Service of type NodePort and expose it:
```
$ kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run -o yaml
$ kubectl expose deployment webapp --type=NodePort --port=8080 --name=webapp-service --dry-run -o yaml > webapp-service.yaml
```

## Create a service to expose port
`kubectl expose pod redis --port=6379 --name redis-service`
