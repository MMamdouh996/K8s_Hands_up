# Kubternates
## Lab 2

### 1-Create a ReplicaSet using the below yaml

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: new-replica-set
  namespace: default
spec:
  replicas: 4
  selector:
    matchLabels:
      name: busybox-pod
  template:
    metadata:
      labels:
        name: busybox-pod
    spec:
      containers:
      - command:
        - sh
        - -c
        - echo Hello Kubernetes! && sleep 3600
        image: busybox777
        imagePullPolicy: Always
        name: busybox-container
```
```bash
$ k create -f rs.yaml 
replicaset.apps/new-replica-set created
```
---
### 2-How many PODs are DESIRED in the new-replica-set?
```bash
$ k get rs 
NAME              DESIRED   CURRENT   READY   AGE
new-replica-set   4         4         0       87s
```
---
### 3-What is the image used to create the pods in the new-replica-set?
```bash
$ k describe replicasets.apps | grep -i "image"
    Image:      busybox777
```
---
### 4-How many PODs are READY in the new-replica-set?
- 0
```bash
$ k get rs
NAME              DESIRED   CURRENT   READY   AGE
new-replica-set   4         4         0       4m37s
```
---
### 5-Why do you think the PODs are not ready?
```bash
$ k describe pod new-replica-set-njp9x
# Containers:
#   busybox-container:
#     Container ID:  
#     Image:         busybox777
#     Image ID:      
#     Port:          <none>
#     Host Port:     <none>
#     Command:
#       sh
#       -c
#       echo Hello Kubernetes! && sleep 36
#     State:          Waiting
#       Reason:       ImagePullBackOff # this is the reason
#     Ready:          False
#     Restart Count:  0
#     Environment:    <none>
#     Mounts:
#       /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-psn8k (ro)
```
---
### 6-Delete any one of the 4 PODs. How many pods now
### 7-Why are there still 4 PODs, even after you deleted one?
```bash
$ k delete pod new-replica-set-njp9x 
pod "new-replica-set-njp9x" deleted
$ k get pods
NAME                    READY   STATUS             RESTARTS   AGE
new-replica-set-p5q9d   0/1     ImagePullBackOff   0          24s
new-replica-set-pjkjn   0/1     ImagePullBackOff   0          4m11s
new-replica-set-wq9jk   0/1     ImagePullBackOff   0          4m11s
new-replica-set-wqj52   0/1     ImagePullBackOff   0          4m11s
```
- because a pod is recreated to match the desired No. of pods in the replica-set
---
### 8-Create a ReplicaSet using the below yaml
#### There is an issue with the file, so try to fix it.

```yaml
apiVersion: apps/v1 # the problem was "apps/" didn't existed
kind: ReplicaSet
metadata:
  name: replicaset-1
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: nginx
        image: nginx
```
```bash
$ vim q8.yaml # and paste the above yaml code inside it and save wiht :wq
$ k create -f q8.yaml 
replicaset.apps/replicaset-1 created
```

