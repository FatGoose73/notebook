---
title: Linux基础(八)：租云服务器及配置docker环境
date: 2022-02-21 23:17:00
tags: [Linux, docker]
categories: 教程
cover: https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fsh.tedu.cn%2Fupload%2F20181116%2F20181116101135_624.jpg&refer=http%3A%2F%2Fsh.tedu.cn&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1648048575&t=8b04d38f8a99b905ac07587a95a3e0a7
mathjax: true
---

#### 概述
##### 云平台的作用:

存放我们的docker容器，让计算跑在云端。
获得公网IP地址，让每个人可以访问到我们的服务。

##### 任选一个云平台即可，推荐配置：
1核 2GB（后期可以动态扩容，前期配置低一些没关系）
网络带宽采用按量付费，最大带宽拉满即可（费用取决于用量，与最大带宽无关）
系统版本：ubuntu 20.04 LTS（推荐用统一版本，避免后期出现配置不兼容的问题）
#### 租云平台
##### 阿里云
阿里云地址：https://www.aliyun.com/

###### 创建工作用户`username`(username改成你自己的)并赋予`sudo`权限

登录到新服务器。打开Terminal，然后：
```shell
ssh root@xxx.xxx.xxx.xxx  # xxx.xxx.xxx.xxx替换成新服务器的公网IP
```
创建`username`用户：
```shell
adduser username  # 创建用户acs
usermod -aG sudo acs  # 给用户acs分配sudo权限
```
###### 配置免密登录方式

退回Terminal，然后配置username用户的别名和免密登录，可以参考[ssh——ssh登录](#ssh)。

###### 配置新服务器的工作环境

将Terminal的配置传到新服务器上：
```shell
scp .bashrc .vimrc .tmux.conf server_name:  # server_name需要换成自己配置的别名
```
###### 安装`tmux`和`docker`
登录自己的服务器，然后安装`tmux`：
```shell
sudo apt-get update
sudo apt-get install tmux
```
打开`tmux`。（养成好习惯，所有工作都在`tmux`里进行，防止意外关闭终端后，工作进度丢失）

然后在`tmux`中根据[docker安装教程](https://docs.docker.com/engine/install/ubuntu/)安装`docker`即可。

##### 腾讯云
腾讯云地址：https://cloud.tencent.com/
##### 华为云
华为云地址：https://www.huaweicloud.com/
#### docker教程
##### 将当前用户添加到`docker`用户组

为了避免每次使用`docker`命令都需要加上`sudo`权限，可以将当前用户加入安装中自动创建的`docker用户组`([可以参考官方文档](https://docs.docker.com/engine/install/linux-postinstall/))：
```shell
sudo usermod -aG docker $USER
```
##### 镜像（images）

1. `docker pull ubuntu:20.04`：拉取一个镜像

2. `docker images`：列出本地所有镜像
3. `docker image rm ubuntu:20.04` 或 `docker rmi ubuntu:20.04`：删除镜像`ubuntu:20.04`
4. `docker [container] commit CONTAINER IMAGE_NAME:TAG`：创建某个`container`的镜像
5. `docker save -o ubuntu_20_04.tar ubuntu:20.04`：将镜像`ubuntu:20.04`导出到本地文件`ubuntu_20_04.tar`中
6. `docker load -i ubuntu_20_04.tar`：将镜像`ubuntu:20.04`从本地文件`ubuntu_20_04.tar`中加载出来

##### 容器(container)

1. `docker [container] create -it ubuntu:20.04`：利用镜像`ubuntu:20.04`创建一个容器。

2. `docker ps -a`：查看本地的所有容器
3. `docker [container] start CONTAINER`：启动容器
4. `docker [container] stop CONTAINER`：停止容器
5. `docker [container] restart CONTAINER`：重启容器
6. `docker [contaienr] run -itd ubuntu:20.04`：创建并启动一个容器
7. `docker [container] attach CONTAINER`：进入容器
   - 先按`Ctrl-p`，再按`Ctrl-q`可以挂起容器
8. `docker [container] exec CONTAINER COMMAND`：在容器中执行命令
9. `docker [container] rm CONTAINER`：删除容器
10. `docker container prune`：删除所有已停止的容器
11. `docker export -o xxx.tar CONTAINER`：将容器`CONTAINER`导出到本地文件`xxx.tar`中
12. `docker import xxx.tar image_name:tag`：将本地文件`xxx.tar`导入成镜像，并将镜像命名为`image_name:tag`
13. `docker export/import与docker save/load`的区别：

    - `export/import`会丢弃历史记录和元数据信息，仅保存容器当时的快照状态

    - `save/load`会保存完整记录，体积更大

14. `docker top CONTAINER`：查看某个容器内的所有进程
15. `docker stats`：查看所有容器的统计信息，包括CPU、内存、存储、网络等信息
16. `docker cp xxx CONTAINER:xxx` 或 `docker cp CONTAINER:xxx xxx`：在本地和容器间复制文件
17. `docker rename CONTAINER1 CONTAINER2`：重命名容器
18. `docker update CONTAINER --memory 500MB`：修改容器限制

##### 实战

进入AC Terminal，然后：
```shell
scp /var/lib/acwing/docker/images/docker_lesson_1_0.tar server_name:  # 将镜像上传到自己租的云端服务器
ssh server_name  # 登录自己的云端服务器

docker load -i docker_lesson_1_0.tar  # 将镜像加载到本地
docker run -p 20000:22 --name my_docker_server -itd docker_lesson:1.0  # 创建并运行docker_lesson:1.0镜像

docker attach my_docker_server  # 进入创建的docker容器
passwd  # 设置root密码
```
去云平台控制台中修改安全组配置，放行端口`20000`。

返回AC Terminal，即可通过`ssh`登录自己的`docker`容器：
```shell
ssh root@xxx.xxx.xxx.xxx -p 20000  # 将xxx.xxx.xxx.xxx替换成自己租的服务器的IP地址
```
然后，可以仿照上节课内容，创建工作账户`acs`。

最后，可以参考[ssh——ssh登录](#ssh)配置`docker`容器的别名和免密登录。

##### 小Tips
如果`apt-get`下载软件速度较慢，可以参考[清华大学开源软件镜像站](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)中的内容，修改软件源。