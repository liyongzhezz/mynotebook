Django在做后台系统过程中，通常每个页面都需要在用户登录之后才能访问。

<br>



## 方式一、login_required装饰器

我们通常都会为view函数添加`@login_required`装饰器，这个装饰器的主要作用就是在用户访问这个方法时，检查用户是否已经成功登陆，如果没有则重定向到登陆页面

登陆页面地址是通过`settings.LOGIN_URL`来获取的，默认为`/accounts/login/`页面，当然你也可以在settings配置文件中通过添加`LOGIN_URL`配置来改掉他，同时`@login_required`也接收参数`login_url`来指定登陆页面。

```python
from django.contrib.auth.decorators import login_required

@login_required(login_url='/login/')
def home(request):
  return JsonResponse({"data": "xxxxx"})
```



<br>



## 方式二、中间件方式

中间件位于用户请求和程序响应之间，当用户访问一个url之后并不是直接交给了view去处理，而是先经过中间件处理，然后再到了view，路线是这样的：user-->middleware-->view，所以针对全局所有view的操作就非常适合放在中间件里去处理

Django的中间件都定义在settings的`MIDDLEWARE`配置下，默认就添加了诸如auth、csrf之类的中间件



> 需要注意的是Django的中间件是有先后顺序的，对于接收到的请求默认从上到下依次执行



先来编写中间件类，代码如下：

```python
from django.shortcuts import redirect
from django.conf import settings


class LoginRequiredMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
        self.login_url = settings.LOGIN_URL
        self.open_urls = [self.login_url] + getattr(settings, 'OPEN_URLS', [])

    def __call__(self, request):
        if not request.user.is_authenticated and request.path_info not in self.open_urls:
            return redirect(self.login_url + '?next=' + request.path)

        return self.get_response(request)
```



`__init__`和`__call__`为middleware的固定格式

`__init__`函数，初始化了几个变量。需要注意的是定义了一个变量`open_urls`，这是一个list，包含所有不需要验证登陆的页面，提供了更强的灵活性，在这个列表里的url都不会验证是否登陆，默认将`login_url`添加到了`open_urls`列表中

`__call__`函数会判断当用户没有登陆且请求的URL不在`open_urls`列表中时，就直接给重定向到登陆页面



然后在setting配置文件的MIDDLEWARE中添加上边这个中间件的方法就可以了

```python
MIDDLEWARE = [
    'coffee.middleware.loginrequired.LoginRequiredMiddleware',
]
```



`LoginRequiredMiddleware`中间件放在最后一行即可，前边为中间件类的路径，`coffee`作为一个app，需要包含在`INSTALLED_APPS`内，目录结构大概如下：

```bash
webapp 
    - coffee
        - middleware
            - __init__.py
            - loginrequired.py
        - views.py
    - webapp
        - __init__.py
        - settings.py
        - urls.py
    - manage.py
```



当我们有需要排除，不用检查是否登陆的URL时，只需要在settings配置文件中添加`OPEN_URLS`配置即可

```python
OPEN_URLS = ['/devops/', '/webssh/']
```

至此，完美解决了所有view都需要添加`@login_required`的问题