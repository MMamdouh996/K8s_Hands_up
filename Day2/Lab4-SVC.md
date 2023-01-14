# Kubernates

## Lab4
---
### 1-How many Services exist on the system?
```bash
$ k get service
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   22d
```
--------
### 2-What is the type of the default kubernetes service?
#### The default type of a Kubernetes service is ClusterIP. This means that the service is only accessible within the cluster and is not exposed externally.


--------
### 3-What is the targetPort configured on the kubernetes service?
```bash
$ k describe service kubernetes | grep -i "target"
TargetPort:        6443/TCP
```

---------
### 4-How many labels are configured on the kubernetes service?
```bash
$ k describe service kubernetes
Labels:            component=apiserver
                   provider=kubernetes
```

-------
### 5-How many Endpoints are attached on the kubernetes service?
```bash
$ k describe service kubernetes
Endpoints:         172.30.1.2:6443

```

-----------
### 6-Create a Deployment using the below yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: simple-webapp-deployment
  namespace: default
spec:
  replicas: 4
  selector:
    matchLabels:
      name: simple-webapp
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        name: simple-webapp
    spec:
      containers:
      - image: kodekloud/simple-webapp:red
        imagePullPolicy: IfNotPresent
        name: simple-webapp
        ports:
        - containerPort: 8080
          protocol: TCP


```bash
$ k create -f q6-lab4.yaml 
deployment.apps/simple-webapp-deployment created
```

----------------
### 7-What is the image used to create the pods in the deployment?

```bash
$ k describe deployments.apps simple-webapp-deployment | grep -i "image"
Image:        kodekloud/simple-webapp:red
```

------------
### 8-Create a new service to access the web application using the the below 
Requirment
```yaml
Name: webapp-service
Type: NodePort
targetPort: 8080
port: 8080
nodePort: 30080
selector:
  name: simple-webapp
```

Yaml file
```yaml
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  type: NodePort
  selector:
    name: simple-webapp
  ports:
      # By default and for convenience, the `targetPort` is set to the same value as the `port` field.
    - port: 8080
      targetPort: 8080
      # Optional field
      # By default and for convenience, the Kubernetes control plane will allocate a port from a range (default: 30000-32767)
      nodePort: 30007
```

```bash
$ k create -f q8-lab4.yaml 
service/webapp-service created
```

-----------
