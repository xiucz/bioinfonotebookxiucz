<!-- @import "[TOC]" {cmd="toc" depthFrom=2 depthTo=3 orderedList=false} -->

[toc]

https://github.com/guoshijiang/docker-virtual-technology

## 概念
Docker 包括三个基本概念:

镜像（Image）：Docker 镜像（Image），就相当于是一个 root 文件系统。比如官方镜像 ubuntu:16.04 就包含了完整的一套 Ubuntu16.04 最小系统的 root 文件系统。
容器（Container）：镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。
仓库（Repository）：仓库可看着一个代码控制中心，用来保存镜像。

+ docker-ce
+ DockerEE
## 基础信息
```
docker --version
docker version
docker info
docker ps 查看正在运行的容器
docker stop停止正在运行的容器
docker start启动容器
docker ps -a查看终止状态的容器
docker rm -f webserver命令来移除正在运行的容器
docker rm $(docker ps -a -q) 删除所有容器
docker images 列出本地镜像
docker rmi 删除镜像
docker run —name NAMES IMAGE 将镜像IMAGE生成NAMES的容器
docker image ls 列出docker镜像
docker search python 寻找镜像
docker pull python:3.5

docker run hello-world 运行docker镜像

```
## 使用
### 在容器内运行应用程序
```
$ docker run -t -i ubuntu:15.10 /bin/echo "Hello world"

#docker: Docker 的二进制执行文件。
#run: 与前面的 docker 组合来运行一个容器。
#ubuntu:15.10 指定要运行的image，Docker 首先从本地主机上查找镜像是否存在，如果不存在，Docker 就会从镜像仓库 Docker Hub 下载公共镜像。
# /bin/echo "Hello world": 在启动的容器里执行的命令

* -t：进入终端，在新容器内指定一个伪终端或终端。
* -i：获得一个交互式的连接，通过获取container的输入
```

### 后台模式启动
```
docker run -d ubuntu:15.10 /bin/sh -c "while true; do echo hello world; sleep 1; done"
```
### 退出
我们可以通过运行 exit 命令或者使用 CTRL+D 来退出容器。

### 进入容器
```
docker attach

docker exec：推荐大家使用 docker exec 命令，因为此退出容器终端，不会导致容器的停止。
```

## 导出和导入容器
```
$ docker export 1e560fca3906 > ubuntu.tar
```

