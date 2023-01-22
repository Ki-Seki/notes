title:      Django 学习笔记
author:     小 K
summary:    《Python Django 2021 - Complete Course》的课程笔记，笔记围绕课程项目 DevSearch 展开
datetime:   2021-10-21 07:00
            2021-11-05 17:20
tags:       Python
            Django

[TOC]

# Django 学习笔记

## 步骤

### 一般步骤（General）

1. 构建项目
   1. `django-admin startproject name`
   2. 在设置中本地化：`LANGUAGE_CODE = 'zh-Hans'`，`TIME_ZONE = 'Asia/Shanghai'`
2. 构建 APP
    1. `cd name`
    2. `python manage.py startapp name`
    3. 在 settings.py 的 `INSTALLED_APPS` 中注册，添加字段 `<app_name>.apps.<AppConfig>`
3. 使用 views
    1. 为 APP 思考应有的主要页面，主要考虑页面应当由 request 的链接中接收哪些信息
    2. 制作一些与 request 对应的 response 的占位数据
    3. 构建对应的 view，其返回内容应当包括占位数据，形式上可以是
        * 相应的不含动态内容的 template，作为占位页面，等编辑 template 时使用
        * 简单的 `django.http.HttpResponse` 对象
    4. 从项目到 APP，自顶向下地用 `path` 或 `include` 配置 urls.py 中的 urlpatterns
4. 使用 templates
    1. 写全局和 APP 的 template
    2. 在 settings.py 的 `TEMPLATES` 项中配置 templates 的位置
    3. 在 view 函数中调用 template
    4. templates 也要做到 APP 层分离
    5. 使用 DTL、Jinja2 进行动态网页设计
5. 启用 admin 应用，以便于观察 models
    1. `python manage.py migrate`
    2. `python manage.py createsuser`
6. 使用 models
    1. 写 model 类
       1. 定义数据表，表之间的关系，数据字段（可利用 drawSQL 工具进行可视化设计）
       2. 在 model 类中加入 `__str__` dunder 函数，以便观察
    2. 将 model 进行迁移，
        1. `python manage.py makemigrations`
        2. `python manage.py migrate`
    3. 在 admins.py 中注册 model，以便通过 admin panel 观察
    4. 通过 admin panel，向所有的 model 添加一些占位数据，以便测试
7. 修改 views 中的方法，使用查询语句，模拟真实情况

### 表单（Form）

1. 建立含有 `<form></form>` 标签的 template
    * `<form></form>` 标签内应注意属性：`action`, `method`, `enctype`
    * 不要忘了 `{% csrf_token %}`
2. 建立对应的 view，并配置其 url
3. 建立基于 `django.forms.ModelForm` 的模型，可以单独创建但文件夹使得代码逻辑清晰
4. CRUD（Create Read Update Delete）
    * C&R（可以参考 view 中的 `create_project` 函数）：
        * 改变 ModelForm 中的 Meta 中的 fields，确定哪些字段可以由用户更改
        * view 返回 redirect 对象
    * U（可以参考 view 中的 `update_project` 函数）：
        * 根据 ID 获取 ProjectForm 实例（实际上要做数据库查询操作）
    * D：
        * 可以重新做一个模板，用来提示用户是否确认删除
        * 仍然使用 form 标签来确认删除

可以建立基于 `django.forms.ModelForm` 的模型，也可以用自带的一些做好的模型如，`django.contrib.auth.forms.UserCreationForm`

### 静态文件（Static File）

注意点：

* Django 使用静态文件有两种方式：Debug 下的静态文件和部署环境下的静态文件。后者不会自动搜索静态文件。
* Django 使用图片必须 `pip install pillow`
* 每当从调试环境进入部署环境时，都要关闭 Debug，同时进行静态文件迁移
* 在浏览器页面按 `ctrl+shift+r` 以进行硬刷新

Debug 下的静态文件的使用：

1. 创建文件
    1. 在项目根目录下创建 static 文件夹
    2. 在 static 下创建 images，js，styles 三个子文件夹
    3. 将静态资源文件放在对应子文件夹中
2. 在 settings.py 中添加 `STATICFILES_DIRS` 配置常量
3. 静态文件的几个使用实例：
    * 在模板中引用 CSS 文件
        1. `{% load static %}`
        2. `<link></link>`
    * 在模板中引用图片文件，如 `<img src="{% static 'images/logo.svg' %}">`
    * 在模型中加入图片数据域
        1. 为模型增加图片字段，`makemigrations`，`migrate`
        2. 确定模型中图片索引位置：在 settings.py 中配置 `MEDIA_ROOT` 常量
        3. 配置图片路由，以使图片可以显示
            1. 在 settings.py 中配置 `MEDIA_URL` 常量
            2. 在项目 urls.py （非 APP 的 urls.py）中添加路由
                1. 导入必要的模块：`from django.conf import settings`，`from django.conf.urls.static import static`
                2. `urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)`
        4. 用 form 提交图片
            1. 为 ModelForm 中的 Meta 中的 fields 增添新值，即模型中定义的图片字段的名称，以让 Django 的 form 自动显示此字段
            2. 为 `<form></form>` 标签添加属性：`enctype="multipart/form-data"`
            3. 修改使用到图片增改操作的 view 函数，例如：
                * `form = ProjectForm(request.POST, instance=project)`，修改为➡
                * `form = ProjectForm(request.POST, request.FILES, instance=project)`
                * 这里是存疑点

部署环境下的静态文件的使用：

1. 进入部署环境
     1. `DEBUG = False`
     2. `ALLOWED_HOSTS = ['127.0.0.1']`
2. 为所有静态文件设置统一文件夹：在 settings.py 中配置 `MEDIA_ROOT` 常量
3. 迁移所有静态文件到方才设置的路径：`python manage.py collectstatic`
4. 配置 url，``urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)``
5. 使用 whitenoise
    1. `pip install whitenoise`
    2. 在 settings.py 中的 MIDDLEWARE 中添加 `whitenoise.middleware.WhiteNoiseMiddleware`

### 主题（Theme）

一般情况：

1. 找到一个不错的 theme
2. 内容由 DTL（Django Template Language）动态生成

表单 input 元素套用 theme，要重写 model：

1. 导入 `django.forms`
2. Meta 内重写 widgets
3. 用 super 重写 `__init__` 函数，在重写函数中修改 widget

### 信号（Signal）

模型的信号：

* 方法一：使用连接函数
    1.导入 `from django.db.models.signals import blah_blah`
    2.创建 receiver 函数
    3.使用 signals.py 中的 `pre_save`, `post_save`, `pre_delete`, `post_delete` 等进行连接
* 方法二：使用装饰器
    1.`from django.dispatch import receiver`
    2. 在接收函数上添加装饰器：如 `@receiver(receiver_fun_name, sender=model_name)`
* 将信号部分分离出为 signals.py 的方法
    1. 在 apps.py 中的 AppConfig 类中添加函数 `ready()`，其函数体中进行 `import app.signals`

### 认证和授权（Authentication & Authorization）

* 认证和授权至少应有
    * 三个视图：登录、登出和注册
    * 一个模板（用分支语句判断是登录、登出还是注册）
    * 一个模型
* 认证
    * 使用 `django.contrib.auth` 下的 `login`，`authenticate`，`logout`
* 授权
    * 法一：通过布尔值 `request.user.is_authenticated` 来判断是否是认证过的用户
    * 法二：使用装饰器 `login_required`
        * `from django.contrib.auth.decorators import login_required`
        * 在 view 函数上装饰如，`@login_required(login_url='name_of_route_that_leads_to_login')`

* 登录
* 登出
* 注册
    * 模板同登录共用一个模板
    * 使用内置表单类 `from django.contrib.auth.forms import UserCreationForm`

### 即时消息（Flash Message）

* 启用 app：`django.contrib.messages` 在 INSTALLED_APPS 中
* `from django.contrib import messages`
* 如：`messages.error(request, 'User not logged in')`

## 原理

### 小贴士

* Django 整个项目中的导入，相对路径等，都应该以 manage.py 为准
* 在 context 中加入页面类型键值对，即可实现模板的复用
* 一些相关模块：
    * `pillow`：使用图片必须有
    * `whitenoise`：部署时，静态文件迁移工具

### 信息流动过程

1. 用户输入网址
2. urls.py 中的 urlpatterns 解析网址
3. 由对应的 view 函数处理 request，返回模板文件
4. model 中的数据要么用 view 函数从服务端发给用户；要么由用户端发起 POST 请求，由服务器端接收

### view 函数返回类型

* render：模板文件
* redirect：重定向
* HttpResponse：html 字符串

### Django 怎么知道那些文件是项目文件

* 使用 `startapp` 命令创建出的文件：不必说，Django 一定知道，比如 admin.py, apps.py, models.py 等
* 被上一条中的文件引用的文件：在运行时 Python 的调用机制自然会调用，比如新建一个分离出的单独的 Model 文件（如 forms.py）
* 除上述两者之外的文件：可以在 apps.py 中的 AppConfig 类中进行引用，比如 signals.py

## Q & A

### 报错 `ValueError at / dictionary update sequence element #0 has length 1; 2 is required`？

可能是 `path('', views.about_me, name='about-me'),` 省略了 `name=`

### CSS 引入图片？

假设有如下文件结构：

```text
.
├─images
│      bg.jpg
│
└─styles
        index.css
```

`index.css` 就可以用 `url(../images/bg.jpg)` 来访问 `bg.jpg`

## 项目相关内容

### 说明

* 关联的课程：[Python Django 2021 - Complete Course](https://www.udemy.com/course/python-django-2021-complete-course/)
* 对应的项目：[DevSearch](https://github.com/divanov11/Django-2021)
* 学习进度：仅学完第七章第一节，[DevSearch](https://github.com/Ki-Seki/DevSearch)

### 目录结构

```text
devsearch
    |---- devsearch     # 项目配置文件夹
    |---- projects      # app
    |---- static        # 调试时的静态文件夹
    |---- staticfiles   # 部署时的静态文件夹
    |---- templates     # 模板文件夹
    |---- db.sqlite3    # SqlLite 数据库文件
    |---- manage.py     # 自动生成的管理程序
```

### 资源

* [SQL Diagram](https://drawsql.app/dennis-ivy/diagrams/dev-search)
* ![Query Set](Querysets%20List.png)
