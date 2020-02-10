1. Let’s update the nginx Pods (`kubectl apply -f controller/nginx-deployment.yaml`) to use the nginx:1.9.1 image instead of the nginx:1.7.9 image. Use the following command `kubectl set image deployment/nginx-deployment nginx=nginx:1.9.1 --record` or `kubectl edit deploy/nginx-deployment`
2. To see the rollout status, run: `kubectl rollout status deploy/nginx-deployment`
3. `kubectl get rs`
4. `kubectl get pods`
5. `kubectl describe deployments`
6. Check the rollout history: `kubectl rollout history deploy/nginx-deployment`
7. Check a rollout history revision: `kubectl rollout history deploy/nginx-deployment --revision 1`
8. Undo the current rollout and rollback to the previous revision: `kubectl rollout undo deploy/nginx-deployment`
9. Rollback to a specific revision by specifying it with --to-revision: `kubectl rollout undo deploy/nginx-deployment --to-revision=2`
10. Scale a Deployment by using the following command: `kubectl scale deploy/nginx-deployment --replicas=10`
11. Pause a deployment by running the following command: `kubectl rollout pause deploy/nginx-deployment`
12. Eventually, resume the Deployment: `kubectl rollout resume deploy/nginx-deployment`
14. Expose a Deployment with the kubectl expose command: `kubectl expose deployment webserver --name=web-service --type=NodePort`

## Examples
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-app
spec:
  replicas: 3
  minReadySeconds:10
  selector:
    matchLabels:
      app: node-app
  template:
    metadata:
      labels:
        app: node-app
    spec:
      containers:
      - name: node-app
        image: node-app:1.0
        ports:
        - containerPort: 80
```