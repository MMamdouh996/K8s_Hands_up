# Kubernates
## Lab6

### 1- Create pod from the below yaml file
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: webapp
spec:
  containers:
  - env:
    - name: LOG_HANDLERS
      value: file
    image: kodekloud/event-simulator
    imagePullPolicy: Always
    name: event-simulator
```
```bash
vagrant@master:~$ vim lab4-q1.yaml # and paste the above yaml file inside it
vagrant@master:~$ k create -f lab4-q1.yaml
pod/webapp created  
```

---
### 2- Configure a volume to store these logs at /var/log/webapp on the host.
- Use the spec provided below.
- Name: webapp
- Image Name: kodekloud/event-simulator
- Volume HostPath: /var/log/webapp
- Volume Mount: /log

#### new yaml for the pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: webapp
spec:
  containers:
  - env:
    - name: LOG_HANDLERS
      value: file
    image: kodekloud/event-simulator
    imagePullPolicy: Always
    name: event-simulator
    volumeMounts:
    - mountPath: /log
      name: webapp
  volumes:
  - name: webapp
    hostPath:
      # directory location on host  NODE
      path: /var/log/webapp
```
----------
### 3- Create a Persistent Volume with the given specification.
- Volume Name: pv-log
- Storage: 100Mi
- Access Modes: ReadWriteMany
- Host Path: /pv/log
- Reclaim Policy: Retain

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-log
  labels:
    type: local
spec:
  persistentVolumeReclaimPolicy: Retain
  storageClassName: manual
  capacity:
    storage: 100Mi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: "/pv/log"
```

---
### 4- Let us claim some of that storage for our application. Create a Persistent Volume Claim with the given specification.
- Volume Name: claim-log-1
- Storage Request: 50Mi
- Access Modes: ReadWriteOnce

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: claim-log-1
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 50Mi
```
------
### 5- What is the state of the Persistent Volume Claim?
```bash
vagrant@master:~/day4-kuber$ k get pvc
NAME          STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
claim-log-1   Pending                                      manual         25s
```
-------
### 6- What is the state of the Persistent Volume?
```bash
vagrant@master:~/day4-kuber$ k get pv
NAME          CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS    REASON   AGE
pv-log        100Mi      RWX            Retain           Available           local-storage            4m8s

```
-------
### 7- Why is the claim not bound to the available Persistent Volume?
- because access mode wasn't the same in the pv and pvc
------
8-Update the Access Mode on the claim to bind it to the PV?
```yaml
  accessModes:
    - ReadWriteOnce
```
```bash
vagrant@master:~/day4-kuber$ k get  pvc
NAME          STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
claim-log-1   Bound    pv-log   100Mi      RWO            manual         5m13s
vagrant@master:~/day4-kuber$ 
```
---------
