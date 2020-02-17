# Installing with kubeadm
Once you become familiar with Kubernetes using Minikube, you may want to start building a real cluster. Currently, the most straightforward method is to use kubeadm, which appeared in Kubernetes v1.4.0, and can be used to bootstrap a cluster quickly. As the community has focused on kubeadm, it has moved from beta to stable and added high availability with v1.15.0.

The Kubernetes website provides documentation on [how to use kubeadm to create a cluster](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/).

Package repositories are available for Ubuntu 18.04 and CentOS 7.1. Packages have not yet been made available for Ubuntu 18.04, but will work as you will see in the lab exercises.

To join other nodes to the cluster, you will need at least one token and an SHA256 hash. This information is returned by the command kubeadm init. Once the master has initialized, you would apply a network plugin. Main steps:

Run kubeadm init on the head node.

Create a network for IP-per-Pod criteria.

Run kubeadm join --token token head-node-IP on worker nodes.
You can also create the network with kubectl by using a resource manifest of the network.

For example, to use the Weave network, you would do the following: 

$ kubectl create -f https://git.io/weave-kube 

Once all the steps are completed, workers and other master nodes joined, you will have a functional multi-node Kubernetes cluster, and you will be able to use kubectl to interact with it.