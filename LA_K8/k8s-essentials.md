# K8S INSTALLATION

## Common commands for MASTER + NODES
```
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

$ sudo apt-get update
$ sudo apt-get install -y docker-ce=18.06.1~ce~3-0~ubuntu
$ sudo apt-mark hold docker-ce

$ curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

$ cat << EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

$ sudo apt-get update
$ sudo apt-get install -y kubelet=1.13.5-00 kubeadm=1.13.5-00 kubectl=1.13.5-00
$ sudo apt-mark hold kubelet kubeadm kubectl
```

## Bootstrapping the Cluster
- On the Kube master node, initialize the cluster: `sudo kubeadm init --pod-network-cidr=10.244.0.0/16`
- On the Kube master node, set up the local kubeconfig:
```
$ mkdir -p $HOME/.kube
$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$ sudo chown $(id -u):$(id -g) $HOME/.kube/config
$ kubectl version
```
- The kubeadm init command should output a kubeadm join command containing a token and hash. Copy that command and run it with sudo on both worker nodes. It should look something like this: `sudo kubeadm join $some_ip:6443 --token $some_token --discovery-token-ca-cert-hash $some_hash`
- Verify that all nodes have successfully joined the cluster:
```
$kubectl get nodes

NAME                            STATUS     ROLES    AGE     VERSION
devcfgc1c.mylabserver.com   NotReady   master   4m43s   v1.12.7
devcfgc2c.mylabserver.com   NotReady   <none>   19s     v1.12.7
devcfgc3c.mylabserver.com   NotReady   <none>   10s     v1.12.7
```

## Configuring Networking with Flannel
- On all three nodes, run the following:
```
$ echo "net.bridge.bridge-nf-call-iptables=1" | sudo tee -a /etc/sysctl.conf
$ sudo sysctl -p
```
- Install Flannel in the cluster by running this only on the Master node: 
`kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml`
- Verify that all the nodes now have a STATUS of Ready:
```
$ kubectl get nodes

NAME                            STATUS   ROLES    AGE     VERSION
devcfgc1c.mylabserver.com   Ready    master   11m     v1.12.7
devcfgc2c.mylabserver.com   Ready    <none>   7m6s    v1.12.7
devcfgc3c.mylabserver.com   Ready    <none>   6m57s   v1.12.7

$ kubectl get pods -n kube-system
```

## Playing around
- Create a simple pod running an nginx container:
```
cat << EOF | kubectl create -f -
apiVersion: v1
kind: Pod
metadata:
name: nginx
spec:
containers:
- name: nginx
    image: nginx
EOF
```
- Get a list of pods and verify that your new nginx pod is in the Running state: `kubectl get pods`
- Get more information about your nginx pod: `kubectl describe pod nginx`
- Delete the pod: `kubectl delete pod nginx`

## Networking in Kubernetes
- Create a deployment with two nginx pods:
```
cat << EOF | kubectl create -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  replicas: 2
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
        image: nginx:1.15.4
        ports:
        - containerPort: 80
EOF
```
- Create a busybox pod to use for testing:
```
cat << EOF | kubectl create -f -
apiVersion: v1
kind: Pod
metadata:
  name: busybox
spec:
  containers:
  - name: busybox
    image: radial/busyboxplus:curl
    args:
    - sleep
    - "1000"
EOF
```
- Get the IP addresses of your pods: `kubectl get pods -o wide`
- Get the IP address of one of the nginx pods, then contact that nginx pod from the busybox pod using the nginx pod's IP address: `kubectl exec busybox -- curl $nginx_pod_ip`

## Deployments
- Create a deployment:
```
cat <<EOF | kubectl create -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 2
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
        image: nginx:1.15.4
        ports:
        - containerPort: 80
EOF
```
- Get a list of deployments: `kubectl get deployments`
- Get more information about a deployment: `kubectl describe deployment nginx-deployment`
- Get a list of pods: `kubectl get pods`

##  Kubernetes Services
- Create a NodePort service on top of your nginx pods:
```
cat << EOF | kubectl create -f -
kind: Service
apiVersion: v1
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
    nodePort: 30080
  type: NodePort
EOF
```
- Get a list of services in the cluster. `kubectl get svc` You should see your service called nginx-service.
- Since this is a NodePort service, you should be able to access it using port 30080 on any of your cluster's servers. You can test this with the command: `curl localhost:30080` You should get an HTML response from nginx!
