
Operating System Upgrades within a Kubernetes Cluster

https://linuxacademy.com/cp/courses/lesson/course/4015/lesson/2/module/327

Evict the pods on a node
kubectl drain [node_name] --ignore-daemonsets

Schedule pods to the node after maintenance is complete
kubectl uncordon [node_name]

Remove a node from the cluster:
kubectl delete node [node_name]

Generate a new token:
sudo kubeadm token generate

List the tokens:
sudo kubeadm token list

Print the kubeadm join command to join a node to the cluster:
sudo kubeadm token create [token_name] --ttl 2h --print-join-command



Backing Up and Restoring a Kubernetes Cluster

https://linuxacademy.com/cp/courses/lesson/course/4015/lesson/3/module/327


Get the etcd binaries:
wget https://github.com/etcd-io/etcd/releases/download/v3.3.12/etcd-v3.3.12-linux-amd64.tar.gz

Unzip the compressed binaries:
tar xvf etcd-v3.3.12-linux-amd64.tar.gz

Move the files into /usr/local/bin:
sudo mv etcd-v3.3.12-linux-amd64/etcd* /usr/local/bin

Take a snapshot of the etcd datastore using etcdctl:
sudo ETCDCTL_API=3 etcdctl snapshot save snapshot.db --cacert /etc/kubernetes/pki/etcd/ca.crt --cert /etc/kubernetes/pki/etcd/server.crt --key /etc/kubernetes/pki/etcd/server.key

View the help page for etcdctl:
ETCDCTL_API=3 etcdctl --help

Browse to the folder that contains the certificate files:
cd /etc/kubernetes/pki/etcd/

View that the snapshot was successful:
ETCDCTL_API=3 etcdctl --write-out=table snapshot status snapshot.db

Zip up the contents of the etcd directory:
sudo tar -zcvf etcd.tar.gz /etc/kubernetes/pki/etcd

Copy the etcd directory to another server:
scp etcd.tar.gz cloud_user@18.219.235.42:~/




LAB
https://app.linuxacademy.com/hands-on-labs/3fcb7ee1-5fd0-48e5-833d-71071433141c?redirect_uri=https:%2F%2Flinuxacademy.com%2Fcp%2Fmodules%2Fview%2Fid%2F327




Look at the iptables rules for your services:

sudo iptables-save | grep KUBE | grep nginx
