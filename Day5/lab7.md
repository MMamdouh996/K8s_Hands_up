# Kubernates
## Lab7

### 1-The DevOps team would like to get the list of all Namespaces in the cluster. Get the list and save it to /opt/namespaces.
```bash
vagrant@master:~$ k get ns
NAME                 STATUS   AGE
default              Active   24h
ingress-nginx        Active   24h
kube-node-lease      Active   24h
kube-public          Active   24h
kube-system          Active   24h
local-path-storage   Active   24h
```
---
###	2- create  ServiceAccount named neptune-sa-v2 in Namespace neptune.
```bash
vagrant@master:~$ k create ns neptune
namespace/neptune created
vagrant@master:~$ k create sa neptune-sa-v2 -n neptune 
serviceaccount/neptune-sa-v2 created
```
---
###	3- Create a new ConfigMap named cm-3392845. Use the spec given on the below.
- ConfigName Name: cm-3392845
- Data: DB_NAME=SQL3322
- Data: DB_HOST=sql322.mycompany.com
- Data: DB_PORT=3306
```yaml
apiVersion: v1
data:
  DB_HOST: sql322.mycompany.com
  DB_NAME: SQL3322
  DB_PORT: "3306"
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: cm-3392845
```
```bash
vagrant@master:~$ k create cm cm-3392845 --from-literal=DB_NAME=SQL3322 --from-literal=DB_HOST=sql322.mycompany.com --from-literal=DB_PORT=3306
configmap/cm-3392845 created
```
---
###	4-	Team Pluto needs a new cluster internal Service.
1. Create a ClusterIP Service named project-plt-6cc-svc in Namespace pluto.
2. This Service should expose a single Pod named project-plt-6cc-api of image nginx:1.17.3-alpine, create that Pod as well.
3. The Pod should be identified by label project: plt-6cc-api.
4. The Service should use tcp port redirection of 3333:80.

- pod ymal file
```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: project-plt-6cc-api
  name: project-plt-6cc-api
  namespace: pluto
spec:
  containers:
  - image: nginx:1.17.3-alpine
    name: project-plt-6cc-api
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```

```bash
$ k create ns pluto
$ k run project-plt-6cc-api --image=nginx:1.17.3-alpine -n pluto $do > day5-kuber/q4-pod.yaml
$ k create -f q4-pod.yaml
$ k expose -n pluto pod project-plt-6cc-api --name=project-plt-6cc-svc --port=3333 --target-port=80 --protocol=TCP $do > cip-svc.yaml
$ k create -f cip-svc.yaml
```
---
###	5- Create a new PersistentVolume named earth-project-earthflower-pv.
1. It should have a capacity of 2Gi
2. accessMode ReadWriteOnce
3. hostPath /Volumes/Data
4. and no storageClassName defined.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: earth-project-earthflower-pv
spec:
  capacity:
    storage: 2Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /Volumes/Data
```

### Next create a new PersistentVolumeClaim in Namespace earth named earth-project-earthflower-pvc.
2. It should request 2Gi storage
3. accessMode ReadWriteOnce
4. and should not define a storageClassName.
5. The PVC should bound to the PV correctly.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: earth-project-earthflower-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
```
---
1. Finally create a new Deployment project-earthflower in Namespace earth
2. which mounts that volume at /tmp/project-data.
3. The Pods of that Deployment should be of image httpd:2.4.41-alpine.

- Deployment Yaml file
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: project-earthflower
  name: project-earthflower
  namespace: earth
spec:
  replicas: 3
  selector:
    matchLabels:
      app: project-earthflower
  template:
    metadata:
      labels:
        app: project-earthflower
    spec:
      containers:
      - image: httpd:2.4.41-alpine
        name: httpd
        volumeMounts:
         - mountPath: /tmp/project-data
           name: mypd
      volumes:
          - name: mypd
            persistentVolumeClaim:
              claimName: earth-project-earthflower-pvc
```
```bash
$ k create ns earth
```
```bash
$ k get pvc
NAME          STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
claim-log-1   Bound    pv-log   100Mi      RWO            manual         23h
```
```bash
$ k get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                                 STORAGECLASS   REASON   AGE
earth-project-earthflower-pv               2Gi        RWO            Retain           Available                                                                 25m
pv-log                                     100Mi      RWO            Retain           Bound       default/claim-log-1                   manual                  23h
pvc-234d2eb9-7aec-48d5-b3b3-8b459cd85d21   2Gi        RWO            Delete           Bound       earth/earth-project-earthflower-pvc   local-path              13m
```
```bash
$ k exec -it -n earth project-earthflower-6f569bb949-hnlnf -- ls /tmp/project-data
file  from  here
```
```bash
$ k exec -it -n earth project-earthflower-6f569bb949-f8fsm -- ls /tmp/project-data
file  from  here
```