---
title: Hello World
date: 2016-04-07 16:25:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - 随笔
tags:   # 文章标签，参数可省略
    - hexo
---
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).
<!--more-->
## 参考：
[ubuntu15.04安装hexo](http://www.linuxdiyf.com/linux/18320.html)
[Hexo 3.1.1 静态博客搭建指南](http://lovenight.github.io/2015/11/10/Hexo-3-1-1-静态博客搭建指南/)
[hexo不同电脑之间同步写博客](https://segmentfault.com/a/1190000007385345)
## Requirements

### nodejs
```
wget -qO- https://raw.githubusercontent.com/creationix/nvm/master/install.sh | sh
nvm install stable
```

### git
```
sudo apt-get install git
```

## install hexo
```
npm install -g hexo-cli
```

## Quick Start

### init
```bash
hexo init
npm install  #安装依赖包
npm install hexo-deployer-git --save
```
### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/deployment.html)
