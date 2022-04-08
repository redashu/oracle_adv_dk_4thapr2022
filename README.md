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

