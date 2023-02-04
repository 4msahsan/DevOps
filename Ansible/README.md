<pre>
<h1> Ansible in CI/CD Pipeline </h1>

[ansadmin@ansible docker]$ docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
[ansadmin@ansible docker]$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[ansadmin@ansible docker]$ ansible-playbook -i inventory regapp.yml

PLAY [ansible] ************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************
ok: [192.168.0.147]

TASK [create docker image] ************************************************************************************************************
changed: [192.168.0.147]

PLAY RECAP ****************************************************************************************************************************
192.168.0.147              : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
[ansadmin@ansible docker]$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
regapp       latest    27e8886cc51d   39 minutes ago   478MB
ansadmin@ansible docker]$ cat Dockerfile
FROM tomcat:latest
RUN cp -R /usr/local/tomcat/webapps.dist/* /usr/local/tomcat/webapps
COPY ./*.war /usr/local/tomcat/webapps

[ansadmin@ansible docker]$ cat regapp.yml
---
- hosts: ansible


  tasks:
  - name: create docker image
    command: docker build -t regapp:latest .
    args:
     chdir: /opt/docker
[ansadmin@ansible docker]$


[ansadmin@ansible docker]$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
regapp       latest    27e8886cc51d   3 hours ago   478MB
[ansadmin@ansible docker]$ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: nixmsa
Password:
WARNING! Your password will be stored unencrypted in /home/ansadmin/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
[ansadmin@ansible docker]$

[ansadmin@ansible docker]$ docker tag 27e8886cc51d nixmsa/regapp:latest
[ansadmin@ansible docker]$ docker images
REPOSITORY      TAG       IMAGE ID       CREATED       SIZE
regapp          latest    27e8886cc51d   3 hours ago   478MB
nixmsa/regapp   latest    27e8886cc51d   3 hours ago   478MB
[ansadmin@ansible docker]$ docker push nixmsa/regapp
Using default tag: latest
The push refers to repository [docker.io/nixmsa/regapp]
8eb60b5b7ced: Pushed
58584ccc8cdb: Pushed
850c57462c5e: Mounted from library/tomcat
971f0c572b95: Mounted from library/tomcat
06f1e4d6c406: Mounted from library/tomcat
4cf6b33083da: Mounted from library/tomcat
2428b038926e: Mounted from library/tomcat
2b1193862943: Mounted from library/tomcat
c5ff2d88f679: Mounted from library/tomcat
latest: digest: sha256:08e5fca15850ea5b4448e3d70f01751d2b7fa32a5d8c558c023de47eade32b3c size: 2207
[ansadmin@ansible docker]$


[ansadmin@ansible docker]$ docker image prune -a
WARNING! This will remove all images without at least one container associated to them.
Are you sure you want to continue? [y/N] y
Deleted Images:
untagged: regapp:latest
untagged: nixmsa/regapp:latest
untagged: nixmsa/regapp@sha256:08e5fca15850ea5b4448e3d70f01751d2b7fa32a5d8c558c023de47eade32b3c
deleted: sha256:27e8886cc51d489938d6a67f5e3bbd2ec10cd86ebd8aa460fdae321da791e45f

Total reclaimed space: 0B
[ansadmin@ansible docker]$ docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
[ansadmin@ansible docker]$ ansible-playbook -i inventory regapp.yml

PLAY [ansible] ************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************
ok: [192.168.0.147]

TASK [create docker image] ************************************************************************************************************
changed: [192.168.0.147]

TASK [create tag to push image onto dockerhub] ****************************************************************************************
changed: [192.168.0.147]

TASK [push docker image] **************************************************************************************************************
changed: [192.168.0.147]

PLAY RECAP ****************************************************************************************************************************
192.168.0.147              : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

[ansadmin@ansible docker]$ docker images
REPOSITORY      TAG       IMAGE ID       CREATED       SIZE
regapp          latest    27e8886cc51d   4 hours ago   478MB
nixmsa/regapp   latest    27e8886cc51d   4 hours ago   478MB
[ansadmin@ansible docker]$

[ansadmin@ansible docker]$ ll
total 24
-rwxrwxrwx 1 ansadmin ansadmin  128 Feb  3 18:03 Dockerfile
-rw-rw-r-- 1 ansadmin ansadmin  431 Feb  3 23:35 regapp_deploy.yml
-rw-rw-r-- 1 ansadmin ansadmin  332 Feb  3 21:43 regapp.yml
-rw-rw-r-- 1 ansadmin ansadmin 9877 Feb  3 22:00 webapp.war

[ansadmin@ansible docker]$ cat regapp_deploy.yml
---
- hosts:  dockerce

  tasks:
  - name: stop existing container
    command: docker stop regapp-server
    ignore_errors: yes

  - name: remove the container
    command: docker rm regapp-server
    ignore_errors: yes

  - name: remove image
    command: docker rmi nginx/regapp:latest
    ignore_errors: yes

  - name: create container
    command: docker run -d --name regapp-server -p 8082:8080 nixmsa/regapp:latest


</pre>

![Alt text](https://github.com/4msahsan/DevOps/blob/main/Ansible/png/01.png "msahsan@hotmail.com")
