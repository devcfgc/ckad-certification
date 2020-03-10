## Job example
```
$ kubectl create job my-job --image=busybox -- echo "Hello I am from job"
$ kubectl create job hello-job --image=busybox --dry-run -o yaml -- echo "Hello I am from job" > hello-job.yaml
```

```
apiVersion: batch/v1
kind: Job
metadata:
  name: reporting-job
spec:
  completions: 3
  parallelism: 3
  template:
    spec:
      containers:
      - name: reporting-job
        image: reporting-job
      restartPolicy: Never
---
apiVersion: batch/v1
kind: Job
metadata:
  name: throw-dice-job
spec:
  backoffLimit: 15 # This is so the job does not quit before it succeeds.
  template:
    spec:
      containers:
      - name: math-add
        image: example/throw-dice
      restartPolicy: Never
```

## CronJob example
`$ kubectl create cronjob my-cronjob --image=busybox --schedule="*/1 * * * *" -- bin/sh -c "date; echo Hello from kubernetes cluster"`

```
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: reporting-job
spec:
  schedule: "*/1 * * * *"
  jobtemplate:
    spec:
       completions: 3
       parallelism: 3
       template:
          spec:
             containers:
               - name: reporting-job
                 image: reporting-job
             restartPolicy: Never
```

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
