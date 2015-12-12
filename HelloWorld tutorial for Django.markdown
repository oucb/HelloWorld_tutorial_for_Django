# HelloWorld tutorial for Django

标签（空格分隔）： web

---
##执行命令默认都是在终端下，本教程适合linux及mac os下练习
###1.安装django
推荐使用pip安装：`pip install django`
检查django已安装版本：`python -c "import django; print(django.get_version())"`
###2.创建HelloWorld工程
执行cd命令(`cd projects`)进入相应目录，然后执行`django-admin startproject HelloWorld`，该命令将在当前目录下新建HelloWorld工程目录，其中包含manage.py文件(project管理）、HelloWorld文件夹(存放project的相关配置文件），如下所示：
```
HelloWorld/
    manage.py
    HelloWorld/
        __init__.py
        settings.py
        urls.py
        wsgi.py
```
###3.创建hellos应用
执行`cd HelloWorld`命令进入到manage.py文件所在目录，可通过`ls`命令查看当前目录是否包含manage.py文件来确认。然后执行`python manage.py startapp hellos`来创建django应用hellos,该命令将在当前目录下新建hellos应用目录，包含应用的相关配置文件，如下所示：
```
hellos/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py
```
###4.编辑views.py文件，添加view调用函数
```python
from django.shortcuts import render

def hello(request):
    return render(request, 'hellos/hello.html')
    # 后面将介绍html文件的创建
def hello_world(request):
    if request.POST['greet']:
        greet = request.POST['greet']
        name = request.POST['name']
        # 从字典request.POST取回用户输入的内容，‘greet’和‘name’作为字典的key在表单form中的input定义
    else:
        greet = 'Hello'
        name = 'Nobody'
    return render(request, 'hellos/hello_world.html', {'greet': greet, 'name': name})
    # 字典{'greet': greet, 'name': name}构建template文件中greet、name与函数中greet、name参数的索引，template文件中的字符'greet'、'name'作为字典的key。
```
hello函数返回hello.html文件的浏览页面，页面将会提供一个可供用户输入的form表单，并将用户输入内容放入request.POST字典，然后提交到hello_world函数所绑定的url地址；hello_form函数将根据form表单中input的name属性内容来取出字典request.POST中对应的用户输入，并赋值给相应变量，hello_world.html文件取得变量的值，并在浏览器显示页面。
###5.url绑定
编辑应用目录下的urls.py文件，如下：
```python
from django.conf.urls import url
from . import views

app_name = 'hellos'
# 方便在template文件中使用{% url 'hellos:hello_world' %}，hellos是应用绑定名称，hello_world是url绑定名称
urlpatterns = [
    url(r'^hello/$', views.hello, name='hello'),
    url(r'^hello_world/$', views.hello_world, name='hello_world'),
    
]
```
编辑工程配置目录下的urls.py文件，如下：
```python
from django.conf.urls import include, url

urlpatterns = [
    url(r'^hellos/', include('hellos.urls')),
    # 包含应用hellos下的urls.py文件
]
```
###6.创建view函数的template
在hellos应用目录下新建templates文件夹，在templates文件夹下新建hellos文件夹，在里面创建所需的模板文件hello.html与hello_world.html，目录结构大致如下：
```
HelloWorld/
    HelloWorld
    hellos/
        templates/
            hellos/
                hello.html
                hello_world.html
```
编辑hello.html文件，最后如下
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Hello!Form</title>
</head>
<body>

<h1 align="center" style="color: deepskyblue;">Fill Out This Form</h1>
<form action="{% url 'hellos:hello_world' %}" method="post" align="center" style="color: green;font-size: 1.7em;">
    {% csrf_token %}
    <label for="greeting" style="display: inline-block;width: 170px;">A Greeeting:</label>
    <input type="text" name="greet" id="greeting" style="font-size: 1em;">
    <br/>
    <label for="names" style="display:inline-block;width: 170px;">Your Name:</label>
    <input type="text" name="name" id="names" style="font-size: 1em;">
    <br/>
    <input type="submit" style="color: orange;">
</form>

</body>
</html>
```
这里需要注意的是form表单中的action内容指向view函数hello_world所绑定的url地址，即提交完表单后跳转的url地址，方法使用的是post，因此添加{% csrf_token %}防止伪提交；表单中用户输入的内容将存储在字典request.POST提交到跳转的url地址，input中`name`的内容将作为request.POST字典的key，而与key绑定的value就是input中用户输入的内容。

编辑hello_world.html文件，最后如下：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Greet</title>
</head>
<body>

<p align="center" style="color: green;font-size: 3em">I just want to say <em style="color: deepskyblue;">{{ greet }}! {{ name }}</em>.</p>

<p align="center"><input type="submit" value="重新填写" onclick="window.history.back()" align="center" style="color: peru;"></p>

</body>
</html>
```
###7.演示
工程目录下，即与manage.py文件同一目录下执行：`python manage.py runserver`
根据提示在浏览器输入相应的地址，默认为http://127.0.0.1:8000/hellos/hello，输入相应内容，提交后即可看到。

                
    






