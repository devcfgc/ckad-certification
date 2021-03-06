# Services
With every object and agent decoupled we need a flexible and scalable agent which connects resources together and will reconnect, should something die and a replacement is spawned. Each Service is a microservice handling a particular bit of traffic, such as a single NodePort or a LoadBalancer to distribute inbound requests among many Pods.

A Service also handles access policies for inbound requests, useful for resource control, as well as for security.

A service, as well as **kubectl**, uses a selector in order to know which objects to connect. There are two selectors currently supported:

* Equality-based
  Filters by label keys and their values. Three operators can be used, such as =, ==, and !=. If multiple values or keys are used, all must be included for a match.
* Set-based
  Filters according to a set of values. The operators are **in, notin**, and **exists**. For example, the use of

  **status notin (dev, test, maint)**

  would select resources with the key of **status** which did not have a value of **dev, test**, nor **maint**.

## ClusterIP
Exposes the Service on a cluster-internal IP. Choosing this value makes the Service only reachable from within the cluster. This is the default ServiceType
```
apiVersion: v1
kind: Service
metadata:
  name: back-end
spec:
  selector:
    app: myapp
    type: back-end
  ports:
    - targetPort: 80
      port: 80
      protocol: http
  type: ClusterIP
```
`$ kubectl create -f service-definition.yaml`

## Nodeport
 Exposes the Service on each Node’s IP at a static port (the NodePort). A ClusterIP Service, to which the NodePort Service routes, is automatically created. You’ll be able to contact the NodePort Service, from outside the cluster, by requesting <NodeIP>:<NodePort>
```
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
    type: front-end
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30008
      protocol: http
  type: NodePort
```
`$ kubectl create -f service-definition.yaml`

## LoadBalancer
Exposes the Service externally using a cloud provider’s load balancer. NodePort and ClusterIP Services, to which the external load balancer routes, are automatically created

## ExternalName
 Maps the Service to the contents of the externalName field (e.g. foo.bar.example.com), by returning a CNAME record

 ## Headless service
 ```
apiVersion: v1
kind: Service
metadata:
  name: mysql-h
spec:
  selector:
    app: mysql
  ports:
    - port: 3306
  clusterIP: none
```
```
kind: Pod 
apiVersion: v1 
metadata:
  name: myapp-pod
  lables:
    app: mysql
spec:
  containers:
    - name: mysql
      image: mysql
  subdomain: mysql-h # Same name as the service - mysql-pod.mysql-h.default.svc.cluster.local
  hostname: mysql-pod
```
```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql-deployment
  labels:
      app: mysql
spec:
  serviceName: mysql-h
  replicas: 3
  matchLabels:
     app: mysql
  template:
    metadata:
    name: myapp-pod
      labels:
        app: mysql
  spec:
    containers:
    - name: mysql
      image: mysql
```