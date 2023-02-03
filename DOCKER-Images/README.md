<pre>
<h2>TomCat Container<h2>
[mahsan@dockerce ~]$ docker pull tomcat
Using default tag: latest
latest: Pulling from library/tomcat
10ac4908093d: Pull complete
6df15e605e38: Pull complete
2db012dd504c: Pull complete
8fa912900627: Pull complete
f8fe20946c04: Pull complete
8093daf900d2: Pull complete
49c22a329043: Pull complete
Digest: sha256:9e2525bd79c5386c9bd9ba56fe450263d7af605e41db9fead44e1969379b588a
Status: Downloaded newer image for tomcat:latest
docker.io/library/tomcat:latest

<b>[mahsan@dockerce ~]$ docker images</b>
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
tomcat       latest    2362f0cdbf14   20 hours ago   474MB

<b>[mahsan@dockerce ~]$ docker run -d --name tomcat-container -p 8081:8080 tomcat</b>
45e838128bd3bbb59f54b41824a0b9a720287598a451e4618b892f5935d23164
[mahsan@dockerce ~]$ docker ps -a
CONTAINER ID   IMAGE     COMMAND             CREATED         STATUS         PORTS                                       NAMES
45e838128bd3   tomcat    "catalina.sh run"   5 seconds ago   Up 4 seconds   0.0.0.0:8081->8080/tcp, :::8081->8080/tcp   tomcat-container
[mahsan@dockerce ~]$ docker ps -a
CONTAINER ID   IMAGE     COMMAND             CREATED       STATUS       PORTS                                       NAMES
45e838128bd3   tomcat    "catalina.sh run"   3 hours ago   Up 3 hours   0.0.0.0:8081->8080/tcp, :::8081->8080/tcp   tomcat-container
<b>[mahsan@dockerce ~]$ docker exec -it tomcat-container /bin/bash</b>
root@45e838128bd3:/usr/local/tomcat# ls
bin           conf             lib      logs            NOTICE     RELEASE-NOTES  temp     webapps.dist
BUILDING.txt  CONTRIBUTING.md  LICENSE  native-jni-lib  README.md  RUNNING.txt    webapps  work
root@45e838128bd3:/usr/local/tomcat# cd webapps
root@45e838128bd3:/usr/local/tomcat/webapps# ll
total 0
drwxr-xr-x 2 root root  6 Feb  1 02:40 ./
drwxr-xr-x 1 root root 30 Feb  1 02:40 ../
root@45e838128bd3:/usr/local/tomcat/webapps# ls
root@45e838128bd3:/usr/local/tomcat/webapps# cd ..
root@45e838128bd3:/usr/local/tomcat# cd /usr/local/tomcat/
root@45e838128bd3:/usr/local/tomcat# ls
bin           conf             lib      logs            NOTICE     RELEASE-NOTES  temp     webapps.dist
BUILDING.txt  CONTRIBUTING.md  LICENSE  native-jni-lib  README.md  RUNNING.txt    webapps  work
root@45e838128bd3:/usr/local/tomcat# cd webapps.dist/
root@45e838128bd3:/usr/local/tomcat/webapps.dist# ls
docs  examples  host-manager  manager  ROOT
root@45e838128bd3:/usr/local/tomcat/webapps.dist# cp -R * ../webapps/
root@45e838128bd3:/usr/local/tomcat/webapps.dist# cd ../webapps
root@45e838128bd3:/usr/local/tomcat/webapps# ls
docs  examples  host-manager  manager  ROOT
root@45e838128bd3:/usr/local/tomcat/webapps#

[mahsan@dockerce images]$ docker ps -a
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                       NAMES
933e672ae106   tomcat    "/opt/tomcat/bin/cat…"   50 seconds ago   Up 50 seconds   0.0.0.0:8083->8080/tcp, :::8083->8080/tcp   mytocat-server
[mahsan@dockerce images]$ cat Dockerfile
FROM rockylinux:8
RUN yum install java -y
RUN mkdir /opt/tomcat
WORKDIR /opt/tomcat
ADD https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.71/bin/apache-tomcat-9.0.71.tar.gz .
RUN tar -xvzf apache-tomcat-9.0.71.tar.gz
RUN mv apache-tomcat-9.0.71/* /opt/tomcat
EXPOSE 8080
CMD ["/opt/tomcat/bin/catalina.sh", "run"]
[mahsan@dockerce images]$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
tomcat       latest    597090d11c05   10 minutes ago   523MB
rockylinux   8         63f107ef819e   6 weeks ago      197MB

[mahsan@dockerce images]$  docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                       NAMES
933e672ae106   tomcat    "/opt/tomcat/bin/cat…"   8 minutes ago   Up 8 minutes   0.0.0.0:8083->8080/tcp, :::8083->8080/tcp   mytocat-server

[mahsan@dockerce images]$ cat Dockerfile
FROM tomcat:latest
RUN cp -R /usr/local/tomcat/webapps.dist/* /usr/local/tomcat/webapps

[mahsan@dockerce images]$ docker ps -a
CONTAINER ID   IMAGE     COMMAND             CREATED          STATUS          PORTS                                       NAMES
d18aa3b16716   tomcat2   "catalina.sh run"   59 seconds ago   Up 58 seconds   0.0.0.0:8085->8080/tcp, :::8085->8080/tcp   tomcat2
[mahsan@dockerce images]$

[dockeradmin@dockerce docker]$ cat Dockerfile
FROM tomcat:latest
RUN cp -R /usr/local/tomcat/webapps.dist/* /usr/local/tomcat/webapps
COPY ./*.war /usr/local/tomcat/webapps

[dockeradmin@dockerce docker]$ ll
total 8
-rwxrwxrwx 1 dockeradmin dockeradmin  129 Feb  2 11:28 Dockerfile
-rw-rw-r-- 1 dockeradmin dockeradmin 2528 Feb  2 11:24 webapp.war
[dockeradmin@dockerce docker]$ docker build -t tomcat:v1 .
Sending build context to Docker daemon   5.12kB
Step 1/3 : FROM tomcat:latest
 ---> 2362f0cdbf14
Step 2/3 : RUN cp -R /usr/local/tomcat/webapps.dist/* /usr/local/tomcat/webapps
 ---> Using cache
 ---> d3d5a7489629
Step 3/3 : COPY ./*.war /usr/local/tomcat/webapps
 ---> 265153fc6b78
Successfully built 265153fc6b78
Successfully tagged tomcat:v1
[dockeradmin@dockerce docker]$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
tomcat       v1        265153fc6b78   6 seconds ago   478MB
tomcat2      latest    d3d5a7489629   13 hours ago    478MB
tomcat       latest    2362f0cdbf14   41 hours ago    474MB
[dockeradmin@dockerce docker]$
[dockeradmin@dockerce docker]$ docker ps -a
CONTAINER ID   IMAGE       COMMAND             CREATED         STATUS         PORTS                                       NAMES
dca58de270f1   tomcat:v1   "catalina.sh run"   8 seconds ago   Up 6 seconds   0.0.0.0:8086->8080/tcp, :::8086->8080/tcp   tomcatv1
d18aa3b16716   tomcat2     "catalina.sh run"   13 hours ago    Up 13 hours    0.0.0.0:8085->8080/tcp, :::8085->8080/tcp   tomcat2

-rw-rw-r-- 1 dockeradmin dockeradmin 2528 Feb  2 11:24 webapp.war
[dockeradmin@dockerce docker]$ docker ps -a
CONTAINER ID   IMAGE       COMMAND             CREATED          STATUS          PORTS                                       NAMES
dca58de270f1   tomcat:v1   "catalina.sh run"   13 minutes ago   Up 13 minutes   0.0.0.0:8086->8080/tcp, :::8086->8080/tcp   tomcatv1
d18aa3b16716   tomcat2     "catalina.sh run"   14 hours ago     Up 14 hours     0.0.0.0:8085->8080/tcp, :::8085->8080/tcp   tomcat2
[dockeradmin@dockerce docker]$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
tomcat       v1        265153fc6b78   16 minutes ago   478MB
tomcat2      latest    d3d5a7489629   14 hours ago     478MB
tomcat       latest    2362f0cdbf14   41 hours ago     474MB
[dockeradmin@dockerce docker]$  docker rm -f $(docker ps -a -q)
dca58de270f1
d18aa3b16716
[dockeradmin@dockerce docker]$  docker rmi $(docker images -a -q)
Untagged: tomcat:v1
Deleted: sha256:265153fc6b786f1c65e01c0da85050a686101efd26cc9d76a6f788f75fde77e7
Deleted: sha256:9b1a83d5068316c0bd0b19e324b5ab51063d8073af1460340e4827d6d24aec49
Untagged: tomcat2:latest
Deleted: sha256:d3d5a7489629b0c905bf2275e0054f4d81959aea3bbfaae2ad0c31ff0267424b
Deleted: sha256:d7b3a1ded699c20fc09878c6c4be91d8a0b6ce0cb1c36bb3fca436c24e1d372f
Untagged: tomcat:latest
Untagged: tomcat@sha256:9e2525bd79c5386c9bd9ba56fe450263d7af605e41db9fead44e1969379b588a
Deleted: sha256:2362f0cdbf14867a1c1ef2e1cc8dc4e78c4f8b66351acdbe71c8ab938e54f364
Deleted: sha256:301da3f7c3d21a8f2b8277a503094c073f8590d50aa14d3ff61474a408b24173
Deleted: sha256:8a77a541c44a41c746957cffbfae4371460b1f3fb830e1efdafc54851410c95f
Deleted: sha256:193b5f6e1d63da1e1e77210f6eaf54ad85e74da97924e0c9a778972bdc04337b
Deleted: sha256:946053b8886e3d663150c166cac63e94978ea8c29f087da3154d64f5a66a56ce
Deleted: sha256:2a6cefd88a47e238c7f3353ceccb865862bf9a8e490320cc781daa7085d041ca
Deleted: sha256:f773875b27ea4af5d222542ae85c25c34b50ae09a6b620ccf7c5198ed1a3aca8
Deleted: sha256:c5ff2d88f67954bdcf1cfdd46fe3d683858d69c2cadd6660812edfc83726c654
[dockeradmin@dockerce docker]$
[dockeradmin@dockerce docker]$ cat Dockerfile
FROM tomcat:latest
RUN cp -R /usr/local/tomcat/webapps.dist/* /usr/local/tomcat/webapps
COPY ./*.war /usr/local/tomcat/webapps
</pre>
![Alt text](https://github.com/4msahsan/DevOps/blob/main/DOCKER-Images/png/01.png "msahsan@hotmail.com")
![Alt text](https://github.com/4msahsan/DevOps/blob/main/DOCKER-Images/png/02.png "msahsan@hotmail.com")
![Alt text](https://github.com/4msahsan/DevOps/blob/main/DOCKER-Images/png/03.png "msahsan@hotmail.com")
![Alt text](https://github.com/4msahsan/DevOps/blob/main/DOCKER-Images/png/04.png "msahsan@hotmail.com")
![Alt text](https://github.com/4msahsan/DevOps/blob/main/DOCKER-Images/png/05.png "msahsan@hotmail.com")
![Alt text](https://github.com/4msahsan/DevOps/blob/main/DOCKER-Images/png/06.png "msahsan@hotmail.com")
![Alt text](https://github.com/4msahsan/DevOps/blob/main/DOCKER-Images/png/07.png "msahsan@hotmail.com")
![Alt text](https://github.com/4msahsan/DevOps/blob/main/DOCKER-Images/png/08.png "msahsan@hotmail.com")
![Alt text](https://github.com/4msahsan/DevOps/blob/main/DOCKER-Images/png/09.png "msahsan@hotmail.com")
![Alt text](https://github.com/4msahsan/DevOps/blob/main/DOCKER-Images/png/10.png "msahsan@hotmail.com")
![Alt text](https://github.com/4msahsan/DevOps/blob/main/DOCKER-Images/png/11.png "msahsan@hotmail.com")
![Alt text](https://github.com/4msahsan/DevOps/blob/main/DOCKER-Images/png/12.png "msahsan@hotmail.com")
