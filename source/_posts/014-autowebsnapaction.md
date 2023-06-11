---
title: 使用GithubAction给任意网页截图
tags:
  - 教程
  - GithubAction
  - 白嫖
top_img: 'https://bing.icodeq.com/?5'
cover: 'https://bing.icodeq.com/?1'
abbrlink: 799e
date: 2022-05-08 08:15:08
---

# 前言：

最近白嫖 [Replit](https://github.com/stars/valetzx/lists/replit) 的时候发现了一个能在 vercel 部署的网页截图程序：[valetzx/websnap](https://github.com/valetzx/websnap) 可以将网页截图并展示 [DEMO](https://github.com/valetzx/websnap) 以下截图是该程序为本页生成的实时渲染。但是这个程序有个非常不友好的BUG就是截图网页中有中文时，中文会被渲染成方框，简单来说就是（ 缺 字 体 ）可是 vercel 的环境非常封闭?比较难去添加字体给系统。

![](https://websnap.vercel.app/blog.learnonly.xyz/p/799e.html/?key=QQ550029691)

那为什么不用 `iframe` 标签呢？整个网页的渲染相比图片还是比较吃力的，我们完全可以把渲染交给服务器。并且网页截图能做更多东西，比如网页快照等等等等（图片是可以永久保存的）而 `iframe` 是真实时。

当时我的朋友 [zkeq](https://github.com/zkeq) 也照着写了个Python程序：[zkeq/Python-WebSite-Screenshot: 一款支持截图中文网站的 Actions 截图工具](https://github.com/zkeq/Python-WebSite-Screenshot) 使用GithubAction的好处在于，我们可以给整个系统添加中文，并且保存图片到仓库。当然也有缺点，比如不能 `实时` 渲染图片了。

![](https://socialify.git.ci/zkeq/Python-WebSite-Screenshot/image?description=1&font=Bitter&forks=1&language=1&owner=1&pattern=Plus&stargazers=1&theme=Dark)

当然这个程序的使用非常的简单，下图是本文章的截图（来自GithubRaw）：

![](https://github.com/valetzx/AutoWebSnap/raw/main/source/gallery/snap/2022-05-07_11-48-24.png)

# 准备：

1. 准备一个 Github 账号
2. 转到 [zkeq/Python-WebSite-Screenshot](https://github.com/zkeq/Python-WebSite-Screenshot) 点 `Fork` 到你的仓库

# 开始：

1. 在 https://github.com/settings/tokens 生成个人 `Access Token` 并且赋予 `repo` 权限
2. 环境变量 `Name` 为 `MY_GIT_TOKEN` /  `Value` 填 上方生成的  `Access Token` 

![image-20220507195716274](https://img.pighog.repl.co/2022/05/image-20220507195716274.png)

3. 在你仓库的 `list.json` 中填入你想生成截图的网站。示例格式：

```json
 {
	"url": "https://alist.learnonly.xyz",
	"timeout": 10,
	"real_time_out": 5,
	"height": 1080,
	"width": 1920,
	"daydel": 1
 },
```

| 参数            | 说明                                          |
| --------------- | --------------------------------------------- |
| `url`           | 网站网址                                      |
| `timeout`       | sele 模块中等待时间，加载出网站后会停止（秒） |
| `real_time_out` | 强制等待时间，在上述 timeout 后休眠时间（秒） |
| `width`         | 截图宽度                                      |
| `height`        | 截图高度                                      |
| `daydel`        | 截图的保存时间（天）                          |

4. 完成后，可以到 Actions 页，点击 Run workflow 进行测试。

![image-20220507201618784](https://img.pighog.repl.co/2022/05/image-20220507201618784.png)

# 进阶：

你可以自己查看并修改 GithubAction 中的内容：

```YML
on:
  workflow_dispatch:
  schedule:
    - cron: '30 */3 * * *'
  watch:
    types: [started]
```

茹 `cron: '30 */3 * * *'` 设置定时触发（相当于定时监控，访问网站）生成截图！

``` bash
#!/bin/bash
mdimgh="![]("
gitraw="https:\/\/raw.githubusercontent.com\/valetzx\/AutoWebSnap\/main\/"
mdimgb=")"

find save -type f -name "*.png" > name.md
sed "/./{s/^/![]($gitraw&/;s/$/&$mdimgb/}" name.md > index.md

echo AutoWebSnap View At https://pighog.vercel.app/gallery/snap > README.md

sed -i '1 i\{% gallery %}' index.md
sed -i '/{% gallery %}/i\\' index.md

sed -i '1 i\---' index.md
sed -i '1 i\top_img: https://bing.icodeq.com ' index.md
sed -i '1 i\swiper_cover: ' index.md
sed -i '1 i\swiper_desc: ' index.md
sed -i '1 i\swiper_index: 996' index.md
sed -i '1 i\type: "gallery" ' index.md
sed -i '1 i\date: 2022/05/06 ' index.md
sed -i '1 i\title: 网页快照' index.md
sed -i '1 i\---' index.md

sed -i '$a\{% endgallery %}'  index.md
```

这个是我自己写的，生成可以让`hexo`相册读取的index.md文件，大家可以看着改

可以在我博客的 [相册]( https://pighog.vercel.app/gallery/snap) 预览仓库中的网页截图（有些许延迟，来自GithubRaw）

``` bash
daydel=$(date +"%Y-%m-%d" -d "-8 day")
find save -type f -name "*$daydel*.png" -exec rm -rf {} \; 
```

你可以在 Action 的 yml-run 中添加以上两行来删除`"-8 day"`天前生成的照片

# 彩蛋：

小彩蛋（ 你会发现你每次刷新这篇文章的时候，封面图与下面第一张预览截图都不一样。

![image-20220507202051078](https://img.pighog.repl.co/2022/05/image-20220507202051078.png)

文章备份：https://b1og.learnonly.xyz/article/01VC3T5Y5PO6A6B2CFSBF2JL7PGMV3GMYX