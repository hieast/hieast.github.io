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
Sphinx格式的文档,需要make(先安装Sphinx包）
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

### 类属性：
- request_class = Request  
- response_class = Response  
- static_path = '/static'  
- secret_key = None （设定用于使用secure cookie）  
- session_cookie_name = 'session'  
- jinja_options （直接带入Jinja2环境的字典）

### 类方法
- def __init__(self, package_name)  
需要传入一个包名字，初始化了多个函数序列，添加静态文件处理，生成了jinja_env（Environment类）。
- def create_jinja_loader(self)  
默认返回一个当前工作目录\'templates'的FileSystemLoader()，pkg_resources有定义则返回PackageLoader(self.package_name)
- def update_template_context(self, context)  
将template_context_processors中所有函数返回的字典更新到context中。默认是栈顶的 'request', 'session' and 'g'
- def run(self, host='localhost', port=5000, **options)  
from werkzeug import run_simple and run_simple(host, port, self, **options)
- def test_client(self)  
Client(self, self.response_class, use_cookies=True)
- def open_resource(self, resource)  
返回一个资源（文件）对象
- def open_session(self, request)  
当secret_key被设定时，再根据session_cookie_name返回一个新的session
- def save_session(self, session, response)  
- def add_url_rule(self, rule, endpoint, **options)  
- def route(self, rule, **options)
- def errorhandler(self, code)
- def before_request(self, f)
- def after_request(self, f)
- def context_processor(self, f)  
以上多个函数都是注册装饰器，用于将函数添加到对应的字典/列表中。
- def match_request(self)  
返回_request_ctx_stack.top.url_adapter.match()
- def dispatch_request(self)  
尝试返回self.view_functions\[endpoint\](**values)
- def make_response(self, rv)  
根据视图函数的返回值rv的类型make response（response_class类可以直接返回）
- def preprocess_request(self)  
运行所有before_request的函数，如rv不为None则中止并返回
- def process_response(self, response)  
调用所有after_request_funcs处理response，最后返回response
- def wsgi_app(self, environ, start_response)  
主要业务逻辑，真正的WSGI application，包括请求上下文载入，预处理请求（返回None时dispatch_request），生成响应，后处理响应，最后返回response(environ, start_response)。
- def request_context(self, environ)
返回一个class _RequestContext(object)
- def test_request_context(self, *args, **kwargs)
Creates a WSGI environment from the given values
- def __call__(self, environ, start_response)
return self.wsgi_app(environ, start_response)


## flask.py 之context locals

- _request_ctx_stack = LocalStack()
- current_app = LocalProxy(lambda: _request_ctx_stack.top.app)
- request = LocalProxy(lambda: _request_ctx_stack.top.request)
- session = LocalProxy(lambda: _request_ctx_stack.top.session)
- g = LocalProxy(lambda: _request_ctx_stack.top.g)
