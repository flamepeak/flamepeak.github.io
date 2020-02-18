---
title: Django tips
date: 2017-02-19 15:50:13
tags: [Python, Django]
categories: [Python]
---

对于初学者，可以去[自强学堂Django](https://code.ziqiangxuetang.com/django)学习

那project和app什么关系呢？

一个项目一般包含多个应用，一个应用也可以用在多个项目中。

### 版本的选择

Django 1.8.x 支持 Python 2.7, 3.2, 3.3, 3.4 和 3.5.  （长期支持版本 LTS)

Django 1.9.x 支持 Python 2.7, 3.4 和 3.5. 不支持 3.3 了

Django 1.10.x 支持 Python 2.7, 3.4 和 3.5. 

Django 1.11.x 支持 Python 2.7, 3.4, 3.5 和 3.6（长期支持版本 LTS) 最后一个支持 Python 2.7 的版本

Django 2.0.x 支持 Python 3.4, 3.5 和 3.6 （注意，不再支持 Python 2）

这么看，对于现在来说，最好的选择是1.11.x版本，具体1.11的哪个版本，可以去[releases notes](https://docs.djangoproject.com/en/dev/releases/)查看

然后使用`pip install django == 1.11.10`来安装

### Basic command

```bash
django-admin startproject mysite  #创建project
cd mysite
python manage.py migrate
python manage.py runserver  #goto :localhost:8000
python manage.py runserver 127.0.0.1:8001 --settings=mysite.settings
python manage.py startapp blog

#models
python manage.py makemigrations blog
python manage.py sqlmigrate blog 0001
python manage.py migrate

#administration
python manage.py createsuperuser
# Register your models here. admin.py
admin.site.register(Post)

#open python shell
python manage.py shell




```

### Projects and Applications
In Django, a project is considered a Django installation with some settings; and an application is a group of models, views, templates, and URLs. Applications interact with the framework to provide some specific functionalities and may be reused in various projects. You can think of the project as your website, which contains several applications like blog, wiki, or forum, which can be used in other projects.

### model
A model is a Python class that subclasses `django.db.models.Model`, in which each attribute represents a database field. Django will create a table for each model defined in the `models.py` file. When you create a model, Django offers you a practical API to query the database easily.

Since we are going to deal with datetimes, we will install the `pytz` module. This module provides timezone definitions for Python and is required by SQLite to work with datetimes.
`pip install pytz`


Django comes with a migration system to track the changes you do to your models and propagate them into database. The `migrate` command applies migrations for all applications listed in `INSTALLED_APPS`; it synchronizes the database with the current models and migrations.

If you edit your `models.py` file in order to add, remove, or change fields of existing models, or if you add new models, you will have to make a new migration using the `makemigrations` command. The migration will allow Django to keep track of model changes. Then you will have to apply it with the `migrate` command to keep the database in sync with your models.

### views
Creating an `urls.py` file for each app is the best way to make your applications reusable by other projects.


### urls.py name详解

[urls.py name详解](https://code.ziqiangxuetang.com/django/django-url-name.html)

```
from django.conf.urls import url
from django.contrib import admin
from calc import views as calc_views
 
 
urlpatterns = [
    url(r'^add/$', calc_views.add, name='add'),
    url(r'^add/(\d+)/(\d+)/$', calc_views.add2, name='add2'),
    url(r'^admin/', admin.site.urls),
]
```

`url(r'^add/$', calc_views.add, name='add')`, 这里的 name='add' 是用来干什么的呢？

简单说，name 可以用于在 templates, models, views ……中得到对应的网址，相当于“给网址取了个名字”，只要这个名字不变，网址变了也能通过名字获取到。

如果网页里面写成死网址，例如`<a href="/add/4/5/">计算 4+5</a>`，会造成后期修改代价很大，容易造成死链接。

首先介绍一下如何在Django中用Python获取对应的网址(可以用在views.py、models.py等需要转换得到网址的地方)，

进入shell, `python manage.py shell`

```
>>> from django.core.urlresolvers import reverse  # django 1.4.x - django 1.10.x
或者
>>> from django.urls import reverse  # Django 1.10.x - Django 2.x 新的，更加规范了
 
>>> reverse('add2', args=(4,5))
u'/add/4/5/'
>>> reverse('add2', args=(444,555))
u'/add/444/555/'
```

**reverse 接收url中的name作为第一个参数**，这样，我们就可以通过`reverse()`来获取对应的网址，只要是url中的name不改，就不需要更改代码中的网址。

在网页模板中也是如此，可以很方便的使用：

```
不带参数的：
{% url 'name'%}

带参数的：参数可以是变量名
{% url 'name' 参数 %}
```

例如：  
```
<a href="{% url 'add2' 4 5 %}">link</a>
```
将被渲染为
```
<a href="/add/4/5/">link</a>
```  
当urls.py更改时，前提是不更改name(这个参数设定好后不要轻易更改), 获取网址也会动态变化，比如更改为:  
```
url(r'^new_add/(\d+)/(\d+)/$', calc_views.add2, name='add2'),
```
网址将会被渲染为：
```
<a href="/new_add/4/5/">link</a>
```


用在views.py和models.py等地方的reverse()函数，同样会根据name对应url获取到新网址。

想要改网址的时候，只需修改urls.py中的正则表达式，其它部分都不需要修改。

**另外，比如用户的收藏夹中的URL是旧的，如何让以前的/add/3/4自动跳转到新的网址呢？**

Django没有替你完成这个工作，需要自己来写一个跳转方法。

具体的，在views.py写一个跳转函数：

```
from django.http import HttpResponseRedirect
from django.urls import reverse

def old_add2_redirect(request, a, b):
    return HttpResponseRedirect(
        reverse('add2', args=(a, b))
    )
```

在urls.py中：
```
url(r'^add/(\d+)/(\d+)/$', calc_views.old_add2_redirect),
url(r'new_add/(\d+)/(\d+)/$', calc_views.add2, name='add2'),
```

开始可能觉得直接写网址简单，但是用多了你一定会发现，用“死网址”的方法很糟糕。

### Django 网页模板 继承

[Django template](https://code.ziqiangxuetang.com/django/django-template.html)

[Django template 进阶](https://code.ziqiangxuetang.com/django/django-template2.html)

一般的变量之类的用 `{{ }}`（变量），功能类的，比如循环，条件判断是用 `{%  %}`（标签）

例如：  
views.py

```
def home(request):
    TutorialList = ["HTML", "CSS", "jQuery", "Python", "Django"]
    return render(request, 'home.html', {'TutorialList': TutorialList})
```

home.html  

```
{% for i in TutorialList %}
{{ i }}
{% endfor %}
```