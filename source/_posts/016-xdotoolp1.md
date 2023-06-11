---
title: 使用xdotool在Ubuntu自动化
tags:
  - 教程
  - 白嫖
top_img: 'https://bing.icodeq.com/?7'
cover: 'https://bing.icodeq.com/?10'
abbrlink: 15af
date: 2022-06-06 16:16:16
---

# 前言：

最近在白嫖某平台的云代码空间（ 想整成服务器/搭点东西玩，由于是代码空间平台本意肯定是给开发者云跑云测试用的，所以在不写代码 / 不干活的时候会睡觉，节省资源。这里写个使用 xdotool 工具来自动写代码（ 当然不保证可以一直白嫖，只是做个记录。

# 准备：

```bash
sudo apt install xdotool
```

安装 xdotool 包，在这之前可以更新一下 apt `apt update` `apt upgrade`

# 开始：

最重要的指令：

```bash
xdotool search --name [name of the window] key [keys to press]
```

由于 xdotool 是命令行工具，在不指定 **窗口** 的情况下，会默认在当前命令行执行自动化（当然你有桌面UI的话可以用xdotool控制鼠标，但是也挺麻烦）

这条指令的意思是 xdotool 搜索 窗口名字为 xxx 的程序 执行 按键 keyxxx 栗子：

```bash
xdotool search --name code key "ctrl+s"
```

这条指令的意思是 xdotool 搜索 窗口名字为 code 的程序 执行按键 ctrl+s 

使用 xdotool 可以操控鼠标，按键等，详细可以看 xdotool help，下面是我自己用的一个栗子：

```bash
#!/bin/bash
pid_list=`xdotool search "code"`
#获取窗口名为code的程序pid存为变量
main=`date "+%Y-%m-%d %H:%M:%S"`
date=`date +%s%N | md5sum |cut -c 1-9`
rad=`head /dev/urandom |cksum |md5sum |cut -c 1-9`
#以上为指定变量，获取一些时间和随机值作为下面键入的内容
for i in $pid_list 
do
name=`xdotool getwindowname "$i"`
#获取指定pid的窗口名（完整）
echo $i
#输出pid值
echo $name
#输出窗口名
id=$i
#指定id变量为pid（ 这里有点重复了晚点修
xdotool windowfocus $id 
#使用 xdotool 聚焦到指定id的窗口（一次）
xdotool key "ctrl+a"
#使用 xdotool 执行按键 ctrl+a 
xdotool  type  $main+$name
#使用 xdotool 键入值 $main+$name
xdotool key "Return"
#使用 xdotool 执行按键 Return
xdotool  type  $id+$rad
#使用 xdotool 键入值 $id+$rad
xdotool key "Return"
#使用 xdotool 执行按键 Return
sleep 3
#休息三秒，这里代码空间有Copilot
xdotool key "Tab"
#使用 xdotool 执行按键 Tab 补全Copilot想写的
xdotool key "Return"
#使用 xdotool 执行按键 Return
sleep 3
#休息三秒让Copilot想想怎么写
xdotool key "Tab"
#使用 xdotool 执行按键 Tab 补全Copilot想写的
xdotool key "Return"
#使用 xdotool 执行按键 Return
xdotool  type  $date
#使用 xdotool 键入值 $date
sleep 3
#休息三秒让Copilot想想怎么写
xdotool key "Tab"
#使用 xdotool 执行按键 Tab 补全Copilot想写的
xdotool key "ctrl+s"
#使用 xdotool 执行按键 ctrl+s 保存
sleep 10
#休息 10 秒，上面的sleep值看你Copilot要想多久
xdotool key "Tab"
#使用 xdotool 执行按键 Tab 补全Copilot想写的
xdotool key "ctrl+s"
#使用 xdotool 执行按键 ctrl+s 保存
echo $id
#输出一下pid（没啥用）当然你Copilot想不出来也没关系
done
```

以上执行时最好啥也不动，因为只聚焦一次到指定pid的窗口。可以另外用 `while` 循环以上命令

```bash
while true
do
sh auto.sh
echo happy sleep
sleep 5
done
```

执行 auto.sh (栗子文件名) 然后执行这个 `while` 文件就能一直在窗口中自动写代码了。

当然你可以设定一些变量值，让 Copilot 写“真实”代码，不过一般写出来的会是一样的，我这里用的是随机，有的时候 Copilot 想不出来，不过没关系。

当然在执行自动化之前，你得把浏览器，及想要自动化的窗口准备好（这个准备也完全可以python自动化）但是我不会，也不太想学，大家可以自己拓展。以下是一些常用补充：

```bash
xdotool key ctrl+s BackSpace #点击ctrl+s，然后是BackSpace键
xdotool search --name chrome key ctrl+c #在名为chrome的窗口上点击ctrl+c
xdotool getactivewindow #获取当前激活的窗口
xdotool getactivewindow windowmove 10 10 #移动当前窗口位置
xdotool mousemove_relative 10 10 #鼠标相对移动
xdotool mousemove_relative --sync 10 10 #异步鼠标相对移动
xdotool mousemove x y click 1 #鼠标移到(x,y)左键 值1是左键 2滚轮中键 3是右键
xdotool getmouselocation #获取鼠标位置
 eval $(xdotool getmouselocation --shell) #获取鼠标位置（可以被引用
 echo $X,$Y
```

文章备份：https://b1og.learnonly.xyz/article/01VC3T5YZGWKFFLGLKERFLPZXDMSQ4TITA

# 注意：

有些东西测试好了再去生产环境执行！