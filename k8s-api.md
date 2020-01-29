# Kubernetes API Resources

Kubernetes uses declarative API which makes the system more robust. But, this means that we create an object using CLI or REST to represent what we want the system to do.

## Get all API resources supported by your K8s cluster:
```
$ kubectl api-resources -o wide
```

## Get API resources for a particular API group:
```
$ kubectl api-resources --api-group apps -o wide
```

## Get more info about the particular resource:
```
$ kubectl explain configmap
```

## Get all API versions supported by your K8s cluster:
```
$ kubectl api-versions
```

## Check if a particular group/version is available for some resource:
```
$ kubectl get deployments.v1.apps -n kube-system
```