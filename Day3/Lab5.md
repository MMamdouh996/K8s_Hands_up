### Create basic resources
- Create the specified Namespace
```bash
$ k create ns lab5
namespace/lab5 created
controlplane $ k get ns
NAME              STATUS   AGE
default           Active   23d
kube-node-lease   Active   23d
kube-public       Active   23d
kube-system       Active   23d
lab5              Active   4s
```
- Create a deployment nginx with 3 replicas Expose the deployment on port 80 
```bash
$ k create deployment nginx-name --replicas=3 --image=nginx:alpine --port=80 --namespace=lab5 -oyaml --dry-run=client > q1-Lab5.yml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: nginx-name
  name: nginx-name
  namespace: lab5
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-name
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx-name
    spec:
      containers:
      - image: nginx:alpine
        name: nginx
        ports:
        - containerPort: 80
```

### Create a CronJob for listing the EndPoints
- Create a serviceaccount cronjob—sa
```bash
$ k create serviceaccount cronjob-sa
serviceaccount/cronjob-sa created
```
- Create a Role that allows listing all the services and endpoints
```bash
$ k create role ALLOW --verb=list --resource=services,endpoints
role.rbac.authorization.k8s.io/ALLOW created
```
- Link the Role with the created SA
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  creationTimestamp: null
  name: allowRB
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: ALLOW
subjects:
- kind: ServiceAccount
  name: cronjob-sa
  namespace: lab5
~                      
```
```bash
$ k create rolebinding allowRB --role=ALLOW --serviceaccount=cronjob-sa
error: serviceaccount must be <namespace>:<name>
$ k create rolebinding allowRB --role=ALLOW --serviceaccount=lab5:cronjob-sa
rolebinding.rbac.authorization.k8s.io/allowRB created
```
- Create a CronJob that lists the endpoints in that namespace every minute and paste the output for the first pod created
```bash
$ k create cronjob listendpoint --image=bitnami/kubectl:latest --schedule="* * * * *" --namespace=lab5 -oyaml --dry-run=client > cj    
```

```yaml

apiVersion: batch/v1
kind: CronJob
metadata:
  creationTimestamp: null
  name: listendpoint
  namespace: lab5
spec:
  jobTemplate:
    metadata:
      creationTimestamp: null
      name: listenddpoint
    spec:
      template:
        metadata:
          creationTimestamp: null
        spec:
          containers:
          - image: bitnami/kubectl:latest
            name: listendddpoint
            command:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
            - kubectl get endpoints -n lab5
          restartPolicy: OnFailure
  schedule: '* * * * *'
status: {}
```

```bash
controlplane $ k create -f cj
cronjob.batch/listendpoint created
```
- After listing try to delete the 3 nginx pods ? again try to view the logs for the newly created pod for that cronJob what do you think happened ? 
