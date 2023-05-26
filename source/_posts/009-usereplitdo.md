---
title: 使用Replit白嫖各种语言项目
date: 2022-04-17 09:15:22
tags: [教程,Replit,OneDrive,白嫖]
top_img: 
cover: 
---

# 前言：

害呀，家里也疫情刚封了一半了，最近正好在捣鼓 [Replit](https://replit.com/) ，这玩意主要是能白嫖，似乎应用没有时间，没有流量限制。能挂很多东西，只是单个仓库的虚拟机配置比较蔡。不过跑跑正经项目肯定是够用的。这里列给大家看一下我挂的项目，下面应该都会写教程。本文在 [Replit](https://hexo.pighog.repl.co/p/aa4a.html) 首发哈哈！
![image-20220417093726604](https://img.pighog.repl.co/2022/04/image-20220417093726604.png)

Vue3博客，[Busuanzi](http://busuanzi.ibruce.info/)计数，[Hexo](https://hexo.io/)博客，[Kodbox](https://kodcloud.com/)可道云，[OneManager-php](https://github.com/qkqpttgf/OneManager-php)，[Panindex](https://libsgh.github.io/PanIndex)，[Alist](https://alist-doc.nn.ci/) 等等。

# 准备：

注册登录 [Replit](https://replit.com/) 并创建教育 Team（私有仓库）。
![image-20220417100205289](https://img.pighog.repl.co/2022/04/image-20220417100205289.png)

# 开始：

## 在 Replit 中运行Bash项目：Panindex，Alist

{% expand  使用 Bash 运行Panindex ,bg,color %}

## [Panindex](https://libsgh.github.io/PanIndex) 程序预览：https://pan.pighog.repl.co/

### 1. 在教育Team创建Bash语言项目

![image-20220417100343779](https://img.pighog.repl.co/2022/04/image-20220417100343779.png)

### 2. 下载二进制程序`amd64`并解压至项目根目录

### 3. 编写 `.replit` 及 `main.sh` 文件

![image-20220417102208102](https://img.pighog.repl.co/2022/04/image-20220417102208102.png)

### .replit需要点右上角三个点Show显示！检查文件并修改成以下内容！

#### 检查`.replit`文件：

```.replit
run = ["bash", "main.sh"]
entrypoint = "main.sh"
```

#### 检查`main.sh`文件：

```bash
chmod  +x  PanIndex-linux-amd64
./PanIndex-linux-amd64
```

### 4. 编写完成后点顶上绿色 ▶ Run 按钮运行。

{% endexpand %}

文章备份：https://b1og.learnonly.xyz/article/01VC3T5Y5NY734QGFJDRE364YAMQD55JRF

{% expand  使用 Bash 运行Alist ,bg,color %}

## [Alist](https://alist-doc.nn.ci/) 程序预览：https://alist.pighog.repl.co/

### 0. [一键部署到Replit（不推荐）](https://github.com/alist-org/alist-replit)

### 1. 在教育Team创建Bash语言项目

### 2. 下载二进制程序`amd64`并解压至项目根目录

### 3. 编写 `.replit` 及 `main.sh` 文件

#### 检查`.replit`文件：

```.replit
run = ["bash", "main.sh"]
entrypoint = "main.sh"
```

#### 检查`main.sh`文件：

```bash
chmod  +x  alist-linux-amd64
./alist-linux-amd64
```

### 4. 编写完成后点顶上绿色 ▶ Run 按钮运行。

{% endexpand %}

文章备份：https://b1og.learnonly.xyz/article/01VC3T5Y6CGJ5Q4JTZWZF23VT3VIOU6QYU

## 在 Replit 中运行PHP Web Server项目：OneManager，Kodbox

{% expand  使用PHP Web Server部署OneManager ,bg,color %}

## OneManager程序预览：https://one.pighog.repl.co/

### 1. 在教育Team创建PHP Web Server语言项目

### 2. Clone [OneManager-php](https://github.com/qkqpttgf/OneManager-php) 仓库至项目根目录

### 3. 编写 `.replit` 及 `main.sh` 文件

#### 检查`.replit`文件：

```.replit
language = "php74"
run = "php -S 0.0.0.0:8000 index.php"
entrypoint = "index.php"
```
### 4. 编写完成后点顶上绿色 ▶ Run 按钮运行。

{% endexpand %}

文章备份：https://b1og.learnonly.xyz/article/01VC3T5YYMXAZXCIULMFD3P5B7MZUAJE5R

{% expand  使用PHP Web Server部署Kodbox ,bg,color %}

## Kodbox程序预览：https://kod.pighog.repl.co/

### 1. 在教育Team创建PHP Web Server语言项目

### 2. 下载 [Kodbox](https://kodcloud.com/download/) 压缩包并解压至项目根目录

### 3. 操作麻烦晚点写。

{% endexpand %}

文章备份：

## 在 Replit 中运行Nodejs 16项目：Hexo

{% expand  使用Nodejs部署Hexo ,bg,color %}

## Hexo程序预览：https://hexo.pighog.repl.co/

### 1. 在教育Team创建Nodejs 16语言项目

### 2. 如果有可以Clone自己Hexo项目的源码至根目录

### 3. 编写`.replit`文件：

```.replit
# DO NOT CHANGE RUN COMMAND HERE
# To change the run command either:
# 1) change the run script in package.json
# 2) replace npm run-script run in run.nix with your command

run = "npm run dev"
entrypoint = "README.md"

language = "nodejs"

[nix]
channel="stable-21_11"

[packager]
language = "nodejs"

[packager.features]
packageSearch = true
guessImports = true

[languages.nodejs]
pattern = "**/*.js"
syntax = "nodejs"

[languages.nodejs.languageServer]
start = [ "typescript-language-server", "--stdio" ]
```

### 4. 编写完成后点顶上绿色 ▶ Run 按钮运行。

{% endexpand %}

文章备份：https://b1og.learnonly.xyz/article/01VC3T5Y22O2IEBABDG5GKG7AFNBCFIJL7

## Replit的应用在一段时间不访问后会自动休眠减少资源消耗，如果大家想要保持运行可以用网站监控休眠设置5分钟定时访问保活。

### 出现各种问题可以在下面评论提问（Gitalk需要在Vercel域用，不能登录请使用Valine评论）

全文备份：https://b1og.learnonly.xyz/article/01VC3T5Y7Z2JOMRAGM3BBZ5E7ZJDLYJLOR
