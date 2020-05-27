<!-- @import "[TOC]" {cmd="toc" depthFrom=2 depthTo=3 orderedList=false} -->

https://github.com/guoshijiang/-virtual-technology

## 概念
 包括三个基本概念:

> 镜像（Image）： 镜像（Image），就相当于是一个 root 文件系统。比如官方镜像 ubuntu:16.04 就包含了完整的一套 Ubuntu16.04 最小系统的 root 文件系统。

> 容器（Container）：镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。

> 仓库（Repository）：仓库可看着一个代码控制中心，用来保存镜像。

+ ce
+ EE
## 基础信息
```
 --version
 version
 info
 ps 查看正在运行的容器
 stop停止正在运行的容器
 start启动容器
 ps -a查看终止状态的容器
 rm -f webserver命令来移除正在运行的容器
 rm $( ps -a -q) 删除所有容器
 images 列出本地镜像
 rmi 删除镜像
 run —name NAMES IMAGE 将镜像IMAGE生成NAMES的容器
 image ls 列出镜像
 search python 寻找镜像
 pull python:3.5 获取镜像

 run hello-world 运行镜像

#复制容器内容到物理机
  cp  testIpMap:/testData    / 
注：将容器testIpMap的/目录下的testData文件夹复制到物理机的 / 目录下

#复制物理机内容到容器
  cp  物理机目录    容器名：容器目录

```
## 容器使用

### 在容器内运行应用程序
```
$   run -t -i ubuntu:15.10 /bin/echo "Hello world"

#:  的二进制执行文件。
#run: 与前面的组合来运行一个容器。
#ubuntu:15.10 指定要运行的image，首先从本地主机上查找镜像是否存在，如果不存在， 就会从镜像仓库  Hub 下载公共镜像。**REPOSITORY:TAG**
# /bin/echo "Hello world": 在启动的容器里执行的命令

* -t：进入终端，在新容器内指定一个伪终端或终端。
* -i：获得一个交互式的连接，通过获取container的输入

#后台模式启动
 run -d ubuntu:15.10 /bin/sh -c "while true; do echo hello world; sleep 1; done"
# 退出
我们可以通过运行 exit 命令或者使用 CTRL+D 来退出容器。

# 进入容器
 attach
 exec：推荐大家使用  exec 命令，因为此退出容器终端，不会导致容器的停止。

## 导出和导入容器
  export 1e560fca3906 > ubuntu.tar
```

## 镜像使用
我们使用 REPOSITORY:TAG 来定义不同的镜像。
```
#删除镜像
rmi hello-world
```

https://mp.weixin.qq.com/s?__biz=MzUzMTEwODk0Ng==&mid=2247490244&idx=1&sn=f42a566710fa68ebd92b7d350359314e&chksm=fa46dff9cd3156ef7b4cd7d151cbcfc61518c00a10056ed4377ddd6c50858c7f99fb4eb2d3e7&scene=21#wechat_redirect


## 
```{bash}
$ docker pull polyactis/accurity
$ docker images
#Log into the docker image, without mounting. Useful just to look inside the docker.
$ docker run -i -t polyactis/accurity /bin/bash
# Log into the docker image.
# Mount /home/mydata, which contains your bam files and the reference data, to /mnt inside the docker
$ docker run -i -t -v /home/mydata:/mnt polyactis/accurity /bin/bash
```

## 问题故障
#### mage is being used by stopped container

https://stackoverflow.com/questions/51188657/image-is-being-used-by-stopped-container
