---
title: 使用脚本定时备份SSH中任意文件至SSH或任意网盘
date: 2022-04-04 08:20:22
tags: [教程,网盘,OneDrive,SSH]
top_img: 
cover: 
---

# 前言：

跳过前言直准备接开始~第三步为设置定时备份~设置远程ip与密码时应删除` `` `符号与空格。格式参照：

```bash
		expect -c "
		spawn scp $bakdescdir/$orowner/$bakdata root@172.0.0.1 :$remotePath  
		expect {
		 \"*password\" {set timeout 500; send \"changethispasswd\r\";}
		 \"yes/no\" {send \"yes\r\"; exp_continue;}
		 }
		expect eof"  
```

# 开始：

准备好要备份的机器Linux等

#### 1. 在需要备份的机器中执行  `yum install -y expect` 安装expect插件（可选 `yum install -y coscmd` 使用cos对象存储）
#### 2. 复制备份脚本 new.sh 至需要备份的机器 /root 
#### 3. 设置cron定时备份：执行 `crontab -e` 在弹出的界面中按 `i` 后输入 `0 0 * * * /root/new.sh` 后按键盘 `esc` 键 输入 `:wq` 保存
#### 4. 执行 `service crond restart` 重启cron定时服务
#### 5. sh new.sh #测试 内容：

```bash
		#!/bin/sh
		dateTime=`date +%Y_%m_%d_%H_%M_%S`       #获取当前系统时间
		days=7       #删除7天前的备份数据
		orowner=root    # 备份到此用户下
		bakdescdir=/root/backup       #备份文件到该路径
		bakdata=$orowner"_"$dateTime.tar.gz     #备份文件名
		baklog=$orowner"_"$dateTime.log       #备份日志
		baksrcdir=/var/lib/mysql    	#需要备份的文件
		remotePath=/root/backup     #远程服务器的路径

		mkdir $bakdescdir			#新建backup文件夹
		cd $bakdescdir    	    #进入备份目录
		mkdir -p $orowner    #按需要备份的用户创建目录     
		cd $orowner     		  #进入目录
		echo "backup start at $dateTime" > $baklog
		echo "--------------------------" >>$baklog
		tar -zcvf $bakdata $baksrcdir $baklog       #压缩备份文件和日志文件

		find $bakdescdir/$orowner -type f -name "*.log" -exec rm {} \;     
		#删除日志文件
		find $bakdescdir/$orowner -type f -name "*.tar.gz" -mtime +$days -exec   rm -rf {} \;
		#删除 7 天前的备份
		
		#设置远程服务器ssh上传 使用expect插件自动输入密码
		expect -c "
		spawn scp $bakdescdir/$orowner/$bakdata root@ `远程服务器ip` :$remotePath  
		expect {
		 \"*password\" {set timeout 500; send \" `远程服务器密码` \r\";}
		 \"yes/no\" {send \"yes\r\"; exp_continue;}
		 }
		expect eof"  
		
		#设置上传到腾讯COS对象存储
		coscmd config -a COS AccessId -s COS SecretKey -b 桶名称 -r 桶地区 -m 10
		coscmd upload -r -s $bakdescdir/$orowner/$bakdata
```

#### 6. 备份数据库的方法很多，这里用最笨的，直接备份文件。如果你没有远程服务器，可以使用微软的 Power Automate 自动将 SSH 中的文件同步至Onedrive
#### 7. 如果输入crontab -e报错请百度解决方法，一般是 环境有问题，可以尝试安装 `yum install -y vixie-cron` 或 `yum install cronie` 或百度！

#### 8.文章备份：https://b1og.learnonly.xyz/article/01VC3T5YZEW3R5W3EEDJHIRZZ4MWAWIQV5
