---
title: jenkins deploy node project
date: 2020-07-30 23:11:41
tags:
---

### Install jenkins

```shell
docker pull jenkins/jenkins
```

### Start jenkins container

```shell
# create /home/jenkins_home file and chage mode
$ mkdir /var/jenkins_home
$ sudo chmod 777 /var/jenkins_home

# start jenkins container
$ docker run -d -p 8080:8080 -p 50000:50000 -v /var/jenkins_home:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock -v /usr/bin/docker:/usr/bin/docker -v /etc/localtime:/etc/localtime --name jenkins docker.io/jenkins/jenkins

-d                                后台运行镜像
-p 8080:8080                      将镜像的8080端口映射到服务器的8080端口(服务器端口：镜像端口)
-p 50000:50000                    基于 JNLP 的 Jenkins 代理通过 TCP 端口 50000 与 Jenkins master 进行通信,所以也要映射
-v /var/jenkins_home:/var/jenkins_home                /var/jenkins_home目录为jenkins工作目录，我们将硬盘上的一个目录挂载到这个位置，方便后续更新镜像后继续使用原来的工作目录。
-v /var/run/docker.sock:/var/run/docker.sock          jenkins容器内也需要docker来执行任务，所以这里将docker命令也映射到容器里
-v /usr/bin/docker:/usr/bin/docker                    将docker命令映射到容器内
-v /etc/localtime:/etc/localtime  让容器使用和服务器同样的时间设置。
--name myjenkins                  给容器起一个别名
docker.io/jenkins/jenkins         使用刚下载的镜像安装
# reference
# https://juejin.im/post/6844904035326099470
# https://www.jenkins.io/doc/book/installing/
```

### Jenkins config

#### Initialize

Open http://host:8080 and input initial password, then install recommended plugin.

```shell
$ docker exec -it jenkins bash
$ cat /var/jenkins_home/secrets/initialAdminPassword
```

When installing plugins is slow , change the Jenkins-> Manage Jenkins -> Manage plugins -> Advanced -> set Update Site URL as  http://mirror.esuni.jp/jenkins/updates/update-center.json

#### SSH

Config ssh key, in order to let jenkis pull code from git if needed.

#### Create jenkins item

1. Create a freestyle project

2. General -> Select GitHub project and put the project url

3. Source Code Management -> Select Git and put the project url

4. Build -> Add build step -> Esecute shell
   
   ```shell
   docker build --tag playground:$BUILD_NUMBER .
   docker stop playground && docker rm playground
   docker run -p 3000:3000 --name playground playground:$BUILD_NUMBER
   ```

    5. Save and build the item

注意：不要使用默认源安装docker， 不要使用默认源安装docker， 不要使用默认源安装docker， 不然会遇到下面的error

```
jenkins /usr/bin/docker: 2: .: Can't open /etc/sysconfig/docker

//

You don't have either docker-client or docker-client-latest installed. Please install either one and retry
```

 if this `Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post` error occurs , run `chmod 777 /var/run/docker.sock`
