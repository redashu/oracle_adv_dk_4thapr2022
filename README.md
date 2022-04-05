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




