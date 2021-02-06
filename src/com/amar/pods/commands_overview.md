# Kubectl commands

These commands are helpful for you to have an overview before you sit for CKAD exam for example

```sh
kubectl set image pod podname nginx=nginx:1.15-alpine

# it's better to edit pod sometime and make quick changes
kubectl edit pod podName

# Create the pod named amardev with version 1.17.4 and expose it on port 80
kubectl run amardev --image=nginx:1.17.4 --restart=Never --port=80

# add the command in the pod by editing it
command: ['sleep', '5000']

# adding args in the pod
args: ['--color', 'green']

# get logs for nginx pod
kubectl logs nginx

# get previous logs of the pod
kubectl logs nginx -p

# just open the terminal for the pod with one container
kubectl exec -it nginx -- /bin/sh

# echo hello world in the container
kubectl exec -it nginx -c containerName -- /bin/sh -c 'echo hello world'

# create a configmap
kubectl create configmap special-config --from-literal=special.how=very --from-literal=special.type=charm

# set an env variable while creating a pod
kubectl run nginx --image=nginx --restart=Never --env=var1=val1

# get all of the env variables for a pod
kubectl exec -it nginx -- env

# create a secret and add some values
kubectl create secret generic db-secret --from-literal=DB_Host=sql01

# multi container pod
kubectl run busybox --image=busybox --restart=Never --dry-run -o yaml -- bin/sh -c "sleep 3600; ls" > multi-container.yaml

# check logs for a container 1 and 2 in the pod busybox
kubectl logs busybox -c busybox1
kubectl logs busybox -c busybox2


# Check the previous logs of the second container busybox2 if any
kubectl logs busybox -c busybox2 --previous

# Run command ls in the third container busybox3 of the above pod
kubectl exec busybox -c busybox3 -- ls

# Show metrics of the above pod containers and puts them into the file.log and verify
kubectl top pod busybox --containers > file.log

kubectl exec -it  multi-cont-pod -c main-container -- sh cat /var/log/main.txt

# show labels
kubectl get pods --show-labels

# apply label
kubectl run nginx-dev1 --image=nginx --restart=Never --labels=env=dev


#Get the pods with label env=dev
kebectl get pods -l env=dev --show-labels

# show labels which env in dev and prod
kubectl get pods -l 'env in (dev,prod)'

# update the label with overwrite
kubectl label po podone area=monteal --overwrite

# remove label named env
kubectl label pods podone env-

# show the labels for the nodes
kubectl get nodes --show-labels

# Annotate the pods with name=webapp
kubectl annnotate po podone name=webapp

# delete all the pods
kubectl delete po --all

# create a deployment with a name and replicas of 3
kubectl create deployment webapp --image=nginx --replicas=3

# scale deployment to have 20 replicas
kubectl scale deploy webapp --replicas=20

# get the rollout status
kubectl rollout status deploy webapp

# get the rollout history
kubectl rollout history deploy webapp

# delete the deployment and watch it getting deleted
kubectl delete deployment webapp --watch

# update the image in the deployment. Note that here nginx in the container name
# set image will cause the rollut to happen, so you can check the status using the `kubectl rollout status` command
kubectl set image deployment depName nginx=nginx:1.17.4

# undo the deployment to revision 1
kubectl rollout undo deployment webapp --to-revision=1

#  Check the history of the specific revision of that deployment
kubectl rollout history deployment webapp --revision=3

# pause the rollout
kubectl rollout pause deployment webapp

# resume the rollout
kubectl rollout resume deployment webapp

# horizontal pod autoscaling
kubectl get hpa

# scale a deployment
kubectl autoscale deployment webapp --min=5 --max=10 --cpu-percent=80

# jobs will create a pod and then finish when work is done
# then we can see the output of that job using the logs command for the pod created
kubectl create job jobName --image=nginx -- node -v

# create a job which will save the TIME information
kubectl create job jonname --image=nginx -- /bin/sh -c "date; echo 'time container'"

# delete all the jobs
kubectl delete jobs --all

# get all the cronjobs
kubectl get cj

# create a cronjob which will show the time everyminute
kubectl create cronjob timecj  --image=nginx --schedule="*/1 * * * *" -- /bin/sh -c "date"

# to add the user id we can defice security context in the container and pod level using
securityContext:
  runAsUser: 1000
  runAsOwner: 1010

# capabilities are added at the container level not at the pod level
containers:
    securityContext:
      capabilities:
        add: ['SYS_TIME']
      runAsUser: 1000
      runAsOwner: 1010


# creating taints
kubects taint node nodename key=value:NoSchedule

# see the taint using describe
kubectl describe deployment depname | grep -i taint

# remove the Taints in the node using, add - at end to remove the taint
kubectl taint node nodename node-role.kubernetes.io/master:NoSchedule-

# use the below affinity in the pod spec
affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: labelKey
            operator: In
            values:
            - labelValue

# get the metircs about the nodes
kubectl top node/pod

# select pods with multiple labels
kubectl get all --selector env=prod,bu=finance,tier=frontend



```
## Notes

1. NodeSelector is used in the pod if we want it to get allocated to a particular node
2. A Job creates one or more Pods and ensures that a specified number of them successfully terminate. As pods successfully complete, the Job tracks the successful completions. When a specified number of successful completions is reached, the task (ie, Job) is complete. Deleting a Job will clean up the Pods it created.
3. A hostPath volume mounts a file or directory from the host node’s filesystem into your Pod.
4. pod talks to API-server using the service account
5. Use nodeaffinity to place the pods in the right nodes. Use the label to select the node. First apply a label on the node and then use that label on the node affinity
6. startup probe, the application will have a maximum of 5 minutes (30 * 10 = 300s) to finish its startup. Once the startup probe has succeeded once, the liveness probe takes over to provide a fast response to container deadlocks. If the startup probe never succeeds, the container is killed after 300s and subject to the pod's restartPolic
7.


# see special chars in VI
use `:set list` to show the special chars and `:set nolist` to go back to normal





