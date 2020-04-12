---
title: Flask如何处理一个请求
date: 2020-04-12 21:02:22
tags: flask
categories: flask
---

<center>Author：闫玉良</center>

客户端发起一个请求，`Flask` 都干了什么呢？`url` 如何与视图进行绑定的？

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.过程简述

1) 创建请求上下文

2) 创建应用上下文

3) 把上下文压入栈

4) 执行请求钩子 `before_first_request` 的相关操作

5) 执行请求钩子 `before_request` 的相关操作

6) 路由

7) 执行请求钩子 `after_request` 的相关操作

8) 执行请求钩子 `teardown_request` 的相关操作

9) 把上下文弹出栈

10) 返回响应结果

## 2.过程详述

### 2.1 wsgi 接口

总所周知，客户端每次发起请求，服务器都会调用框架实现的 `wsgi` 接口进行通讯。在 `Flask` 中，每个请求都会先调用 `Flask.__call__` 方法，而此方法又调用了 `Flask.wsgi_app` ，它便是 `Flask` 中的 `wsgi` 接口了。接下来我们结合源码进行说明。

> 小闫使用的版本为 `flask1.1.2`，以下所有源码都源自于此版本，并以 `wsgi_app` 方法展开描述。

```python
# flask/app.py

class Flask(_PackageBoundObject):

    ... 省略其他方法

    def wsgi_app(self, environ, start_response):
        """The actual WSGI application. 
        ... 此处省略一些说明
        :param environ: A WSGI environment. 环境字典，包含全部 HTTP 请求信息
        :param start_response: A callable accepting a status code,
            a list of headers, and an optional exception context to
            start the response. 回调函数
        """
        # 创建请求上下文（过程中创建了应用上下文）
        ctx = self.request_context(environ)
        error = None
        try:
            try:
                # 上下文入栈
                ctx.push()
                # 路由
                response = self.full_dispatch_request()
            except Exception as e:
                error = e
                response = self.handle_exception(e)
            except:  # noqa: B001
                error = sys.exc_info()[1]
                raise
            # 返回响应结果
            return response(environ, start_response)
        finally:
            if self.should_ignore_error(error):
                error = None
            # 上下文出栈
            ctx.auto_pop(error)

    def __call__(self, environ, start_response):
        """The WSGI server calls the Flask application object as the
        WSGI application. This calls :meth:`wsgi_app` which can be
        wrapped to applying middleware."""
        return self.wsgi_app(environ, start_response)
```

### 2.2 创建上下文

结合上面提供的 `wsgi_app` 源码，我们可以看到先执行了如下代码：

```python
ctx = self.request_context(environ)
```

此处便是创建上下文操作。框架会先去创建请求上下文，并去判断是否有应用上下文，以及应用上下文与当前应用是否一致，然后决定是否去创建一个应用上下文。如下便是依次进行调用的方法：

```python
# flask/app.py

class Flask(_PackageBoundObject):
    def request_context(self, environ):
        return RequestContext(self, environ)
        
# flask/ctx.py

class RequestContext(object):
    def push(self):
        top = _request_ctx_stack.top
        if top is not None and top.preserved:
            top.pop(top._preserved_exc)
        
        # 从栈中弹出一个应用上下文
        app_ctx = _app_ctx_stack.top
        # 判断应用上下文是否存在并与当前应用一致
        if app_ctx is None or app_ctx.app != self.app:
            # 创建应用上下文
            app_ctx = self.app.app_context()
            # 入栈
            app_ctx.push()
            self._implicit_app_ctx_stack.append(app_ctx)
        else:
            self._implicit_app_ctx_stack.append(None)

        if hasattr(sys, "exc_clear"):
            sys.exc_clear()

        _request_ctx_stack.push(self)

        if self.session is None:
            session_interface = self.app.session_interface
            self.session = session_interface.open_session(self.app, self.request)

            if self.session is None:
                self.session = session_interface.make_null_session(self.app)

        if self.url_adapter is not None:
            self.match_request()
```


### 2.3 请求钩子

如果在代码中定义了四种请求钩子，那么它们会按照如下顺序执行。

```mermaid
graph LR
before_first_request --> before_request
before_request --> after_request
after_request --> teardown_request
```

1.`before_first_request`:在处理第一个请求前执行

2.`before_request`:在每次请求前执行,在该装饰函数中,一旦return,视图函数不再执行

3.`after_request`:如果没有抛出错误，在每次请求后执行

    a.接受一个参数：视图函数作出的响应
    b.在此函数中可以对响应值,在返回之前做最后一步处理,再返回
    
4.teardown_request：在每次请求后执行

    a.接受一个参数:用来接收错误信息

### 2.4 路由

在 `wsgi_app` 方法中如下代码便会进行请求分发：

```python
response = self.full_dispatch_request()
```

下面将所涉及到的方法源码依次列出：


```python
# flask/app.py

class Flask(_PackageBoundObject):
    def full_dispatch_request(self):
        """Dispatches the request and on top of that performs request
        pre and postprocessing as well as HTTP exception catching and
        error handling.
        """
        # 首次处理请求前的操作，通过 @before_first_request 定义，也就是我们的请求钩子
        self.try_trigger_before_first_request_functions()
        try:
            request_started.send(self)
            # 每次处理请求前进行的操作, 通过 @before_request 来定义
            rv = self.preprocess_request()
            if rv is None:
                # 调用 dispatch_request 函数匹配 url，执行请求调度
                rv = self.dispatch_request()
        except Exception as e:
            rv = self.handle_user_exception(e)
        # 调用 finalize_request 方法将视图函数的返回值转换成一个真正的响应对象
        return self.finalize_request(rv)
        
    def dispatch_request(self):
        """Does the request dispatching.  Matches the URL and returns the
        return value of the view or error handler.  This does not have to
        be a response object.  In order to convert the return value to a
        proper response object, call :func:`make_response`.
        """
        req = _request_ctx_stack.top.request
        if req.routing_exception is not None:
            self.raise_routing_exception(req)
        rule = req.url_rule
        # if we provide automatic options for this URL and the
        # request came with the OPTIONS method, reply automatically
        if (
            getattr(rule, "provide_automatic_options", False)
            and req.method == "OPTIONS"
        ):
            return self.make_default_options_response()
        # otherwise dispatch to the handler for that endpoint
        return self.view_functions[rule.endpoint](**req.view_args)
        
    def finalize_request(self, rv, from_error_handler=False):
        """Given the return value from a view function this finalizes
        the request by converting it into a response and invoking the
        postprocessing functions.  This is invoked for both normal
        request dispatching as well as error handlers.

        Because this means that it might be called as a result of a
        failure a special safe mode is available which can be enabled
        with the `from_error_handler` flag.  If enabled, failures in
        response processing will be logged and otherwise ignored.

        :internal:
        """
        response = self.make_response(rv)
        try:
            # 每次正常处理请求后进行的操作, 通过 @after_request 来定义
            response = self.process_response(response)
            request_finished.send(self, response=response)
        except Exception:
            if not from_error_handler:
                raise
            self.logger.exception(
                "Request finalizing failed with an error while handling an error"
            )
        return response
```

### 2.5 路由表的创建

下面是一个最简单的视图，其作用便是在访问根目录时，返回 `Hello World`。

```python
@app.route('/')
def hello_world():
    return 'Hello World!'
```

你有没有想过视图函数与 `url` 是如何绑定的呢？它是通过方法 `add_url_rule` 创建的路由规则，下面我们来看一下源码：

1) 先查看一下 `@app.route` 装饰器干了哪些工作：

```python
# flask/app.py

class Flask(_PackageBoundObject):
    def route(self, rule, **options):
        def decorator(f):
            # 获取 endpoint，可以看作为每个 view_func 的 ID
            endpoint = options.pop("endpoint", None)
            # 调用 add_url_rule 方法添加路由信息
            self.add_url_rule(rule, endpoint, f, **options)
            return f

        return decorator
```

2) 然后就来看看 `add_url_rule` 为何方神圣。

```python
# flask/app.py

class Flask(_PackageBoundObject):

    # 定义view_functions
    self.view_functions = {}
    # 定义url_map
    self.url_map = Map()

    @setupmethod
    def add_url_rule(
        self,
        rule,
        endpoint=None,
        view_func=None,
        provide_automatic_options=None,
        **options
    ):
        if endpoint is None:
            endpoint = _endpoint_from_view_func(view_func)
        options["endpoint"] = endpoint
        methods = options.pop("methods", None)

        # if the methods are not given and the view_func object knows its
        # methods we can use that instead.  If neither exists, we go with
        # a tuple of only ``GET`` as default.
        if methods is None:
            methods = getattr(view_func, "methods", None) or ("GET",)
        if isinstance(methods, string_types):
            raise TypeError(
                "Allowed methods have to be iterables of strings, "
                'for example: @app.route(..., methods=["POST"])'
            )
        methods = set(item.upper() for item in methods)

        # Methods that should always be added
        required_methods = set(getattr(view_func, "required_methods", ()))

        # starting with Flask 0.8 the view_func object can disable and
        # force-enable the automatic options handling.
        if provide_automatic_options is None:
            provide_automatic_options = getattr(
                view_func, "provide_automatic_options", None
            )

        if provide_automatic_options is None:
            if "OPTIONS" not in methods:
                provide_automatic_options = True
                required_methods.add("OPTIONS")
            else:
                provide_automatic_options = False

        # Add the required methods now.
        methods |= required_methods
        
        # 创建 rule
        rule = self.url_rule_class(rule, methods=methods, **options)
        rule.provide_automatic_options = provide_automatic_options
        
        # 把 rule 添加到 url_map
        self.url_map.add(rule)
        if view_func is not None:
            old_func = self.view_functions.get(endpoint)
            if old_func is not None and old_func != view_func:
                raise AssertionError(
                    "View function mapping is overwriting an "
                    "existing endpoint function: %s" % endpoint
                )
             # 把 view_func 添加到 view_functions 字典
            self.view_functions[endpoint] = view_func
```


***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***