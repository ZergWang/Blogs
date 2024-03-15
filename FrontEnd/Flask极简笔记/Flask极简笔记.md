# 简介
Flask是Python下的轻量级Web应用框架。使用Flask可实现一些简单的网站服务。

举个例子，使用如下代码，即可实现一个运行一个简单的网站服务。在本机浏览器输入网址“127.0.0.1:5000/123/”可看到页面中显示的“Hello World!”。

```py
from flask import Flask

app = Flask(__name__)

@app.route('/123/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run()
```

# 