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

<h2> Application deployed kubernetes cluster on EC2 instanc</h2>

<img src="https://shreyagorey.s3.ap-south-1.amazonaws.com/knote_app.JPG" />

Issue faced: Application was accessible inside EC2 instance. Checked service knote

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

$ k port-forward --address 0.0.0.0 svc/knote 8080:80
    Forwarding from 0.0.0.0:8080 -> 3000
    Handling connection for 8080

    <h3> That solved the issue</h3>
_Last updated: 04 Jan 2022_
