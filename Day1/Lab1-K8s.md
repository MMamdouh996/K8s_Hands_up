# Kubternates
## Lab 1
- k is alias for 'kubectl'
---
### 1-How many pods exist on the system ?
```bash
$ k get pods
No resources found in default namespace
```
---
### 2-How many Nodes exist on the system ?
```bash
$ k get nodes
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   26d   v1.26.0
node01         Ready    <none>          26d   v1.26.0
```
---
### 3-Create a new pod with the nginx image, Image name: nginx
```bash
$ k run nginx --image=nginx
pod/nginx created
```
---
### 4-Which nodes are these pods placed on?
```bash
$ k describe pod nginx |grep -i "node"
Node:             node01/172.30.2.2
```
---
### 5-Create pod from the below yaml using kubectl apply command.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: webapp
  namespace: default
spec:
  containers:
  - image: nginx
    imagePullPolicy: Always
    name: nginx
  - image: agentx
    imagePullPolicy: Always
    name: agentx
```
```bash
$ vim file.yaml # then paste the code above and then save it using :wq
$ k apply -f file.yaml
pod/webapp created
```
---
### 6-How many containers are part of the pod webapp?
### 7-What images are used in the new webapp pod?
```bash
$ k describe pod webapp # you will find that it has 2 containers nginx and agentx 
# or from the yaml file above 
# containers:
#   - image: nginx #image name
#     imagePullPolicy: Always
#     name: nginx #container name
#   - image: agentx #image name
#     imagePullPolicy: Always 
#     name: agentx #container name
```
---
### 8-What is the state of the container agentx in the pod webapp
### 9-Why do you think the container agentx in pod webapp is in error?
```bash
$ k describe pod webapp
```
- nginx is ready
```bash
# nginx:
#     Container ID:   containerd://65814ba5b2af3831ccef4b96425e503a4c23838a121111095507b2a4b1091326
#     Image:          nginx
#     Image ID:       docker.io/library/nginx@sha256:b8f2383a95879e1ae064940d9a200f67a6c79e710ed82ac42263397367e7cc4e
#     Port:           <none>
#     Host Port:      <none>
#     State:          Running
#       Started:      Wed, 18 Jan 2023 13:53:09 +0000
#     Ready:          True
```
- agentx is not ready
- reason : imagePullBackOff
- reason : couldn't pull the image i give him from the main registry
```bash
# agentx:
#     Container ID:   
#     Image:          agentx
#     Image ID:       
#     Port:           <none>
#     Host Port:      <none>
#     State:          Waiting
#       Reason:       ImagePullBackOff
#     Ready:          False
```
---
### 10-Delete the webapp Pod.
```bash
$ k delete pod webapp
pod "webapp" deleted
```
---
### 11- Create a new pod with the name redis and with the image redis12
```bash
$ k run redis --image=redis123
pod/redis created
```
---
### 12- Now change the image on this pod to redis.  Once done, the pod should be in a running state.
```bash
$ k set image pod/redis redis=redis
pod/redis image updated
```
### 13- Create a pod called my-pod of image nginx:alpine
### 14- Delete the pod called my-pod
```bash
$ k run my-pod --image=nginx:alpine
pod/my-pod created
$ k delete pod my-pod
pod "my-pod" deleted
```


