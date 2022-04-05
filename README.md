# Plan 

<img src="plan.png">

## day1 revision 

<img src="rev.png">

### Docker host data migration 

<img src="migration.png">

### new docker host with external storage 

```
17  mkfs.xfs  /dev/nvme1n1 
   18  mkdir  /mnt/docker-data
   19  mount /dev/nvme1n1  /mnt/docker-data/
   20  vim /etc/fstab 
   21  docker  info 
   22  systemctl status  docker  
   23  vim  /usr/lib/systemd/system/docker.service
   24  systemctl daemon-reload 
   25  systemctl restart docker
   26  docker  info 

```

### Now from Source docker host --

```
 
[root@docker-host ~]# rsync -avr  /var/lib/docker/  root@18.214.154.73:/mnt/docker-data/  
root@18.214.154.73's password: 
sending incremental file list
```
### on the new docker host 

```
systemctl daemon-reload 
[root@ip-172-31-80-255 docker-data]# systemctl restart docker 
[root@ip-172-31-80-255 docker-data]# docker  images
REPOSITORY                           TAG       IMAGE ID       CREATED         SIZE

```

## COntainer orchestration engines 

<img src="orch.png">

## k8s architecture Understanding 

### Level 1 arch of k8s 

<img src="k8s1.png">

### LEvel 2 -- Minion side components 

<img src="minion.png">

### LEvel 3  master node component -- kube-apiServer. --

<img src="apis.png">

### client to cluster / master node connection --

```
kubectl  version 
Client Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.5", GitCommit:"c285e781331a3785a7f436042c65c5641ce8a9e9", GitTreeState:"clean", BuildDate:"2022-03-16T15:58:47Z", GoVersion:"go1.17.8", Compiler:"gc", Platform:"darwin/amd64"}
The connection to the server localhost:8080 was refused - did you specify the right host or port?
fire@ashutoshhs-MacBook-Air ~ % 
fire@ashutoshhs-MacBook-Air ~ % 
fire@ashutoshhs-MacBook-Air ~ % kubectl  cluster-info 

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
The connection to the server localhost:8080 was refused - did you specify the right host or port?
fire@ashutoshhs-MacBook-Air ~ % 

```
### location of auth file in master node / cluster side 

```
[root@control-plane ~]# cd  /etc/kubernetes/
[root@control-plane kubernetes]# ls
admin.conf
```

### from Client / admin /dev 

```
kubectl  version   --kubeconfig  admin.conf.txt 
Client Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.5", GitCommit:"c285e781331a3785a7f436042c65c5641ce8a9e9", GitTreeState:"clean", BuildDate:"2022-03-16T15:58:47Z", GoVersion:"go1.17.8", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.5", GitCommit:"c285e781331a3785a7f436042c65c5641ce8a9e9", GitTreeState:"clean", BuildDate:"2022-03-16T15:52:18Z", GoVersion:"go1.17.8", Compiler:"gc", Platform:"linux/amd64"}
fire@ashutoshhs-MacBook-Air Desktop % 
fire@ashutoshhs-MacBook-Air Desktop % 
fire@ashutoshhs-MacBook-Air Desktop % 
fire@ashutoshhs-MacBook-Air Desktop % kubectl  cluster-info   --kubeconfig  admin.conf.txt 
Kubernetes control plane is running at https://3.225.69.61:6443
CoreDNS is running at https://3.225.69.61:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

```

### more request 

```
kubectl  get  nodes   --kubeconfig  admin.conf.txt 
NAME            STATUS   ROLES                  AGE    VERSION
control-plane   Ready    control-plane,master   4h5m   v1.23.5
minion1         Ready    <none>                 4h2m   v1.23.5
minion2         Ready    <none>                 4h2m   v1.23.5
```

### copy auth file in default location 

```
% cp  admin.conf.txt  ~/.kube/config 
fire@ashutoshhs-MacBook-Air Desktop % 
fire@ashutoshhs-MacBook-Air Desktop % 
fire@ashutoshhs-MacBook-Air Desktop % 
fire@ashutoshhs-MacBook-Air Desktop % kubectl  get nodes
NAME            STATUS   ROLES                  AGE    VERSION
control-plane   Ready    control-plane,master   4h7m   v1.23.5
minion1         Ready    <none>                 4h4m   v1.23.5
minion2         Ready    <none>                 4h4m   v1.23.5
fire@ashutoshhs-MacBook-Air Desktop % 
fire@ashutoshhs-MacBook-Air Desktop % kubectl  cluster-info                                
Kubernetes control plane is running at https://3.225.69.61:6443
CoreDNS is running at https://3.225.69.61:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```


### ETCD -- brain of k8s 

<img src="etcd.png">

### kube-schedular in master node 

<img src="sch.png">

## Deploy apps in k8s 

<img src="dep.png">

### Intro to POD 

<img src="pod.png">

## kubernetes api resources 

<img src="resouces.png">

### first poD file 

```
apiVersion: v1 # requesting to apiversion v1 
kind: Pod # type of resource under above define apiVersion 
metadata: # info about resouces like name
  name: ashupod-007  # name of my POd 
spec: # info about your container app 
  containers:
  - name: ashuc1 # name of container inside pod 
    image: nginx # name of image from Docker hub 
    ports:
    - containerPort: 80  # app port that will be used by container 

```

### testing and deploy POd 

```
kubectl create  -f  ashupod1.yaml --dry-run=client 
pod/ashupod-007 created (dry run)
fire@ashutoshhs-MacBook-Air containers_apps % 
fire@ashutoshhs-MacBook-Air containers_apps % kubectl create  -f  ashupod1.yaml                  
pod/ashupod-007 created
fire@ashutoshhs-MacBook-Air containers_apps % kubectl  get  pods
NAME               READY   STATUS    RESTARTS   AGE
ashupod-007        1/1     Running   0          60s
natarajanpod-007   1/1     Running   0          37s
rameshpod-007      1/1     Running   0          41s

```
