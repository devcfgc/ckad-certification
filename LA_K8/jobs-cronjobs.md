This Job calculates the first 2000 digits of pi.
```
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: perl
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4
```

You can use kubectl get to list and check the status of Jobs. `kubectl get jobs`

Here is a CronJob that prints some text to the console every minute.
```
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            args:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```

You can use kubectl get to list and check the status of CronJobs. `kubectl get cronjobs`

## Examples
Your company has a simple data cleanup process that is run periodically for maintenance purposes. They would like to stop doing this manually in order to save time, so you have been asked to implement a cron job in the Kubernetes cluster to run this process. Create a cron job called cleanup-cronjob using the linuxacademycontent/data-cleanup:1 image. Have the job run every minute with the following cron expression: */1 * * * *.

Create a descriptor for the cron job with `vi ~/cleanup-cronjob.yml`.

```
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: cleanup-cronjob
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: data-cleanup
            image: linuxacademycontent/data-cleanup:1
          restartPolicy: OnFailure
```

Create the cron job in the cluster. `kubectl apply -f ~/cleanup-cronjob.yml`

Give the cron job a minute or so to run once, and then check the status. `kubectl get cronjob cleanup-cronjob`
You should see a timestamp under LAST-SCHEDULE, indicating the job was executed.