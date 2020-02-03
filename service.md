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