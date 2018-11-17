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
- `ubuntu:latest`：这是指用 ubuntu:latest 镜像为基础来启动容器。
- `bash`：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 bash。进入容器后，我们可以在 Shell 下操作，执行任何所需的命令。这里，我们执行了 cat /etc/os-release，这是 Linux 常用的查看当前系统版本的命令，从返回的结果可以看到容器内是 Ubuntu 8.04.1 LTS 系统。
- `--name` : 标注启动的容器的名字，不然会随机生成一个名字。

&emsp;&emsp;最后我们通过 exit 退出了这个容器。

&emsp;&emsp;<font color='Violet' size=3px>这里注意一点，各种操作指令放在前面，后面放镜像名。</font>
### 4.3 查看运行的容器
&emsp;&emsp;使用`docker ps`来查看正在运行的容器
```
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
5035e60c8e24        ubuntu:latest       "/bin/bash"         21 hours ago        Up 2 hours                              ubuntu
```
&emsp;&emsp;使用`docker ps -a`来查看所有启动过的容器，包括停止的容器
```
$ docker ps -a 
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                    PORTS               NAMES
5035e60c8e24        ubuntu:latest       "/bin/bash"         21 hours ago        Up 2 hours                                    ubuntu
2242a78ae6f1        hello-world         "/hello"            22 hours ago        Exited (0) 22 hours ago                       eloquent_cohen

```
### 4.4 删除容器或镜像
```
docker rm [容器ID或名字]      //删除容器
docker rmi [镜像ID或名字]     //删除镜像
```
## 5. Dockerfile
&emsp;&emsp;`Dockerfile` 是一个文本文件，其内包含了一条条的指令(Instruction)，每一条指令构建一层，因此每一条指令的内容，就是描述该层应当如何构建。
```
FROM ubuntu:latest
RUN apt-get update
```
&emsp;&emsp;这个 Dockerfile 很简单，一共就两行。涉及到了两条指令，FROM 和 RUN。
### FROM 指定基础镜像
&emsp;&emsp;所谓定制镜像，那一定是以一个镜像为基础，在其上进行定制。就像我们之前运行了一个 nginx 镜像的容器，再进行修改一样，基础镜像是必须指定的。而 FROM 就是指定基础镜像，因此一个 Dockerfile 中 FROM 是必备的指令，并且必须是第一条指令。

&emsp;&emsp;在 [Docker Store](https://store.docker.com/) 上有非常多的高质量的官方镜像，有可以直接拿来使用的服务类的镜像，如 nginx、redis、mongo、mysql、httpd、php、tomcat 等；也有一些方便开发、构建、运行各种语言应用的镜像，如 node、openjdk、python、ruby、golang 等。可以在其中寻找一个最符合我们最终目标的镜像为基础镜像进行定制。

&emsp;&emsp;如果没有找到对应服务的镜像，官方镜像中还提供了一些更为基础的操作系统镜像，如 ubuntu、debian、centos、fedora、alpine 等，这些操作系统的软件库为我们提供了更广阔的扩展空间。

&emsp;&emsp;除了选择现有镜像为基础镜像外，Docker 还存在一个特殊的镜像，名为 `scratch`。这个镜像是虚拟的概念，并不实际存在，它表示一个空白的镜像。
### RUN 执行命令
&emsp;&emsp;RUN 指令是用来执行命令行命令的。由于命令行的强大能力，RUN 指令在定制镜像时是最常用的指令之一。其格式有两种：

- shell 格式：RUN <命令>，就像直接在命令行中输入的命令一样。刚才写的 Dockerfile 中的 RUN 指令就是这种格式。
```
RUN apt-get update
```
- exec 格式：RUN ["可执行文件", "参数1", "参数2"]


&emsp;&emsp;既然 RUN 就像 Shell 脚本一样可以执行命令，那么我们是否就可以像 Shell 脚本一样把每个命令对应一个 RUN 
```shell
FROM ubuntu:latest

RUN apt-get update
RUN apt-get install -y gcc libc6-dev make
RUN wget -O redis.tar.gz "http://download.redis.io/releases/redis-3.2.5.tar.gz"
RUN mkdir -p /usr/src/redis
RUN tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1
RUN make -C /usr/src/redis
RUN make -C /usr/src/redis install
```
&emsp;&emsp;之前说过，Dockerfile 中每一个指令都会建立一层，RUN 也不例外。每一个 RUN 的行为，就和刚才我们手工建立镜像的过程一样：新建立一层，在其上执行这些命令，执行结束后，commit 这一层的修改，构成新的镜像。

&emsp;&emsp;而上面的这种写法，创建了 7 层镜像。这是完全没有意义的，而且很多运行时不需要的东西，都被装进了镜像里，比如编译环境、更新的软件包等等。结果就是产生非常臃肿、非常多层的镜像，不仅仅增加了构建部署的时间，也很容易出错。 这是很多初学 Docker 的人常犯的一个错误。

`上面的 Dockerfile 正确的写法应该是这样：`
```shell
FROM ubuntu:latest

RUN buildDeps='gcc libc6-dev make' \
    && apt-get update \
    && apt-get install -y $buildDeps \
    && wget -O redis.tar.gz "http://download.redis.io/releases/redis-3.2.5.tar.gz" \
    && mkdir -p /usr/src/redis \
    && tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \
    && make -C /usr/src/redis \
    && make -C /usr/src/redis install \
    && rm -rf /var/lib/apt/lists/* \
    && rm redis.tar.gz \
    && rm -r /usr/src/redis \
    && apt-get purge -y --auto-remove $buildDeps
```
&emsp;&emsp;首先，之前所有的命令只有一个目的，就是编译、安装 redis 可执行文件。因此没有必要建立很多层，这只是一层的事情。因此，这里没有使用很多个 RUN 对一一对应不同的命令，而是仅仅使用一个 RUN 指令，并使用 && 将各个所需命令串联起来。将之前的 7 层，简化为了 1 层。在撰写 Dockerfile 的时候，要经常提醒自己，这并不是在写 Shell 脚本，而是在定义每一层该如何构建。

&emsp;&emsp;并且，这里为了格式化还进行了换行。Dockerfile 支持 Shell 类的行尾添加 \ 的命令换行方式，以及行首 # 进行注释的格式。良好的格式，比如换行、缩进、注释等，会让维护、排障更为容易，这是一个比较好的习惯。

&emsp;&emsp;此外，还可以看到这一组命令的最后添加了清理工作的命令，删除了为了编译构建所需要的软件，清理了所有下载、展开的文件，并且还清理了 apt 缓存文件。这是很重要的一步，我们之前说过，镜像是多层存储，每一层的东西并不会在下一层被删除，会一直跟随着镜像。因此镜像构建时，一定要确保每一层只添加真正需要添加的东西，任何无关的东西都应该清理掉。

&emsp;&emsp;很多人初学 Docker 制作出了很臃肿的镜像的原因之一，就是忘记了每一层构建的最后一定要清理掉无关文件。

### 构建镜像
在 `Dockerfile` 文件所在目录执行：
```
$ docker build -t myubuntu .
Sending build context to Docker daemon  2.048kB
Step 1/2 : FROM ubuntu:latest
 ---> ea4c82dcd15a
Step 2/2 : RUN apt-get update
 ---> Running in 5f945d0d62fb
Get:1 http://security.ubuntu.com/ubuntu bionic-security InRelease [83.2 kB]
Get:2 http://archive.ubuntu.com/ubuntu bionic InRelease [242 kB]
Get:3 http://security.ubuntu.com/ubuntu bionic-security/multiverse amd64 Packages [1367 B]
Get:4 http://security.ubuntu.com/ubuntu bionic-security/main amd64 Packages [262 kB]
Get:5 http://archive.ubuntu.com/ubuntu bionic-updates InRelease [88.7 kB]
Get:6 http://archive.ubuntu.com/ubuntu bionic-backports InRelease [74.6 kB]
Get:7 http://security.ubuntu.com/ubuntu bionic-security/universe amd64 Packages [119 kB]
Get:8 http://archive.ubuntu.com/ubuntu bionic/multiverse amd64 Packages [186 kB]
Get:9 http://archive.ubuntu.com/ubuntu bionic/main amd64 Packages [1344 kB]
Get:10 http://archive.ubuntu.com/ubuntu bionic/universe amd64 Packages [11.3 MB]
Get:11 http://archive.ubuntu.com/ubuntu bionic/restricted amd64 Packages [13.5 kB]
Get:12 http://archive.ubuntu.com/ubuntu bionic-updates/universe amd64 Packages [730 kB]
Get:13 http://archive.ubuntu.com/ubuntu bionic-updates/restricted amd64 Packages [10.7 kB]
Get:14 http://archive.ubuntu.com/ubuntu bionic-updates/main amd64 Packages [567 kB]
Get:15 http://archive.ubuntu.com/ubuntu bionic-updates/multiverse amd64 Packages [6142 B]
Get:16 http://archive.ubuntu.com/ubuntu bionic-backports/universe amd64 Packages [2975 B]
Fetched 15.1 MB in 6s (2412 kB/s)
Reading package lists...
Removing intermediate container 5f945d0d62fb
 ---> f55da3abb808
Successfully built f55da3abb808
Successfully tagged myubuntu:latest

```


&emsp;&emsp;从命令的输出结果中，我们可以清晰的看到镜像的构建过程。在 Step 2 中，如同我们之前所说的那样，RUN 指令启动了一个容器 `5f945d0d62fb`，执行了所要求的命令，并最后提交了这一层 `f55da3abb808`，随后删除了所用到的这个容器 `5f945d0d62fb`。

这里我们使用了 docker build 命令进行镜像构建。其格式为：
```
docker build [选项] <上下文路径/URL/->
```
- `-t` : 指明生成镜像名称

&emsp;&emsp;如果注意，会看到 docker build 命令最后有一个 `.`。`.` 表示当前目录，而 Dockerfile 就在当前目录，因此不少初学者以为这个路径是在指定 Dockerfile 所在路径，这么理解其实是不准确的。如果对应上面的命令格式，你可能会发现，这是在指定上下文路径。那么什么是上下文呢？

&emsp;&emsp;首先我们要理解 docker build 的工作原理。Docker 在运行时分为 Docker 引擎（也就是服务端守护进程）和客户端工具。Docker 的引擎提供了一组 REST API，被称为 Docker Remote API，而如 docker 命令这样的客户端工具，则是通过这组 API 与 Docker 引擎交互，从而完成各种功能。因此，虽然表面上我们好像是在本机执行各种 docker 功能，但实际上，一切都是使用的远程调用形式在服务端（Docker 引擎）完成。也因为这种 C/S 设计，让我们操作远程服务器的 Docker 引擎变得轻而易举。

&emsp;&emsp;当我们进行镜像构建的时候，并非所有定制都会通过 RUN 指令完成，经常会需要将一些本地文件复制进镜像，比如通过 COPY 指令、ADD 指令等。而 docker build 命令构建镜像，其实并非在本地构建，而是在服务端，也就是 Docker 引擎中构建的。那么在这种客户端/服务端的架构中，如何才能让服务端获得本地文件呢？

&emsp;&emsp;这就引入了上下文的概念。当构建的时候，用户会指定构建镜像上下文的路径，docker build 命令得知这个路径后，会将路径下的所有内容打包，然后上传给 Docker 引擎。这样 Docker 引擎收到这个上下文包后，展开就会获得构建镜像所需的一切文件。

&emsp;&emsp;一般来说，应该会将 Dockerfile 置于一个空目录下，或者项目根目录下。如果该目录下没有所需文件，那么应该把所需文件复制一份过来。如果目录下有些东西确实不希望构建时传给 Docker 引擎，那么可以用 .gitignore 一样的语法写一个 .dockerignore，该文件是用于剔除不需要作为上下文传递给 Docker 引擎的。

&emsp;&emsp;那么为什么会有人误以为 . 是指定 Dockerfile 所在目录呢？这是因为在默认情况下，如果不额外指定 Dockerfile 的话，会将上下文目录下的名为 Dockerfile 的文件作为 Dockerfile。

&emsp;&emsp;这只是默认行为，实际上 Dockerfile 的文件名并不要求必须为 Dockerfile，而且并不要求必须位于上下文目录中，比如可以用 -f ../Dockerfile.php 参数指定某个文件作为 Dockerfile。

&emsp;&emsp;当然，一般大家习惯性的会使用默认的文件名 Dockerfile，以及会将其置于镜像构建上下文目录中。


## 6. Dockerfile指令详解

### COPY
&emsp;&emsp;`COPY` 指令将从构建上下文目录中 `<源路径>`的文件/目录复制到新的一层的镜像内的`<目标路径>`位置。比如：
```
COPY package.json /usr/src/app/
```
### ADD
&emsp;&emsp;`ADD` 指令和 `COPY` 的格式和性质基本一致。但是在 `COPY` 基础上增加了一些功能。

&emsp;&emsp;在 `Docker` 官方的 `Dockerfile` 最佳实践文档 中要求，尽可能的使用 `COPY`，因为 `COPY` 的语义很明确，就是复制文件而已，而 `ADD` 则包含了更复杂的功能，其行为也不一定很清晰。最适合使用 `ADD` 的场合，就是所提及的需要自动解压缩的场合。

&emsp;&emsp;在某些情况下，这个自动解压缩的功能非常有用，比如官方镜像 `ubuntu` 中：
```
FROM scratch
ADD ubuntu-xenial-core-cloudimg-amd64-root.tar.gz /
...
```
&emsp;&emsp;因此在 `COPY` 和 `ADD` 指令中选择的时候，可以遵循这样的原则，所有的文件复制均使用 `COPY` 指令，仅在需要自动解压缩的场合使用 `ADD`。





  