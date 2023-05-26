---
title: 使用Typora写作并配置PicGo图床推荐
date: 2022-03-12 21:04:11
tags: [教程,GithubAction,图床,白嫖,网盘,OneDrive]
top_img: https://img.pighog.repl.co/2022/03/psc3.jpg
cover: https://img.pighog.repl.co/2022/03/psc3.jpg
---
# 前言：
搭好博客需要开始写作了，如果你想在多个平台发布你的文章的话，一定少不了一个图床的使用。这里就不啰嗦图床是什么了（网上有一大堆Picgo+Github/Gitee之类的教程）这里总结一下我用的图床以及配置教程。

# 准备：

下载 [PicGo](https://picgo.github.io/PicGo-Doc/zh/)（图片上传到图床的程序）

下载 [Typora](https://typora.io/)（用来写MarkDown的程序） 

# 开始：

## 图床推荐

这里不推荐使用Gitee（仓库小，图片超过5M不能直链显示，国内服务有实名审核等问题），但是后面会使用 GithubAction 将图床同步至Gitee。

### OneDrive 

1.https://poweredby.cloud/ 
可用 5G 个人OneDrive 储存
PicGo插件：https://github.com/wayjam/picgo-plugin-s3
优点：方便管理，访问快速
问题：PicGo不支持4M以上文件上传

2.https://pigone.000webhostapp.com/
可用 5T OneDrive For Business 储存
（需自建应用程序或使用公共配置）
PicGo插件：https://github.com/yuki-xin/picgo-plugin-web-uploader
优点：存储空间巨巨大，方便管理，访问速度看vps
问题：PicGo不支持4M以上文件上传，搭建程序可能有额外费用

### Github
可使用 1G 仓库储存
优点：配置方便，GithubAction灵活
问题：空间小，仓库存满有概率检测封号

### Gitlab
所有仓库共用 10G 储存
PicGo插件：https://github.com/D-W-X/picgo-plugin-gitlab-files
优点：国内访问速度比Github快
问题：？

推荐以上所有仓库都新建账号使用。

我日常使用为：大图片直接上传至 企业5T OneDrive 后手动复制直链，截图等内容使用Poweredby.cloud的S3服务，传至 个人OneDrive 写作时Typora自动转换直链。Github仓库应该放些小文件，或重要的备份文件。

## Github进阶

{% expand  将Github仓库同步至网盘及Gitlab，Gitee ,bg,color %}



{% endexpand %}

## Typora配置