<pre>
<h1> Kubernetes Commands </h1>

[root@microk8s ~]# dnf install epel-release

root@microk8s ~]# dnf --enablerepo=epel -y install snapd

root@microk8s ~]# ln -s /var/lib/snapd/snap /snap
[root@microk8s ~]#  echo 'export PATH=$PATH:/var/lib/snapd/snap/bin' > /etc/profile.d/snap.sh
[root@microk8s ~]# systemctl enable --now snapd.service snapd.socket
Created symlink /etc/systemd/system/multi-user.target.wants/snapd.service → /usr/lib/systemd/system/snapd.service.
Created symlink /etc/systemd/system/sockets.target.wants/snapd.socket → /usr/lib/systemd/system/snapd.socket.
[root@microk8s ~]# snap list
No snaps are installed yet. Try 'snap install hello-world'.
[root@microk8s ~]#  snap find kubernetes
Name                         Version               Publisher              Notes    Summary
kubernetes-worker            0.0.2                 canonical✓             -        A complete Kubernetes worker
kubernetes-test              1.26.1                canonical✓             classic  tests for kubernetes
kubernetes-test-eks          1.10.3                canonical-cloud-snaps  classic  tests for kubernetes
microk8s                     v1.26.0               canonical✓             classic  Kubernetes for workstations and appliances
kata-containers              2.4.2                 katacontainers✓        c

 snap install microk8s --classic
 
 
 [root@microk8s ~]# kk create deployment demo-nginx --image=nginx --port=80 --replicas=3
deployment.apps/demo-nginx created
[root@microk8s ~]# kk get po
NAME                          READY   STATUS              RESTARTS   AGE
demo-nginx-65476f6c6d-gq8xl   0/1     ContainerCreating   0          10s
demo-nginx-65476f6c6d-76xm9   0/1     ContainerCreating   0          10s
demo-nginx-65476f6c6d-zsp52   0/1     ContainerCreating   0          10s
[root@microk8s ~]# kk get deploy
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
demo-nginx   3/3     3            3           17s
[root@microk8s ~]# kk get po
NAME                          READY   STATUS    RESTARTS   AGE
demo-nginx-65476f6c6d-gq8xl   1/1     Running   0          19s
demo-nginx-65476f6c6d-76xm9   1/1     Running   0          19s
demo-nginx-65476f6c6d-zsp52   1/1     Running   0          19s
[root@microk8s ~]#

[root@microk8s ~]# kk get replicaset
NAME                    DESIRED   CURRENT   READY   AGE
demo-nginx-65476f6c6d   3         3         3       98s
[root@microk8s ~]#

[root@microk8s ~]# kk expose deployment demo-nginx --port=80 --type=NodePort
service/demo-nginx exposed

[root@microk8s ~]# kk get svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.152.183.1    <none>        443/TCP        10h
demo-nginx   NodePort    10.152.183.54   <none>        80:31081/TCP   10s
[root@microk8s ~]#

[root@microk8s ~]# 192.168.0.159
[root@microk8s ~]# kk get svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.152.183.1    <none>        443/TCP        10h
demo-nginx   NodePort    10.152.183.54   <none>        80:31081/TCP   3m17s
[root@microk8s ~]# curl 192.168.0.159:31081
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
Welcome to nginx!
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
[root@microk8s ~]#


[root@microk8s YML]# cat pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: demo-pad
  labels:
    app: demo-app
spec:
  containers:
  - name: demo-nginx
    image: nginx
    ports:
      - name: demo-nginx
        containerPort: 80
[root@microk8s YML]# kk get pods
NAME       READY   STATUS    RESTARTS   AGE
demo-pad   1/1     Running   0          45s

root@microk8s YML]# kk describe pod demo-pad
Name:             demo-pad
Namespace:        default
Priority:         0
Service Account:  default
Node:             microk8s/192.168.0.159
Start Time:       Sat, 04 Feb 2023 21:46:47 -0800
Labels:           app=demo-app
Annotations:      cni.projectcalico.org/containerID: 7495bf1b32bab7bafea2381ad8112fdb35ef6265f21e68802fbc1b9af5c5e3f5
                  cni.projectcalico.org/podIP: 10.1.128.200/32
                  cni.projectcalico.org/podIPs: 10.1.128.200/32
Status:           Running
IP:               10.1.128.200
IPs:
  IP:  10.1.128.200
Containers:
  demo-nginx:
    Container ID:   containerd://aeb2e02a91762922e35477507ec06a66682d6e6ea64e4a8decf8d3e077ed6dfa
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:c54fb26749e49dc2df77c6155e8b5f0f78b781b7f0eadd96ecfabdcdfa5b1ec4
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sat, 04 Feb 2023 21:46:50 -0800
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-z92sx (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  kube-api-access-z92sx:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason             Age                From               Message
  ----     ------             ----               ----               -------
  Normal   Scheduled          97s                default-scheduler  Successfully assigned default/demo-pad to microk8s
  Normal   Pulling            96s                kubelet            Pulling image "nginx"
  Normal   Pulled             96s                kubelet            Successfully pulled image "nginx" in 907.608344ms (907.612475ms including waiting)
  Normal   Created            96s                kubelet            Created container demo-nginx
  Normal   Started            95s                kubelet            Started container demo-nginx
  Warning  MissingClusterDNS  10s (x5 over 97s)  kubelet            pod: "demo-pad_default(16181463-770d-41de-aa61-42685212c3f9)". kubelet does not have ClusterDNS IP configured and cannot create Pod using "ClusterFirst" policy. Falling back to "Default" policy.


[root@microk8s YML]# kk create -f service.yml
service/demo-service created
[root@microk8s YML]# kk get svc
NAME           TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
kubernetes     ClusterIP   10.152.183.1     <none>        443/TCP   11h
demo-service   ClusterIP   10.152.183.200   <none>        80/TCP    7s
[root@microk8s YML]# cat service.yml
apiVersion: v1
kind: Service
metadata:
  name: demo-service
spec:
  ports:
  - name: nginx-port
    port: 80
    targetPort: 80
[root@microk8s YML]#

[root@microk8s YML]# kk get all
NAME           READY   STATUS    RESTARTS   AGE
pod/demo-pad   1/1     Running   0          10m

NAME                   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
service/kubernetes     ClusterIP   10.152.183.1     <none>        443/TCP   11h
service/demo-service   ClusterIP   10.152.183.200   <none>        80/TCP    86s
[root@microk8s YML]#

[root@microk8s YML]# cat service.yml
apiVersion: v1
kind: Service
metadata:
  name: demo-service
spec:
  type: NodePort
  ports:
  - name: nginx-port
    port: 80
    targetPort: 80
    nodePort: 30007
[root@microk8s YML]# kk get svc
NAME           TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes     ClusterIP   10.152.183.1     <none>        443/TCP        11h
demo-service   NodePort    10.152.183.106   <none>        80:30007/TCP   37s
[root@microk8s YML]#



[root@microk8s YML]# kk describe svc demo-service
Name:                     demo-service
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 <none>
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.152.183.106
IPs:                      10.152.183.106
Port:                     nginx-port  80/TCP
TargetPort:               80/TCP
NodePort:                 nginx-port  30007/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
[root@microk8s YML]#

[root@microk8s YML]# kk get svc
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.152.183.1   <none>        443/TCP   12h
[root@microk8s YML]# ll
total 8
-rw-r--r-- 1 root root 207 Feb  4 21:46 pod.yml
-rw-r--r-- 1 root root 212 Feb  4 23:23 service.yml
[root@microk8s YML]# kk apply -f service.yml
service/demo-service created
[root@microk8s YML]# kk get svc
NAME           TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes     ClusterIP   10.152.183.1     <none>        443/TCP        12h
demo-service   NodePort    10.152.183.241   <none>        80:30007/TCP   3s
[root@microk8s YML]# kk describe svc demo-service
Name:                     demo-service
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=demo-app
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.152.183.241
IPs:                      10.152.183.241
Port:                     nginx-port  80/TCP
TargetPort:               80/TCP
NodePort:                 nginx-port  30007/TCP
Endpoints:                10.1.128.200:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
[root@microk8s YML]# curl 10.1.128.200:80
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

[root@microk8s YML]# cat pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: demo-pad
<b> labels:
    app: demo-app </b>
spec:
  containers:
  - name: demo-nginx
    image: nginx
    ports:
      - name: demo-nginx
        containerPort: 80



[root@microk8s YML]# cat service.yml
apiVersion: v1
kind: Service
metadata:
  name: demo-service
spec:
  type: NodePort
  ports:
  - name: nginx-port
    port: 80
    targetPort: 80
    nodePort: 30007
<b> selector:
    app: demo-app
</b>

[root@microk8s YML]#

[root@microk8s YML]# cat regapp-deployment.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nixmsa-regapp
  labels:
    app: regapp
spec:
  replicas: 2
  selector:
    matchLabels:
      app: regapp

  template:
    metadata:
      labels:
        app: regapp
    spec:
      containers:
      - name: regapp
        image: nixmsa/regapp
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
  strategy:
    type: RollingUpdate
    rollingUpdate:
        maxSurge: 1
        maxUnavailable: 1
[root@microk8s YML]# kk get deploy
NAME            READY   UP-TO-DATE   AVAILABLE   AGE
nixmsa-regapp   2/2     2            2           4m3s
[root@microk8s YML]# kk get po
NAME                             READY   STATUS    RESTARTS   AGE
demo-pad                         1/1     Running   0          164m
nixmsa-regapp-76d947f6b8-zwsvb   1/1     Running   0          4m8s
nixmsa-regapp-76d947f6b8-kmh4v   1/1     Running   0          4m8s
[root@microk8s YML]# kk describe deploy nixmsa-regapp
Name:                   nixmsa-regapp
Namespace:              default
CreationTimestamp:      Sun, 05 Feb 2023 00:26:41 -0800
Labels:                 app=regapp
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=regapp
Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  1 max unavailable, 1 max surge
Pod Template:
  Labels:  app=regapp
  Containers:
   regapp:
    Image:        nixmsa/regapp
    Port:         8080/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nixmsa-regapp-76d947f6b8 (2/2 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  4m26s  deployment-controller  Scaled up replica set nixmsa-regapp-76d947f6b8 to 2
[root@microk8s YML]#

[root@microk8s YML]# kk describe pod nixmsa-regapp-76d947f6b8-zwsvb
Name:             nixmsa-regapp-76d947f6b8-zwsvb
Namespace:        default
Priority:         0
Service Account:  default
Node:             microk8s/192.168.0.159
Start Time:       Sun, 05 Feb 2023 00:26:41 -0800
Labels:           app=regapp
                  pod-template-hash=76d947f6b8
Annotations:      cni.projectcalico.org/containerID: c3cd7e2d17aca1348d1de8fc46ed4e540e48a734f64b90b69abc4117f16bbdae
                  cni.projectcalico.org/podIP: 10.1.128.203/32
                  cni.projectcalico.org/podIPs: 10.1.128.203/32
Status:           Running
IP:               10.1.128.203
IPs:
  IP:           10.1.128.203
Controlled By:  ReplicaSet/nixmsa-regapp-76d947f6b8
Containers:
  regapp:
    Container ID:   containerd://4678402c9e3977e2c59288a8d1da460a0474b3f00e2a3061039c59d2cbc16376
    Image:          nixmsa/regapp
    Image ID:       docker.io/nixmsa/regapp@sha256:e64ed381c5c09257f7a1b0f90ff4b3d744baa20f5bd1edab37acc0c7645fca84
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sun, 05 Feb 2023 00:27:16 -0800
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-6784m (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  kube-api-access-6784m:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason             Age                  From     Message
  ----     ------             ----                 ----     -------
  Normal   Pulled             5m                   kubelet  Successfully pulled image "nixmsa/regapp" in 32.554840939s (32.554993784s including waiting)
  Normal   Created            5m                   kubelet  Created container regapp
  Normal   Started            4m59s                kubelet  Started container regapp
  Warning  MissingClusterDNS  60s (x7 over 5m34s)  kubelet  pod: "nixmsa-regapp-76d947f6b8-zwsvb_default(34c27573-2212-4928-8e5c-dabb895a9180)". kubelet does not have ClusterDNS IP configured and cannot create Pod using "ClusterFirst" policy. Falling back to "Default" policy.
[root@microk8s YML]#


** SERVICE **
root@microk8s YML]# cat regapp-svc.yml
apiVersion: v1
kind: Service
metadata:
  name: nixmsa-service
  labels:
    app: regapp
spec:
  selector:
    app: regapp
  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 30009

  type: NodePort
[root@microk8s YML]# kk get svc
NAME             TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
kubernetes       ClusterIP   10.152.183.1     <none>        443/TCP          14h
demo-service     NodePort    10.152.183.241   <none>        80:30007/TCP     72m
nixmsa-service   NodePort    10.152.183.83    <none>        8080:30009/TCP   32s
[root@microk8s YML]#

[root@microk8s YML]# kk describe svc nixmsa-service
Name:                     nixmsa-service
Namespace:                default
Labels:                   app=regapp
Annotations:              <none>
Selector:                 app=regapp
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.152.183.83
IPs:                      10.152.183.83
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  30009/TCP
Endpoints:                10.1.128.203:8080,10.1.128.204:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
[root@microk8s YML]#

[root@microk8s YML]# curl 10.1.128.203:8080



<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <title>Apache Tomcat/10.1.5</title>
        <link href="favicon.ico" rel="icon" type="image/x-icon" />
        <link href="tomcat.css" rel="stylesheet" type="text/css" />
    </head>

    <body>
        <div id="wrapper">
            <div id="navigation" class="curved container">
                <span id="nav-home"><a href="https://tomcat.apache.org/">Home</a></span>
                <span id="nav-hosts"><a href="/docs/">Documentation</a></span>
                <span id="nav-config"><a href="/docs/config/">Configuration</a></span>
                <span id="nav-examples"><a href="/examples/">Examples</a></span>
                <span id="nav-wiki"><a href="https://cwiki.apache.org/confluence/display/TOMCAT/">Wiki</a></span>
                <span id="nav-lists"><a href="https://tomcat.apache.org/lists.html">Mailing Lists</a></span>
                <span id="nav-help"><a href="https://tomcat.apache.org/findhelp.html">Find Help</a></span>
                <br class="separator" />
            </div>
            <div id="asf-box">
                <h1>Apache Tomcat/10.1.5</h1>



</pre>

