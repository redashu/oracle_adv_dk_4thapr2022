# Plan 

<img src="plan.png">

## k8s advanced controllers 

### RC 
<img src="rc.png">

### cloud controller and node controller 

<img src="cloudnode.png">

### new minion node registration 

<img src="node.png">

### creating token for new minion join -- in control plane

```
kubeadm   token  create  --print-join-command

```

### paste output in new node u want to join as minion 

## Multi container Design --

<img src="multicont.png">

### Demo 

```
kubectl  run  loggen  --image=busybox --dry-run=client -oyaml   >multicontainer.yaml 
```

###

```
kubectl apply -f multicontainer.yaml 
pod/loggen created
fire@ashutoshhs-MacBook-Air containers_apps % kubectl  get  po
NAME     READY   STATUS    RESTARTS   AGE
loggen   2/2     Running   0          6s
```

### checking from kubectl 

```
 kubectl  get  po
NAME     READY   STATUS    RESTARTS   AGE
loggen   2/2     Running   0          6m39s
fire@ashutoshhs-MacBook-Air ~ % 
fire@ashutoshhs-MacBook-Air ~ % 
fire@ashutoshhs-MacBook-Air ~ % kubectl  exec -it  loggen -- bash 
Defaulted container "view-logs" out of: view-logs, loggen
root@loggen:/# 
root@loggen:/# 
root@loggen:/# cd  /usr/share/nginx/html/
root@loggen:/usr/share/nginx/html# ls
logs.txt
root@loggen:/usr/share/nginx/html# rm logs.txt 
rm: cannot remove 'logs.txt': Read-only file system
root@loggen:/usr/share/nginx/html# cat logs.txt 
hello loggers my name is ? 
hello loggers my name is ? 
hello loggers my name is ? 
hello loggers my name is ? 

```

### checkings

```
 kubectl  exec -it  loggen  -c  loggen    -- sh   
/ # 
/ # 
/ # 
/ # cd /mnt/data/
/mnt/data # ls
logs.txt
/mnt/data # mkdir  hello 
/mnt/data # ls
hello     logs.txt
/mnt/data # exit
fire@ashutoshhs-MacBook-Air ~ % kubectl  exec -it  loggen -- bash              
Defaulted container "view-logs" out of: view-logs, loggen
root@loggen:/# cd /usr/share/nginx/html/
root@loggen:/usr/share/nginx/html# ls
hello  logs.txt
root@loggen:/usr/share/nginx/html# exit
exit

```

### creating service --

```
 kubectl  get  po 
NAME     READY   STATUS    RESTARTS   AGE
loggen   2/2     Running   0          13m
fire@ashutoshhs-MacBook-Air ~ % kubectl expose pod  loggen --type NodePort --port 1234 --target-port 80 --name ashusvc1 
service/ashusvc1 exposed
fire@ashutoshhs-MacBook-Air ~ % kubectl  get  svc
NAME       TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
ashusvc1   NodePort   10.105.254.206   <none>        1234:31490/TCP   8s
fire@ashutoshhs-MacBook-Air ~ % 

```

## ROle Base Access control (RBAC)

### users in k8s
-->. ### cluster users 

<img src="clsuser.png">

### service accounts in k8s 

<img src="sa.png">

### more on service accounts 

```
kubectl  get  serviceaccount 
NAME      SECRETS   AGE
default   1         45h
fire@ashutoshhs-MacBook-Air ~ % kubectl  get  sa             
NAME      SECRETS   AGE
default   1         45h
fire@ashutoshhs-MacBook-Air ~ % kubectl  get  secret
NAME                  TYPE                                  DATA   AGE
dbpass                Opaque                                1      20h
default-token-lh76q   kubernetes.io/service-account-token   3      45h
myimgsec              kubernetes.io/dockerconfigjson        1      44h
fire@ashutoshhs-MacBook-Air ~ % 
fire@ashutoshhs-MacBook-Air ~ % kubectl  describe secret  default-token-lh76q  
Name:         default-token-lh76q
Namespace:    ashu-project
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: default
              kubernetes.io/service-account.uid: f33d82be-f3b1-4d88-80bf-b12ae40854b9

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1099 bytes
namespace:  12 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6Ims4S2N2S2VsRG4tM0RqbThwM0tjU2ZlTkctcDAySlY1ZWFBUVFqM01MRE0ifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJhc2h1LXByb2plY3QiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlY3JldC5uYW1lIjoiZGVmYXVsdC10b2tlbi1saDc2cSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiZjMzZDgyYmUtZjNiMS00ZDg4LTgwYmYtYjEyYWU0MDg1NGI5Iiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50OmFzaHUtcHJvamVjdDpkZWZhdWx0In0.a9713lHtLDOZOkE4HVIcJq7SpjUEmsT4orutt9DCLcC_Pz8PbS85pUQqb5hfy0PWXByVSggWtX0nyoaDxS97AI5VqgmfYpf4eKncoj72iQMJpkUxmKXFNoK6manN60vO1P7q53Fnan9dT7xes445_Sp_2UCEyVltz6ov_X75uxrwh80zCEPYx9cL4ThcmRjkXmvhMyUxT6tMdGyLaA1mBCZfphQ7p4YOjyCp8XIzd7ZHUyhBjBXa8
```

### creating service account 

```
kubectl  get  sa -n ashu-developer
NAME      SECRETS   AGE
default   1         17m
fire@ashutoshhs-MacBook-Air ~ % kubectl  create   sa  project  -n ashu-developer
serviceaccount/project created
fire@ashutoshhs-MacBook-Air ~ % 
fire@ashutoshhs-MacBook-Air ~ % 
fire@ashutoshhs-MacBook-Air ~ % kubectl  get  sa -n ashu-developer              
NAME      SECRETS   AGE
default   1         17m
project   1         3s
fire@ashutoshhs-MacBook-Air ~ % kubectl  get  secret  -n ashu-developer
NAME                  TYPE                                  DATA   AGE
default-token-pds2b   kubernetes.io/service-account-token   3      17m
project-token-gsdlq   kubernetes.io/service-account-token   3      10s
fire@ashutoshhs-MacBook-Air ~ % 


```
### roles in k8s

<img src="roles.png">

### creating roles 

```
kubectl create role  pod-reader --resource=pod --verb=list  --namespace ashu-developer  --dry-run=client -o yaml 
```

### 

```
kubectl apply -f role1.yaml 
role.rbac.authorization.k8s.io/pod-reader created
fire@ashutoshhs-MacBook-Air containers_apps % kubectl get roles -n ashu-developer
NAME         CREATED AT
pod-reader   2022-04-08T09:12:41Z
```

### creating rolebinding 

```
kubectl  create rolebinding bind-to-project  --role pod-reader  --serviceaccount=ashu-developer:project  -n  ashu-developer

===
 kubectl  get  sa -n  ashu-developer 
NAME      SECRETS   AGE
default   1         147m
project   1         129m
fire@ashutoshhs-MacBook-Air Desktop % kubectl  get  secret -n  ashu-developer 
NAME                  TYPE                                  DATA   AGE
default-token-pds2b   kubernetes.io/service-account-token   3      147m
project-token-gsdlq   kubernetes.io/service-account-token   3      129m
fire@ashutoshhs-MacBook-Air Desktop % kubectl  get  role -n  ashu-developer 
NAME         CREATED AT
pod-reader   2022-04-08T09:12:41Z
fire@ashutoshhs-MacBook-Air Desktop % kubectl  get  rolebinding -n  ashu-developer 
NAME              ROLE              AGE
bind-to-project   Role/pod-reader   51s

```

### testing 

```
kubectl  get  pods  --kubeconfig devconf.yaml 
No resources found in ashu-developer namespace.
fire@ashutoshhs-MacBook-Air containers_apps % kubectl  get  svc  --kubeconfig devconf.yaml
Error from server (Forbidden): services is forbidden: User "system:serviceaccount:ashu-developer:project" cannot list resource "services" in API group "" in the namespace "ashu-developer"
fire@ashutoshhs-MacBook-Air containers_apps % kubectl  get  nodes  --kubeconfig devconf.yaml
Error from server (Forbidden): nodes is forbidden: User "system:serviceaccount:ashu-developer:project" cannot list resource "nodes" in API group "" at the cluster scope
```


