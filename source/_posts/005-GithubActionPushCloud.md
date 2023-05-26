---
title: 使用GithubAction推送仓库至任意网盘
date: 2022-03-10 09:12:34
tags: [教程,GithubAction,自动部署,白嫖,网盘]
top_img: https://img.pighog.repl.co/2022/03/psc.jpg
cover: https://img.pighog.repl.co/2022/03/psc.jpg
---

# 前言：

### Github站队，虽然不影响我们使用，但是这里发一个备份方法，希望帮助到有这样想法的大家。

### 同时还有Github同步到Gitee，Gitlab的Action，后面会一起发（这个网上其实很多）。

# 准备：

下载 [Rclone](https://rclone.org/) （用来生成 rclone.conf ）

下载 [7Zip](http://www.7-zip.org.cn/) （用来压缩 rclone.conf 并设置密码 ）

准备好需要备份的仓库。

准备一个网盘，教程中使用 [Alist](https://alist-doc.nn.ci/) 挂载阿里云。

其他网盘可以 [百度 Rclone挂载](https://www.bing.com/search?q=rclone%E6%8C%82%E8%BD%BD) 你想使用的网盘。

教程视频：[哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1ER4y1V7wu/)

# 开始：

### 首先打开 Rclone Gui 或 CMD ，新建网盘。

![image-20220309230601882](https://img.pighog.repl.co/2022/03/image-20220309230601882.png)

在弹出的CMD中按流程新建，并生成配置文件。生成后打开 rclone.conf

![image-20220309230831786](https://img.pighog.repl.co/2022/03/image-20220309230831786.png)

打开 rclone.conf 后内容大致为如下：

```
[aliyun]
type = webdav #网盘类别
url = #webdav路径
vendor = other #webdav供应商
user = admin #webdav用户
pass = #webdav密码 此密码在CMD中输入密码后自动生成
```

```
[e5]
type = onedrive
client_id = #客户端id
client_secret = #客户端机密
token = {"access_token":"xxx","expiry":"timexxx"}
drive_id = #自动生成
drive_type = business
```

rclone挂载各种各样盘可以百度 [rclone挂载阿里云](https://www.bing.com/search?q=rclone%E6%8C%82%E8%BD%BD%E9%98%BF%E9%87%8C%E4%BA%91) [rclone挂载onedrive](https://www.bing.com/search?q=rclone%E6%8C%82%E8%BD%BDonedrive) 等。以上有任意一个可以挂载的即可。确认可以挂载后，将 rclone.conf 压缩为zip `rclone.zip` 并设置 **密码** （保护网盘信息）。

### 设置Secrets 添加密码

打开需要备份的仓库，点仓库中的 **Settings** ,并找到 **New repository secret** ← 点击。

![image-20220309232516695](https://img.pighog.repl.co/2022/03/image-20220309232516695.png)

![image-20220309232705904](https://img.pighog.repl.co/2022/03/image-20220309232705904.png)

在弹出的界面中 `Name` 项框中填 `PASSWD` 全大写，`Value` 项框中填 `刚刚设置的压缩包zip密码` 填好点 `Add secret ` 按钮。

### 上传 `rclone.zip` 至项目(需要备份的仓库)根目录

### 打开你需要备份的仓库，点击 **Actions** 选项，新建 **Workflow**

![image-20220309224714579](https://img.pighog.repl.co/2022/03/image-20220309224714579.png)

![image-20220309224810932](https://img.pighog.repl.co/2022/03/image-20220309224810932.png)

### 点 **set up a workflow yourself** →

将以下代码粘贴至 main.yml （可改名.yml）

```yml
name: backup to onedrive #Action运行标题

on: #运行条件
  push: #推送到 master 时运行
    branches: 
      - master  #看自己仓库，有些是main
  schedule: #定时运行
    - cron: 0 22 1/2 * *
  watch: #点star时运行
    types: started
jobs:
  run-it:
    runs-on: ubuntu-latest
    name: zip
    steps:

      - name: Checkout codes
        uses: actions/checkout@v2

      - name: Install rclone #安装rclone
        run: curl https://rclone.org/install.sh | sudo bash

      - name: Unzip config #解压配置文件
        run: |
         mkdir -p ~/.config/rclone/
         unzip -P ${{ secrets.PASSWD }} rclone.zip -d ~/.config/rclone/
     
      - name: Run rclone #运行rclone
        run: |
          
          # 延迟函数 可以删
          delay()
          {
          random_time=$(($RANDOM % $1))
          echo "等待 ${random_time} 分钟后开始"
          for((i=1;i<=${random_time};i++));  
          do
          echo "倒计时 $[${random_time}-$i] 分钟"
          sleep 1m
          done
          }
          # 随机延迟0~2分钟后再进行操作,可自行设置时间 都能删
          delay 2 

          # 更改时区 可以删
          sudo cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
          ls -a
          # 生成时间写入time.log ，将网盘目录写入lsf.log 可以删
          echo `date +"%Y-%m-%d %H:%M:%S"` begin > time.log 
          echo "列出文件(此处不展示)" #可以删
          rclone lsf e5:/ > "lsf.log"
          rclone mkdir e5:/github/xxx
          rclone sync ./ e5:/github/xxx

      - name: Zip config #打包配置文件
        run: zip -P ${{ secrets.PASSWD }} rclone.zip -j ~/.config/rclone/rclone.conf

      - name: Commit config #提交配置文件
        run: |
         git config --global user.email "550029691@qq.com"
         git config --global user.name "valetzx"
         git add .
         git commit -am "GitHub Actions Auto Rclone at $(date +'%Y-%m-%d %H:%M:%S')"
         git push --force # --force 是强制推送可以删
```

![image-20220309225159141](https://img.pighog.repl.co/2022/03/image-20220309225159141.png)

此处 e5 是rclone.conf中的[e5]盘名（rclone新建网盘时指定的名字）

`rclone mkdir e5:/github/xxx` 指在 e5 中新建 github/xxx 文件夹

`rclone sync ./ e5:/github/xxx` 指 同步 ./ 文件夹至 e5:/github/xxx

此处 ./ 是github根目录，更多命令可以百度，改完之后出问题就暂时不解决了。

## 如果需要备份多个仓库，以上几个步骤缺一不可（顺序不重要）

当然 `rclone.zip` 可以不用每次都生成，如果你想备份在同一个盘里的话。

![image-20220309233721287](https://img.pighog.repl.co/2022/03/image-20220309233721287.png)

其实有一个Action就能同步多个仓库的方法，但是不能实时(推送后同步，而且需要用Github服务器下载。有概率封号?这里放一下，建议别用。在更改时区后可以插入更多命令，比如git clone。想试可以自己试。

```
# 更改时区
          sudo cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
          git clone https://github.com/pigeonpig/pigeonpig.github.io.git #需要备份的仓库
          git clone https://github.com/pigeonpig/xxx #需要备份的仓库
          git clone https://github.com/pigeonpig/xxx #需要备份的仓库
          #最后也是用 sync 同步 但是 吃资源 不太 香，这个方法没试过，不想整。
```

## ~~视频教程直链Alist：[202203092151.mp4 ](https://flyist.fly.dev/Sharelist/movie!/猪/202203092151.mp4)~~

## 本文章备份地址：[Pigpigeon's blog](https://b1og.learnonly.xyz/article/01VC3T5Y66KR42LGMUYBCZNUEBT7MWKMJL)

