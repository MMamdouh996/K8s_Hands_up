# Kubernates
## Lab 5

### Create basic resources
- Create the specified Namespace
```bash
$ k create ns lab5
namespace/lab5 created
```
- Create a deployment nginx with 3 replicas Expose the deployment on port 80 
```bash
$ k create deployment nginx-name --replicas=3 --image=nginx:alpine --port=80 --namespace=lab5
deployment.apps/nginx-name created
```

```bash
$ k expose deployment nginx-name --port=80 -n lab5
service/nginx-name exposed
```

---
### Create a CronJob for listing the EndPoints
- Create a serviceaccount cronjob—sa
```bash
$ k create serviceaccount cronjob-sa -n lab5
serviceaccount/cronjob-sa created
```
- Create a Role that allows listing all the services and endpoints
```bash
$ k create role ALLOW --verb=list,get --resource=services,endpoints,pods -n lab5
role.rbac.authorization.k8s.io/ALLOW created
```
- Link the Role with the created SA
```bash
$ k create rolebinding allowRB --role=ALLOW --serviceaccount=lab5:cronjob-sa -n lab5
rolebinding.rbac.authorization.k8s.io/allowRB created
```

- Create a CronJob that lists the endpoints in that namespace every minute and paste the output for the first pod created

```bash
$ k create cronjob listendpoint --image=bitnami/kubectl:latest --schedule="* * * * *" -n lab5 -oyaml --dry-run=client > cj.yaml   
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
      name: listendpoint
    spec:
      template:
        metadata:
          creationTimestamp: null
        spec:
          serviceAccountName: cronjob-sa # add this line to attach service account
          containers:
          - image: bitnami/kubectl:latest
            name: listendpoint
            command: ["sh", "-c"]
            args:
            - "kubectl get endpoints"
          restartPolicy: OnFailure
  schedule: '* * * * *'
status: {}       
```
```bash
$ k create -f cj.yaml 
cronjob.batch/listendpoint created
$ k logs -n lab5 listendpoint-27901487-qd67z 
NAME         ENDPOINTS                                      AGE
nginx-name   192.168.0.6:80,192.168.1.3:80,192.168.1.4:80   2m27s
```


- After listing try to delete the 3 nginx pods ? again try to view the logs for the newly created pod for that cronJob what do you think happened ? 
```bash
$ k delete pod -n lab5 nginx-name-7bd8c79549-5ll7w nginx-name-7bd8c79549-vjbpz nginx-name-7bd8c79549-c2j7c   
pod "nginx-name-7bd8c79549-5ll7w" deleted
pod "nginx-name-7bd8c79549-vjbpz" deleted
pod "nginx-name-7bd8c79549-c2j7c" deleted
controlplane $ k get ep -n lab5
NAME         ENDPOINTS                                        AGE
nginx-name   192.168.0.7:80,192.168.1.37:80,192.168.1.38:80   11m
controlplane $ k get po -n lab5
NAME                          READY   STATUS      RESTARTS   AGE
listendpoint-27896475-psqxq   0/1     Completed   0          2m48s
listendpoint-27896476-fnzsf   0/1     Completed   0          108s
listendpoint-27896477-t9nd5   0/1     Completed   0          48s
nginx-name-7bd8c79549-d6rk7   1/1     Running     0          13s
nginx-name-7bd8c79549-kg24j   1/1     Running     0          13s
nginx-name-7bd8c79549-sqcbq   1/1     Running     0          13s 
```
- ips and ngainx pods regenerated 
