# Kubernates
---
## Lab3
---

### 1.1 Create a deployment called my-first-deployment of image nginx:alpine in the default namespace.
```python
$ kubectl create deployment my-first-deployment --image==nginx:alpine --namespace=default
deployment.apps/my-first-deployment created
```
### 1.2 Check to make sure the deployment is healthy.
```python
$ k get deployments.apps 
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
my-first-deployment   1/1     1            1           68s

```
----------------
### 2.1 Scale my-first-deployment up to run 3 replicas.
```python
$ k scale deployment my-first-deployment --replicas=3
deployment.apps/my-first-deployment scaled
```
### 2.2 Check to make sure all 3 replicas are ready.
```python
$ k get deployments.apps 
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
my-first-deployment   3/3     3            3           4m40s
```
----------------
### 3-Scale my-first-deployment down to run 2 replicas.
```python
 $ k scale deployment my-first-deployment --replicas=2
deployment.apps/my-first-deployment scaled

$ k get deployments.apps 
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
my-first-deployment   2/2     2            2           6m31s
```
---------------
### 4-Change the image my-first-deployment runs from nginx:alpine to httpd:alpine .
```python
$ k set image deployment my-first-deployment nginx=httpd:alpine
deployment.apps/my-first-deployment image updated
```
--------------------------
### 5-Delete the deployment my-first-deployment
```python
$ k delete deployments.apps my-first-deployment 
deployment.apps "my-first-deployment" deleted
```
----------------------------
### 6-Create deployment from the below yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-deployment
  namespace: default
spec:
  replicas: 4
  selector:
    matchLabels:
      name: busybox-pod
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
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
        image: busybox888
        imagePullPolicy: Always
        name: busybox-container
```

```python
$ k create -f q6-lab3.yaml 
deployment.apps/frontend-deployment created
```
        
-------------
7-How many ReplicaSets exist on the system now?
```python
$ k get replicasets.apps 
NAME                             DESIRED   CURRENT   READY   AGE
frontend-deployment-7fbf4f5cd9   4         4         0       53s
```
------------------------------
8-How many PODs exist on the system now?
```python
controlplane $ k get pods
NAME                                   READY   STATUS             RESTARTS   AGE
frontend-deployment-7fbf4f5cd9-lktz4   0/1     ImagePullBackOff   0          105s
frontend-deployment-7fbf4f5cd9-ltrmj   0/1     ErrImagePull       0          105s
frontend-deployment-7fbf4f5cd9-qv9zz   0/1     ImagePullBackOff   0          105s
frontend-deployment-7fbf4f5cd9-vbjkc   0/1     ImagePullBackOff   0          105s
```
-----------------
9-Out of all the existing PODs, how many are ready?
```python
0
```
-------------------
10-What is the image used to create the pods in the new deployment?
```python
$ k describe deployments.apps frontend-deployment | grep -i "image"
    Image:      busybox888
```
---------------------
11-Why do you think the deployment is not ready?
```python
$ k get pods
NAME                                   READY   STATUS             RESTARTS   AGE
frontend-deployment-7fbf4f5cd9-lktz4   0/1     ImagePullBackOff   0          5m59s
frontend-deployment-7fbf4f5cd9-ltrmj   0/1     ImagePullBackOff   0          5m59s
frontend-deployment-7fbf4f5cd9-qv9zz   0/1     ImagePullBackOff   0          5m59s
frontend-deployment-7fbf4f5cd9-vbjkc   0/1     ImagePullBackOff   0          5m59s
```
----------------------
12-Create a new Deployment using the below yaml 
```python
there is no yaml file to create from
```
----------------
13-
There is an issue with the file, so try to fix it.
and correct the value of kind.
```python
there is no file exist
```
