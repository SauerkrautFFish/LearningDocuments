# python django学习

## 一些命令

创建项目

django-admin startproject first_project

创建应用

python manage.py startapp first_app

启动服务器

python manage.py runserver [端口，可选]

## MTV

models：数据库管理，非必须

templates：模板引擎，非必须

views：接口引擎，必须



浏览器输入地址-> urls.py -> view





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

## 配置相关

新添加应用后，需要在settings.py配置文件里的installed_apps进行注册，在该数组里添加一行[应用名].apps.[应用名]Config，具体可以看应用里的apps.py文件。