## Patch some parameter
```
$ kubectl patch cronjob JOB-NAME -p '{"spec":{"suspend":false}}'
```

## TTL Controller for Finished Resources
#### https://kubernetes.io/docs/concepts/workloads/controllers/ttlafterfinished/
```
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  ttlSecondsAfterFinished: 20
  template:
    spec:
      containers:
      - name: pi
        image: perl
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4
```