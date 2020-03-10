# General
```
$ alias k=kubectl
$ source <(kubectl completion bash)
$ echo "source <(kubectl completion bash)" >> ~/.bashrc
$ sudo -i

$ KUBE_EDITOR=vim kubectl edit deploy nginx
$ export KUBE_EDITOR=vim

$ kubectl config get-contexts                # display list of contexts 
$ kubectl config current-context             # display the current-context
$ kubectl config use-context my-cluster-name # set the default context to my-cluster-name

# permanently save the namespace for all subsequent kubectl commands in that context.
$ kubectl config set-context $(kubectl config current-context) --namespace=dev
$ kubectl config set-context --current --namespace=ggckad-s2

$ kubectl explain cronjob.spec.jobTemplate --recursive

$ kubectl get pod my-pod -o yaml --export > pod-definition.yaml # Get a pod's YAML without 
$ kubectl get pod <pod-name> -o yaml > pod-definition.yaml
$ kubectl get po --show-labels
$ kubectl get deploy --show-labels

$ kubectl exec -it <POD_NAME> -- /bin/bash
$ kubectl exec -it <POD_NAME> --container <CONTAINER_NAME> -- /bin/bash

# Compares the current state of the cluster against the state that the cluster would be in if the manifest was applied.
$ kubectl diff -f ./my-manifest.yaml

# Delete a pod without any delay (force delete)
$ kubectl delete po nginx --grace-period=0 --force

$ kubectl exec webapp cat /log/app.log # Execute commands inside a POD

# get pods sorted by
$ kubectl get pods --sort-by=.metadata.name
$ kubectl get pods --sort-by=.metadata.creationTimestamp
```

## Get metrics
```
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

kubectl run # without flag creates a deployment
kubectl run — restart=Never #Creates a Pod
kubectl run — restart=OnFailure #Creates a job
kubectl run — restart=OnFailure — schedule=”* * * * *” # Creates a cronjob

# POD
$ kubectl run nginx --image=nginx --restart=Never --port=80 --dry-run -o yaml > pod.yaml # Run pod nginx and write its spec into a file called pod.yaml
$ kubectl run busybox --image=busybox --restart=Never -- /bin/sh -c "sleep 3600"

# DEPLOYMENT
$ kubectl run redis --image=redis:alpine -l tier=db
$ kubectl run nginx --image=nginx --dry-run -o yaml
$ kubectl run nginx --image=nginx --replicas=4 --dry-run -o yaml > nginx-deployment.yaml
$ kubectl run wordpress --image=wordpress --restart=Never --requests=cpu=200,memory=250Mi --limits=cpu=400m,memory=500Mi

$ kubectl create deployment --image=nginx nginx -l app=nginx --dry-run -o yaml # does not have a `--replicas` option. You could first create it and then scale it using the kubectl scale command.
$ kubectl scale deployment/nginx --replicas=3

# SERVICES
$ kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run -o yaml
$ kubectl expose pod redis --port=6379 --name redis-service
$ kubectl expose rc nginx --port=80 --target-port=8000
$ kubectl expose deploy webapp --type=NodePort --port=8080 --name=webapp-service --dry-run -o yaml > webapp-service.yaml
$ kubectl port-forward svc/webapp-service 8080:8080

# The command below finds the allocated port and executes a HTTP request.
$ export PORT=$(kubectl get svc webapp-service -o go-template='{{range.spec.ports}}{{if .nodePort}}{{.nodePort}}{{"\n"}}{{end}}{{end}}')
$ echo "Accessing host01:$PORT"
$ curl host01:$PORT

# JOB
$ kubectl run my-job --image=busybox --restart=OnFailure -- /bin/sh -c "sleep 4800"

# CRONJOB
$ kubectl run my-cronjob --image=busybox --restart=OnFailure --schedule="*/1 * * * *" -- date
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

## Set POD's date
`$ kubectl exec -it POD-NAME -- date -s '19 APR 2012 11:14:00'`