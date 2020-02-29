`vim pizza-deploy.yaml`

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pizza-deployment
  namespace: pizza
spec:
  replicas: 3
  selector:
    matchLabels:
      app: pizza
  template:
    metadata:
      labels:
        app: pizza
    spec:
      containers:
      - name: pizza
        image: linuxacademycontent/pizza-service:1.14.6
        command: ["nginx"]
        args: ["-g", "daemon off;", "-q"]
        ports:
        - containerPort: 80
        livenessProbe:
          httpGet:
            path: /healthz
            port: 8081
        readinessProbe:
          httpGet:
            path: /
            port: 80
```

`kubectl apply -f pizza-deploy.yaml`

`vim pizza-svc.yaml`

```
apiVersion: v1
kind: Service
metadata:
  name: pizza-service
  namespace: pizza
spec:
  type: NodePort
  selector:
    app: pizza
  ports:
  - protocol: TCP
    port: 80
    nodePort: 30080
```

`kubectl apply -f pizza-svc.yaml`
