## Create an NGINX Pod
`kubectl run --generator=run-pod/v1 nginx --image=nginx`

## Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)
`kubectl run --generator=run-pod/v1 nginx --image=nginx --dry-run -o yaml`

## Create a deployment
`kubectl run --generator=deployment/v1beta1 nginx --image=nginx`

Or the newer recommended way:

`kubectl create deployment --image=nginx nginx`

## Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)

`kubectl run --generator=deployment/v1beta1 nginx --image=nginx --dry-run -o yaml`

Or

`kubectl create deployment --image=nginx nginx --dry-run -o yaml`

## Generate Deployment YAML file (-o yaml). Don't create it(--dry-run) with 4 Replicas (--replicas=4)
`kubectl run --generator=deployment/v1beta1 nginx --image=nginx --dry-run --replicas=4 -o yaml`

> `kubectl create deployment` does not have a `--replicas` option. You could first create it and then scale it using the kubectl scale command.

## Save it to a file - (If you need to modify or add some other details)
`kubectl run --generator=deployment/v1beta1 nginx --image=nginx --dry-run --replicas=4 -o yaml > nginx-deployment.yaml`

## Create a Service named nginx of type NodePort and expose it on port 30080 on the nodes:
`kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run -o yaml`