---
title: 使用Graph获取微软永久客户端秘钥
tags:
  - 教程
  - 白嫖
abbrlink: 34c
date: 2022-04-30 23:15:22
top_img: 'https://bing.icodeq.com/?87'
cover: 'https://bing.icodeq.com/?86'
---

# 前言：

现在微软客户端秘钥client_secret有效期24个月，但是还是可以获得永久的secret的。方便一次设置 永久（理论）续订！

# 开始：

1. 打开 https://developer.microsoft.com/en-us/graph/graph-explorer
2. 点最左边蓝色 `Sign in to Graph Explorer` 登录
3. 右边 框框 `Modify permissions (Preview)` 将所有权限`√`同意
4. 左侧栏，`Applications (8)` ，下面找到绿色的`POST` ，有两个，选择，`create a new application` ，双击就会在右侧呈现（创建新应用程序）
5. 打开 [应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 选择想获取永久秘钥的应用程序，复制`对象ID` 不是client_id！不是 应用程序(客户端)ID！是 对象ID！
6. 再回到Graph Explorer，将POST的URL地址替换为 `https://graph.microsoft.com/v1.0/applications/你的对象ID/addPassword` 
7. 将 `Request body` 中内容删除，替换为：

```
{
	"passwordCredential": {
		"displayName": "你想要给密钥添加的描述",
		"endDateTime": "9999-12-31T00:00:00Z"
	}
}
```
8. 最后，点击 `Run query`，得到的结果里面的`secretText`的值就是“永久密钥”啦！

![image-20220430213929444](https://img.pighog.repl.co/2022/04/image-20220430213929444.png)

![image-20220430213936478](https://img.pighog.repl.co/2022/04/image-20220430213936478.png)

# 注意！

请注意！`对象ID` 应该在 `应用注册` 选项中的 `概述` 面板中获取，而不是 `企业应用程序` 面板的 对象ID 这俩东西不一样！
