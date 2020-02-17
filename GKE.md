# Using Google Kubernetes Engine (GKE)
You will then be able to follow the [GKE quickstart guide](https://cloud.google.com/kubernetes-engine/docs/quickstart) and you will be ready to create your first Kubernetes cluster:
```
$ gcloud init
$ gcloud projects create devcfgc-ckad
$ gcloud config set project devcfgc-ckad
$ gcloud container clusters create linuxfoundation --region=europe-west1-d
$ gcloud container clusters list
$ kubectl get nodes
```

Once you are done, **do not forget to delete your cluster**, otherwise you will keep on getting charged for it:
```
$ gcloud container clusters delete linuxfoundation --region=europe-west1-d
```
