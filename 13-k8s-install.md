# kubernetes install 

## install microk8s
* uninstall kind and microk8s
* install microk8s and setup

```
	sudo snap install microk8s --classic
	microk8s.enable dns # enable CoreDNS
	microk8s.status # check status, running
```

* kubectl version 

```
Client Version: version.Info{Major:"1", Minor:"20+", GitVersion:"v1.20.7-34+df7df22a741dbc", GitCommit:"df7df22a741dbc18dc3de3000b2393a1e3c32d36", GitTreeState:"clean", BuildDate:"2021-05-12T21:08:20Z", GoVersion:"go1.15.10", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"20+", GitVersion:"v1.20.7-34+df7df22a741dbc", GitCommit:"df7df22a741dbc18dc3de3000b2393a1e3c32d36", GitTreeState:"clean", BuildDate:"2021-05-12T21:09:51Z", GoVersion:"go1.15.10", Compiler:"gc", Platform:"linux/amd64"}
```

## Terms
* Pod: basic unit of deployment, 1 or more containers
* Controller: controls/updates pod.  Deployment, ReplicaSet, StatefulSet, Job, ...
* Service: network endpoint to connect to a pod
* Namespace: filter on view (not security feature)
* Secrets, ConfigMaps, ...

## First Pod
* deploy via **command** or yml file

  kubectl run my-nginx --image nginx

*   pod/my-nginx created

### kubectl get pods

```
NAME       READY   STATUS    RESTARTS   AGE
my-nginx   1/1     Running   0          73s
```

### kubectl get all
```
NAME           READY   STATUS    RESTARTS   AGE
pod/my-nginx   1/1     Running   0          2m44s

NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.152.183.1   <none>        443/TCP   69m
```

### kubectl delete pod/my-nginx
```
pod "my-nginx" deleted
```

## Scale pod

### kubectl create deployment my-apache --image httpd
deployment.apps/my-apache created
### kubectl scale deploy/my-apache --replicas 3
deployment.apps/my-apache scaled
### kubectl get pods
```
my-apache-7b68fdd849-gz2ff   1/1     Running   0          2m42s
my-apache-7b68fdd849-m82d5   1/1     Running   0          2m2s
my-apache-7b68fdd849-6z2t8   1/1     Running   0          2m2s
```

### kubectl logs deploy/my-apache
```
Found 3 pods, using pod/my-apache-7b68fdd849-gz2ff
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.1.91.135. Set the 'ServerName' directive globally to suppress this message
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.1.91.135. Set the 'ServerName' directive globally to suppress this message
[Sun Jun 06 17:45:25.773521 2021] [mpm_event:notice] [pid 1:tid 139751834154112] AH00489: Apache/2.4.48 (Unix) configured -- resuming normal operations
[Sun Jun 06 17:45:25.773639 2021] [core:notice] [pid 1:tid 139751834154112] AH00094: Command line: 'httpd -D FOREGROUND'

```
### n't have a resource type "my-apache-7b68fdd849-gz2ff"
```
(base) glenn@t3600:~$ k describe pod/my-apache-7b68fdd849-gz2ff
Name:         my-apache-7b68fdd849-gz2ff
Namespace:    default
Priority:     0
Node:         t3600/192.168.0.19
Start Time:   Sun, 06 Jun 2021 12:45:24 -0500
Labels:       app=my-apache
              pod-template-hash=7b68fdd849
Annotations:  cni.projectcalico.org/podIP: 10.1.91.135/32
              cni.projectcalico.org/podIPs: 10.1.91.135/32
Status:       Running
IP:           10.1.91.135
...
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  7m1s  default-scheduler  Successfully assigned default/my-apache-7b68fdd849-gz2ff to t3600
  Normal  Pulling    7m1s  kubelet            Pulling image "httpd"
  Normal  Pulled     7m    kubelet            Successfully pulled image "httpd" in 662.739654ms
  Normal  Created    7m    kubelet            Created container httpd
  Normal  Started    7m    kubelet            Started container httpd

```


### kubectl delete deploy/my-apache
deployment.apps "my-apache" deleted


```
