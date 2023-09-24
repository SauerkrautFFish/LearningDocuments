# python django学习

## 一些命令

创建项目

django-admin startproject first_project

创建应用

python manage.py startapp first_app

启动服务器

python manage.py runserver [端口，可选]

## 项目结构

```bash
mysite
-manage.py [启动，管理]
-mysite
 -__init__.py
 -settings.py [配置文件]
 -urls.py [url和函数的对应关系]
 -asgi.py [接收网络请求][不用动][异步方式]
 -wsgi.py [接收网络请求][不用动][同步方式]
-app01
 -__init__.py
 -admin.py [默认提供了admin后台管理]
 -apps.py [app启动类]
 -migrations [数据库变更记录]
  -__init__.py
 -models.py [对数据库操作]
 -tests.py [单元测试]
 -views.py
 
```

### MTV

models：数据库管理，非必须

templates：模板引擎，非必须

views：接口引擎，必须



浏览器输入地址-> urls.py -> view

## 配置相关

新添加应用后，需要在settings.py配置文件里的installed_apps进行注册，在该数组里添加一行[应用名].apps.[应用名]Config，具体可以看应用里的apps.py文件（直接填应用名好像也可以。。。）。





https://www.bilibili.com/video/BV1NL41157ph?p=1&vd_source=da303f8b38422434eae039096186e9ad

## 模板

### render

如果是返回页面而不是返回数据，就不能使用HttpResponse，而是使用

```python
def user_list(req):
    name = "123"
    roles = ["123", "456"]
    user_info = {"nnn": 123, "nnaaa": "@3123"}
    return render(req, "user_list.html", {"name": name, "roles": roles, "user_info": user_info})
```

如果在settings.py里有配置templates的dirs，会先在dirs找（初始值是根目录）。

如果没找到，会按照在settings.py中注册应用（installed_apps）的顺序，在每一个应用的的templates文件夹寻找该user_list.html，第三个参数是要返回的数据，前端可以用模板语法进行获取。

render会读取html，并把里面的模板语法进行填充渲染，最终只得到包含html的标签的页面，再返回。

### 模板语法

```html
<div>
    <!-- 单个值 -->
    {{ name }}
    <!-- 数组 -->
    {{ roles.0 }}
	{{ roles.1 }}
    {% for item in roles %}
    	<span>{{ item }}</span>
    {% endfor %}
    <!-- 字典 -->
    {{ user_info.nnn }}
    <!-- (  value in user_info.values) (k, v in user_info.items) -->
    {% for key in user_info.keys %}
    	<span>{{ item }}</span>
    {% endfor %}
</div>
```

```html
{% if name == "123" %}
	<div></div>
{% elif name == "1223" %}
	<div></div>
{% else %}
	<div></div>
{% endif %}
```

## request和response

```python
def something(req):
    # req 是一个对象，封装用户发送过来的所有相关数据
    
    # 1.获取请求方式
    print(req.method)# get post
    # 2.获取url上传递的数据
    print(req.GET) #/?n1=1&n2=2
    # 3.获取在请求体上提交的数据
    print(req.POST)
    print(req.POST.get("user"))
    # 4.将字符串内容返回给请求者
    return HttpResponse("123")
	# 5.render在上面有提到 读取页面+渲染返回给用户
    # 让浏览器重定向到其他页面(重定向是需要浏览器二次发送请求, 转发是内部帮转)
    return redirect("https://www.baidu.com")
```

如果要提交表单，需要添加csrf_token(django要校验是否是非法请求)

<form>
	{% csrf_token %}
    <input type="text">
    <input type="text">
    <input type="submit">
</form>

