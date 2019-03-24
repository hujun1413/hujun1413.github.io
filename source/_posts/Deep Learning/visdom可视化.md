---
title: visdom可视化
date: 2018-05-28 16:25:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - Deep Learning
tags:   # 文章标签，参数可省略
    - Pytorch
---
### visdom可视化
#### 安装
```
pip install visdom

python -m visdom.server #启动visdom服务
```
<!--more-->

#### API
[visdom官方github](https://github.com/facebookresearch/visdom)

[PyTorch 可视化工具 Visdom 介绍](http://www.pytorchtutorial.com/pytorch-visdom/#visimage)

[使用 Visdom 在 PyTorch 中进行可视化](http://www.pytorchtutorial.com/using-visdom-for-visualization-in-pytorch/#plotline)

[在PyTorch中使用Visdom可视化工具](https://ptorch.com/news/77.html)

#### visdom画线图
```python
losses_his = [[1],[2],[4],[9]]
x = [[1],[2],[3],[4]]
vis.vis.line(Y=np.array(losses_his), X=np.array(x), win='hujun_vis1', 
opts=dict(
    title='hujun_vis1', 
    #legend=['SGD', 'Momentum', 'RMSprop', 'Adam'],
    ytickmin=0,
    ytickmax=12,
    ),
)
    
vis = visdom.Visdom(env=u'test1') #visdom作图
vis.line(Y=torch.from_numpy(np.array(losses_his).T), win='losses', 
opts=dict(
    title='y=losses', 
    legend=['SGD', 'Momentum', 'RMSprop', 'Adam'],
    ytickmin=0,
    ytickmax=0.2,
    ),
)

losses_his = [[1],[2],[4],[9] #一条线，N*M表示M条长度为N的线
vis.vis.line(Y=t.from_numpy(np.array(losses_his)), win='hujun_vis1', 
opts=dict(
    title='hujun_vis1', 
    #legend=['SGD', 'Momentum', 'RMSprop', 'Adam'],
    ytickmin=0,
    ytickmax=12,
    ),
)#在hujun_vis1这个pane上画线图，title为hujun_vis1

losses_his = [[1,0],[2,1],[4,3],[9,8],[10,9]] #两条线
vis.vis.line(Y=t.from_numpy(np.array(losses_his)), win='hujun_vis2', 
opts=dict(
    title='hujun_vis1', 
    #legend=['SGD', 'Momentum', 'RMSprop', 'Adam'],
    ytickmin=0,
    ytickmax=14,
    ),
)#在hujun_vis2这个pane上画线图，title为hujun_vis1
#若继续在hujun_vis1这个pane上画图，将会覆盖掉之前的。换一个pane就可以画出多个图了。

# line updates，最好更新一开始就有的线，比如一开始画了5根线，更新的就是name就最好是1,2,3,4,5
win = viz.line(
    X=np.column_stack((np.arange(0, 10), np.arange(0, 10))),
    Y=np.column_stack((np.linspace(5, 10, 10),
                       np.linspace(5, 10, 10) + 5)),
    opts=dict(showlegend=True)
)
viz.line(
    X=np.column_stack((np.arange(10, 20), np.arange(10, 20))),
    Y=np.column_stack((np.linspace(5, 10, 10),
                       np.linspace(5, 10, 10) + 5)),
    win=win,
    update='append'
)
viz.line(
    X=np.arange(21, 30),
    Y=np.arange(1, 10),
    win=win,
    name='2',
    update='append'
)

viz.line(
    X=np.arange(1, 10),
    Y=np.arange(11, 20),
    win=win,
    name='2',
    update='append'
)

viz.line(
    X=np.arange(1, 10),
    Y=np.arange(12, 21),
    win=win,
    #name='3', #默认为1，即给第一条线append数据
    update='append'
)

#viz.line(X=np.arange(5, 10),Y=np.arange(6, 11), win=win, name='5', update='remove', opts=dict(legend=['2'], showlegend=True))
viz.line(X=np.arange(5, 10),Y=np.arange(6, 11), win=win, name='3', update='append', opts=dict(showlegend=True)) #当name不存在时，新增一条线
```

#### 另一台机器远程可视化
```python
python -m visdom.server #远程服务器visdom作图

ssh -L 18097:127.0.0.1:8097 cutcat@192.168.1.106

ssh -L 18097:127.0.0.1:8097 cscg@166.111.71.121
#通过端口映射，将远程机器的ip端口映射到本地的另一个端口
#在本地机器的浏览器输入127.0.0.1:18097，即可查看在远程服务器上8097端口的图
```

#### 指定端口
```python
vis = visdom.Visdom(port=2333,env='example')  #port缺省值为8097，env缺省值为'main'
vis.text('Hello, world!')
vis.image(np.ones((3, 10, 10)))


python -m visdom.server -p 2333
# -p参数指定端口，缺省值为8097，这里，我们指定2333端口
```

#### 保存env中的图
点击localhost:8097网页上Environment右边的文件夹，点击Save or fork 下面的那个save按钮，即可将该env中的所有图保存为环境名.json文件，文件存在~/.visdom目录下。关闭visdom服务后，下次启动时会读取jsom文件，重新可视化该env中的图。也可以在网页上的图片上选择保存为png或者文本到本地，这样就算删除了该env，也留下了之前跑的结果。

若在生成了env中的图后未点击保存，关闭visdom服务下次再启动时，将会丢失上次运行但未保存的所有图。


如果想要删除，就点击localhost:8097网页上Environment右边的文件夹，点击Delete environment selected 下面的那个Delete按钮，即可将环境名.json文件从~/.visdom目录下删除。

如果上次跑的图没有保存，也没有关掉visdom服务，则网页上仍会维持着上次程序跑出来的图。但如果这时又要重新在这个env中画图（如：启动一个和上次相同port和env的程序来可视化），则会覆盖掉之前跑出的图。所以在多次执行一个程序前，记得保存上次执行跑出来的图到本地，做好记录。也可以将view进行fork，或者将整个env进行fork

Fork: 有过您输入了一个新的env 名字，saving会建立一个心的env – 有效的forking之前的状态。（注：这个fork等价于github的fork，跟复制的意思差不多