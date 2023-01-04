# Knote js

<div align="center">
    <a href="https://learnk8s.io/"><img src="https://avatars.githubusercontent.com/u/31817372?s=200&v=4" /></a>
    <p>Accompany repository for <a href="https://learnk8s.io/kubernetes-for-developers">Kubernetes for developers minicourse</a></p>
</div>

The folders in this repository correspond to the exercises in the course. In the course, you will learn how to:

1. Develop applications for Kubernetes using Express.js and Node.js and package it using Linux (and Docker) containers.
1. Set up a local Kubernetes environment with minikube and how to deploy applications in it.
1. Scale your application to more than a single instance.
1. Deploy your app in the cloud using Kubernetes, Amazon Web Services (AWS) and their managed Kubernetes offering.

<h2> Application deployed on kubernetes cluster on EC2 instance</h2>

<img src="https://shreyagorey.s3.ap-south-1.amazonaws.com/knote_app.JPG" />

Issue faced: Application was accessible only inside EC2 instance.  Checked service knote
<pre>
$k describe svc knote
Name:                     knote
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=knote
Type:                     LoadBalancer
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.104.197.183
IPs:                      10.104.197.183
Port:                     <unset>  80/TCP
TargetPort:               3000/TCP
NodePort:                 <unset>  32171/TCP
Endpoints:                172.17.0.3:3000
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
</pre>
$ k port-forward --address 0.0.0.0 svc/knote 8080:80
<pre>    Forwarding from 0.0.0.0:8080 -> 3000
    Handling connection for 8080
</pre>
    <h3> That solved the issue</h3>
 <h2> Got ImagePullBackOff error due to storage issue </h2>
 <pre>
 [ec2-user@ip-172-31-85-178 04-05]$ k get po --watch
NAME                     READY   STATUS              RESTARTS   AGE
knote-69f49fd6d9-szktx   1/1     Running             0          13s
minio-69bb788dfc-tngmx   1/1     Running             0          13s
mongo-586867d494-ksdsf   0/1     ContainerCreating   0          13s
mongo-586867d494-ksdsf   0/1     ErrImagePull        0          21s
mongo-586867d494-ksdsf   0/1     ImagePullBackOff    0          34s
    </pre>
<h2> Less storage i.e. less than 500M for device /dev/nvme0n1p1</h2>

<pre>
[ec2-user@ip-172-31-85-178 04-05]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        1.9G     0  1.9G   0% /dev
tmpfs           1.9G     0  1.9G   0% /dev/shm
tmpfs           1.9G  528K  1.9G   1% /run
tmpfs           1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/nvme0n1p1  8.0G  7.7G  381M  96% /
tmpfs           388M     0  388M   0% /run/user/1000
</pre>
<h2> Cleaned the disk space used by Docker to reclaim the storage space by first command. Modified EBS volume by increasing volume to 10G.</h2>
<ol>
<li> docker system prune --all --force</li>
<li> growpart /dev/nvme0n1p1 </li>
<li> xfs_growfs / </li>
  </ol>
 <pre>
 [ec2-user@ip-172-31-85-178 04-05]$ sudo xfs_growfs /
meta-data=/dev/nvme0n1p1         isize=512    agcount=4, agsize=524159 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=0, rmapbt=0
         =                       reflink=0    bigtime=0 inobtcount=0
data     =                       bsize=4096   blocks=2096635, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 2096635 to 2620923
</pre>
<h2> Increased filesystem to 10G : Output of df -h</h2>
<pre>
[ec2-user@ip-172-31-85-178 04-05]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        1.9G     0  1.9G   0% /dev
tmpfs           1.9G     0  1.9G   0% /dev/shm
tmpfs           1.9G  536K  1.9G   1% /run
tmpfs           1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/nvme0n1p1   10G  7.7G  2.4G  77% /
tmpfs           388M     0  388M   0% /run/user/1000
</pre>
_Last updated: 04 Jan 2022_
