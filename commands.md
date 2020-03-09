# General
```
$ alias k=kubectl
$ source <(kubectl completion bash)
$ echo "source <(kubectl completion bash)" >> ~/.bashrc
$ sudi -i

$ KUBE_EDITOR=nano kubectl edit deploy nginx

$ kubectl config get-contexts                # display list of contexts 
$ kubectl config current-context             # display the current-context
$ kubectl config use-context my-cluster-name # set the default context to my-cluster-name

# permanently save the namespace for all subsequent kubectl commands in that context.
$ kubectl config set-context $(kubectl config current-context) --namespace=dev
$ kubectl config set-context --current --namespace=ggckad-s2

$ kubectl explain cronjob.spec.jobTemplate --recursive

$ kubectl get pod my-pod -o yaml --export # Get a pod's YAML without cluster specific information
$ kubectl get pod <pod-name> -o yaml > pod-definition.yaml
$ kubectl get po --show-labels
$ kubectl get deploy --show-labels

$ kubectl exec -it <POD_NAME> -- /bin/bash
$ kubectl exec -it <POD_NAME> --container <CONTAINER_NAME> -- /bin/bash

# Compares the current state of the cluster against the state that the cluster would be in if the manifest was applied.
$ kubectl diff -f ./my-manifest.yaml

$ kubectl top node
$ kubectl top node my-node # Show metrics for a given node
$ kubectl top po
$ kubectl top po my-pod # Show metrics for a given pod
$ kubectl top po --containers

```
# Imperative commands
```
$ kubectl cluster-info
$ kubectl get all
$ kubectl run [container-name] --image=[image-name]
$ kubectl port-forward [pod] [ports]
```

## Create a some resources
```
$ kubectl create namespace ny-namespace
$ kubectl create configmap nginx-configuration --namespace my-namespace
$ kubectl create serviceaccount ingress-serviceaccount --namespace my-namespace
$ kubectl get roles,rolebindings --namespace my-namespace

$ kubectl create quota myrq --hard=cpu=1,memory=1G,pods=2 --dry-run -o yaml

$ kubectl run nginx --image=nginx --generator=run-pod/v1
$ kubectl run redis --image=redis:alpine -l tier=db --generator=run-pod/v1
$ kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > pod.yaml # Run pod nginx and write its spec into a file called pod.yaml

$ kubectl exec webapp cat /log/app.log # Execute commands inside a POD
$ kubectl exec webapp cat /log/app.log

$ kubectl create deployment webapp --image=kodekloud/webapp-color
$ kubectl scale deployment/webapp --replicas=3
$ kubectl expose deployment webapp --type=NodePort --port=8080 --name=webapp-service --dry-run -o yaml > webapp-service.yaml

$ kubectl create deployment --image=nginx nginx --save-config
$ kubectl create deployment webapp --image=example/webapp-color --save-config
$ kubectl create deployment demo --image=nginx --dry-run -oyaml > deployment.yaml
$ kubectl run blue --image=nginx --replicas=6
$ kubectl get deployments --show-labels
$ kubectl get deployments -l app=nginx
$ kubectl scale deployment/webapp --replicas=3
# Create a service for a replicated nginx, which serves on port 80 and connects to the containers on port 8000

$ kubectl expose pod redis --port=6379 --name redis-service
$ kubectl expose rc nginx --port=80 --target-port=8000
```

## Updating resources
```
$ kubectl set image deployment/nginx nginx=nginx:1.9.1 --record
$ kubectl set image deployment/frontend www=image:v2 # Rolling update "www" containers of "frontend" deployment, updating the image
$ kubectl rollout history deployment/frontend # Check history of deploy including the revision 
$ kubectl rollout undo deployment/frontend # Rollback to the previous deployment
$ kubectl rollout undo deployment/frontend --to-revision=2 # Rollback to a specific revision
$ kubectl rollout status -w deployment/frontend # Watch rolling update status of "frontend" deployment until completion
$ kubectl rollout restart deployment/frontend # Rolling restart of the "frontend" deployment

```

## Get POD definition into YAML
```
$ kubectl get pod webapp-color -oyaml > temp1.yaml
```

## Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)
```
$ kubectl run nginx --image=nginx --generator=run-pod/v1 --dry-run -o yaml
```

## Set POD's date
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

The command below finds the allocated port and executes a HTTP request.
```
$ export PORT=$(kubectl get svc webapp-service -o go-template='{{range.spec.ports}}{{if .nodePort}}{{.nodePort}}{{"\n"}}{{end}}{{end}}')
$ echo "Accessing host01:$PORT"
$ curl host01:$PORT
```

## Create a service to expose port
`$ kubectl expose pod redis --port=6379 --name redis-service`

## Port forward a service
`$ kubectl port-forward svc/demo 8080:8080`
