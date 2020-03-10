## Job example
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
```
```
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
---
apiVersion: batch/v1
kind: Job
metadata:
  name: throw-dice-job
spec:
  completions: 3
  backoffLimit: 25 # This is so the job does not quit before it succeeds.
  template:
    spec:
      containers:
      - name: math-add
        image: kodekloud/throw-dice
      restartPolicy: Never
---
apiVersion: batch/v1
kind: Job
metadata:
  name: throw-dice-job
spec:
  completions: 3
  parallelism: 3
  backoffLimit: 25 # This is so the job does not quit before it succeeds.
  template:
    spec:
      containers:
      - name: math-add
        image: kodekloud/throw-dice
      restartPolicy: Never
```

## CronJob example
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
---
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: throw-dice-cron-job
spec:
  schedule: "30 21 * * *"
  jobTemplate:
    spec:
      completions: 3
      parallelism: 3
      backoffLimit: 25 # This is so the job does not quit before it succeeds.
      template:
        spec:
          containers:
          - name: math-add
            image: kodekloud/throw-dice
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
