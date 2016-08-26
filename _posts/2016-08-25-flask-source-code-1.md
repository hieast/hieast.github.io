--- 
layout: post 
title: Flask 0.1版本源代码分析（1） 
category: Tech 
tags: flask python  
---

# 前言
flask的定位是微框架，其依赖关系比较简单，WSGI工具箱采用Werkzeug，模板引擎采用Jinja2。
一方面，把从其他包中导入的部分弄清楚，有助于理解其源码；另一方面，Request和Response等主体是继承的，了解其超类可以帮助我们更好地使用其子类。

---

#  结构分层深入阅读

## flask-0.1 整体结构

- artwork  
有一个svg格式的矢量logo
- docs  
Sphinx格式的文档,需要make
- examples  
两个使用Flask和sqlite3写的小网站
- tests
  - static  
  用于测试路由的静态文件
  - templates
  用于测试render_template的模板文件
  - flask_tests.py  
  单元测试主程序
- website
一个简单的Flask介绍页面，页面的html和css写法并不值得学习
- flask.py  
主程序，以下重点。
- LICENSE  
[BSD LICENSES](https://en.wikipedia.org/wiki/BSD_licenses) from Berkeley Software Distribution
- Makefile
执行规则是all先清理pyc/pyo/~结尾的文件（python编译后的二进制文件），然后执行tests/flask_tests.py。细节：[PHONY](http://www.cnblogs.com/hnrainll/archive/2011/04/12/2013377.html)
- README 
- setup.py  
用于安装的文件，使用了setuptools 中的setup，[详细文档](https://setuptools.readthedocs.io/en/latest/setuptools.html#basic-use).[依赖部分](https://setuptools.readthedocs.io/en/latest/setuptools.html#declaring-dependencies)。

## flask.py 之 class & function
- class Request(RequestBase)  
继承了werkzeug的Request，构造函数中添加了令两个（endpoint和view_argsNone）初始化为None的变量。
- class Response(ResponseBase)  
继承了werkzeug的Response，在类中命名空间中把默认类型改为了'text/html'。
- class \_RequestGlobals(object)  
该类被初始化为一个空的新类。该类是请求的全局变量。
- class \_RequestContext(object)
该类实现了一个init函数，在实例中保存了app, environ的一系列变量，特别的self.g = \_RequestGlobals()。  
该类还实现了enter和exit用以支持with语句。  
该类在初始化的时候并没有加入上下文，当使用with时，\_request\_ctx\_stack才在栈顶添加该实例，运行结束后出栈。
- def url_for(endpoint, **values)
返回栈顶对应endpoint和values的URL。用到了 \_request\_ctx\_stack。
- def flash(message)  
把message加入session['\_flashes']数组，无返回值。
- def get\_flashed\_messages()
尝试返回\_request\_ctx\_stack.top.flashes，如果为空，用session.pop('\_flashes', [])更新并返回。
- def render\_template(template\_name, **context)
利用jinja2使用context填充template_name模板，并将其返回。
- def render\_template\_string(source, **context)
用一个string（source）来接受填充。
- def \_default\_template\_ctx\_processor()
返回一个字典，包括栈顶的 'request', 'session' and 'g'。
- def \_get\_package\_path(name)  
"""Returns the path to a package or cwd if that cannot be found."""
- class Flask(object)  
这个是重点。

## flask.py 之 class Flask(object) 
类属性：
request_class = Request  
response_class = Response  
static_path = '/static'  
secret_key = None （设定用于使用secure cookie）


## flask.py 之context locals

_request_ctx_stack = LocalStack()
current_app = LocalProxy(lambda: _request_ctx_stack.top.app)
request = LocalProxy(lambda: _request_ctx_stack.top.request)
session = LocalProxy(lambda: _request_ctx_stack.top.session)
g = LocalProxy(lambda: _request_ctx_stack.top.g)
