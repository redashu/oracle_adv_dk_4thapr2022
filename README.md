# Plan 

<img src="plan.png">

## CRE to Host 

<img src="cre.png">

### OCI 

<img src="oci.png">

### Docker using RUNC 

<img src="runc.png">

### install docker and enable service 

```
 2  yum  install  docker  
    3  history 
    4  systemctl enable --now docker 
    5  systemctl status docker 
```

### NON root users can not access docker serivce 

<img src="ds.png">

### addding non root users to docker group 

```
 grep -i docker  /etc/group
docker:x:992:
[root@docker-host ~]# 
[root@docker-host ~]# 
[root@docker-host ~]# for  i in  natraj ashu akshay dev ramesh rohit roni 
> do
> useradd $i 
> echo "Oracle098#" |  passwd $i --stdin 
> done 
Changing password for user natraj.
passwd: all authentication tokens updated successfully.
Changing password for user ashu.
passwd: all authentication tokens updated successfully.
Changing password for user akshay.
passwd: all authentication tokens updated successfully.
Changing password for user dev.
passwd: all authentication tokens updated successfully.
Changing password for user ramesh.
passwd: all authentication tokens updated successfully.
Changing password for user rohit.
passwd: all authentication tokens updated successfully.
Changing password for user roni.
passwd: all authentication tokens updated successfully.
[root@docker-host ~]# su  - ashu 
[ashu@docker-host ~]$ docker  images
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.40/images/json": dial unix /var/run/docker.sock: connect: permission denied
[ashu@docker-host ~]$ exit
logout
[root@docker-host ~]# usermod -a -G  docker  ashu
[root@docker-host ~]# grep -i docker  /etc/group
docker:x:992:ashu
[root@docker-host ~]# 
[root@docker-host ~]# su - ashu 
Last login: Mon Apr  4 05:06:28 GMT 2022 on pts/0
[ashu@docker-host ~]$ docker  images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
[ashu@docker-host ~]$ logout
[root@docker-host ~]# for  i in  natraj ashu akshay dev ramesh rohit roni ; do usermod -a -G docker $i ; done 
[root@docker-host ~]# 
[root@docker-host ~]# 
[root@docker-host ~]# grep -i docker  /etc/group
docker:x:992:ashu,natraj,akshay,dev,ramesh,rohit,roni
```

### creating container and checking process 

```
 docker  run -it -d  --name  ashuc1 alpine  
Unable to find image 'alpine:latest' locally
Trying to pull repository docker.io/library/alpine ... 
latest: Pulling from docker.io/library/alpine
40e059520d19: Pull complete 
Digest: sha256:f22945d45ee2eb4dd463ed5a431d9f04fcd80ca768bb1acf898d91ce51f7bf04
Status: Downloaded newer image for alpine:latest
71b7efa07e4d848365d0e4c8140defb4616afdf9eaf93fab38742a0330f47f36
[ashu@docker-host ~]$ 
[ashu@docker-host ~]$ docker  ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
71b7efa07e4d        alpine              "/bin/sh"           5 seconds ago       Up 4 seconds                            ashuc1

```

### container using same host kernel but they are isolated due to Namespaces 

<img src="ns.png">

### container app process ID 

```
docker  inspect  ddbe5d93bdc1  --format='{{.State.Pid}}'
19718

```

### checking in backend 

```
 cd  /proc/19718/
[root@docker-host 19718]# ls
arch_status  cmdline          environ  io         mem         ns             pagemap      schedstat     stack    task
attr         comm             exe      limits     mountinfo   numa_maps      personality  sessionid     stat     timers
auxv         coredump_filter  fd       loginuid   mounts      oom_adj        projid_map   setgroups     statm    timerslack_ns
cgroup       cpuset           fdinfo   map_files  mountstats  oom_score      root         smaps         status   uid_map
clear_refs   cwd              gid_map  maps       net         oom_score_adj  sched        smaps_rollup  syscall  wchan
[root@docker-host 19718]# ls ns/
cgroup  ipc  mnt  net  pid  pid_for_children  user  uts
```

### Namespace in the backend 

```
nsenter -t  24115  --net  ifconfig 
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.9  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:ac:11:00:09  txqueuelen 0  (Ethernet)
        RX packets 98  bytes 9236 (9.0 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 88  bytes 8168 (7.9 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

[root@docker-host 19718]# nsenter -t  24115  --uts  --mount  sh 
/ # cat etc/os-release 
NAME="Alpine Linux"
ID=alpine
VERSION_ID=3.15.3
PRETTY_NAME="Alpine Linux v3.15"
HOME_URL="https://alpinelinux.org/"
BUG_REPORT_URL="https://bugs.alpinelinux.org/"
/ # ifconfig 
ifconfig: /proc/net/dev: No such file or directory
docker0   Link encap:Ethernet  HWaddr 02:42:E1:36:5B:50  
          inet addr:172.17.0.1  Bcast:172.17.255.255  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1

ens3      Link encap:Ethernet  HWaddr 02:00:17:09:EC:4F  
          inet addr:10.0.0.76  Bcast:10.0.0.255  Mask:255.255.255.0
          UP BROADCAST RUNNING MULTICAST  MTU:9000  Metric:1

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1

/ # exit
[root@docker-host 19718]# ls
ls: cannot open directory .: No such process
[root@docker-host 19718]# nsenter -t  24115  --uts  --mount  sh 
/ # ls
bin    dev    etc    home   lib    media  mnt    opt    proc   root   run    sbin   srv    sys    tmp    usr    var
```

