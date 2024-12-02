---
title: Docker常用命令
date: 2024-12-02T03:58:14.967Z
---

![](https://www.docker.com/wp-content/uploads/2024/07/1300x1300_evergreen-docker-blog_d-980x980.png)
# 前言
由于Docker的普遍适用性，使得越来越多的服务与应用都是用Docker进行统一封装，投放到Docker Hub之后其他用户直接通过命令行拉取搭建即可。总结一下经常用到的Docker命令。

# 命令
注意，一般和Docker相关的操作都需要用到Root用户权限，所以绝大多数情况下要在命令前添加sudo，或者切换到root用户来进行提权操作
## 基础管理
列出所有**正在运行**的Docker容器
```bash
docker ps
```
列出所有Docker——包含已经停止运行的docker容器
```bash
docker ps -a
```
通过容器的ID或者自定义名字来停止某个正在运行的docker
```bash
docker stop [container ID | container Name]
```
根据ID或自定义容器名重启容器
```bash
docker start [container ID| container Name]
```


删除容器——容器必须**先停止运行**
```bash
docker rm [container ID | container Name]
```
## docker Run
基础语法：
```bash
docker run [options] [ImageName] [command] [argument]
```
常用Options
|选项|含义|
|-|--|
|-a stdin|指定标准输入输出内容——即Linux内核的STDIN STDOUT STDERR信息|
|-d|后台运行，在成功运行container之后，会在命令行给出新容器的ID|
|-P|制定端口映射，格式为[*宿主端口号*]:[*容器端口号*]|
|--name|自定义容器名称，即上文中container Name|
|--volume或者-v|制定container的存储位置，格式为[宿主机路径]:[容器中某个路径]|
### docker run示例
```bash
docker run -d --name=maxkb -p 8080:8080 -v ~/.maxkb:/var/lib/postgresql/data 1panel/maxkb
```
- -d代表后台运行，在执行完成后会在命令行给出新容器的ID
- --name=maxkb，代表新容器的自定义名称为maxkb
- -p指定将宿主机的8080端口映射到容器的8080端口
- -v则将宿主机的~/.maxkb路径映射到容器内的/var/lib/postgresql/data
- 1panbel/maxkb 表明拉取的镜像是1panel/maxkb
## 镜像
在上一步当中涉及到镜像概念，其实就是已经封装完成的docker应用，放在镜像站供人拉取，在镜像拉取到本地之后创建docker容器实例。

- 拉取远程镜像——不创建docker实例
```bash
docker pull [authorName]/[imageName]
```
- 通过docker run 拉取——创建docker实例

参照上文当中的docker run实例，最后部分的1panel/maxkb即为镜像作者/镜像名称

在拉取过程中，docker会首先寻找本地是否有相同镜像名称的镜像，如果有则会直接使用本地的镜像运行对应的镜像实例，否则会到远程拉取。

### 从第三方的tar文件镜像进行操作：
- 导出镜像
```bash
docker save [ImageID] > [fileName].tar
```
imageID的来源：

查看所有本地镜像
```bash
docker image ls
```
- 从tar文件中导入镜像
```bash
docker load < [fileName].tar
```
- dokckerfile生成镜像
dockerfile本身为一种特殊的文本文件，其中包含了详细的docker镜像创建流程——按顺序，内容如下：

|参数|内容|
|-|-|
|from|基础镜像来源|
|maintainer|author|
|run|docker内的运行命令|
|add|添加镜像所需要的文件|
|workdir|工作目录|
|volume|挂载目录|
|expose|对外暴露的端口|
|run|容器启动之后的命令|
- 将容器保存为镜像
停止正在运行的容器：
```bash
docker stop [container ID | container Name]
```
通过docker commit进行操作
```bash
docker commit [container ID] [imageName]
```
其中imageName即为需要生成的镜像文件名称

### 镜像管理
前文以及提及的
```bash
docker image ls #列出所有镜像
```
删除镜像
```bash
docker image rm [imageName | imageID]
#或
docker rmi [imageName | imageID]
```

## 对正在运行中的docker进行更改
docker exec
```bash
docker exec -it [containerName | containerID] bash
```

