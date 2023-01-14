1-Create a ReplicaSet using the below yaml

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
--------------------
2-How many PODs are DESIRED in the new-replica-set?
----------------------
3-What is the image used to create the pods in the new-replica-set?
busybox777
---------------------
4-How many PODs are READY in the new-replica-set?
0
---------------------
5-Why do you think the PODs are not ready?
ImagePullBackOff
------------------------
6-Delete any one of the 4 PODs. 
How many pods now
4
---------------------
7-Why are there still 4 PODs, even after you deleted one?
because a pod is recreated from the replica-set
-----------------
8-Create a ReplicaSet using the below yaml

There is an issue with the file, so try to fix it.

apiVersion: v1
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


