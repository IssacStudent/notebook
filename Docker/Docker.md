[toc]

# 一、Docker 概述

## 1. Docker 为什么出现

一款产品：开发—上线 两套环境（应用环境，应用配置）
开发—-运维。问题：我在我的电脑上可以运行！版本更新，导致服务不可用！对于运维来说，考验就十分大？
环境配置是十分的麻烦，每一个机器都要部署环境（集群 Redis、ES、Hadoop…）费时费力。
发布一个项目（jar +（Redis MySQL jdk ES）），项目能不能都带上环境安装打包！
之前在服务器配置一个应用的环境 Redis MySQL jdk ES Hadoop，配置超麻烦了，不能够跨平台。
Windows 开发，最后发布到 Linux？

+   传统：开发 jar，运维来做！
+   现在：开发打包部署上线，一套流程做完！

以安卓端的 apk 类比一下：

*   java —- apk — 发布（应用商店）—-张三使用 apk—-安装即可用！

*   java —- jar（环境）—- 打包项目带上环境（镜像）—-（Docker 仓库：商店）—-下载我们发布的镜像—直接运行即可！

**Docker 给以上的问题，提出了解决方案！**

Docker 的思想就来自于集装箱！

隔离：Docker 核心思想！打包装箱！每个箱子是互相隔离的。

>   本质：所有的技术都是因为出现了一些问题，我们需要去解决，才去学习。

## 2. Docker 的历史

2010 年，几个搞 IT 的年轻人，就在美国成立了一家公司 **dotcloud** 做一些 Pass 的云计算服务！

LXC 有关的容器技术！他们将自己的技术（容器化技术）命名就是 Docker！

Docker 刚刚诞生的时候，没有引起行业的注意！（dotCloud 就活不下去）

**开源**（开放源代码）

2013 年，Docker 开源！

Docker 越来越多的人发现了 docker 的优点！就火了，Docker 每个月都会更新一个版本！

2014 年 4 月 9 日，Docker1.0 发布！

Docker 为什么这么火？**十分的轻巧**

在容器技术出来之前，我们都是使用虚拟机技术！

虚拟机：在 window 中装一个 Vmware，通过这个软件我们可以虚拟出来一台或者多台电脑！（很笨重）

虚拟机也是属于虚拟化技术，Docker 容器技术，也是一种虚拟化技术！

>   vm：隔离，需要开启多个虚拟机！
>
>   linux centos 原生镜像（一个电脑！）需要几个G内存空间，开启需要几分钟！
>
>   docker：隔离，镜像（最核心的环境 4m 内存）十分的小巧，运行镜像就可以了！小巧！几个M或KB的内存空间，秒级启动！

Docker 是基于 Go 语言开发的！开源项目！

官网：https://www.docker.com/

文档地址：https://docs.docker.com/

Docker 镜像仓库地址：https://hub.docker.com/

## 3. Docker 能做什么

>   之前的虚拟机技术

![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272018485.jpeg)

虚拟机技术缺点：

1.   资源占用十分多
2.   冗余步骤多
3.   启动很慢！

>   容器化技术

**容器化技术不是模拟的一个完整的操作系统**
![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272018047.jpeg)

比较 Docker 和虚拟机技术的不同：

+   传统虚拟机，虚拟出一条硬件，运行一个完整的操作系统，然后在这个系统上安装和运行软件。
+   容器内的应用直接运行在宿主机的内核中，容器是没有自己的内核的，也没有虚拟我们的硬件，所以就轻便了。
+   每个容器间是互相隔离，每个容器内都有一个属于自己的文件系统，互不影响。

>   Devops（开发、运维）

**应用更快速的交付和部署**

*   传统：一堆帮助文档，安装程序!
*   Docker：打包镜像发布测试，一键运行!

**更便捷的升级和扩缩容**

使用了 Docker 之后，我们部署应用就和搭积木一样！项目打包为一个镜像，扩展服务器 A！服务器 B!

**更简单的系统运维**

在容器化之后，我们的开发，测试环境都是高度一致的。

**更高效的计算资源利用**

Docker 是内核级别的虚拟化，可以在一个物理机上可以运行很多的容器实例！服务器的性能可以被压榨到极致。

# 二、Docker 安装

## 1. Docker 的基本组成

![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272018198.jpeg)
**镜像（image）：**
docker 镜像就好比是一个模板，可以通过这个模板来创建容器服务，tomcat 镜像 ===>run==> tomcat01 容器（提供服务器），通过这个镜像可以创建多个容器（最终服务运行或者项目运行就是在容器中的)。

**容器（container）：**
Docker 利用容器技术，独立运行一个或者一个组应用，通过镜像来创建的。
启动，停止，删除，基本命令！
目前就可以把这个容器理解为就是一个简易的 linux 系统

**仓库（repository）：**
仓库就是存放镜像的地方！
仓库分为公有仓库和私有仓库！
Docker Hub（默认是国外的）阿里云.…都有容器服务器（配置镜像加速！）

## 2. 安装 Docker

>   环境准备

1.  需要会一点点的 Linux 的基础
2.  CentOS7
3.  我们使用 Xshell 连接远程服务器进行操作

>   环境查看

```shell
#系统内核要求3.0以上
[root@localhost ~]# uname -r
3.10.0-1062.el7.x86_64

#系统版本
[root@localhost ~]# cat /etc/os-release 
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

>   安装

查看文档：https://docs.docker.com/ （https://docs.docker.com/engine/install/centos/）

```shell
#1.卸载旧版本
 yum remove docker \
>                   docker-client \
>                   docker-client-latest \
>                   docker-common \
>                   docker-latest \
>                   docker-latest-logrotate \
>                   docker-logrotate \
>                   docker-engine

#2.需要的安装包
yum install -y yum-utils

#3.设置镜像的仓库
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
#上述方法默认是从国外的，不推荐

#推荐使用国内的
yum-config-manager \
    --add-repo \
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
  
#更新软件包索引
yum makecache fast

#4.安装docker docker-ce 社区版 而ee是企业版
yum install docker-ce docker-ce-cli containerd.io # 这里我们使用社区版即可

#5.启动docker
systemctl start docker

#6.使用docker version 查看是否安装成功
docker version
```

![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272019983.jpeg)

```shell
# 7.测试hello-world
docker run hello-world
```

![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272019216.jpeg)

```shell
#8.查看一下下载的hello-world镜像
[root@localhost /]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB

```

>   了解：卸载docker

```shell
#1.卸载依赖
yum remove docker-ce docker-ce-cli containerd.io

#2. 删除资源
rm -rf /var/lib/docker
# /var/lib/docker 是docker的默认工作路径！
```

## 3. 阿里云镜像加速

1.  登录阿里云，找到容器服务。

2.  找到镜像加速地址。

3.  配置使用。

    四个命令，依次执行即可：

    ```shell
    sudo mkdir -p /etc/docker
    ```

    ```shell
    sudo tee /etc/docker/daemon.json <<-'EOF'{"registry-mirrors": ["https://xxx.xxx.xxx.com"]}EOF
    ```

    ```shell
    sudo systemctl daemon-reload
    ```

    ```shell
    sudo systemctl restart docker
    ```

## 4. 回顾 hello-world 流程

![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272019142.png)

## 5. 底层原理

Docker 是怎么工作的？

Docker 是一个 Client-Server 结构的系统，Docker 的守护进程运行在主机上。通过 Socket 从客户端访问！

Docker-Server 接收到 Docker-Client 的指令，就会执行这个命令！
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272019392.png)

**Docker 为什么比 VM 快？**

1.   Docker 有着比虚拟机更少的抽象层。
2.   Docker 利用的是宿主机的内核，vm 需要是 Guest OS。
     ![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272019717.png)
     所以说，新建一个容器的时候，docker 不需要像虚拟机一样重新加载一个操作系统内核，避免引导。
     虚拟机是加载 GuestOS，分钟级别的，而 docker 是利用宿主机的操作系统，省略了这个复杂的过程，秒级！

# 三、Docker 的常用命令

## 1. 帮助命令

```shell
docker version     # 显示docker的版本信息
docker info        # 显示docker的系统信息，包括镜像和容器的数量
docker 命令 --help  # 帮助命令
```

帮助文档的地址：https://docs.docker.com/reference/

## 2. 镜像命令

### dokcer images

查看所有本地的主机上的镜像

```shell
[root@localhost /]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB

#解释
REPOSITORY  镜像的仓库源
TAG         镜像标签
IMAGE ID    镜像id
CREATED     镜像的创建时间
SIZE        镜像的大小

#可选项
Options:
  -a, --all             # 列出所有镜像
  -q, --quiet           # 只显示镜像id

```

### docker search

搜索镜像

```shell
[root@localhost /]# docker search mysql
NAME                              DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   9604                [OK]                
mariadb                           MariaDB is a community-developed fork of MyS…   3490                [OK]                

#可选项，通过收藏来过滤
--filter=STARS=3000  #搜索出来的镜像就是STARS大于3000的
[root@localhost /]# docker search mysql --filter=STARS=3000
NAME                DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql               MySQL is a widely used, open-source relation…   9604                [OK]                
mariadb             MariaDB is a community-developed fork of MyS…   3490                [OK]                
```

### docker pull

下载镜像

```shell
# 下载镜像 docker pull 镜像名[:tag]
[root@localhost /]# docker pull mysql
Using default tag: latest    # 如果不写 tag,默认就是latest
latest: Pulling from library/mysql
8559a31e96f4: Pull complete  # 分层下载，docker image的核心 联合文件系统
d51ce1c2e575: Pull complete 
c2344adc4858: Pull complete 
fcf3ceff18fc: Pull complete 
16da0c38dc5b: Pull complete 
b905d1797e97: Pull complete 
4b50d1c6b05c: Pull complete 
c75914a65ca2: Pull complete 
1ae8042bdd09: Pull complete 
453ac13c00a3: Pull complete 
9e680cd72f08: Pull complete 
a6b5dc864b6c: Pull complete 
Digest: sha256:8b7b328a7ff6de46ef96bcf83af048cb00a1c86282bfca0cb119c84568b4caf6 # 签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest  # 真实地址

docker pull mysql 等价于: docker pull docker.io/library/mysql:latest

# 指定版本下载
[root@localhost /]# docker pull mysql:5.7
5.7: Pulling from library/mysql
8559a31e96f4: Already exists   # 联合文件系统的好处：上面下载过的MySQL与5.7版本的MySQL有相同的文件时不需要重复下载
d51ce1c2e575: Already exists 
c2344adc4858: Already exists 
fcf3ceff18fc: Already exists 
16da0c38dc5b: Already exists 
b905d1797e97: Already exists 
4b50d1c6b05c: Already exists 
d85174a87144: Pull complete 
a4ad33703fa8: Pull complete 
f7a5433ce20d: Pull complete 
3dcd2a278b4a: Pull complete 
Digest: sha256:32f9d9a069f7a735e28fd44ea944d53c61f990ba71460c5c183e610854ca4854
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7

```

### docker rmi

删除镜像

```shell
[root@localhost /]# docker rmi -f 镜像id  				 #删除指定镜像
[root@localhost /]# docker rmi -f 镜像id 镜像id 镜像id  	   #删除多个镜像
[root@localhost /]# docker rmi -f $(docker images -aq)     #删除全部镜像
```

## 3. 容器命令

说明：我们有了镜像才可以创建容器，linux，下载一个centos 镜像来测试学习。

```shell
docker pull centos
```

### 新建容器并启动

```shell
docker run [可选参数] image

# 参数说明
--name="Name"	容器名字 tomcat01 tomcat02 ，用来区分容器
-d              后台方式运行
-it             使用交互方式运行，进入容器查看内容
-p              指定容器的端口 -p 8080:80
	-p  ip:主机(即宿主机)端口：容器端口
	-p  主机端口：容器端口  #这种方式常用
	-p  容器端口
	容器端口P
-P              随机指定端口(大写P)

# 测试，启动并进入容器
[root@localhost /]# docker run -it centos /bin/bash
[root@8b4c74381205 /]# ls     #查看容器内的centos,基础版本，很多命令都是不完善的！
bin  etc   lib	  lost+found  mnt  proc  run   srv  tmp  var
dev  home  lib64  media       opt  root  sbin  sys  usr

# 从容器中退回主机
[root@8b4c74381205 /]# exit
exit
[root@localhost /]# ls
123  bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
222  boot  etc  lib   media  opt  root  sbin  sys  usr
```

### 列出所有运行的容器

```shell
# docker ps 命令
(不加） # 列出当前正在运行的容器
-a     # 列出当前正在运行的容器 + 带出历史运行过的容器
-n=?   # 显示最近创建的容器
-q    # 只显示当前容器的编号
[root@localhost /]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@localhost /]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                          PORTS               NAMES
8b4c74381205        centos              "/bin/bash"         4 minutes ago       Exited (0) About a minute ago                       epic_wilson
fb87667bbc19        bf756fb1ae65        "/hello"            2 hours ago         Exited (0) 2 hours ago                              awesome_banach
[root@localhost /]# docker ps -a -n=1
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
8b4c74381205        centos              "/bin/bash"         9 minutes ago       Exited (0) 6 minutes ago                       epic_wilson
[root@localhost /]# docker ps -aq
8b4c74381205
fb87667bbc19
```

### 退出容器

```shell
exit   # 直接退出容器
Ctrl + p + q  # 容器不停止退出
```

### 删除容器

```shell
docker rm 容器id				   # 删除指定容器，不能删除正在运行的容器，如果要强制删除 rm -f
docker rm -f $(docker ps -aq)    # 删除所有容器 
docker ps -a -q|xargs docker rm  # 删除所有容器
```

### 启动和停止容器的操作

```shell
docker start 容器id     # 启动容器
docker restart 容器id   # 重启容器
docker stop 容器id      # 停止当前正在运行的容器
docker kill 容器id      # 强制停止当前正在运行的容器
```

## 4. 常用其他命令

### 后台启动容器

```shell
# 命令 docker run -d 镜像名

[root@localhost /]# docker run -d centos
e9d60f206fa19963203db6c42c2f83c5120eb90eeee2b7ba9fdc4589370fd6b6
[root@localhost /]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

# 问题docker ps,发现 centos 停止了

# 常见的坑，docker 容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止
# nginx,容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了
```

### 查看日志

```shell
docker logs -f -t --tail 数字 容器id

# 显示日志
-tf 	# 显示日志
--tail  # 要显示的日志条数
[root@localhost /]# docker logs -tf --tail 10 ce989f90023d 
```

### 查看容器中进程的信息

```shell
# 命令 docker top 容器id
[root@localhost /]# docker top ce989f90023d
UID                 PID                 PPID                C                   STIME               TTY                 TIME     
root                12249               12232               0                   22:44               pts/0               00:00:00 
```

### 查看镜像的元数据

```shell
# 命令
docker inspect 容器id

# 测试
[root@localhost /]# docker inspect ce989f90023d
[
    {
        "Id": "ce989f90023dedc0b3f39c057b91f5c0b17180b3aef7aea0df8c93731e724244",
        "Created": "2020-06-10T14:44:45.025360147Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 12249,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-06-10T14:44:45.770227584Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:470671670cac686c7cf0081e0b37da2e9f4f768ddc5f6a26102ccd1c6954c1ee",
        "ResolvConfPath": "/var/lib/docker/containers/ce989f90023dedc0b3f39c057b91f5c0b17180b3aef7aea0df8c93731e724244/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/ce989f90023dedc0b3f39c057b91f5c0b17180b3aef7aea0df8c93731e724244/hostname",
        "HostsPath": "/var/lib/docker/containers/ce989f90023dedc0b3f39c057b91f5c0b17180b3aef7aea0df8c93731e724244/hosts",
        "LogPath": "/var/lib/docker/containers/ce989f90023dedc0b3f39c057b91f5c0b17180b3aef7aea0df8c93731e724244/ce989f90023dedc0b3f39c057b91f5c0b17180b3aef7aea0df8c93731e724244-json.log",
        "Name": "/nifty_johnson",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Capabilities": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/bce8b2400427de29dd406d54ec08b3c07dc95530e80d37977a156ca971b37641-init/diff:/var/lib/docker/overlay2/d4cd3bedb1e7340e62bb292c1e0d5ae37b1d1689ffc1640da67b2a8325facc21/diff",
                "MergedDir": "/var/lib/docker/overlay2/bce8b2400427de29dd406d54ec08b3c07dc95530e80d37977a156ca971b37641/merged",
                "UpperDir": "/var/lib/docker/overlay2/bce8b2400427de29dd406d54ec08b3c07dc95530e80d37977a156ca971b37641/diff",
                "WorkDir": "/var/lib/docker/overlay2/bce8b2400427de29dd406d54ec08b3c07dc95530e80d37977a156ca971b37641/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "ce989f90023d",
            "Domainname": "",
            "User": "",
            "AttachStdin": true,
            "AttachStdout": true,
            "AttachStderr": true,
            "Tty": true,
            "OpenStdin": true,
            "StdinOnce": true,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20200114",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS",
                "org.opencontainers.image.created": "2020-01-14 00:00:00-08:00",
                "org.opencontainers.image.licenses": "GPL-2.0-only",
                "org.opencontainers.image.title": "CentOS Base Image",
                "org.opencontainers.image.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "74d140bbc60432c5fdce865fa48f78c1138923dd292e708a25c4de17de812d56",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/74d140bbc604",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "3580dd1064b07f434c61e316f14cb7d7b53a3d6d7c9c0f77eb6570f1781623bc",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.3",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:03",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "58fd9703e96d12128c30f244be3205e3fe31fc7d1fb7fffdddba72d981e782f4",
                    "EndpointID": "3580dd1064b07f434c61e316f14cb7d7b53a3d6d7c9c0f77eb6570f1781623bc",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.3",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:03",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

### 进入当前正在运行的容器

```shell
# 我们通常容器都是使用后台方式运行的，需要进入容器，修改一些配置

# 命令
docker exec -it 容器id bashShell

# 测试
[root@localhost /]# docker exec -it ce989f90023d /bin/bash
[root@ce989f90023d /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@ce989f90023d /]# ps -ef
UID         PID   PPID  C STIME TTY          TIME CMD
root          1      0  0 14:44 pts/0    00:00:00 /bin/bash
root         15      0  0 15:19 pts/1    00:00:00 /bin/bash
root         29     15  0 15:20 pts/1    00:00:00 ps -ef

# 方式二
docker attach 容器id
# 测试
[root@localhost /]# docker attach ce989f90023d
正在执行当前的代码...

# docker exec		# 进入容器后开启一个新的终端，可以在里面操作（常用）
# docker attach 	# 进入容器正在执行的终端，不会启动新的进程
```

### 从容器内拷贝文件到主机上

```
docker cp 容器id:容器内目标文件路径  目的主机路径

# 查看当前主机目录
[root@localhost home]# ls
ztx

# 进入docker容器内部
[root@localhost home]# docker attach ce989f90023d
[root@ce989f90023d /]# cd /home/
[root@ce989f90023d home]# ls

# 在容器内新建一个文件
[root@ce989f90023d home]# touch test.java
[root@ce989f90023d home]# exit
exit
[root@localhost home]# docker ps -a
CONTAINER ID     IMAGE      COMMAND       CREATED           STATUS                PORTS           NAMES
ce989f90023d     centos  "/bin/bash"  44 minutes ago  Exited (0) 46 seconds ago               nifty_johnson

# 将docker内文件拷贝到主机上
[root@localhost home]# docker cp ce989f90023d:/home/test.java /home
[root@localhost home]# ls
test.java  ztx
[root@localhost home]# 

# 拷贝是一个手动过程，未来我们使用 -v 卷的技术，可以实现自动同步 
```

## 5. 小结

![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272019066.png)

### [常用命令大全](./Appendix.md/# 常用命令详解)。

## 6. 作业练习

### docker 安装 nginx

```shell
# 1.搜索镜像 search 建议去docker搜索，可以看到帮助文档
# 2.下载镜像 pull
# 3.运行测试
[root@localhost /]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              2622e6cca7eb        23 hours ago        132MB
centos              latest              470671670cac        4 months ago        237MB

# -d 后台运行
# --name 给容器命名
# -p 宿主机端口：容器内部端口   【端口映射操作】
[root@localhost /]# docker run -d --name nginx01 -p 3344:80 nginx
d60570d1e45024e3687e3bf3105a6959af8ee68d34f0c62a7deee1c16ec6579f
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
d60570d1e450        nginx               "/docker-entrypoint.…"   2 minutes ago       Up 2 minutes        0.0.0.0:3344->80/tcp   nginx01
# 本地测试访问nginx
[root@localhost /]# curl localhost:3344

# 进入容器
[root@localhost /]# docker exec -it nginx01 /bin/bash
root@d60570d1e450:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@d60570d1e450:/# cd /etc/nginx/
root@d60570d1e450:/etc/nginx# ls
conf.d	fastcgi_params	koi-utf  koi-win  mime.types  modules  nginx.conf  scgi_params	uwsgi_params  win-utf
```

>   端口暴露的概念

![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272019166.png)
`思考问题：我们每次改动nginx配置文件，都需要进入容器内部？十分的麻烦，我要是可以在容器外部提供一个映射路径，达到在容器外修改文件名，容器内部就可以自动修改？-v 数据卷`

### docker 安装 tomcat

```shell
# 官方文档
docker run -it --rm tomcat:9.0

# 我们之前的启动都是后台，停止了容器之后，容器还是可以查到 docker run -it --rm,一般用来测试，用完就删除

# 下载再启动
docker pull tomcat

# 启动运行
docker run -d -p 3355:8080 --name tomcat01 tomcat

#测试访问没有问题


# 进入容器
[root@localhost /]# docker exec -it tomcat01 /bin/bash

# 发现问题：1、linux命令少了 2、webapps内没有内容（这是阿里云镜像的原因：默认是最小镜像，所有不必要的都删除）
# 保证最小可运行环境
#解决方法：将webapps.dist目录下内容拷至webapps下
root@c435d5b974a7:/usr/local/tomcat# cd webapps
root@c435d5b974a7:/usr/local/tomcat/webapps# ls
root@c435d5b974a7:/usr/local/tomcat/webapps# cd ..
root@c435d5b974a7:/usr/local/tomcat# ls
BUILDING.txt  CONTRIBUTING.md  LICENSE	NOTICE	README.md  RELEASE-NOTES  RUNNING.txt  bin  conf  lib  logs  native-jni-lib  temp  webapps  webapps.dist  work
root@c435d5b974a7:/usr/local/tomcat# cd webapps.dist/
root@c435d5b974a7:/usr/local/tomcat/webapps.dist# ls
ROOT  docs  examples  host-manager  manager
root@c435d5b974a7:/usr/local/tomcat/webapps.dist# cd ..
root@c435d5b974a7:/usr/local/tomcat# cp -r webapps.dist/* webapps 
root@c435d5b974a7:/usr/local/tomcat# cd webapps
root@c435d5b974a7:/usr/local/tomcat/webapps# ls
ROOT  docs  examples  host-manager  manager
```

思考问题：我们以后要部署项目，如果每次都要进入容器是不是十分麻烦？我要是可以在容器外部提供一个映射路径，webapps，我们在外部放置项目，就自动同步到内部就好了！

### 部署 ES+kibana

```shell
# es 暴露的端口很多！
# es 十分耗内存
# es 的数据一般需要放置到安全目录！挂载
# --net somenetwork？网络配置

# 启动 elasticsearch
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2

# 启动了 Linux就可卡住了   docker stats 查看cpu的状态
# es 是十分耗内存的

# 测试一下es是否成功了
[root@localhost /]# curl localhost:9200
{
  "name" : "83b0d5dca26e",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "MjhNfYTvRVui1UCrAwMdqw",
  "version" : {
    "number" : "7.6.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "ef48eb35cf30adf4db14086e8aabd07ef6fb113f",
    "build_date" : "2020-03-26T06:34:37.794943Z",
    "build_snapshot" : false,
    "lucene_version" : "8.4.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}

# 查看docker容器占用资源情况
```

作业：使用 kibana 连接 es？思考网络如何才能连接过去！
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272020458.png)

## 7. 可视化

+   portainer（先用这个）

```shell
docker run -d -p 8088:9000 \
--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

+   Rancher（CI/CD时再用这个）

### 什么是 portainer ?

Docker 图形化界面管理工具！提供一个后台面板供我们操作！

```shell
docker run -d -p 8088:9000 \
--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

访问测试：[http://ip:8088](http://ip:8088/)
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272020609.png)
选择本地的：Local
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272020840.png)
进入之后的面板
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272020945.png)

# 四、Docker 镜像讲解

## 1. 镜像是什么

镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时、库、环境变量和配置文件。
所有的应用，直接打包 docker 镜像，就可以直接跑起来！
如何得到镜像：

+   从远程仓库下载
+   朋友拷贝给你
+   自己制作一个镜像 DockerFile

## 2. Docker 镜像加速原理

### UnionFS（联合文件系统）

我们下载的时候看到的一层层就是这个！

UnionFS（联合文件系统）：Union文件系统（UnionFS）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下（unite several directories into a single virtual filesystem）。Union 文件系统是Docker 镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录。

### Docker镜像加载原型

docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFS。

bootfs（boot file system）主要包含bootloader和kernel，bootloader主要是引导加载kernel，Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是bootfs。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。

rootfs（root file system），在bootfs之上。包含的就是典型Linux系统中的/dev，/proc，/bin，/etc等标准目录和文件。

rootfs就是各种不同的操作系统发行版，比如Ubuntu，Centos等等。
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272020229.png)

平时我们安装进虚拟机的CentOS都是好几个G，为什么Docker这里才200M？
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272020376.png)

对于一个精简的OS，rootfs可以很小，只需要包含最基本的命令，工具和程序库就可以了，因为底层直接用Host的kernel，自己只需要提供rootfs就可以了。

由此可见对于不同的linux发行版，bootfs基本是一致的，rootfs会有差别，因此不同的发行版可以公用bootfs。
（虚拟机是分钟级，容器是秒级！)

## 3. 分层理解

### 分层的镜像

我们可以去下载一个镜像，注意观察下载的日志输出，可以看到是一层一层的在下载！
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272020286.png)

思考：为什么Docker镜像要采用这种分层的结构呢？

最大的好处，我觉得莫过于是资源共享了！比如有多个镜像都从相同的Base镜像构建而来，那么宿主机只需在磁盘上保留一份base镜像，同时内存中也只需要加载一份base镜像，这样就可以为所有的容器服务了，而且镜像的每一层都可以被共享。

查看镜像分层的方式可以通过 docker image inspect 命令！

```shell
➜  / docker image inspect redis          
[
    {
        "Id": "sha256:f9b9909726890b00d2098081642edf32e5211b7ab53563929a47f250bcdc1d7c",
        "RepoTags": [
            "redis:latest"
        ],
        "RepoDigests": [
            "redis@sha256:399a9b17b8522e24fbe2fd3b42474d4bb668d3994153c4b5d38c3dafd5903e32"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2020-05-02T01:40:19.112130797Z",
        "Container": "d30c0bcea88561bc5139821227d2199bb027eeba9083f90c701891b4affce3bc",
        "ContainerConfig": {
            "Hostname": "d30c0bcea885",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.12",
                "REDIS_VERSION=6.0.1",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.0.1.tar.gz",
                "REDIS_DOWNLOAD_SHA=b8756e430479edc162ba9c44dc89ac394316cd482f2dc6b91bcd5fe12593f273"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"redis-server\"]"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:704c602fa36f41a6d2d08e49bd2319ccd6915418f545c838416318b3c29811e0",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {}
        },
        "DockerVersion": "18.09.7",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.12",
                "REDIS_VERSION=6.0.1",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.0.1.tar.gz",
                "REDIS_DOWNLOAD_SHA=b8756e430479edc162ba9c44dc89ac394316cd482f2dc6b91bcd5fe12593f273"
            ],
            "Cmd": [
                "redis-server"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:704c602fa36f41a6d2d08e49bd2319ccd6915418f545c838416318b3c29811e0",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": null
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 104101893,
        "VirtualSize": 104101893,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/adea96bbe6518657dc2d4c6331a807eea70567144abda686588ef6c3bb0d778a/diff:/var/lib/docker/overlay2/66abd822d34dc6446e6bebe73721dfd1dc497c2c8063c43ffb8cf8140e2caeb6/diff:/var/lib/docker/overlay2/d19d24fb6a24801c5fa639c1d979d19f3f17196b3c6dde96d3b69cd2ad07ba8a/diff:/var/lib/docker/overlay2/a1e95aae5e09ca6df4f71b542c86c677b884f5280c1d3e3a1111b13644b221f9/diff:/var/lib/docker/overlay2/cd90f7a9cd0227c1db29ea992e889e4e6af057d9ab2835dd18a67a019c18bab4/diff",
                "MergedDir": "/var/lib/docker/overlay2/afa1de233453b60686a3847854624ef191d7bc317fb01e015b4f06671139fb11/merged",
                "UpperDir": "/var/lib/docker/overlay2/afa1de233453b60686a3847854624ef191d7bc317fb01e015b4f06671139fb11/diff",
                "WorkDir": "/var/lib/docker/overlay2/afa1de233453b60686a3847854624ef191d7bc317fb01e015b4f06671139fb11/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:c2adabaecedbda0af72b153c6499a0555f3a769d52370469d8f6bd6328af9b13",
                "sha256:744315296a49be711c312dfa1b3a80516116f78c437367ff0bc678da1123e990",
                "sha256:379ef5d5cb402a5538413d7285b21aa58a560882d15f1f553f7868dc4b66afa8",
                "sha256:d00fd460effb7b066760f97447c071492d471c5176d05b8af1751806a1f905f8",
                "sha256:4d0c196331523cfed7bf5bafd616ecb3855256838d850b6f3d5fba911f6c4123",
                "sha256:98b4a6242af2536383425ba2d6de033a510e049d9ca07ff501b95052da76e894"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```

**理解：**

所有的 Docker 镜像都起始于一个基础镜像层，当进行修改或增加新的内容时，就会在当前镜像层之上，创建新的镜像层。

举一个简单的例子，假如基于 Ubuntu Linux 16.04 创建一个新的镜像，这就是新镜像的第一层；如果在该镜像中添加Python 包，就会在基础镜像层之上创建第二个镜像层；如果继续添加一个安全补丁，就会创建第三个镜像层。
该镜像当前已经包含 3 个镜像层，如下图所示（这只是一个用于演示的很简单的例子）。
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272021887.png)

在添加额外的镜像层的同时，镜像始终保持是当前所有镜像的组合，理解这一点非常重要。下图中举了一个简单的例子，每个镜像层包含 3 个文件，而镜像包含了来自两个镜像层的 6 个文件。
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272020787.png)

上图中的镜像层跟之前图中的略有区别，主要目的是便于展示文件。

下图中展示了一个稍微复杂的三层镜像，在外部看来整个镜像只有 6 个文件，这是因为最上层中的文件 7 是文件 5 的一个更新版本。
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272020804.png)

这种情况下，上层镜像层中的文件覆盖了底层镜像层中的文件。这样就使得文件的更新版本作为一个新镜像层添加到镜像当中。

Docker 通过存储引擎（新版本采用快照机制）的方式来实现镜像层堆栈，并保证多镜像层对外展示为统一的文件系统。

Linux 上可用的存储引擎有 AUFS、Overlay2、Device Mapper、Btrfs 以及 ZFS。顾名思义，每种存储引擎都基于 Linux 中对应的文件系统或者块设备技术，并且每种存储引擎都有其独有的性能特点。

Docker 在 Windows 上仅支持 windowsfilter 一种存储引擎，该引擎基于 NTFS 文件系统之上实现了分层和CoW[1]。

下图展示了与系统显示相同的三层镜像。所有镜像层堆叠并合并，对外提供统一的视图。
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272021968.png)

### 特点

Docker 镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部！
这一层就是我们通常说的容器层，容器之下的都叫镜像层！
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272021583.png)

## 4. commit 镜像

```shell
docker commit 提交容器成为一个新的副本

# 命令和git原理类似
docker commit -m="描述信息" -a="作者" 容器id 目标镜像名:[版本TAG]
```

### 实战测试

```shell
#1、启动一个默认的tomcat

#2、发现这个默认的tomcat是没有webapps应用的，镜像的原因。官方的镜像默认webapps下面是没有文件的！

#3、我自己将webapp.dist下文件拷贝至webapps下

#4、将我们操作过的容器通过commit提交为一个镜像！我们以后就可以使用我们修改过的镜像了，这就是我们自己的一个修改的镜像
```

![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272021217.png)

学习方式说明：理解概念，但是一定要实践，最后实践和理论相结合一次搞定这个知识如果你想要保存当前容器的状态，就可以通过commit来提交，获得一个镜像，就好比我们以前学习VM时候，快照！到了这里才算是入门Dokcer！

# 五、容器数据卷

## 1. 什么是容器数据卷

**docker 的理念回顾**

将应用和环境打包成一个镜像！

数据？如果数据都在容器中，那么我们容器删除，数据就会丢失！（需求：数据可以持久化）

MySQL，容器删了，删库跑路！（需求：MySQL 数据可以存储在本地）

容器之间可以有一个数据共享的技术！Docker 容器中产生的数据，同步到本地！

这就是卷技术！目录的挂载，将我们容器内的目录，挂载到 Linux 主机上面！
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272021417.png)
`总结一句话：容器的持久化和同步操作！容器间也是可以数据共享的！`

## 2. 使用数据卷

### 直接使用命令来挂载：-v

```shell
docker run -it -v 主机目录:容器目录

# 测试
[root@localhost home]# docker run -it -v /home/ceshi:/home  centos  /bin/bash

# 启动起来的时候，我们可以通过docker inspect 容器id 来查看挂载情况：（见下图）
```

![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272021893.png)
测试文件的同步：
在容器的 /home 文件夹下，新建 test.java 文件，会同步到主机的 /home/ceshi 文件夹下。
`删除操作也是同步的；双向绑定，保证两边文件夹下的数据始终是一直的。`

![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272022736.png)
再来测试：
停止容器后，在主机的/home/ceshi文件夹下，修改文件或新增文件，启动容器，查看容器的/home文件夹，发现容器内的数据依旧是同步的

1.  停止容器。
2.  宿主机上修改文件。
3.  启动容器。
4.  容器内的数据依旧是同步的。
    ![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272022183.png)
    好处：我们以后修改只需要在本地修改即可，容器内会自动同步！|

## 3. 实战：安装mysql

```shell
# 获取镜像
[root@localhost home]# docker pull mysql:5.7

# 运行容器，需要做数据挂载！ # 安装mysql,需要配置密码，这是要注意的点！
# 官方测试：docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

# 启动我们的MySQL容器
-d	后台运行
-p	端口映射
-v	卷挂载
-e  环境配置
--name  容器名字
[root@localhost home]# docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7

# 启动成功之后，我们在本地使用sqlyog 连接测试一下
# sqlyog —— 连接到服务器的3310 —— 3310和容器内的3306映射，这个时候我们就可以连接上了！

# 本地测试创建一个数据库，查看一下我们的映射的路径是否ok!
```

如果我们将容器删除：
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272022904.png)

发现，我们挂载到本地的数据卷依旧没有丢失，这就实现了容器数据持久化功能！

## 4. 匿名和具名挂载

```shell
# 匿名挂载
-v 容器内路径
docker run -d -P --name nginx01 -v /etc/nginx nginx


# 查看所有卷的情况
[root@localhost data]# docker volume ls
DRIVER              VOLUME NAME
local               2dd0379216c9ee4441ed56f8ce53461c19abe78b8cfd024ac5fbe07c3b8f09ba

# 这里发现，这种就是匿名挂载，我们在 -v 后只写了容器内的路径，没有写容器外的路径！

# 具名挂载
[root@localhost home]# docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx
5ba5708389bf71b2156fdbcedc50a62b16ac27adb2a3dfac42c52e9da5ace79f
[root@localhost home]# docker volume ls
DRIVER              VOLUME NAME
local               juming-nginx

# 通过 -v 卷名：容器内路径
# 查看一下这个卷  # 先找到卷所在路径 docker volume inspect 卷名，如下图：
```

![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272022395.png)
所有的docker容器内的卷，没有指定目录的情况下都是在`/var/lib/docker/volumes/xxxx/_data"`（xxxx是卷名)
我们通过具名挂载可以方便的找到我们的一个卷，`大多数情况在使用的具名挂载`

```shell
# 如何确定是具名挂载还是匿名挂载，还是指定路径挂载！
-v	容器内路径		       # 匿名挂载
-v	卷名:容器内路径	 	 # 具名挂载
-v	/宿主机路径:容器内路径   # 指定路径挂载！
```

拓展：

```shell
# 通过 -v 容器内路径：ro 或 rw   改变读写权限
ro	 #readonly 只读
rw	 #readwrite 可读可写

# 一旦创建容器时设置了容器权限，容器对我们挂载出来的内容就有限定了！
docker run -d -P --name nginx05 -v juming:/etc/nginx:ro nginx
docker run -d -P --name nginx05 -v juming:/etc/nginx:rw nginx

# 默认是 rw
# ro 只要看到ro就说明这个路径只能通过宿主机来操作，容器内部是无法操作！
```

## 5. 初识Dockerfile

Dockerfile就是用来构建 docker 镜像的构建命令！命令脚本！先体验一下！
通过这个脚本可以生成镜像，镜像是一层一层的，脚本一个一个的命令，每个命令都是一层！

```shell
# 创建一个dockerfile文件，名字可以随机，建议 dockerfile

[root@localhost docker-test-volume]# vim dockerfile
# 文件中的内容：指令(大写) 参数
FROM centos

VOLUME ["volume01","volume02"]

CMD echo"----end----"

CMD /bin/bash

# 这里的每个命令，就是镜像的一层！
```

![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272022080.png)
启动自己写的容器：
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272022331.png)
这个卷和外部一定有一个同步的目录！
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272022629.png)
查看一下卷挂载的路径：docker inspect 容器id
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272023495.png)
测试一下文件是否同步出去：在容器的volume01文件夹下创建文件，查看宿主机对应目录是否同步成功。
`这种方式我们未来使用的十分多，因为我们通常会构建自己的镜像！`
如果构建镜像时候没有挂载卷，就需要自己手动镜像挂载目录！

## 6. 数据卷容器

多个mysql同步数据
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272023158.png)

```shell
# 启动3个容器，通过我们刚才自己的写镜像启动。
```

![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272023117.png)
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272023014.png)
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272023549.png)

```shell
# 测试：可以删除docker01，查看一下docker02和docker03是否还可以访问这个文件# 测试依旧可以访问
```

![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272023179.png)
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272023080.png)
多个mysql实现数据共享：

```shell
➜  ~ docker run -d -p 3306:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7
➜  ~ docker run -d -p 3307:3306 -e MYSQL_ROOT_PASSWORD=123456 --name mysql02 --volumes-from mysql01  mysql:5.7
# 这个时候，可以实现两个容器数据同步！
```

结论：
容器之间配置信息的传递，数据卷容器的生命同期一直持续到没有容器使用为止。
但是一旦你持久化到了本地，这个时候，本地的数据是不会删除的！

# 六、DockerFile

## 1. DockerFile介绍

dockerfile是用来构建docker镜像的文件！命令参数脚本！
构建步骤：
1、编写一个dockerfile文件
2、docker build 构建成为一个镜像
3、docker run运行镜像
4、docker push发布镜像（DockerHub、阿里云镜像仓库！）
查看一下官方是怎么做的？
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272023752.png)
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272023011.png)

## 2. DockerFile的构建过程

### 基础知识

1、每个保留关键字（指令）都是必须是大写字母
2、执行从上到下顺序执行
3、# 表示注释
4、每一个指令都会创建提交一个新的镜像层，并提交！
![在这里插入图片描述](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272024305.png)
dockerfile是面向开发的，我们以后要发布项目，做镜像，就需要编写dockerfile文件，这个文件十分简单！
Docker镜像逐渐成为企业交付的标准，必须要掌握！
步骤：开发，部署，运维。。。缺一不可！|
DockerFile：构建文件，定义了一切的步骤，源代码。
Dockerlmages：通过DockerFile构建生成的镜像，最终发布和运行的产品。
Docker容器：容器就是镜像运行起来提供服务的。

## 3. DockerFile的命令

以前的话我们就是使用别人的，现在我们知道了这些指令后，我们来练习自己写一个镜像！

```shell
FROM			# 基础镜像，一切从这里开始构建
MAINTAINER		# 镜像是谁写的，姓名+邮箱
RUN				# 镜像构建的时候需要运行的命令
ADD				# 步骤：tomcat镜像，这个tomcat压缩包！ 添加内容
WORKDIR			# 镜像的工作目录
VOLUME			# 挂载的目录
EXPOSE          # 暴露端口配置，跟 -p 是一个道理
CMD				# 指定这个容器启动时要执行的命令,只有最后一个命令会生效，可悲替代
ENTRYPOINT		# 指定这个容器启动的时候要执行的命令，可以追加命令
ONBUILD			# 当构建一个被继承DockerFile 这个时候就会运行ONBUILD的指令。触发指令
COPY			# 类似ADD,将我们文件拷贝到镜像中
ENV				# 构建的时候设置环境变量，跟 -e 是一个意思

# CMD 和 ENTRYPOINT 的区别说明：（后面也会介绍）
# 若CMD 和 ENTRYPOINT 后跟的都是 ls -a 这个命令，当docker run 一个容器时，添加了 -l 选项，则CMD里的ls -a 命令就会被替换成-l;而ENTRYPOINT中的 ls -a会追加-l变成 ls -a -l  
```

![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272024804.jpg)

## 4. 实战测试

Docker Hub中99%镜像都是从这个基础镜像过来的FROM scratch，然后配置需要的软件和配置来进行的构建。

```shell
# 1、编写DockerFile文件，内容如下：
[root@localhost dockerfile]# cat mydockerfile-centos
FROM centos						
MAINTAINER ztx<123456@qq.com> 

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "----end----"
CMD /bin/bash

# 2、通过这个文件构建镜像
# 命令docker build -f dockerfile文件路径 -t 镜像名:[tag] .
[root@localhost dockerfile]# docker build -f mydockerfile-centos -t mycentos:0.1 .
....
Successfully built c987078b06cb
Successfully tagged mycentos:0.1

# 3、测试运行
```

对比：
之前的原生的centos7：
![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272024623.jpg)
我们增加之后的镜像：
![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272024986.jpg)
我们可以列出本地进行的变更历史：docker history 镜像id
![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272024366.jpg)

>   CMD和ENTRYPOINT区别

```shell
CMD            # 指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代ENTRYPOINT     # 指定这个容器启动的时候要运行的命令，可以追加命令
```

测试CMD

```shell
# 编写dockerfile文件
$ vim dockerfile-test-cmd
FROM centos
CMD ["ls","-a"]
# 构建镜像
$ docker build  -f dockerfile-test-cmd -t cmd-test:0.1 .
# 运行镜像
$ docker run cmd-test:0.1
.
..
.dockerenv
bin
dev

# 想追加一个命令  -l 成为ls -al
$ docker run cmd-test:0.1 -l
docker: Error response from daemon: OCI runtime create failed: container_linux.go:349: starting container process caused "exec: \"-l\":
 executable file not found in $PATH": unknown.
ERRO[0000] error waiting for container: context canceled 
# cmd的情况下 -l 替换了CMD["ls","-l"]。 -l  不是命令,所以报错
```

![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272024526.jpg)

测试ENTRYPOINT

```shell
# 编写dockerfile文件
$ vim dockerfile-test-entrypoint
FROM centos
ENTRYPOINT ["ls","-a"]
$ docker run entrypoint-test:0.1
.
..
.dockerenv
bin
dev
etc
home
lib
lib64
lost+found ...
# 我们的命令，是直接拼接在我们的ENTRYPOINT命令后面的
$ docker run entrypoint-test:0.1 -l
total 56
drwxr-xr-x   1 root root 4096 May 16 06:32 .
drwxr-xr-x   1 root root 4096 May 16 06:32 ..
-rwxr-xr-x   1 root root    0 May 16 06:32 .dockerenv
lrwxrwxrwx   1 root root    7 May 11  2019 bin -> usr/bin
drwxr-xr-x   5 root root  340 May 16 06:32 dev
drwxr-xr-x   1 root root 4096 May 16 06:32 etc
drwxr-xr-x   2 root root 4096 May 11  2019 home
lrwxrwxrwx   1 root root    7 May 11  2019 lib -> usr/lib
lrwxrwxrwx   1 root root    9 May 11  2019 lib64 -> usr/lib64 ....
```

![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272024287.jpg)

![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272024448.jpg)

```shell
# 原因：ENTRYPOINT命令的情况下，"-l"追加在ENTRYPOINT ["1s"，"-a"]命令后面，得到"ls -al"的命令，所以命令正常执行！# （我们的追加命令，是直接拼接在我们的ENTRYPOINT命令的后面）
```

## 5. 制作tomcat镜像

1.  准备镜像文件：tomcat压缩包，jdk的压缩包！
    ![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272024111.jpg)

2.  编写dockerfile文件，官方命名**Dockerfile**，build会自动寻找这个文件，就不需要-f指定文件名了！

    ```shell
    FROM centos
    MAINTAINER kuangshen<123456@qq.com>
    
    COPY readme.txt /usr/local/readme.txt
    
    ADD jdk-8u161-linux-x64.tar.gz    /usr/local/
    ADD apache-tomcat-8.0.53.tar.gz   /usr/local
    
    RUN yum -y install vim
    ENV MYPATH /usr/local
    WORKDIR $MYPATH
    
    
    ENV JAVA_HOME /usr/local/jdk1.8.0_161
    ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
    ENV CATALINA_HOME /usr/local/apache-tomcat-8.0.53
    ENV CATALINA_BASH /usr/local/apache-tomcat-8.0.53
    ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin
    
    EXPOSE 8080
    
    CMD /usr/local/apache-tomcat-8.0.53/bin/startup.sh && tail -F /usr/local/apache-tomcat-8.0.53/bin/logs/catalina.out
    
    ```

3.  构建镜像

    ```shell
    # docker build -t diytomcat .     diytomcat是定义的镜像名
    ```

4.  启动镜像

    ```shell
    # docker run -d -p 9090:8080 --name kuangshentomcat02 -v /home/kuangshen/build/tomcat/test:/usr/local/apache-tomcat-8.0.53/webapps/test -v /home/kuangshen/build/tomcat/tomcatlogs/:/usr/local/apache-tomcat-8.0.53/logs diytomcat
    
    ```

5.  访问测试
    ![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272025001.jpg)

6.  发布项目（由于做了卷挂载，我们直接在本地test文件夹下编写项目就可以发布了！）

    ```shell
    <?xml version="1.0" encoding="UTF-8"?>
    <web-app xmlns="http://java.sun.com/xml/ns/javaee"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
                                   http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
               version="2.5">
    
    </web-app>
    ```
    
    ```shell
    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>hello kuangshen</title>vim
    </head>
    <body>
    Hello World!<br/>
    <%
    System.out.println("---my test web logs---");
    %>
    </body>
    </html>
    ```
    
    发现：项目部署成功，可以直接访问ok！（ip地址:3355/test）
    
    我们以后开发的步骤：需要掌握Dokcerfile的编写！我们之后的一切都是使用docker镜像来发布运行！

## 6. 发布自己的镜像

### 1. 发布到dockerhub上

1.  https://hub.docker.com/ 注册自己的账号

2.  确定这个账号可以登录

3.  在我们服务器上提交自己的镜像

    ```shell
    [root@localhost tomcat]# docker login --help
    
    Usage:	docker login [OPTIONS] [SERVER]
    
    Log in to a Docker registry.
    If no server is specified, the default is defined by the daemon.
    
    Options:
      -p, --password string   Password
          --password-stdin    Take the password from stdin
      -u, --username string   Username
    
    # 登录dockerhub
    [root@localhost tomcat]# docker login -u ztx115
    Password: 
    WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
    Configure a credential helper to remove this warning. See
    https://docs.docker.com/engine/reference/commandline/login/#credentials-store
    
    Login Succeeded
    ```

4.  登录完毕后就可以提交镜像了，就是一步： docker push

    ```shell
    # push自己的镜像到服务器上！
    [root@localhost tomcat]# docker push diytomcat
    The push refers to repository [docker.io/library/diytomcat]
    c5593011cd68: Preparing 
    d3ce40b8178e: Preparing 
    02084c67dcc9: Preparing 
    2b7c1c6c89c5: Preparing 
    0683de282177: Preparing 
    denied: requested access to the resource is denied  # 拒绝
    
    # push镜像的问题？
    # 解决：增加一个tag         docker tag  指定镜像的id   dockerhub的用户名/镜像重命名:[tag]
    [root@localhost tomcat]# docker tag bb64ab96b432 ztx115/tomcat:1.0
    ```

### 2. 发布到阿里云镜像服务上

1.  登录阿里云账号
2.  找到镜像容器服务
3.  创建命名空间（一个账号只能创建3个命名空间）
4.  创建镜像仓库
5.  浏览相关操作命令

## 7. 小结

![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272025740.jpg)

# 七、Docker网络

## 1. 理解docker0

### 测试

![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272025534.jpg)
有三个网络

```shell
# 问题：docker是如何处理容器网络访问的？
```

![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272025845.jpg)

```shell
# [root@localhost /]# docker run -d -P --name tomcat01 tomcat

# 查看容器的内部网络地址   ip addr,  发现容器启动的时候会得到一个 eth0@if43 ip地址，docker分配的！
[root@localhost /]# docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
42: eth0@if43: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever

# 思考：linux能不能ping通docker容器内部！
[root@localhost /]# ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.476 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.099 ms
64 bytes from 172.17.0.2: icmp_seq=3 ttl=64 time=0.105 ms
...
# linux 可以ping通docker容器内部
```

### 原理

1.  我们每启动一个docker容器，docker就会给docker容器分配一个ip，我们只要安装了ddcker，就会有一个网卡docker0
    （桥接模式，使用的技术是veth-pair技术）
    再次测试：
    ![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272025146.png)

2.  在启动一个容器测试，发现又多了一对网卡。
    ![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272025226.jpg)
    ![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272025309.jpg)

    ```shell
    # 我们发现这个容器带来网卡，都是一对对的
    # veth-pair 就是一对的虚拟设备接口，他们都是成对出现的，一段连着协议，一段彼此相连
    # 正因为有这个特性，veth-pair 充当一个桥梁，连接各种虚拟网络设备
    # OpenStack，Docker容器之间的连接，OVS的连接都是使用veth-pair技术
    ```
    
3.  我们来测试下tomcat01和tomcat02是否可以ping通！（可以ping通）

    ```shell
    [root@localhost /]# docker exec -it tomcat02 ping 172.17.0.2
    PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
    64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.556 ms
    64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.096 ms
    64 bytes from 172.17.0.2: icmp_seq=3 ttl=64 time=0.111 ms
    ...
    
    # 结论：容器与容器之间是可以相互ping通的！！！
    ```
    
    绘制一个网络模型图
    ![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272025546.jpg)
    结论：tomcat01和tomcat02是公用的一个路由器，dockero。
    所有的容器不指定网络的情况下，都是docker0路由的，docker会给我们的容器分配一个默认的可用IP

![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272026483.jpg)
Docker中的所有的网络接口都是虚拟的。虚拟的转发效率高！（内网传递文件！）
只要容器删除，对应网桥一对就没了！
![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272026469.jpg)

## 2. \-\-link

### 思考一个场景（高可用）

我们编写了一个微服务，database url=ip:，项目不重启，数据库ip换掉了，我们希望可以处理这个问题，可以名字来进行访问容器？

```shell
# tomcat02 想通过直接ping 容器名（即"tomcat01"）来ping通，而不是ip，发现失败了！
[root@localhost /]# docker exec -it tomcat02 ping tomcat01
ping: tomcat01: Name or service not known

# 如何解决这个问题呢？
# 通过--link 就可以解决这个网络联通问题了！！！      发现新建的tomcat03可以ping通tomcat02
[root@localhost /]# docker run -d -P --name tomcat03 --link tomcat02 tomcat
87a0e5f5e6da34a7f043ff6210b57f92f40b24d0d4558462e7746b2e19902721
[root@localhost /]# docker exec -it tomcat03 ping tomcat02
PING tomcat02 (172.17.0.3) 56(84) bytes of data.
64 bytes from tomcat02 (172.17.0.3): icmp_seq=1 ttl=64 time=0.132 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=2 ttl=64 time=0.116 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=3 ttl=64 time=0.116 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=4 ttl=64 time=0.116 ms

# 反向能ping通吗？       发现tomcat02不能oing通tomcat03
[root@localhost /]# docker exec -it tomcat02 ping tomcat03
ping: tomcat03: Name or service not known
```

探究：docker network inspect networkID (docker network ls可以查看networkID)
![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272026112.jpg)
其实这个tomcat03就是在本地配置了tomcat02的配置？

```shell
# 查看hosts 配置，在这里发现原理！  
[root@localhost /]# docker exec -it tomcat03 cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
172.17.0.3	tomcat02 95303c12f6d9    # 就像windows中的 host 文件一样，做了地址绑定
172.17.0.4	87a0e5f5e6da
```

本质探究：—link 就是我们在hosts配置中增加了一个”172.17.0.3 tomcat02 20398a94efa7”
我们现在玩Docker已经不建议使用—link了！
自定义网络！不适用docker0！
docker0问题：他不支持容器名连接访问！

## 3. 自定义网络

### 查看所有的docker网络

![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272026783.jpg)

### 网络模式

bridge：桥接 docker（默认，自己创建也使用bridge桥接模式）
none：不配置网络
host：和主机共享网络
container：容器网络连通！（用的少！局限很大）

### 测试

```shell
# 我们之前直接启动的命令 (默认是使用--net bridge，可省)，这个bridge就是我们的docker0 
docker run -d -P --name tomcat01 tomcat   
docker run -d -P --name tomcat01 --net bridge tomcat
# 上面两句等价

# docker0（即bridge）默认不支持域名访问 ！ --link可以打通连接，即支持域名访问！

# 我们可以自定义一个网络！
# --driver bridge    		网络模式定义为 ：桥接
# --subnet 192.168.0.0/16	定义子网 ，范围为：192.168.0.2 ~ 192.168.255.255
# --gateway 192.168.0.1		子网网关设为： 192.168.0.1 
[root@localhost /]# docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
7ee3adf259c8c3d86fce6fd2c2c9f85df94e6e57c2dce5449e69a5b024efc28c
[root@localhost /]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
461bf576946c        bridge              bridge              local
c501704cf28e        host                host                local
7ee3adf259c8        mynet               bridge              local  	#自定义的网络
9354fbcc160f        none                null                local

```

我们自己的网络就创建好了
![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272026550.jpg)
启动两个容器测试：

![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272026285.jpg)

```shell
[root@localhost /]# docker run -d -P --name tomcat-net-01 --net mynet tomcat
b168a37d31fcdc2ff172fd969e4de6de731adf53a2960eeae3dd9c24e14fac67
[root@localhost /]# docker run -d -P --name tomcat-net-02 --net mynet tomcat
c07d634e17152ca27e318c6fcf6c02e937e6d5e7a1631676a39166049a44c03c
[root@localhost /]# docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "7ee3adf259c8c3d86fce6fd2c2c9f85df94e6e57c2dce5449e69a5b024efc28c",
        "Created": "2020-06-14T01:03:53.767960765+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "b168a37d31fcdc2ff172fd969e4de6de731adf53a2960eeae3dd9c24e14fac67": {
                "Name": "tomcat-net-01",
                "EndpointID": "f0af1c33fc5d47031650d07d5bc769e0333da0989f73f4503140151d0e13f789",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            },
            "c07d634e17152ca27e318c6fcf6c02e937e6d5e7a1631676a39166049a44c03c": {
                "Name": "tomcat-net-02",
                "EndpointID": "ba114b9bd5f3b75983097aa82f71678653619733efc1835db857b3862e744fbc",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]


# 再次测试 ping 连接
[root@localhost /]# docker exec -it tomcat-net-01 ping 192.168.0.3
PING 192.168.0.3 (192.168.0.3) 56(84) bytes of data.
64 bytes from 192.168.0.3: icmp_seq=1 ttl=64 time=0.199 ms
64 bytes from 192.168.0.3: icmp_seq=2 ttl=64 time=0.121 ms
^C
--- 192.168.0.3 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 2ms
rtt min/avg/max/mdev = 0.121/0.160/0.199/0.039 ms

# 现在不使用 --link,也可以ping 名字了！！！！！！
[root@localhost /]# docker exec -it tomcat-net-01 ping tomcat-net-02
PING tomcat-net-02 (192.168.0.3) 56(84) bytes of data.
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.145 ms
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=2 ttl=64 time=0.117 ms
^C
--- tomcat-net-02 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 3ms
rtt min/avg/max/mdev = 0.117/0.131/0.145/0.014 ms

```

我们自定义的网络docker都已经帮我们维护好了对应的关系，**推荐我们平时这样使用网络**！
redis -不同的集群使用不同的网络，保证集群是安全和健康的
mysql -不同的集群使用不同的网络，保证集群是安全和健康的

## 4. 网络连通

```shell
# 测试打通 tomcat01 — mynet
[root@localhost /]# docker network connect mynet tomcat01

# 连通之后就是将 tomcat01 放到了 mynet 网络下！ （见下图）
# 这就产生了 一个容器有两个ip地址 ! 参考阿里云的公有ip和私有ip
[root@localhost /]# docker network inspect mynet
```

容器和mynet网络需要打通
![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272027103.jpg)
打通命令
![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272027106.jpg)
![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272027246.jpg)

```shell
# 测试：打通tomcat01连接mynetdocker network connect mynet tomcat01
# 连通之后就是将tomcat01放到了mynet网络下
# 一个容器两个ip地址！
# 阿里云服务：公网ip和私网ip
```

![img](https://raw.githubusercontent.com/IssacStudent/MarkdownImg/master/img/202210272027152.jpg)

```shell
# tomcat01 连通ok
[root@localhost /]# docker exec -it tomcat01 ping tomcat-net-01
PING tomcat-net-01 (192.168.0.2) 56(84) bytes of data.
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=1 ttl=64 time=0.124 ms
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=2 ttl=64 time=0.162 ms
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=3 ttl=64 time=0.107 ms
^C
--- tomcat-net-01 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 3ms
rtt min/avg/max/mdev = 0.107/0.131/0.162/0.023 ms

# tomcat02 是依旧打不通的
[root@localhost /]# docker exec -it tomcat02 ping tomcat-net-01
ping: tomcat-net-01: Name or service not known
```

结论：假设要跨网络操作别人，就需要使用docker network connect连通！。。。。

## 5. 实战：部署redis集群

启动6个redis容器，上面三个是主，下面三个是备！

使用shell脚本启动！

```shell
# 创建redis集群网络
docker network create redis --subnet 172.38.0.0/16

# 通过脚本创建六个redis配置
[root@localhost /]# for port in $(seq 1 6);\
> do \
> mkdir -p /mydata/redis/node-${port}/conf
> touch /mydata/redis/node-${port}/conf/redis.conf
> cat <<EOF>>/mydata/redis/node-${port}/conf/redis.conf
> port 6379
> bind 0.0.0.0
> cluster-enabled yes
> cluster-config-file nodes.conf
> cluster-node-timeout 5000
> cluster-announce-ip 172.38.0.1${port}
> cluster-announce-port 6379
> cluster-announce-bus-port 16379
> appendonly yes
> EOF
> done

# 查看创建的六个redis
[root@localhost /]# cd /mydata/
[root@localhost mydata]# \ls
redis
[root@localhost mydata]# cd redis/
[root@localhost redis]# ls
node-1  node-2  node-3  node-4  node-5  node-6

# 查看redis-1的配置信息
[root@localhost redis]# cd node-1
[root@localhost node-1]# ls
conf
[root@localhost node-1]# cd conf/
[root@localhost conf]# ls
redis.conf
[root@localhost conf]# cat redis.conf 
port 6379
bind 0.0.0.0
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.38.0.11
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
```

```shell
docker run -p 6371:6379 -p 16371:16379 --name redis-1 \
-v /mydata/redis/node-1/data:/data \
-v /mydata/redis/node-1/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.11 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

docker run -p 6372:6379 -p 16372:16379 --name redis-2 \
-v /mydata/redis/node-2/data:/data \
-v /mydata/redis/node-2/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.12 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

docker run -p 6373:6379 -p 16373:16379 --name redis-3 \
-v /mydata/redis/node-3/data:/data \
-v /mydata/redis/node-3/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.13 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

docker run -p 6374:6379 -p 16374:16379 --name redis-4 \
-v /mydata/redis/node-4/data:/data \
-v /mydata/redis/node-4/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.14 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

docker run -p 6375:6379 -p 16375:16379 --name redis-5 \
-v /mydata/redis/node-5/data:/data \
-v /mydata/redis/node-5/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.15 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

docker run -p 6376:6379 -p 16376:16379 --name redis-6 \
-v /mydata/redis/node-6/data:/data \
-v /mydata/redis/node-6/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.16 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
```

```shell
[root@localhost conf]# docker exec -it redis-1 /bin/sh
/data # ls
appendonly.aof  nodes.conf
/data # redis-cli --cluster create 172.38.0.11:6379 172.38.0.12:6379 172.38.0.13:6379 172.38.0.14:6379 172.38.0.15:6379 172.38.0.16:6379 --cluster-replicas 1
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 172.38.0.15:6379 to 172.38.0.11:6379
Adding replica 172.38.0.16:6379 to 172.38.0.12:6379
Adding replica 172.38.0.14:6379 to 172.38.0.13:6379
M: c5551e2a30c220fc9de9df2e34692f20f3382b32 172.38.0.11:6379
   slots:[0-5460] (5461 slots) master
M: d12ebd8c9e12dbbe22e7b9b18f0f143bdc14e94b 172.38.0.12:6379
   slots:[5461-10922] (5462 slots) master
M: 825146ce6ab80fbb46ec43fcfec1c6e2dac55157 172.38.0.13:6379
   slots:[10923-16383] (5461 slots) master
S: 9f810c0e15ac99af68e114a0ee4e32c4c7067e2b 172.38.0.14:6379
   replicates 825146ce6ab80fbb46ec43fcfec1c6e2dac55157
S: e370225bf57d6ef6d54ad8e3d5d745a52b382d1a 172.38.0.15:6379
   replicates c5551e2a30c220fc9de9df2e34692f20f3382b32
S: 79428c1d018dd29cf191678658008cbe5100b714 172.38.0.16:6379
   replicates d12ebd8c9e12dbbe22e7b9b18f0f143bdc14e94b
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
....
>>> Performing Cluster Check (using node 172.38.0.11:6379)
M: c5551e2a30c220fc9de9df2e34692f20f3382b32 172.38.0.11:6379
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
S: 79428c1d018dd29cf191678658008cbe5100b714 172.38.0.16:6379
   slots: (0 slots) slave
   replicates d12ebd8c9e12dbbe22e7b9b18f0f143bdc14e94b
M: d12ebd8c9e12dbbe22e7b9b18f0f143bdc14e94b 172.38.0.12:6379
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: e370225bf57d6ef6d54ad8e3d5d745a52b382d1a 172.38.0.15:6379
   slots: (0 slots) slave
   replicates c5551e2a30c220fc9de9df2e34692f20f3382b32
S: 9f810c0e15ac99af68e114a0ee4e32c4c7067e2b 172.38.0.14:6379
   slots: (0 slots) slave
   replicates 825146ce6ab80fbb46ec43fcfec1c6e2dac55157
M: 825146ce6ab80fbb46ec43fcfec1c6e2dac55157 172.38.0.13:6379
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.

```

docker搭建redis集群完成！

我们使用docker之后，所有的技术都会慢慢变得简单起来！

# 八、SpringBoot微服务打包docker镜像
