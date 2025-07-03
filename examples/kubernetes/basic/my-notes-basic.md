## Example Kubernetes 

https://github.com/SanjeevKGupta/inference-gateway/tree/main/examples/kubernetes/basic

### On Mac
#### 1. You will need `docker`CLI 
Docker desktop having restriction on Mac. Use rancher desktop instead. Comes with `docker` CLI.
- https://rancherdesktop.io
#### 2. Fork and then clone locally on your Mac 
```
git clone https://github.com/SanjeevKGupta/inference-gateway.git
```
#### 3. Install come CLIs
3.1 Install `task`
```
brew install go-task
```
3.2 Install `ctlptl` called "cattle patrol"
```
brew install tilt-dev/tap/ctlptl
```
3.3 Install `k3d`
```
brew install k3d
```
3.4 Install `kubectl` and `helm`

#### 4. Install infrastructure 
```
inference-gateway/inference-gateway/examples/kubernetes/basic
task deploy-infrastructure
```
```
kubectl cluster-info
Kubernetes control plane is running at https://0.0.0.0:53150
CoreDNS is running at https://0.0.0.0:53150/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
Metrics-server is running at https://0.0.0.0:53150/api/v1/namespaces/kube-system/services/https:metrics-server:https/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```
#### 5. Verify infrastructure 
```
sanjeev@Sanjeevs-MBP basic % kc get ns
NAME              STATUS   AGE
default           Active   7m10s
kube-node-lease   Active   7m10s
kube-public       Active   7m10s
kube-system       Active   7m10s
sanjeev@Sanjeevs-MBP basic % kc get all -n kube-system
NAME                                                READY   STATUS    RESTARTS   AGE
pod/coredns-ff8999cc5-t22w8                         1/1     Running   0          7m16s
pod/ingress-nginx-controller-6885cfc548-lg2ld       1/1     Running   0          7m4s
pod/local-path-provisioner-698b58967b-njdtv         1/1     Running   0          7m16s
pod/metrics-server-8584b5786c-64rgt                 1/1     Running   0          7m16s
pod/svclb-ingress-nginx-controller-cc444a6d-4pflc   2/2     Running   0          7m4s
pod/svclb-ingress-nginx-controller-cc444a6d-zrpkc   2/2     Running   0          7m4s

NAME                                         TYPE           CLUSTER-IP      EXTERNAL-IP             PORT(S)                      AGE
service/ingress-nginx-controller             LoadBalancer   10.43.252.174   172.18.0.2,172.18.0.3   80:32628/TCP,443:31634/TCP   7m4s
service/ingress-nginx-controller-admission   ClusterIP      10.43.127.154   <none>                  443/TCP                      7m4s
service/kube-dns                             ClusterIP      10.43.0.10      <none>                  53/UDP,53/TCP,9153/TCP       7m20s
service/metrics-server                       ClusterIP      10.43.88.197    <none>                  443/TCP                      7m19s

NAME                                                     DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemonset.apps/svclb-ingress-nginx-controller-cc444a6d   2         2         2       2            2           <none>          7m4s

NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/coredns                    1/1     1            1           7m20s
deployment.apps/ingress-nginx-controller   1/1     1            1           7m4s
deployment.apps/local-path-provisioner     1/1     1            1           7m20s
deployment.apps/metrics-server             1/1     1            1           7m19s

NAME                                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/coredns-ff8999cc5                     1         1         1       7m17s
replicaset.apps/ingress-nginx-controller-6885cfc548   1         1         1       7m4s
replicaset.apps/local-path-provisioner-698b58967b     1         1         1       7m17s
replicaset.apps/metrics-server-8584b5786c             1         1         1       7m17s
sanjeev@Sanjeevs-MBP basic % kc get all -n kube-public
No resources found in kube-public namespace.
sanjeev@Sanjeevs-MBP basic % kc get all -n kube-node-lease
No resources found in kube-node-lease namespace.
sanjeev@Sanjeevs-MBP basic % kc get all -n default        
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   7m44s
```
#### 6. Test deployment
Update `/etc/hosts` file with
```
127.0.0.1 api.inference-gateway.local
127.0.0.1 keycloak.inference-gateway.local
127.0.0.1 grafana.inference-gateway.local
```
#### 6.1
```
curl http://api.inference-gateway.local/v1/models

<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx</center>
</body>
</html>
```



