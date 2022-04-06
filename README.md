# Plan 

<img src="plan.png">

## CNI model of container networking adopted by k8s 

<img src="cni.png">

### pods can have ip address from CNI bridge 

<img src="bridge.png">

### checking pod to pod communication 

```
 kubectl  run  ashupod1  --image=alpine  --command ping 127.0.0.1 
pod/ashupod1 created
fire@ashutoshhs-MacBook-Air ~ % kubectl  get  pods
NAME       READY   STATUS    RESTARTS   AGE
ashupod1   1/1     Running   0          4s
fire@ashutoshhs-MacBook-Air ~ % kubectl  get  pods -o wide
NAME       READY   STATUS    RESTARTS   AGE   IP                NODE      NOMINATED NODE   READINESS GATES
ashupod1   1/1     Running   0          10s   192.168.179.222   minion2   <none>           <none>
fire@ashutoshhs-MacBook-Air ~ % 

```

### testing 

```
 % kubectl  get  pods -o wide
NAME         READY   STATUS              RESTARTS   AGE    IP                NODE      NOMINATED NODE   READINESS GATES
ashupod1     1/1     Running             0          109s   192.168.179.222   minion2   <none>           <none>
natrajpod1   0/1     ContainerCreating   0          2s     <none>            minion2   <none>           <none>
rameshpod1   1/1     Running             0          13s    192.168.34.20     minion1   <none>           <none>
rohitpod1    1/1     Running             0          60s    192.168.179.223   minion2   <none>           <none>
ronipod1     1/1     Running             0          56s    192.168.34.19     minion1   <none>           <none>
fire@ashutoshhs-MacBook-Air ~ % 
fire@ashutoshhs-MacBook-Air ~ % kubectl  exec -it  ashupod1  -- sh 
/ # ping  192.168.34.19
PING 192.168.34.19 (192.168.34.19): 56 data bytes
64 bytes from 192.168.34.19: seq=0 ttl=62 time=0.777 ms
64 bytes from 192.168.34.19: seq=1 ttl=62 time=0.417 ms
64 bytes from 192.168.34.19: seq=2 ttl=62 time=0.369 ms
^C

```

### auto generating pod yaml 

```
 kubectl run ashuweb  --image=dockerashu/customerapp:30thmarch2022   --port 80 --dry-run=client -oyaml  >webapp.yaml

```

### creating webapp pod 

```
kubectl create -f webapp.yaml 
pod/ashuweb created
fire@ashutoshhs-MacBook-Air containers_apps % kubectl  get  pods
NAME      READY   STATUS    RESTARTS   AGE
ashuweb   1/1     Running   0          9s
```

### exposing local to k8s client machine 

```
kubectl port-forward   ashuweb  1234:80 
Forwarding from 127.0.0.1:1234 -> 80
Forwarding from [::1]:1234 -> 80
Handling connection for 1234
Handling connection for 1234

```

### Internal LB 

<img src="lb.png">

