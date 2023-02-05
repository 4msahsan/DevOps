<pre>
[ansadmin@ansible docker]$ cat regapp-deployment-k8s.yml
---
<b>
- hosts: microk8s
  user: root
  #become: true

  tasks:
  - name: deploy regapp on k8s
    command: microk8s  kubectl apply -f regapp-deployment.yml </b>

[ansadmin@ansible docker]$ ansible-playbook regapp-deployment-k8s.yml

PLAY [microk8s] ***************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************************************************************************
ok: [192.168.0.159]

TASK [deploy regapp on k8s] ***************************************************************************************************************************************************************************************************
changed: [192.168.0.159]

PLAY RECAP ********************************************************************************************************************************************************************************************************************
192.168.0.159              : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

[ansadmin@ansible docker]$ cat regapp-sevice-k8s.yml <b>
---
- hosts: microk8s
  user: root
  #become: true
  tasks:
  - name: deploy regapp on k8s
    command: microk8s  kubectl apply -f regapp-svc.yml </b>

[ansadmin@ansible docker]$ ansible-playbook regapp-sevice-k8s.yml

PLAY [microk8s] ***************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************************************************************************************************
ok: [192.168.0.159]

TASK [deploy regapp on k8s] ***************************************************************************************************************************************************************************************************
changed: [192.168.0.159]

PLAY RECAP ********************************************************************************************************************************************************************************************************************
192.168.0.159              : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

<b>
[root@microk8s ~]# kk get svc
NAME           TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes     ClusterIP   10.152.183.1     <none>        443/TCP        15h
demo-service   NodePort    10.152.183.241   <none>        80:30007/TCP   168m
[root@microk8s ~]# kk get deploy
No resources found in default namespace.
[root@microk8s ~]# kk get po
NAME       READY   STATUS    RESTARTS   AGE
demo-pad   1/1     Running   0          4h26m
[root@microk8s ~]# kk get deploy
NAME            READY   UP-TO-DATE   AVAILABLE   AGE
nixmsa-regapp   2/2     2            2           66s
[root@microk8s ~]# kk get svc
NAME             TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
kubernetes       ClusterIP   10.152.183.1     <none>        443/TCP          15h
demo-service     NodePort    10.152.183.241   <none>        80:30007/TCP     171m
nixmsa-service   NodePort    10.152.183.39    <none>        8080:30009/TCP   51s
[root@microk8s ~]# kk get po
NAME                             READY   STATUS    RESTARTS   AGE
demo-pad                         1/1     Running   0          4h29m
nixmsa-regapp-76d947f6b8-wqpcd   1/1     Running   0          73s
nixmsa-regapp-76d947f6b8-gw7g5   1/1     Running   0          73s
[root@microk8s ~]#
</b>
</pre>

