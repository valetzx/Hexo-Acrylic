---
title: 在Replit部署每日新闻项目
tags:
  - 教程
  - Replit
  - 白嫖
top_img: 'https://bing.icodeq.com/?15'
cover: 'https://bing.icodeq.com/?16'
abbrlink: '7e53'
date: 2022-07-12 14:28:24
---

# 前言：

相信大家都有看到过，群里，公众号中，发过的每日新闻图片吧，作为一个爬虫项目的金典例子，这里给大家带来一个新的仓库，可以合并这些内容的项目：

[zkeq/news: 前后端均基于 vercel 的轻量级每日早报项目，支持一键部署，支持部署至服务器。后端由 FastAPI + BeautifulSoup 实现。 (github.com)](https://github.com/zkeq/news)

由于我的 `vercel` 请求太多了，这个项目需要用到 `severless` 资源，这里我们将这个项目部署至 `Replit` 来减少 `vercel` 负担(

# 准备：

1. 注册一个Replit账号
2. 最好再注册一个Team

# 开始：

打开 `replit` 新建一个 `python` 仓库

在 shell 中输入如下命令：

```bash
git clone https://github.com/valetzx/newsonreplit && mv -b newsonreplit/* ./ && mv -b newsonreplit/.[^.]* ./ && rm -rf *~ && rm -rf newsonreplit
```

等待 `Loading Nix environment...` 完成后，点绿色 `Run` 运行

是不是肥肠的简单捏！

# 进阶：

很明显，这个前端只能从部署的域名访问吗？

并不是，我们可以通过修改 `index.js` 达到前后端分离的效果：

在你的服务器上下载 https://github.com/zkeq/news 源码解压

修改 47行到67行的 `api` 请求，以及241行到257行的 `api` 请求：

```javascript
function first_xhr () {
    const now_time = new Date().getHours() +"hrs" + new Date().getMinutes() + "min";
    try{
        const xhr_zhihu = new XMLHttpRequest();
        xhr_zhihu.open('GET', '/api?origin=zhihu&_vercel_no_cache=1' + '&cache=' + now_time);
        xhr_zhihu.onload = zhihu_first_load;
        xhr_zhihu.onerror = handleError_zhihu;
        xhr_zhihu.send();
    }catch(e){
        handleError_zhihu(e);
    }
    try{
        const xhr_163 = new XMLHttpRequest();
        xhr_163.open('GET', '/api?origin=163&_vercel_no_cache=1'+ '&cache=' + now_time);
        xhr_163.onload = _163_init_load;
        xhr_163.onerror = handleError_163;
        xhr_163.send();
    }catch(e){
        handleError_163(e);
    }
}
```

```javascript
function get_day_news(index, origin){
      try{
        NProgress.start();
        const xhr = new XMLHttpRequest();
        if (origin === 'zhihu') {
            cache =  localStorage.getItem('zhihu_cache');
        }else{
            cache =  localStorage.getItem('163_cache');
        }
        xhr.open('GET', `/api?index=${index}&cache=${cache}&origin=${origin}`);
        xhr.onload = days_load;
        xhr.onerror = handleError;
        xhr.send();
      } catch(error){
            handleError(error);
        }
}
```

将所有的 `/api` 前，添加上你的replit部署完成的域名，例：

```javascript
xhr_zhihu.open('GET', 'https://news.pighog.repl.co/api?origin=zhihu&_vercel_no_cache=1' + '&cache=' + now_time);
```

修改完成后，在你的服务器中配置访问 `index.html` 就可以达到前后端分离的效果拉！
