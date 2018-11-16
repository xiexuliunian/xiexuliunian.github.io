---
title: docker学习
date: 2018-11-16 20:04:36
categories: 技术工具
---
现今容器化技术对于技术行业的改变是非常巨大的，特别是对于企业来说，因此学习掌握容器化技术的基础是非常必要的。
## 1.docker安装
本文使用manjaro(Archlinux)系linux系统，由于有pacman、yaour管理软件包的加持，加上manjaro的图形化前端pamac,如下图

<div style="width: 80%; margin: auto">
<center>{% asset_img 1.png 说明 %}</center>
</div>

安装docker，nvidia-docker只需要点安装即可，当然前提是你要有个好的网络，接下来会写一篇讲如何在linux下使用全局代理的博文，以备不时之需。

## 2.遇到的第一个问题
看教程这时候会让你输`docker info`查看一下本机的docker安装信息情况，正常情况如下 
>$ docker info  
Containers: 2
 Running: 1
 Paused: 0
 Stopped: 1
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
Runtimes: runc nvidia
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

这是正常情况,但是你很有可能会得到：
>Cannot connect to the Docker daemon at unix:/var/run/docker.sock. Is the docker daemon running?