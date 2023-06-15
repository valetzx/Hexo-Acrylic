---
title: 从0开始刷小米路由器4a千兆版
date: '2023-06-15 08:01:24'
tags:
  - 教程
  - ZeroTier
top_img: 'https://bing.icodeq.com/?230615'
cover: 'https://bing.icodeq.com/?230614'
categories: '2023新篇'
abbrlink: 630b
---

# 前言：

本文会带来一些工具，与你一起从入手开始为 小米路由器4a千兆版 `下文均用R4A代替` 刷机。最新购买的路由器版本号可能为 `2.30.28` 已经缺失了很多功能，需要下小米WiFi APP才能看到，所以我们选择刷 `OpenWrt` 并使用 `ZeroTier` 远程访问，如果你是23年前购买的老机型，可以参考网络上其他教程！

PS：写到一半发现很多原作者都讲了，这篇文章随意讲讲小坑吧

# 准备：

一根网线以及一个能插网线的电脑

解锁文件来自：https://www.right.com.cn/forum/thread-8261014-1-1.html

下载 `2022-R4A最新开ssh工具.zip`

下载地址：https://410252889.lanzouv.com/b0bt7de8d 密码:60rx

固件推荐：https://github.com/MrTaiKe/Action_OpenWrt_Xiaomi_R4AGv2

我在使用：https://github.com/ceclinux/Action_OpenWrt_Xiaomi_R4AGv2

准备好可以连接ssh/telnet的工具，Xshell或Termius，以及连接FTP的工具

这俩工具Windows其实都有自带，可以在 `启用或关闭Windows功能` 中安装

PS: 网上有些固件似乎不支持ipv6，大家看情况下载，我正在使用的固件支持ipv6并且自带了zerotier，但是没有科学上网工具我觉得问题不大，R4A本身性能不高不推荐什么都往里面塞。

# 开始：

## 解锁 ssh/telnet

解压 **2022-R4A最新开ssh工具.zip** 尝试管理员打开 **1.V2版开Telnet.bat** 如果没反应或直接退出，双击进入 **OpenWRTInvasion** 文件夹。`SHIFT+右键` -> `在此处打开 powershell 窗口` 输入 `.\python.exe .\remote_command_execution_vulnerability_v2.py` 回车运行等待完成后再次运行一遍指令即可开启 **telnet** 使用Windows自带的telnet或xshell等工具连接 **192.168.31.1** 用户名密码都为 `root`

### 备份 原厂固件！重要！

登录telnet后，输入 `cat /proc/mtd&&dd if=/dev/mtd0 of=/tmp/all_backup.bin` 文件会存在tmp目录，可以用ftp工具取出（如果使用资源管理器，请右键 all_backup.bin -> 复制到文件夹，而不是用快捷键CTRL+C复制！），取出文件后确认文件大小为16M！如果非常小，建议重新复制文件！这个文件非常重要！后面恢复原厂固件需要用到！！！

### 刷入 breed（不死鸟）

准备FTB连接工具或资源管理器，在资源管理器路径栏填入 `ftp://192.168.31.1/tmp/` 回车，将 **2022-R4A最新开ssh工具** 目录中的 `breed.bin` 复制到 **ftp://192.168.31.1/tmp/** 文件夹内，在telnet中输入 `md5sum /tmp/breed.bin` 比对输出的MD5值 **24e62762809c15ba3872e610a37451a3** 如果不一样，请重新上传文件！

**确认以上步骤！备份固件 `all_backup.bin 16MB` ！上传 `breed.bin` 均完成后！**

刷入breed！在telnet中输入 `mtd write /tmp/breed.bin Bootloader` 这一步完成后会断网，很快。需要用网线，将网线插入wan口（你原来光猫接到路由器的口，拔下来用连电脑的网线插进去）在浏览器中输入 192.168.1.1 ，如果能进入breed后台，就可以随便造了。

如果没能进入breed后台，请将路由器断电，然后按住复位键不放，路由器通电，等5到10秒钟松开复位键，浏览器输入192.168.1.1就能进入breed后台，以后想换固件也是这样进。

## 刷入固件

在刷入openwrt前，我们先在固件备份中备份 `编程器固件` 以及 `EEPROM`！然后点固件更新->常规固件，选择固件（你准备好的openwrt.bin固件或其他固件），选择你刚刚备份的 **EEPROM** 文件，这两个选好后，点上传，等待上传完成后会自动重启，在浏览器输入192.168.31.1等着就行。接下来的用户名和密码大概率为 `root/password` 如果不对，请询问固件作者。

## 刷回官方

将连接电脑的网线插入路由器wan口，将路由器断电，然后按住复位键不放，路由器通电，等5到10秒钟松开复位键，浏览器输入192.168.1.1进入breed后台。选择固件更新->编程器固件，选择上文备份的 **all_backup.bin 16MB** 确认勾选`自动重启`，取消勾选`保留现有Bootloader`，取消勾选 `保留现有EEPROM` 点上传，等待一会后，192.168.31.1会变回原来的小米路由器后台，并且失去breed，如果想再刷需要重新解锁telnet以及重新刷入breed！

# 进阶：

## 配置zerotier

注册zerotier账号 - 新建network - 复制network id

### 配置config

config目录：`/etc/config/zerotier` 内容：

```
config zerotier sample_config
        option enabled 0

        # persistent configuration folder (for ZT controller mode)
        #option config_path '/etc/zerotier'

        #option port '9993'

        # Generate secret on first start
        option secret 'generate'

        # Join a public network called Earth
        list join '8056c2e21c000001'
```
我们需要改两处内容，`option enabled 0` 改 `1` 启用， `list join '8056c2e21c000001'` 中 `80xxxxx` 内容改为你的 `network id`

改好保存后可以重启一次路由，如果你的zerotier network是私有的(Private)，需要在zerotier后台勾选上你的设备。

### 配置网段

在 **Interfaces** 接口中添加类似 **ztr2q3rmku** 这样的接口，并新建配置一个防火墙。

将防火墙设置为全部允许，并添加转发目标 **wan**。

在zerotier后台添加路由表：

|  你设备的网段   | zerotier分配给设备的ip  |
|  :----:  | :----:  |
| 192.168.110.0/24  | 	172.22.11.22 |
| 192.168.31.0/24  | 	172.22.11.22 |
| 192.168.41.0/24  |  172.22.11.23 |

以上表有两个 openwrt 设备，ip 分别是 **172.22.11.22** / **172.22.11.23** ，而 `172.22.11.22` 有两个网段，分别是 **192.168.110.0/24** / **192.168.31.0/24** 那我就可以通过另一个设备，连上zerotier后 直接访问 192.168.110.2，192.168.110.3 这样的设备，以及 192.168.31.1 这两个网段中的所有设备。

# 结语：

有很多地方都略过了，比如zerotier后台的一些操作，这些网上都有，我以前的文章页写过，这里就不多赘述了。如果后面有空，会再讲讲自构建你的openwrt，因为像这个小米路由器4a千兆版有很多ipk其实都不支持，需要自己编译。有问题可以到 Github 联系我。