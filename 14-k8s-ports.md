# service - expose containers
* kubectl expose : create service for pods

## service types
| service      | notes                           |
|--------------|---------------------------------|
| ClusterIP    | within nodes/pods               |
| NodePort     | high port testing node          |
| LoadBalancer | lb from outside                 |
| ExternalName | node communicate outside system |
| Ingress      | (later)                         |

* ClusterIP (dft): only within the cluster (nodes/pods)
* NodePort: high port on each node
* LoadBalancer: LB endpoint outside cluster
* ExternalName: allows cluster to communicate outside system
* Ingress: later

# Create ClusterIP service

## create pods
### kubectl create deploy httpenv --image=bretfisher/httpenv
## scale
### kubectl scale deploy/httpenv --replicas=3
deployment.apps/httpenv scaled
### kubectl get pods
```
NAME                       READY   STATUS    RESTARTS   AGE
httpenv-6fdc8554fb-2ck4d   1/1     Running   0          118s
httpenv-6fdc8554fb-9c84k   1/1     Running   0          12s
httpenv-6fdc8554fb-668ds   1/1     Running   0          12s
```

### kubectl expose deploy/httpenv --port 8888
service/httpenv exposed
### kubectl get service
```
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
kubernetes   ClusterIP   10.152.183.1     <none>        443/TCP    3h28m
httpenv      ClusterIP   10.152.183.211   <none>        8888/TCP   30s
```
## check from linux curl <service-ip>:port
### curl 10.152.183.211:8888
`{"HOME":"/root","HOSTNAME":"httpenv-6fdc8554fb-9c84k","KUBERNETES_PORT":"tcp://10.152.183.1:443","KUBERNETES_PORT_443_TCP":"tcp://10.152.183.1:443","KUBERNETES_PORT_443_TCP_ADDR":"10.152.183.1","KUBERNETES_PORT_443_TCP_PORT":"443","KUBERNETES_PORT_443_TCP_PROTO":"tcp","KUBERNETES_SERVICE_HOST":"10.152.183.1","KUBERNETES_SERVICE_PORT":"443","KUBERNETES_SERVICE_PORT_HTTPS":"443","PATH":"/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"}`

# Create NodePort service

## create nodeport
### kubectl expose deploy/httpenv --port 8888 --name httpenv-np --type NodePort
service/httpenv-np exposed
### kubectl get service
```
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
kubernetes   ClusterIP   10.152.183.1     <none>        443/TCP          3h39m
httpenv      ClusterIP   10.152.183.211   <none>        8888/TCP         10m
httpenv-np   NodePort    10.152.183.212   <none>        8888:31167/TCP   14s
```
## test localhost:port
### curl localhost:31167
{"HOME":"/root","HOSTNAME":"httpenv-6fdc8554fb-9c84k","KUBERNETES_PORT":"tcp://10.152.183.1:443","KUBERNETES_PORT_443_TCP":"tcp://10.152.183.1:443","KUBERNETES_PORT_443_TCP_ADDR":"10.152.183.1","KUBERNETES_PORT_443_TCP_PORT":"443","KUBERNETES_PORT_443_TCP_PROTO":"tcp","KUBERNETES_SERVICE_HOST":"10.152.183.1","KUBERNETES_SERVICE_PORT":"443","KUBERNETES_SERVICE_PORT_HTTPS":"443","PATH":"/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"}

# Create LoadBalancer service

### kubectl expose deploy/httpenv --port 8888 --name httpenv-lb --type LoadBalancer
service/httpenv-lb exposed
### kubectl get service
```
NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
kubernetes   ClusterIP      10.152.183.1     <none>        443/TCP          3h44m
httpenv      ClusterIP      10.152.183.211   <none>        8888/TCP         16m
httpenv-np   NodePort       10.152.183.212   <none>        8888:31167/TCP   5m38s
httpenv-lb   LoadBalancer   10.152.183.141   <pending>     8888:30305/TCP   17s
```
* NB microk8s does not support lb, pending

# Cleanup
### kubectl delete service/httpenv service/httpenv-np service/httpenv-lb deploy/httpenv
```
service "httpenv" deleted
service "httpenv-np" deleted
service "httpenv-lb" deleted
deployment.apps "httpenv" deleted
```

# Kubernetes DNS

## Namespaces

### k get namespaces
```
NAME              STATUS   AGE
kube-system       Active   3h59m
kube-public       Active   3h59m
kube-node-lease   Active   3h59m
default           Active   3h59m
```
