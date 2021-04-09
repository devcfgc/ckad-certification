# General
```
alias kgp="k get po" && alias  ka="kubectl apply -f" \
    && alias kdf="k delete --grace-period=0 --force" \
    && alias kdr="k --dry-run=client -oyaml"
export KUBE_EDITOR=vim
echo "set nu et ic ts=2 sts=2 sw=2" > ~/.vimrc

$ kubectl config get-contexts                # display list of contexts
$ kubectl config current-context             # display the current-context
$ kubectl config use-context my-cluster-name # set the default context to my-cluster-name

# permanently save the namespace for all subsequent kubectl commands in that context.
$ kubectl config set-context $(kubectl config current-context) --namespace=dev
$ kubectl config set-context --current --namespace=ggckad-s2
```

## POD
Create an NGINX Pod
`kubectl run nginx --image=nginx`

Generate POD Manifest YAML file
`kubectl run nginx --image=nginx --dry-run=client -o yaml`

## LOGS
`kubectl logs nginx -f --previous`


## Deployment
Create a deployment
`kubectl create deployment --image=nginx nginx`

Generate Deployment YAML file
`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml`

IMPORTANT:
kubectl create deployment does not have a --replicas option. You could first create it and then scale it using the kubectl scale command or Save it to a file.
`kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml`


## Service
Create a Service named redis-service of type ClusterIP to expose pod redis on port 6379
`kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml`
(This will automatically use the pod's labels as selectors)

Or

`kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml` (This will not use the pods labels as selectors, instead it will assume selectors as app=redis. You cannot pass in selectors as an option. So it does not work very well if your pod has a different label set. So generate the file and modify the selectors before creating the service)

Create a Service named nginx of type NodePort to expose pod nginx's port 80 on port 30080 on the nodes:
`kubectl expose pod nginx --port=80 --name nginx-service --type=NodePort --dry-run=client -o yaml`
(This will automatically use the pod's labels as selectors, but you cannot specify the node port. You have to generate a definition file and then add the node port in manually before creating the service with the pod.)

Or

`kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run=client -o yaml`
(This will not use the pods labels as selectors)


## kubectl exec - Execute a command against a container in a pod.
Get output from running 'date' from pod <pod-name>. By default, output is from the first container.
`kubectl exec <pod-name> -- date`

Get output from running 'date' in container <container-name> of pod <pod-name>.
`kubectl exec <pod-name> -c <container-name> -- date`

Get an interactive TTY and run /bin/bash from pod <pod-name>. By default, output is from the first container.
`kubectl exec -ti <pod-name> -- /bin/bash`



```
kubectl run some-deployment-name --image=some-image --dry-run -o yaml > deployment.yml
kubectl run some-pod-name --restart=Never --image=some-image --dry-run -o yaml > pod.yml
kubectl run some-job-name --restart=OnFailure --image=some-image --dry-run -o yaml > job.yml
kubectl run some-cron-name --schedule="*/1 * * * *" --restart=OnFailure --image=some-image --dry-run -o yaml > cron.yml

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

## Imperative commands
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
kubectl run busybox --image=busybox --dry-run -o yaml -- sh -c 'sleep 3600' > test.yaml

# POD
$ kubectl run nginx --image=nginx --restart=Never --port=80 --dry-run -o yaml > pod.yaml # Run pod nginx and write its spec into a file called pod.yaml
$ kubectl run busybox --image=busybox --restart=Never -- /bin/sh -c "sleep 3600"
$ kubectl run wordpress --image=wordpress --restart=Never --requests=cpu=200,memory=250Mi --limits=cpu=400m,memory=500Mi

# DEPLOYMENT
$ kubectl run redis --image=redis:alpine -l tier=db
$ kubectl run nginx --image=nginx --dry-run -o yaml
$ kubectl run nginx --image=nginx --replicas=4 --dry-run -o yaml > nginx-deployment.yaml

$ kubectl run busybox --image=busybox --restart=Never --dry-run -o yaml -- bin/sh -c "sleep 3600; ls" > multi-container.yaml

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
$ kubectl set env deployment/my_deployment DATABASE_URL=connection_string_to_db

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

## DEBUG
`kubectl run -i --tty busybox --image=busybox --restart=Never -- sh`

## NODES
```
$ kubectl drain <NODE_NAME>    # safely evict all of your pods from a node
$ kubectl drain <NODE_NAME> --ignore-daemonsets --force
$ kubectl cordon <NODE_NAME>   # mark a Node unschedulable
$ kubectl uncordon <NODE_NAME> # schedule new pods onto the node
```

## ETCD
To make use of etcdctl for tasks such as back up and restore, make sure that you set the ETCDCTL_API to 3. You can do this by exporting the variable ETCDCTL_API prior to using the etcdctl client. This can be done as follows: `export ETCDCTL_API=3`

### ETCD BACKUPS
https://github.com/mmumshad/kubernetes-cka-practice-test-solution-etcd-backup-and-restore
