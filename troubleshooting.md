# ETCD
```
ps -aux | grep etcd
cat /etc/kubernetes/manifests/etcd.yaml
```


# KUBE-CONTROLLER
```
$ kubectl get pods -n kube-system
$ kubectl -n kube-system describe pod kube-controller-manager-master
$ kubectl -n kube-system logs kube-controller-manager-master

# kube-controller-manager is a static pod
# 1. cat /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
# 2. KUBELET CONFIG - cat /var/lib/kubelet/config.yaml
# 3. grep -i staticPodPath /var/lib/kubelet/config.yaml
# 4. cd /etc/kubernetes/manifests/
# 5. vim kube-controller-manager.yaml
```

# KUBE-SCHEDULER
```
$ ps -ef | grep kube-scheduler
$ kubectl get pods -n kube-system
$ kubectl -n kube-system describe pod kube-scheduler-master
$ kubectl -n kube-system logs kube-scheduler-master

# kube-scheduler is a static pod
# 1. cat /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
# 2. KUBELET CONFIG - cat /var/lib/kubelet/config.yaml
# 3. grep -i staticPodPath /var/lib/kubelet/config.yaml
# 4. cd /etc/kubernetes/manifests/
# 5. vim kube-scheduler.yaml
```

# WORKER NODE FAILURE
```
$ kubectl describe node worker-1
$ top
$ df -h
```
Check kubelet service
```
$ service kubelet status
$ systemctl status kubelet.service

$ sudo journalctl -u kubelet -f
$ cd /etc/systemd/system/kubelet.service.d/
$ cat 10-kubeadm.conf
$ cat /var/lib/kubelet/config.yaml
$ systemctl daemon-reload
$ systemctl restart kubelet
```
Check certificates
```
$ openssl x509 -in /var/lib/kubelet/worker-1.crt -text
```

# NETWORKING
```
$ ip link

$ ip addr
$ ip addr add 192.168.1.10/24 dev eth0

$ ip route
$ ip route add 192.168.1.0/24 via 192.168.2.1
$ ip route show default

$ cat /proc/sys/net/ipv4/ip_forward
$ arp
$ netstat -plnt
$ netstat -anp | grep etcd
```
