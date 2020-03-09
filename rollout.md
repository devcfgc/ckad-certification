## Rollout commands
```
$ kubectl rollout status deployment/myapp-deployment
$ kubectl rollout history deployment/myapp-deployment
```
## Deployment definition file - deployment-definition.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
  labels:
    app: myapp
    type: front-end
spec:
  replicas: 3
  selector:
    matchLabels:
      type: front-end
  template:
    metadata:
      labels:
        app: myapp
        type: front-end
    spec:
      containers:
      - name: nginx-container
        image: nginx:1.7.9
```
```
$ kubectl apply -f deployment-definition.yaml
$ kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1
```

## Rollback
```
$ kubectl rollout undo deployment/myapp-deployment
```

## EXAMPLE
- Simple deployment and inspect the rollout status and the rollout history.
  ```
  master $ kubectl create deployment nginx --image=nginx:1.16
  deployment.apps/nginx created
  
  master $ kubectl rollout status deployment nginx
  Waiting for deployment "nginx" rollout to finish: 0 of 1 updated replicas are available...
  deployment "nginx" successfully rolled out
  
  master $ kubectl rollout history deployment nginx
  deployment.extensions/nginx
  REVISION CHANGE-CAUSE
  1     <none>
  ```

- Using the --revision flag
  ```
  master $ kubectl rollout history deployment nginx --revision=1
  deployment.extensions/nginx with revision #1
  
  Pod Template:
  Labels: app=nginx    
          pod-template-hash=6454457cdb
  Containers:  
    nginx:
      Image:   nginx:1.16
    Port:    <none>
    Host Port: <none>
    Environment:    <none>
    Mounts:   <none>
  Volumes:   <none>
  ```

- Using the --record flag. You would have noticed that the "change-cause" field is empty in the rollout history output. We can use the --record flag to save the command used to create/update a deployment against the revision number.
  ```
  master $ kubectl set image deployment nginx nginx=nginx:1.17 --record
  deployment.extensions/nginx image updated
  
  master $ kubectl rollout history deployment nginx
  deployment.extensions/nginx
  
  REVISION CHANGE-CAUSE
  1     <none>
  2     kubectl set image deployment nginx nginx=nginx:1.17 --record=true
  ```

  Let's make some more changes. In the example below, we are editing the deployment and changing the image from nginx:1.17 to nginx:latest while making use of the --record flag.
  ```
  master $ kubectl edit deployments. nginx --record
  deployment.extensions/nginx edited
  
  master $ kubectl rollout history deployment nginx
  REVISION CHANGE-CAUSE
  1     <none>
  2     kubectl set image deployment nginx nginx=nginx:1.17 --record=true
  3     kubectl edit deployments. nginx --record=true
      
  master $ kubectl rollout history deployment nginx --revision=3
  deployment.extensions/nginx with revision #3
  
  Pod Template: Labels: app=nginx
                        pod-template-hash=df6487dc 
  Annotations: kubernetes.io/change-cause: kubectl edit deployments. nginx --record=true
  
  Containers:
    nginx:
    Image:   nginx:latest
    Port:    <none>
    Host Port: <none>
    Environment:    <none>
    Mounts:   <none>
  Volumes:   <none>
  ```

- Undo a change. Lets now rollback to the previous revision:
  ```
  master $ kubectl rollout undo deployment nginx
  deployment.extensions/nginx rolled back
  
  master $ kubectl rollout history deployment nginx
  deployment.extensions/nginxREVISION CHANGE-CAUSE
  1     <none>
  3     kubectl edit deployments. nginx --record=true
  4     kubectl set image deployment nginx nginx=nginx:1.17 --record=true

  
  master $ kubectl rollout history deployment nginx --revision=4
  deployment.extensions/nginx with revision #4Pod Template:
  Labels:    app=nginx    pod-template-hash=b99b98f9
  Annotations: kubernetes.io/change-cause: kubectl set image deployment nginx nginx=nginx:1.17 --record=true
  Containers:
    nginx:
    Image:   nginx:1.17
    Port:    <none>
    Host Port: <none>
    Environment:    <none>
    Mounts:   <none>
  Volumes:   <none>
  
  
  master $ kubectl describe deployments. nginx | grep -i image:
    Image:    nginx:1.17
  ```