---
title: Django 一些记录
date: 2020-08-28 11:07:36
tags: [Django]
categories: [Python]
---



### 添加本地js

在Django应用下新建目录static（与templates同级目录），然后修改`settings.py`  

```python
import os

STATIC_URL = '/static/'
STATICFILES_DIRS = (
    os.path.join(BASE_DIR, 'static'),
)
```

然后把自己的js、css目录放置到static目录下。

在html页面中设置本地js、css如下：

```html
<!-- Bootstrap CSS -->
<link rel="stylesheet" href="./static/css/bootstrap.min.css" />

<!-- jQuery first, then Popper.js, then Bootstrap JS -->
<script src="./static/js/jquery-3.0.0.min.js" type="text/javascript"></script>
<script src="./static/js/bootstrap.bundle.min.js" type="text/javascript"></script>
```

关于bootstrap的问题，简要提一下：  

因为bootstrap需要jQuery，所以需要先引入jQuery，至于`propper.js`，`popper.min.js` 用于设置弹窗、提示、下拉菜单，是第三方插件，目前 `bootstrap.bundle.min.js` 已经包含了 `popper.min.js`，所以可以直接按照上面的写法。



### 自定义时间格式

**DateTimeField、DateField和TimeField**，其值分别对应着Python里的datetime.datetime、datetime.date和datetime.time三个实例，这三个Field里都有两个参数：**auto_now和auto_now_add，默认值均为False**。

**auto_now参数说明：**

> 每次保存对象时自动将字段值设置为当前时间，能够在保存该字段时,将其值设置为当前时间,并且每次修改model,都会自动更新。因此这个参数在需要存储“最后修改时间”的场景下,十分方便，常用类似“last-modified”或者"update_time"字段。

[参考](https://blog.51cto.com/xujpxm/2090382)

如果默认时间格式是英文显示，那么可以修改`settings.py`进行自定义：  

```python
USE_L10N = False
DATE_FORMAT = 'Y-m-d'
DATETIME_FORMAT = 'Y-m-d H:i:s'
```

**注意事项：如果USE_L10N设置为了True，那么语言环境规定的格式具有更高的优先级并将被应用，即DATE_FORMAT不生效。**



### list-group 设置固定宽度

```html
{% for itm in item2FA_list %}
    <ul class="list-group list-group-horizontal">
        <li class="list-group-item" style="width: 18rem"> {{ itm.website_text }} </li>
        <li class="list-group-item" style="width: 28rem"> {{ itm.account_text }} </li>
        <li class="list-group-item" style="width: 28rem"> {{ itm.pub_date }} </li>
    </ul>
{% endfor %}
```



### Django 开启SSL（HTTPS）

Django development server (run by `python manage.py runserver`) ***cannot handle https***.

One of simplest solutions from the answer is to use [django-sslserver](https://github.com/teddziuba/django-sslserver) package.

Other solutions include running some kind of https proxy locally.

Or, run it not in development mode (no live reload) using wsgi server and configuring proper https on it.



[https://stackoverflow.com/questions/57849833/how-to-make-django-site-https-in-windows10](https://stackoverflow.com/questions/57849833/how-to-make-django-site-https-in-windows10)



### html中value含有空格被截断

```html
<form action="{% url 'update2FA' %}" method="post">
    {% csrf_token %}
    <button type="submit" name="update_btn" value={{ source_key_text }} >更新</button>
</form>
```

现在`source_key_text`的值为`aaaa bbbb cccc dddd`，在POST传入时，只传入了aaaa，后面的被截断。正确的写法为：  

```html
<form action="{% url 'update2FA' %}" method="post">
    {% csrf_token %}
    <button type="submit" name="update_btn" value='{{ source_key_text }}' >更新</button>
</form>
```

html的标签属性是按照空格来区分的，如果不加引号会识别出错。

具体可以参考: [参考](https://axiu.me/coding/value-has-spaces-breaks-in-html/)



### 前端往后端提交数据( 3种方法)

#### 1. **HTML原生表单**

html原生就带表单<form>标签，<form>标签提供设置method，可以使用get/post方法提交数据到后端，而如果加上action，则可以将数据指定提交到特定的url（即对应的后端函数）。对应submit按钮，需要将type="submit"，而不能是普通的type="button"。

```text
<form method="post" action="/mopasswd/">
......
<button type="submit">submit</button>
</form>
```

#### 2 . jquery的`.post`

jquery的`.post`请求格式如下：`.post`是高层的操作，jQuery会自动封装调用底层的`$.ajax`。

```text
$.post(url,{json格式的数据},function (条件) {
           具体业务函数
    },'返回的数据格式')
```

一般示例如下：

```text
$.post('/changepassword/',{
    'username':$('#username').val(),
    'password':$('#password').val(),
    },function (res) {
            alert("添加成功！");
            window.location.reload();
    },'json')
```

#### 3. jquery的`.ajax`

ajax是jquery另外一种向后台推送数据的方法，格式如下：`$.ajax`是一种通用的底层封装，`$.ajax()`请求数据之后，则需要使用回调函数，有beforeSend、error、dataFilter、success、complete等。

```text
$.ajax(
{
    url:url,
    type:"post",
    data:{
    },
    beforeSend:function(){},
    async:true,
    success:function(){

    },
    error:function(){
    },
}
```

一般示例如下：

```text
$.ajax(
{
    url:'/changepassword/',
    type:"post",
    data:{
        'username':$('#username').val(),
        'password':$('#password').val(),
    },
    beforeSend:function(){},
    async:true,
    success:function(res){
        alert("添加成功！");
        window.location.reload();
    },
    error:function(){
    },
}
```





### **后端往前端返回数据**

后端往前端返回数据有各种render，render_to_response，JsonResponse等，看似不同，其实都是差不多的。

```text
#render只能返回静态页面
render(request,'index.html')

#render_to_response可以返回页面与同时向前端返回数据
#locals()表示会把函数内所有的变量数据都会以json格式返回到前端
render_to_response（'index.html',locals()）
#或者可以按照json格式，指定返回给前端的变量与数值
render_to_response('index.html', {'userinfo':userinfo})

#JsonResponse不返回页面，仅以json格式向前端返回数据
JsonResponse({'result': result})
```



### 前端接收后端数据并展示

####　1．使用python的mako语法

第一种方式就是使用python的mako语法，就是前面用表格遍历数据库查询结果，并且展示的语法。

```text
{% for i in data %}
    {{ i.xxx }}
    ......
{% endfor %}
```

这种用法好处是简单，易学易用易懂。缺点是只能在页面第一次加载的时候加载数据，页面不刷新/重新加载，无法修改数据。

#### 2. 使用javascript/jquery

第二种方式是使用`javascript/jquery`，控制/修改变量的`value`。

```text
#在form里面以<h1>标题格式，显示“这是一段文字”
var str = "<h1>这是一段文字</h1>"
$('.form').html(str)

#将id为"button1"的按钮的文字显示设置为“修改”
$('#button1').text('修改')
```

### 

### ajax Post请求Forbidden (CSRF token missing or incorrect.)错误

需要在请求中加入`csrfmiddlewaretoken:'{{ csrf_token }}'`  

```javascript
<script>
    $('#update_btn').on('click', function(){
    $.ajax({
        url:'/update2FA',
        type:'post',
        data:{'source_key_text': $('#update_btn').val(),
              csrfmiddlewaretoken:'{{ csrf_token  }}'},
        success:function(data){
            $('#secret_val').val(data)}
    })
})
</script>
```



### django中model的主键的设置与自增

在django所定义的model里是可以自己设置主键的，具体方法为

```python
AgentInfo_id = models.IntegerField(primary_key=True)
```

但是要注意的是，为了添加元素的过程中主键的自增，主键的设置中不能加入default属性，否则save()的过程将不再是新建元素而是替换元素。

google 的一个group中说不要用IntegerField 用AutoField. 

使用AutoField在管理界面输入数据时，不会显示此项。而IntegerField会显示此项。



### ajax  Uncaught ReferenceError: $ is not defined

代码如下：

```javascript
<script rel="javascript" type="text/javascript">
    $('#update_btn{{itm.account_id}}').on('click', function(){
    $.ajax({
        url:'/update2FA',
        type:'post',
        data:{'source_key_text': $('#update_btn{{itm.account_id}}').val(),
              csrfmiddlewaretoken:'{{ csrf_token  }}'},
        success:function(data){
            $('#totp_val{{itm.account_id}}').val(data)}
    })
})
</script>
```

代码上没什么错误，从同一个文件其它地方复制过来的。

错误的原因是，jQuery没有在此之前引入，需要把

```html
<!-- Bootstrap CSS -->
<link rel="stylesheet" href="./static/css/bootstrap.min.css" />
<!-- Optional JavaScript -->
<!-- jQuery first, then Popper.js, then Bootstrap JS -->
<script src="./static/js/jquery-3.0.0.min.js" type="text/javascript"></script>
<script src="./static/js/bootstrap.bundle.min.js" type="text/javascript"></script>
```

写在ajax代码的前面，否则识别会出错。



### button按钮文字修改val、text区别

例如：button按钮代码  

```html
<button type="button"  class="btn btn-primary" id="totp_val{{itm.account_id}}" value="123456"> 000000 </button>
```

如下ajax代码：  

```javascript
<script>
    $('#update_btn{{itm.account_id}}').on('click', function(){
    $.ajax({
        url:'/update2FA',
        type:'post',
        data:{'account_id': $('#update_btn{{itm.account_id}}').val(),
              csrfmiddlewaretoken:'{{ csrf_token  }}'},
        success:function(data){
            $('#totp_val{{itm.account_id}}').val(data)}
    })
})
</script>
```

倒数第四行如果是val，那么修改的是button中value属性的值，即123456的值；  

倒数第四行如果是text，那么修改的是button中显示文本的值，即000000的值。





### Ajax返回Json数据例子

```javascript
$(document).ready(function(){
    $.ajax({
        url: 'ajaxfile.php',
        type: 'get',
        dataType: 'JSON',
        success: function(response){
            var len = response.length;
            for(var i=0; i<len; i++){
                var id = response[i].id;
                var username = response[i].username;
                var name = response[i].name;
                var email = response[i].email;

                var tr_str = "<tr>" +
                    "<td align='center'>" + (i+1) + "</td>" +
                    "<td align='center'>" + username + "</td>" +
                    "<td align='center'>" + name + "</td>" +
                    "<td align='center'>" + email + "</td>" +
                    "</tr>";

                $("#userTable tbody").append(tr_str);
            }
        }
    });
});
```

下面是我自己的例子，注意，如果格式解析不正确那么就需要下面的方法，格式化json数据：

```javascript
<script>
    $('#update_btn{{itm.account_id}}').on('click', function(){
    $.ajax({
        url:'/update2FA',
        type:'post',
        data:{'account_id': $('#update_btn{{itm.account_id}}').val(),
              csrfmiddlewaretoken:'{{ csrf_token  }}'
             },
        success:function(resp){
            var objData = jQuery.parseJSON(resp);
            $('#totp_val{{itm.account_id}}').text(objData.totp_val);
            $('#update_btn{{itm.account_id}}').text(objData.time_remain);
        }
    })
})
</script>
```



### bootstrap 圆形样式设置

[参考: How to get circular buttons in bootstrap 4 ](https://www.geeksforgeeks.org/how-to-get-circular-buttons-in-bootstrap-4/)

[CSS样式-border-radius圆形边框基本用法](https://www.cnblogs.com/an2333/p/11520832.html)

关键设置是`border-radius`，如果将值设为50%，则实现正圆形。

```css
<style type="text/css">
.btn-circle.btn-sm {
    width: 40px;
    height: 40px;
    padding: 6px 0px;
    border-radius: 50%;
    font-size: 16px;
    text-align: center;
}
.btn-circle.btn-md {
    width: 50px;
    height: 50px;
    padding: 7px 10px;
    border-radius: 25px;
    font-size: 10px;
    text-align: center;
}
.btn-circle.btn-xl {
    width: 70px;
    height: 70px;
    padding: 10px 16px;
    border-radius: 35px;
    font-size: 12px;
    text-align: center;
}
</style>
```

html:

```html
<button type="button"  class="btn btn-danger btn-circle btn-sm"> 30 </button>
```





### Ajax 实现倒计时

```javascript
var count = 30;
setInterval(function() {
    count--;

    // update timer here

    if (count === 0) {
        count = 30;
        // ajax code here
    }
}, 1000);
```

[参考：Run ajax every 30 seconds and show a countdown timer](https://stackoverflow.com/questions/21022634/run-ajax-every-30-seconds-and-show-a-countdown-timer)

修改后代码：

```javascript
var count = objData.time_remain;
var refreshId  = setInterval(function() {
    count--;
    $('#time_remain{{itm.account_id}}').text(count);
    // update timer here

    if (count === 0) {
        $('#update_btn{{itm.account_id}}').text('更新');
        clearInterval(refreshId);//退出计时器
        // ajax code here
    }
}, 1000);
```





参考:

[Python+Django网站开发系列12-前后端数据交互总结](https://zhuanlan.zhihu.com/p/104425834)



