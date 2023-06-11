---
title: 使用Docker运行Macos
tags:
  - 教程
  - 白嫖
  - Docker
top_img: 'https://bing.icodeq.com/?9'
cover: 'https://bing.icodeq.com/?12'
abbrlink: '3412'
date: 2022-06-10 08:16:16
---

# 前言：

玩了玩Docker，最近在白嫖某代码空间，正好看到个可以在Docker中跑Mac的仓库（[taylorcoffelt/macos-docker: Runs a macOS image on QEMU in a docker container, with VNC, SSH and SPICE access (github.com)](https://github.com/taylorcoffelt/macos-docker)），就运行了跑了跑。原仓库默认的 `docker-compose.yml` 配了4G运存，但是在某代码空间跑了三遍，都在第一次启动展开镜像下系统的时候寄(鲨进程)了，后来缓存了2G运行内存，理论上 mojave 是能带起来跑一跑的，毕竟系统老(当然如果你自己有新macos镜像，并且机器配置过硬的话，也可以自己构建，原仓库都有教程，也是比较简单的。

![image-20220609201714432](https://img.pighog.repl.co/2022/06/image-20220609201714432.png)

# 仓库介绍：

这个仓库是用来在 Docker 上跑 Macos 的，当前仓库文件 `18.8G` 默认系统为 `macos-mojave`

将本仓库下载至你的 Linux/Ubuntu 本地后，确认 `docker-compose.yml` 中的内容

```yml
version: "2"
services:
  macos:
    container_name: macos
    privileged: true
    hostname: macos
    image: taylorcoffelt/macos-docker
    environment:
      - SYSTEM_DISK=MyDisk.qcow2 #系统盘：默认 18.8G/64G
      - HEADLESS=1
      - MEM=2G #运行内存
      - CPUS=4 #CUP核心数
      - SPICE=1
    ports: #开放的端口 ( 容器外部 : 容器内部 )
      - "5900:5900" #5900 VNC 端口 tcp
      - "5930:5930" #5930 SPICE remote-viewer 端口
      - "5922:22" #5922 MAC 中的远程端口
    volumes: #需要挂载的 盘/文件夹
      - /tmp/macos/MyDisk.qcow2:/macostools/MyDisk.qcow2
      - ./BaseSystem.img:/macostools/BaseSystem.img
      - ./ESP.qcow2:/macostools/ESP.qcow2
      - ./firmware:/macostools/firmware
    #以上四个盘的文件请对照自己本地路径更改
    #冒号规则和PORT一样 冒号前是你容器外部的文件路径
    #这四个盘和挂载的文件夹请别乱删，很有可能删了开不了机(
    restart: always #永远会重启 (手动停止不会)
```

确认完毕后，使用 docker-compose up 启动 ->

打开 VNC 客户端，输入你的 Linux/Ubuntu 公网ip:5900 连接 Macos

如果你的机器没有公网IP，这里推荐 zerotier 内网穿透。

# 目录文件介绍：

```markdown
 - firmware           macos协议文件
 - docker-compose.yml docker启动文件
 - Dockerfile         docker启动文件
 - ESP.qcow2          boot启动文件
 - headless.sh        环境启动文件
 - MyDisk.qcow2       macos硬盘储存
 - BaseSystem.img     macos镜像文件
```
# 进阶教程：

第一次启动后的镜像约为 18.8G ，你也可以使用指令创建新的macos硬盘储存 64G

```bash
qemu-img create -f qcow2 MyDisk.qcow2 64G
```

然后进系统，格式化(擦除)这个硬盘，重新释放安装macos镜像文件。

本仓库内容参考：
 - https://github.com/sickcodes/Docker-OSX
 - https://github.com/taylorcoffelt/macos-docker
 - https://github.com/foxlet/macOS-Simple-KVM

你也可以 `git clone macOS-Simple-KVM` 后 手动构建其他Macos镜像：

参数：`--high-sierra`  `--mojave`  `--catalina` 例：

```bash
./jumpstart.sh --mojave
```

本仓库永久链接：https://pigeonpig-my.sharepoint.com/:f:/g/personal/drive_pigeonpig_onmicrosoft_com/EvVTi8ryKTZHju-LGrWlgGABSjTgKj7KPRmmhOJ2jqSViw?e=iu4eq3

理论永久链接：https://alist.learnonly.xyz/!Macos

MyDisk.qcow2直链：https://alist.learnonly.xyz/d/!Macos/MyDisk.qcow2

文章备份：https://b1og.learnonly.xyz/article/01VC3T5Y5FJHPGYLBE4JHYW4UYZIXLH47W