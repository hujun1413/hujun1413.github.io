---
title:  Python模块
date: 2016-12-19 20:35:26 # 文章生成时间，一般不改
categories:   # 文章分类目录，参数可省略
    - 编程语言
    - Python
tags:   # 文章标签，参数可省略
---
### 参考资料
[欢迎使用Flask](http://docs.jinkan.org/docs/flask/index.html)
[快速入门](http://www.pythondoc.com/flask/quickstart.html#)

### 一个最小的应用
```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run()
```

### 构建URL
```
>>> from flask import Flask, url_for
>>> app = Flask(__name__)
>>> @app.route('/')
... def index(): pass
...
>>> @app.route('/login')
... def login(): pass
...
>>> @app.route('/user/<username>')
... def profile(username): pass
...
>>> with app.test_request_context():
...  print url_for('index')
...  print url_for('login')
...  print url_for('login', next='/')
...  print url_for('profile', username='John Doe')
...
/
/login
/login?next=/
/user/John%20Doe
```
test_request_context()告诉 Flask 表现得像是在处理一个请求，即使我们正在通过 Python 的 shell 交互。

### HTTP方法
* GET: 浏览器通知服务器只获取页面上的信息并且发送回来。(获取数据)
* HEAD：类似GET，只是服务器接收但不传递实际内容。
* POST：浏览器在URL上提交一些信息。这是HTML表单通常发送数据到服务器的方法。服务器保证数据只存储一次。（提交数据）
* PUT：类似POST，但服务器可能触发了多次存储过程，多次覆盖掉旧值。
* DELETE：移除给定位置的信息
* OPTIONS：给客户端提供一个快速的途径来指出这个RL支持哪些HTTP方法。

### 静态文件
CSS
JavaScript





















