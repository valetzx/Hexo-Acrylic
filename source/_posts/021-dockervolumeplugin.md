---
title: 在Docker中使用Rclone作为储存卷
date: '2022-08-12 13:02:24'
tags:
  - 教程
  - 白嫖
  - Docker
  - 网盘
top_img: 'https://bing.icodeq.com/?18'
cover: 'https://bing.icodeq.com/?17'
abbrlink: 13d3
---

# 前言：

在各个平台，或本地使用 `Docker` 时，一定会遇到一些容器，想让它们的储存同步到云端，方便以后新建容器时好继承上一个容器的储存。本文会教大家如何安装 `rclone` 作为 `docker` 插件，以方便使用各个云平台作为容器储存卷。*本文提到的`插件`若未明确表达均指代 `rclone docker plugin`。部分内容来自 https://rclone.org/docker/ 

# 准备：

你需要准备一个有 `Docker API Version 1.12` 以上的环境，推荐 `1.40+` 可以使用 `docker version` 查看你当前安装的 Docker 版本。并安装 `docker-comps` (可选)：

```
Client:
 Version:           20.10.17+azure-1
 API version:       1.41
 Go version:        go1.17.11
 Git commit:        
 Built:             Mon Jun  6 21:36:39 UTC 2022
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server:
 Engine:
  Version:          20.10.17+azure-1
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.17.11
  Git commit:       
  Built:            Mon Jun  6 22:32:38 2022
  OS/Arch:          linux/amd64
  Experimental:     false
```

# 开始：

### 安装流程：

1. 首先你需要在你的 主机(VPS等) 安装 `FUSE` 模块，这是必要的！

```bash
sudo apt-get -y install fuse
```

2. 创建两个 `rclone docker plugin` 的数据缓存文件夹（默认）

```bash
sudo mkdir -p /var/lib/docker-plugins/rclone/config
sudo mkdir -p /var/lib/docker-plugins/rclone/cache
```

3. 为你的docker安装插件

```bash
docker plugin install rclone/docker-volume-rclone:amd64 args="-v" --alias rclone --grant-all-permissions
docker plugin list #查看插件列表 出现NAME=rclone:latest;ENABLED=true 说明安装并启用成功了
```

4. 你可以根据需求，更换 `rclone.conf` 及 `cache` 文件夹

```bash
docker plugin disable rclone #禁用rclone插件
docker plugin set rclone RCLONE_VERBOSE=2 config=/root/.config/rclone cache=/tmp/rclone args="--vfs-cache-mode=writes --allow-other" #配置文件夹及缓存文件夹
docker plugin enable rclone #启用rclone插件
docker plugin inspect rclone #查看插件配置
```

### 挂载流程：

#### SFTP 卷挂载示例

1. 创建 `sftpvolume` 卷（ sftpvolume为储存卷名 ）

```bash
docker volume create sftpvolume -d rclone -o type=sftp -o sftp-host=_hostname_ -o sftp-user=_username_ -o sftp-pass=_password_ -o allow-other=true
```

2. 创建使用 `sftpvolume` 卷的容器

```bash
docker run --rm -it -v sftpvolume:/mnt --workdir /mnt ubuntu:latest bash
# sftpvolume:/mnt 指的是：你SFTP中 /mnt 目录（和原版rclone是一样）
# --workdir /mnt 指的的：容器内部目录 /mnt
```

如果一切配置正确的话，你会进入到这个新的容器。你可以输入`ls`命令查看 /mnt 内容

#### 任意网络盘挂载示例

*[Rclone官方示例](https://rclone.org/docker/) 为 Google Drive 我们这里用 Onedrive 做演示

0. 在另外任意一台机器，创建你想挂载的网盘，比如 Onedrive，将生成的 `rclone.conf` 配置文件 移动到你 主机(VPS等) 的 `/var/lib/docker-plugins/rclone/config/rclone.conf` 目录（默认：如果没有走安装流程的第四步，就是一开始 `mkdir` 的文件夹）  [附录：创建网盘教程](https://rclone.org/docs/) 配置文件看起来应该是下面这样（onedrive）：
```
[oodrive]
type = onedrive
client_id = 180xxxx2-0114-xxxxaf31-24aaxxxxx0cb
client_secret = Xik7Q~JxxxxxoaO8aKxxxxxvclxlxxxxx8
token = {"access_token":"xxx","expiry":"2022-05-04T07:30:31.869533611+08:00"}
drive_id = b!xxxxxxxx-SdYUkcM9xT9TKp
drive_type = business
```

1. 创建一个文件名为 `example.yml` 的 docker swarm stack：

```yaml
version: '3'
services:
  heimdall:
    image: linuxserver/heimdall:latest
    ports: [8080:80]
    volumes: [configdata:/config]
volumes:
  configdata:
    driver: rclone
    driver_opts:
      remote: 'oodrive:heimdall'
      allow_other: 'true'
      vfs_cache_mode: full
      poll_interval: 0
```

2. 运行这个stack

```
docker stack deploy example -c ./example.yml
```

3. 在一段时间后，会创建 `example_heimdall` 服务并在8080端口启动，你可以使用以下指令查看

```
docker service ls
docker service ps example_heimdall
docker volume ls
```

4. 在测试结束后你可以使用 `docker stack remove example` 移除服务，使用 `docker volume remove example_configdata` 移除储存卷。

#### 使用CLI创建卷

```
docker volume create vol1 -d rclone -o remote=storj: -o vfs-cache-mode=full
docker volume create vol2 -d rclone -o remote=:storj,access_grant=xxx:heimdall
docker volume create vol3 -d rclone -o type=storj -o path=heimdall -o storj-access-grant=xxx -o poll-interval=0
```

#### 使用Docker Swarm 或Docker Compose

```yaml
volumes:
  volume_name_1:
    driver: rclone
    driver_opts:
      remote: 'oodrive:'
      allow_other: 'true'
      vfs_cache_mode: writes
      token: '{"type": "borrower", "expires": "2021-12-31"}'
      poll_interval: 0
```

### 实战总结：

以下为使用 compos 部署 alist 并挂载云端的配置，注意 `oodrive` 为你的 `rclone` 配置文件中的网盘名字，请读完全文再抄袭！

```yaml
version: '3'
services:
 alistr:                            #服务名 alistr 任意
   image: xhofe/alist:latest        #容器镜像 xhofe/alist
   ports: [5244:5244]               #容器端口 5244
   network_mode: host               #网络模式 host
   volumes: [adata:/opt/alist/data] #卷名 adata 挂载至容器内/ opt/alist/data
volumes:                            #储存卷配置：
 adata:                             #定义上面的卷名 adata
   driver: rclone                   #磁盘名 rclone
   driver_opts:                     #磁盘配置
     remote: oodrive:/example/alist #同步 oodrive 下 /example/alist 文件
     allow_other: 'true'            #类似rclone mount配置
     vfs_cache_mode: writes         #类似rclone mount配置
     poll_interval: 0               #类似rclone mount配置
```

如果出现 `Unsupported config option for services.volumes: 'XXXX'` 一般为 `rclone.conf` 未配置正确，请检查路径及网盘token密码等。

# 结语：

当然，你可以在每次机器启动后 手动/使用守护进程 挂载onedrive或其他rclone配置，再重启docker。但是使用插件可以大大增加效率，配置一次就能永久爽快体验。
