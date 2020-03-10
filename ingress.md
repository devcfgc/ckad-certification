
## https://kubernetes.github.io/ingress-nginx/

## Name-Based Virtual Hosting ingress definition
```
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: virtual-host-ingress
  namespace: default
spec:
  rules:
  - host: blue.example.com
    http:
      paths:
      - backend:
          serviceName: webserver-blue-svc
          servicePort: 80
  - host: green.example.com
    http:
      paths:
      - backend:
          serviceName: webserver-green-svc
          servicePort: 80
```

## Fanout ingress definition
```
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: fan-out-ingress
  namespace: default
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /blue
        backend:
          serviceName: webserver-blue-svc
          servicePort: 80
      - path: /green
        backend:
          serviceName: webserver-green-svc
          servicePort: 80
```

## What is the rewrite-target option?
Our gift app displays the gifts webpage at `http://<gifts-service>:<port>/`
Our plane app displays the planes webpage at `http://<planes-service>:<port>/`

What needs to be achieved:

```
http://<ingress-service>:<ingress-port>/gifts` --> `http://<gifts-service>:<port>/
http://<ingress-service>:<ingress-port>/planes --> http://<planes-service>:<port>/
```


Without the rewrite-target option, this is what would happen:

```
http://<ingress-service>:<ingress-port>/gifts --> http://<gifts-service>:<port>/gifts
http://<ingress-service>:<ingress-port>/planes` --> `http://<planes-service>:<port>/planes
```

The target applications are not configured with /gifts or /planes paths.

To fix that we want to **"ReWrite"** the URL when the request is passed on to the gifts or planes applications. We don't want to pass in the same path that user typed in. So we specify the rewrite-target option. This rewrites the URL by replacing whatever is under rules->http->paths->path.

```
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-gifts-planes
  namespace: app-space
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
  - http:
      paths:
      - path: /gifts
        backend:
          serviceName: gifts-service
          servicePort: 8080
      - path: /planes
        backend:
          serviceName: planes-service
          servicePort: 8080
```

### example rewrite-target
```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-ingress
  namespace: critical-space
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /gifts
        backend:
          serviceName: gifts-service
          servicePort: 8282
```

### In another example given here, this could also be:

`replace("/something(/|$)(.*)", "/$2")`

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$2
  name: rewrite
  namespace: default
spec:
  rules:
  - host: rewrite.bar.com
    http:
      paths:
      - backend:
          serviceName: http-svc
          servicePort: 80
        path: /something(/|$)(.*)
```