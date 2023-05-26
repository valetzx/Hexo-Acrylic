---
title: 在Replit中配置Nix环境
date: 2022-04-20 12:15:22
tags: [教程,Replit,白嫖]
top_img: 
cover: 
---

# 前言：

[在Replit中白嫖](https://pighog.vercel.app/p/aa4a.html) 时，一定会遇到有不能一键部署的环境，那我们如何选择自己想要的环境呢？这里需要用到Replit环境中自带的 `replit.nix` 文件。[Nix](https://nixos.org/) 是什么，我们要怎么使用，下面的文章会解答凝的疑问（

# 准备：

其实没什么好准备的，在Replit准备一个Bash环境（理论上任意环境都行），打开仓库后，点文件最右边三个点，点显示隐藏文件，然后仓库根文件夹中会多出 `.replit` `replit.nix` 两个文件。我们要使用 `replit.nix` 配置环境。

![image-20220420121713553](https://img.pighog.repl.co/2022/04/image-20220420121713553.png)

# 开始：

首先 [Nix](https://nixos.org/) 是一个包管理器，就像一个软件库，我们可以用 `replit.nix` 直接使用 Nix 中的软件包。这里用 [onedrive-vercel-index](https://github.com/spencerwooo/onedrive-vercel-index) 做演示。

onedrive-vercel-index 是一个部署在 [vercel](https://vercel.com/) 中的文件浏览程序，需要使用到 Next.js（属于vercel），我们可以在 Replit 直接创建 next.js 语言项目。

![image-20220420124304065](https://img.pighog.repl.co/2022/04/image-20220420124304065.png)

onedrive-vercel-index 还需要用到 Redis 缓存以及 pnpm包管理器。

我们浏览原始的 `replit.nix` 文件，查看文件结构，发现他自带node.js16，tsserver，yarn，jest。现在我们需要给他添加 redis 以及 pnpm。

![image-20220420124801826](https://img.pighog.repl.co/2022/04/image-20220420124801826.png)

打开 [Nix 搜索](https://search.nixos.org/packages) redis ，找到自己想要的redis包，点名字展开后，复制包名。

![image-20220420125550617](https://img.pighog.repl.co/2022/04/image-20220420125550617.png)

返回到 Replit 的 `replit.nix` 文件。换行后输入包名 `pkgs.redis` 

![image-20220420130608399](https://img.pighog.repl.co/2022/04/image-20220420130608399.png)

等待 ` Loading Nix environment... ` 加载完；

转到 Shell 中启动 Redis server，可以发现已经安装好了。

![image-20220420130750850](https://img.pighog.repl.co/2022/04/image-20220420130750850.png)

同理的 pnpm 搜索，复制包名，粘贴：`pkgs.nodePackages.pnpm` 

![image-20220420131521120](https://img.pighog.repl.co/2022/04/image-20220420131521120.png)

![image-20220420131640141](https://img.pighog.repl.co/2022/04/image-20220420131640141.png)

如果是npm包，请不要漏掉 nodePackages 而且请最好写在 `pkgs.nodejs-16_x` 下方，并缩进。写好程序的运行脚本，运行后，可以看到onedrive-vercel-index已成功启动并运行了。

![image-20220420132204024](https://img.pighog.repl.co/2022/04/image-20220420132204024.png)

```replit.nix
{ pkgs }: {
	deps = [
		pkgs.redis
		pkgs.nodejs-16_x
			pkgs.nodePackages.typescript-language-server
			pkgs.nodePackages.yarn
			pkgs.nodePackages.pnpm
			pkgs.replitPackages.jest
	];
}
```

# 进阶：

以下有两个我自己写好的环境仓库，有兴趣可以参照看一下：

### 在 Replit 自动构建 Alist [alistbuildonreplit](https://github.com/valetzx/alist-build-on-replit)

用到的环境有：go1.18，gcc，vite，nodejs16等

### 在 Replit 白嫖3G MC 服务器 [mcserveronreplit ](https://github.com/valetzx/mcserveronreplit)

用到的环境有：jdk17，php80，python等

# 注意：

有部分网路类型的包，例如 ngrok，zerotier-one 需要用别的方式安装，这里暂时不讲。以及rclone，可以使用，但是不能挂载到本地。后面有空会写，如何使用AriaNg，白嫖replit的服务器网络下载。速度也是肥肠可观的。

![image-20220420133548528](https://img.pighog.repl.co/2022/04/image-20220420133548528.png)



文章备份：https://b1og.learnonly.xyz/article/01VC3T5Y2CQ65HLXTYVVAY4YIPSXNPOL6O