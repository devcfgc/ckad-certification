## Volumes Types
- emptyDir
    An empty Volume is created for the Pod as soon as it is scheduled on the worker node. The Volume's life is tightly coupled with the Pod. If the Pod is terminated, the content of emptyDir is deleted forever.  
- hostPath
    With the hostPath Volume Type, we can share a directory from the host to the Pod. If the Pod is terminated, the content of the Volume is still available on the host.
- gcePersistentDisk
    With the gcePersistentDisk Volume Type, we can mount a Google Compute Engine (GCE) persistent disk into a Pod.
- awsElasticBlockStore
    With the awsElasticBlockStore Volume Type, we can mount an AWS EBS Volume into a Pod. 
- azureDisk
    With azureDisk we can mount a Microsoft Azure Data Disk into a Pod.
-   azureFile
    With azureFile we can mount a Microsoft Azure File Volume into a Pod.
- cephfs
    With cephfs, an existing CephFS volume can be mounted into a Pod. When a Pod terminates, the volume is unmounted and the contents of the volume are preserved.
- nfs
    With nfs, we can mount an NFS share into a Pod.
- iscsi
    With iscsi, we can mount an iSCSI share into a Pod.
- secret
    With the secret Volume Type, we can pass sensitive information, such as passwords, to Pods. We will take a look at an example in a later chapter.
- configMap
    With configMap objects, we can provide configuration data, or shell commands and arguments into a Pod.
- persistentVolumeClaim
    We can attach a PersistentVolume to a Pod using a persistentVolumeClaim. We will cover this in our next section.

## PersistentVolumes
K8s provides APIs for users and administrators to manage and consume persistent storage. To manage the Volume, it uses the PersistentVolume API resource type, and to consume it, it uses the PersistentVolumeClaim API resource type. A Persistent Volume is a network-attached storage in the cluster, which is provisioned by the administrator.

PersistentVolumes can be dynamically provisioned based on the StorageClass resource. A StorageClass contains pre-defined provisioners and parameters to create a PersistentVolume. Using PersistentVolumeClaims, a user sends the request for dynamic PV creation, which gets wired to the StorageClass resource.

Some of the Volume Types that support managing storage using PersistentVolumes are:

- GCEPersistentDisk
- AWSElasticBlockStore
- AzureFile
- AzureDisk
- CephFS
- NFS
- iSCSI.

## PersistentVolumeClaims
A PersistentVolumeClaim (PVC) is a request for storage by a user. Users request for PersistentVolume resources based on type, access mode, and size. There are three access modes: ReadWriteOnce (read-write by a single node), ReadOnlyMany (read-only by many nodes), and ReadWriteMany (read-write by many nodes). Once a suitable PersistentVolume is found, it is bound to a PersistentVolumeClaim. Once a user finishes its work, the attached PersistentVolumes can be released. The underlying PersistentVolumes can then be reclaimed (for an admin to verify and/or aggregate data), deleted (both data and volume are deleted), or recycled for future usage (only data is deleted). 
