

3) Go through these exercises once to strengthen your concepts and to improve your speed - https://lnkd.in/gfUGc5f

4) Remember that in case you want to create a temporary pod in Kubernetes version v1.18, you have to use the --restart=Never flag while creating the pod as shown below :
Example: k run temp --image=busybox --restart=Never --rm -it -- /bin/sh -c 'echo temporary pod'