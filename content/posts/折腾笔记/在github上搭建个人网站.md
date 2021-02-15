---
title: 在github上搭建个人网站
date: 2021-02-15
categories: [折腾笔记]
tags: [github]
---

## 使用github免费图床

github可以开一个仓库专门用于存放图片，然后图片就会产生一个链接，这样我们就可以使用这个图片了，这就是 **github图床**，并且不要钱呦！

我们这里使用的是： **picgo+github** 来实现命令行图片上传

1、安装picgo：https://github.com/PicGo/PicGo-Core 

```bash
$ npm install picgo -g
$ vim $HOME/.picgo/config.json #配置文件路径
```

2、配置`config.json`

```bash
 {
  picBed: {
   	uploader: github,
   	current: github,
   	github: {
  	  repo: biningo/cdn, #github仓库地址 <username>/<repo>
  	  branch: master, #分支
  	  token: dsadsadsada3231321321321, #访问token 需要github后台生成
  	  path: img/, #仓库下的路径
   }
  },
  picgoPlugins: {}
}

```

3、pic上传命令：

```bash
picgo u ~$PATH/aaaa.png #上传之后就会返回URL链接地址
```

​    

## hugo静态博客

我采用的是 `hugo+github page+github action` 来实现自动化部署，我只需要在本地写完博客执行一下`push`发布上去即可实现部署，非常方便，具体可以参照我的仓库的`action`：https://github.com/biningo/biningo.github.io

因为push前需要先和github仓库rebase，命令比较多所以我写成一个脚本每次执行这个脚本即可，博客发布脚本：

```bash
#因为 hugo生成页面的actions会生成public页面，所以和本地不一样了，需要先rebase，然后再push才能push成功，每次push就会触发action生成页面
git fetch origin master
git rebase origin/master
git push
```

