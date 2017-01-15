---
title: Git基础
date: 2016-04-08 16:26:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - Linux
tags:   # 文章标签，参数可省略
    - Git   # 个数不限，单个可直接跟在tags后面
---
## 参考
[廖雪峰的官方网站Git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
[git clone，push，pull，fetch命令详解。（转载）](http://blog.csdn.net/u012575819/article/details/50553501)
[学习 Git 操作之二：基础](https://zhangnew.com/2015-06-24-git-base.html)
[git操作命令](http://www.tuicool.com/articles/IRNZzur)
[git pull更新错误解决办法](http://blog.chinaunix.net/uid-10415985-id-4142896.html)
读完本章你就能上手使用 Git 了。
<!--more-->
## 本地管理
### 创建版本库
```bash
mkdir blog
cd blog
git init

git add readme.txt
git add .   #添加所有文件
git add --all  #当有文件被删除时，用此命令确认删除
               #用于将这些工作区内已删除的文件从版本库中删除

git commit -m "write a readme file"
git commit -a -m ""  #不提交新创建的，但把修改的和删除的都提交
git commit --help  #查看帮助
```

### 版本回退
```bash
git log    #查看提交历史
或者：git log --pretty=oneline
git log --graph  #查看分支合并图

git reset --hard [version number,eg:ea345]

git reflog  @查看命令历史
```

### 管理修改
```bash
git status   #查看状态
未add:
git checkout -- [file name,eg:readme.txt]  #丢弃工作区的修改
已add未commit:
git reset HEAD [file name,eg:readme.txt]  #撤销暂存区的修改
git checkout -- [file name,eg:readme.txt]  #丢弃工作区的修改
已commit:
版本回退：
git reset --hard HEAD/[version number,eg:ea345]
```

### 删除文件
```bash
git rm test.txt  #从暂存区库中删除文件

git checkout -- test.txt  #从版本库恢复工作区内已删除的文件
```

## 远程仓库
### 添加远程库
```bash
#用户主目录没有.ssh目录
ssh-keygen -t rsa -C "hujun1413.bupt.edu.cn"
#在github的ADD SSH KEY中粘贴.ssh/id_rsa.pub的内容
git remote add origin git@github.com:hujun1413/hujun1413.github.io.git

git push -u origin hexo
git push origin hexo
```

### 从远程库克隆
```bash
git clone git://github.com:xx/xxx.git  #不支持push
git clone git@github.com:hujun1413/hujun1413.github.io.git 
git clone -b hexo https://github.com/hujun1413/hujun1413.github.io.git --depth 1 blog
#只克隆仓库中的hexo分支,克隆深度为1，存在当前目录的blog文件夹下

#解决方案：将远程主机的url修改成SSH/HTTP协议(每次都要输密码)
git remote set-url origin git@github.com:hujun1413/hujun1413.github.io.git
git remote set-url origin https://github.com/hujun1413/hujun1413.github.io.git
```

### 创建/合并/删除分支
```bash
git branch hexo    #创建分支
git checkout hexo    #切换分支
git checkout -b hexo = git branch hexo + git checkout hexo   #创建并切换至hexo分支,默认从当前分支copy
git checkout -b master origin/master #从远程的origin/master创建分支

git branch      #查看本地分支
git branch -a   #查看所有分支
git branch -r   #查看远程分支
git branch -d/-D hexo  #删除hexo分支，-D强制删除
git branch -r -d origin/hongchangfirst #删除在本地的远程分支

git merge hexo  #将当前分支与hexo 分支进行合并
git merge --no-ff -m "merge with no-ff" dev   #--no-ff参数，表示禁用Fast forward：

git rebase origin/master   #合并，但把另一个分支的最新commit改变为当前分支的基础。
                           #让分支历史看起来像没有经过任何合并
```

### 解决冲突
```bash
git merge hexo
git add [file_name]
git commit -m "confict fixed"
```

### 对比差异(diff/log)
```bash
git diff  工作区与暂存区的差异
git diff --cached / git diff --staged  暂存区(staged)和版本库的差异
git diff HEAD  / git diff [当前branch]  工作区和版本库的差异
git diff sha1 sha2
git diff tmp  当前分支与tmp分支的差异

git log -p -2
#常用 -p 选项展开显示每次提交的内容差异，用 -2 则仅显示最近的两次更新
git log -p hexo origin/hexo -1

```

### fetch
```bash
git fetch <远程主机名> <分支名>  #取回origin主机的master分支。
#所取回的更新，在本地主机上要用"远程主机名/分支名"的形式读取。比如origin主机的master，就要用origin/master读取。

git fetch origin hexo
git checkout -b tmp origin/hexo  #在origin/master的基础上，
上面两句=git fetch origin hexo:tmp

git checkout hexo
git merge tmp

#远程强制覆盖本地
git fetch origin hexo
git reset --hard origin/hexo
```

### pull
```bash
git pull <远程主机名> <远程分支名>:<本地分支名>

合并需要采用rebase模式，可以使用--rebase选项。
git pull --rebase <远程主机名> <远程分支名>:<本地分支名>
```

### push
```bash
git push <远程主机名> <本地分支名>:<远程分支名>
git push origin master
把本地master分支的最新修改推送至GitHub

git push不会推送标签（tag），除非使用--tags选项。
git push origin --tags

如果远程主机的版本比本地版本更新，推送时Git会报错，要求先在本地做git pull合并差异，然后再推送到远程主机。这时，如果你一定要推送，可以使用--force选项。
git push --force origin   #远程主机上更新的版本被覆盖

git push origin :article    #删除远程的article分支
git push origin article     #当前分支推送至远程，若远程不存在则创建之
```

### remote
```bash
git remote -v 查看远程主机的网址
git clone -o jQuery https://github.com/jquery/jquery.git
#克隆的时候，指定远程主机叫做jQuery。

git remote show <主机名>  #查看该主机的详细信息。

git remote add <主机名> <网址>  #添加远程主机。

git remote rm <主机名>  #删除远程主机。

git remote rename <原主机名> <新主机名>  #用于远程主机的改名。

git remote set-url origin git@github.com:hujun1413/hujun1413.github.io.git
#更改主机的url
```

### 删除远程的提交记录
```bash
git reset --hard [version number,eg:ea345]  #回退到某个版本
git push --force    #强行推送至远程，远程的提交记录则只到该次提交，之后的提交均被删除

#或直接删除远程分支后重新提交建立新的分支
git push origin :article
git push origin article
```

### gitignore文件
```
在本地项目目录下删除缓存（否则想忽略但已经在版本库中记录的文件将不会被删除）
git rm -r --cached .

新建.gitignore文件
再次add所有文件
git add .

再commmit即可
```

### 其他
```bash
git clean -df    #清理git的本地缓存
git rm --cached bundle/xxxx  删除子模组
git gc  #压缩历史信息来节约磁盘和内容空间
git branch --set-upstream master origin/next  #指定master分支追踪origin/next分支
```

