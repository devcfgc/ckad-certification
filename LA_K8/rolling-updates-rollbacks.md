Here is a sample deployment you can use to practice rolling updates and rollbacks.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rolling-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.1
        ports:
        - containerPort: 80
```

Perform a rolling update. `kubectl set image deployment/rolling-deployment nginx=nginx:1.7.9 --record`

Explore the rollout history of the deployment.
```
kubectl rollout history deployment/rolling-deployment

kubectl rollout history deployment/rolling-deployment --revision=2
```

You can roll back to the previous revision like so. `kubectl rollout undo deployment/rolling-deployment`

You can also roll back to a specific earlier revision by providing the revision number. `kubectl rollout undo deployment/rolling-deployment --to-revision=1`

You can also control how rolling updates are performed by setting maxSurge and maxUnavailable in the deployment spec:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rolling-deployment
spec:
  strategy:
    rollingUpdate:
      maxSurge: 3
      maxUnavailable: 2
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.1
        ports:
        - containerPort: 80
```

## Examples
Your company's developers have just finished developing a new version of their candy-themed mobile game. They are ready to update the backend services that are running in your Kubernetes cluster. There is a deployment in the cluster managing the replicas for this application. The deployment is called candy-deployment. You have been asked to update the image for the container named candy-ws in this deployment template to a new version, linuxacademycontent/candy-service:3.

After you have updated the image using a rolling update, check on the status of the update to make sure it is working. If it is not working, perform a rollback to the previous state.

```
$ kubectl set image deployment/candy-deployment candy-ws=linuxacademycontent/candy-service:3 --record

# Check the progress of the rolling update:
$ kubectl rollout status deployment/candy-deployment
```

In this scenario, the rolling update should fail. This is because the specified image, linuxacademycontent/candy-service:3, does not exist. Roll back to the previous version to get things working again.

Get a list of previous revisions. `kubectl rollout history deployment/candy-deployment`

Undo the last revision. `kubectl rollout undo deployment/candy-deployment`

Check the status of the rollout. `kubectl rollout status deployment/candy-deployment`