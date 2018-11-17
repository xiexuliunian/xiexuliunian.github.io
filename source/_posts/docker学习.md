---
title: docker学习
date: 2018-11-16 20:04:36
categories: 技术工具
---
&emsp;&emsp;现今容器化技术对于技术行业的改变是非常巨大的，特别是对于企业来说，因此学习掌握容器化技术的基础是非常必要的。
## 1.docker安装
&emsp;&emsp;本文使用manjaro(Archlinux)系linux系统，由于有pacman、yaour管理软件包的加持，加上manjaro的图形化前端pamac,如下图

<div style="width: 80%; margin: auto">
<center>{% asset_img 1.png 说明 %}</center>
</div>

&emsp;&emsp;安装docker，nvidia-docker只需要点安装即可，当然前提是你要有个好的网络，接下来会写一篇讲如何在linux下使用全局代理的博文，以备不时之需。
### 建立docker用户组
&emsp;&emsp;默认情况下，`docker`命令会使用 `Unix socket`与 `Docker`引擎通讯。而只有 `root` 用户和 `docker` 组的用户才可以访问 Docker 引擎的 `Unix socket`。出于安全考虑，一般 Linux 系统上不会直接使用 `root` 用户。因此，更好地做法是将需要使用 docker 的用户加入 `docker` 用户组。

建立`docker`组：
```
sudo groupadd docker
```
将当前用户加入`docker`组：
```
sudo usermod -aG docker $USER
```
退出当前终端并重新登录，进行如下测试。
```
docker run hello-world
```
正常情况下你会得到
```
Hello from Docker!
This message shows that your installation appears to be working correctly.
To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.
To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash
Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/
For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

## 2.遇到的第一个问题
但是你很有可能会得到：
>Cannot connect to the Docker daemon at unix:/var/run/docker.sock. Is the docker daemon running?

&emsp;&emsp;这是肿么回事呢？<font color='Violet' size=3px>因为你只是安装了docker服务，但是没有启动呀!</font>

### 启动docker
`sudo systemctl start docker`:  启动docker服务

`sudo systemctl enable docker`: **开机启动docker服务**

&emsp;&emsp;再运行之前的命令就没有什么问题了，当然前提是你有一个好的网络，那如果网络不好，怎么知道我有没有安装启动好服务呢？

`docker info`命令可以显示出当前的docker环境的一些信息，若显示正常，那应该没什么问题。
```
$ docker info           
Containers: 3
 Running: 0
 Paused: 0
 Stopped: 3
Images: 2
Server Version: 18.09.0-ce
Storage Driver: overlay2
 Backing Filesystem: extfs
 Supports d_type: true
 Native Overlay Diff: false
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins:
 Volume: local
 Network: bridge host macvlan null overlay
 Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
Swarm: inactive
Runtimes: nvidia runc
Default Runtime: nvidia
Init Binary: docker-init
containerd version: 9f2e07b1fc1342d1c48fe4d7bbb94cb6d1bf278b.m
runc version: 4fc53a81fb7c994640722ac585fa9ca548971871-dirty
init version: fec3683
Security Options:
 seccomp
  Profile: default
Kernel Version: 4.14.80-1-MANJARO
Operating System: Manjaro Linux
OSType: linux
Architecture: x86_64
CPUs: 8
Total Memory: 15.6GiB
Name: zxd
ID: JEYJ:QQXD:VSQ3:RRDA:ETZN:BEON:QFLV:UWUY:YRYE:UTUE:7M5U:VM62
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): false
Registry: https://index.docker.io/v1/
Labels:
Experimental: false
Insecure Registries:
 127.0.0.0/8
Live Restore Enabled: false
```
## 3. 镜像 容器 仓库
### 镜像
&emsp;&emsp;我们都知道，操作系统分为内核和用户空间。对于 Linux 而言，内核启动后，会挂载 root 文件系统为其提供用户空间支持。而 Docker 镜像（Image），就相当于是一个 root 文件系统。比如官方镜像 ubuntu:16.04 就包含了完整的一套 Ubuntu 16.04 最小系统的 root 文件系统。

&emsp;&emsp; 镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像不包含任何动态数据，其内容在构建之后也不会被改变。

&emsp;&emsp;镜像构建时，会一层层构建，前一层是后一层的基础。每一层构建完就不会再发生改变，后一层上的任何改变只发生在自己这一层。比如，删除前一层文件的操作，实际不是真的删除前一层的文件，而是仅在当前层标记为该文件已删除。在最终容器运行的时候，虽然不会看到这个文件，但是实际上该文件会一直跟随镜像。因此，在构建镜像的时候，需要额外小心，每一层尽量只包含该层需要添加的东西，任何额外的东西应该在该层构建结束前清理掉。
### 容器
&emsp;&emsp;镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的 类 和 实例 一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。

&emsp;&emsp;容器的实质是进程，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的 命名空间。因此容器可以拥有自己的 root 文件系统、自己的网络配置、自己的进程空间，甚至自己的用户 ID 空间。容器内的进程是运行在一个隔离的环境里，使用起来，就好像是在一个独立于宿主的系统下操作一样。这种特性使得容器封装的应用比直接在宿主运行更加安全。也因为这种隔离的特性，很多人初学 Docker 时常常会混淆容器和虚拟机。

&emsp;&emsp;前面讲过镜像使用的是分层存储，容器也是如此。每一个容器运行时，是以镜像为基础层，在其上创建一个当前容器的存储层，我们可以称这个为容器运行时读写而准备的存储层为容器存储层。

&emsp;&emsp;容器存储层的生存周期和容器一样，容器消亡时，容器存储层也随之消亡。因此，任何保存于容器存储层的信息都会随容器删除而丢失。

&emsp;&emsp;按照 Docker 最佳实践的要求，容器不应该向其存储层内写入任何数据，容器存储层要保持无状态化。所有的文件写入操作，都应该使用 数据卷（Volume）、或者绑定宿主目录，在这些位置的读写会跳过容器存储层，直接对宿主（或网络存储）发生读写，其性能和稳定性更高。

&emsp;&emsp;数据卷的生存周期独立于容器，容器消亡，数据卷不会消亡。因此，使用数据卷后，容器删除或者重新运行之后，数据却不会丢失。
### 仓库
&emsp;&emsp;镜像构建完成后，可以很容易的在当前宿主机上运行，但是，如果需要在其它服务器上使用这个镜像，我们就需要一个集中的存储、分发镜像的服务，Docker Registry 就是这样的服务。

&emsp;&emsp;一个 Docker Registry 中可以包含多个仓库（Repository）；每个仓库可以包含多个标签（Tag）；每个标签对应一个镜像。

&emsp;&emsp;通常，一个仓库会包含同一个软件不同版本的镜像，而标签就常用于对应该软件的各个版本。我们可以通过 <仓库名>:<标签> 的格式来指定具体是这个软件哪个版本的镜像。如果不给出标签，将以 latest 作为默认标签。

&emsp;&emsp;以 Ubuntu 镜像 为例，ubuntu 是仓库的名字，其内包含有不同的版本标签，如，14.04, 16.04。我们可以通过 ubuntu:14.04，或者 ubuntu:16.04 来具体指定所需哪个版本的镜像。如果忽略了标签，比如 ubuntu，那将视为 ubuntu:latest。

&emsp;&emsp;仓库名经常以 两段式路径 形式出现，比如 jwilder/nginx-proxy，前者往往意味着 Docker Registry 多用户环境下的用户名，后者则往往是对应的软件名。但这并非绝对，取决于所使用的具体 Docker Registry 的软件或服务。

## 4. 使用镜像

&emsp;&emsp;在之前的介绍中，我们知道镜像是 Docker 的三大组件之一。
Docker 运行容器前需要本地存在对应的镜像，如果本地不存在该镜像，Docker 会从镜像仓库下载该镜像。

### 4.1 获取镜像
&emsp;&emsp;之前提到过，Docker Hub 上有大量的高质量的镜像可以用，这里我们就说一下怎么获取这些镜像。

&emsp;&emsp;从 Docker 镜像仓库获取镜像的命令是 docker pull。其命令格式为：

```
 docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
```

&emsp;&emsp;具体的选项可以通过 docker pull --help 命令看到，这里我们说一下镜像名称的格式。

&emsp;&emsp;Docker 镜像仓库地址：地址的格式一般是 <域名/IP>[:端口号]。默认地址是 Docker Hub。

&emsp;&emsp;仓库名：如之前所说，这里的仓库名是两段式名称，即 <用户名>/<软件名>。对于 Docker Hub，如果不给出用户名，则默认为 library，也就是官方镜像。
```
$ docker pull ubuntu:16.04
16.04: Pulling from library/ubuntu
18d680d61657: Pull complete 
0addb6fece63: Pull complete 
78e58219b215: Pull complete 
eb6959a66df2: Pull complete 
Digest: sha256:76702ec53c5e7771ba3f2c4f6152c3796c142af2b3cb1a02fce66c697db24f12
Status: Downloaded newer image for ubuntu:16.04
```
&emsp;&emsp;看一下现在都有哪些镜像了

&emsp;&emsp;`docker image ls`==`docker images`都是列出已存在的镜像的意思。
<div style="width: 100%; margin: auto">
<center>{% asset_img 2.png 说明 %}</center>
</div>

### 4.2 运行镜像
&emsp;&emsp;直接从镜像运行需要使用`docker run`

> docker run 来创建容器时，Docker 在后台运行的标准操作包括：
* 检查本地是否存在指定的镜像，不存在就从公有仓库下载
* 利用镜像创建并启动一个容器
* 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
* 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
* 从地址池配置一个 ip 地址给容器
* 执行用户指定的应用程序
* 执行完毕后容器被终止
* 
&emsp;&emsp;`docker run -i -t --rm ubuntu:latest bash`
```
# zxd @ zxd in ~ [15:28:01] 
$ docker run -i -t --rm ubuntu:latest bash
root@1a0399b42d27:/# cat /etc/os-release 
NAME="Ubuntu"
VERSION="18.04.1 LTS (Bionic Beaver)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 18.04.1 LTS"
VERSION_ID="18.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=bionic
UBUNTU_CODENAME=bionic

```
&emsp;&emsp;`docker run` 就是运行容器的命令，我们这里简要的说明一下上面用到的参数。

- `-it`：这是两个参数，一个是 -i：交互式操作，一个是 -t 终端。我们这里打算进入 bash 执行一些命令并查看返回结果，因此我们需要交互式终端。
- `--rm`：这个参数是说容器退出后随之将其删除。默认情况下，为了排障需求，退出的容器并不会立即删除，除非手动 docker rm。我们这里只是随便执行个命令，看看结果，不需要排障和保留结果，因此使用 --rm 可以避免浪费空间。
- `ubuntu:16.04`：这是指用 ubuntu:16.04 镜像为基础来启动容器。
- `bash`：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 bash。
进入容器后，我们可以在 Shell 下操作，执行任何所需的命令。这里，我们执行了 cat /etc/os-release，这是 Linux 常用的查看当前系统版本的命令，从返回的结果可以看到容器内是 Ubuntu 16.04.4 LTS 系统。

&emsp;&emsp;最后我们通过 exit 退出了这个容器。

&emsp;&emsp;<font color='Violet' size=3px>这里注意一点，各种操作指令放在前面，后面放镜像名。</font>