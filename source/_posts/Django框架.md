---
title: Django框架
toc: true
date: 2020-05-02 11:24:02
tags:
categories:
- Python
- 第三方库
---

# 一个简单的web框架
<!--more-->
```python
import socket
def f4(request):
    import pymysql
    # 创建连接
    conn = pymysql.connect(host='127.0.0.1', port=3306, user='root', passwd='123', db='db666')
    cursor = conn.cursor(cursor=pymysql.cursors.DictCursor)
    cursor.execute("select id,username,password from userinfo")
    user_list = cursor.fetchall()
    cursor.close()
    conn.close()

    f = open('hostlist.html','r',encoding='utf-8')
    data = f.read()
    f.close()

    # 基于第三方工具实现的模板渲染
    from jinja2 import Template
    template = Template(data)
    data = template.render(xxxxx=user_list, user='dsafsdfsdf')
    return data.encode('utf-8')

# 路由系统
routers = [
      ('/host.html', f4),
]

def run():
    sock = socket.socket()
    sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    sock.bind(('127.0.0.1',8080))
    sock.listen(5)

    while True:
        conn,addr = sock.accept() # hang住
        # 有人来连接了
        # 获取用户发送的数据
        data = conn.recv(8096)
        data = str(data,encoding='utf-8')
        headers,bodys = data.split('\r\n\r\n')
        temp_list = headers.split('\r\n')
        method,url,protocal = temp_list[0].split(' ')
        conn.send(b"HTTP/1.1 200 OK\r\n\r\n")

        func_name = None
        for item in routers:
            if item[0] == url:
                func_name = item[1]
                break

        if func_name:
            response = func_name(data)
        else:
            response = b"404"

        conn.send(response)
        conn.close()

if __name__ == '__main__':
    run()
```

# Django
## 基础
### 创建程序
```
# 创建django程序
django-admin startproject <sitename>
cd <sitename>
# 创建app(后台和用户分开，多个不相关功能分开)
python manage.py startapp xx
# 启动服务
python manage.py runserver 127.0.0.1:8080

########## 配置
# 配置模板路径
settings.py中的template
# 静态文件目录
settings.py中添加STATICFILES_DIRS=((path),)
# 额外配置
注释
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    # 'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

### 程序目录
```
    sitename
        settings.py # Django配置文件
        urls.py # 路由系统：url->函数
        wsgi.py # 定义用于django的socket，wsgiref | uwsgi
    app
        admin.py # django自带后台管理
        model.py # 写类，根据类创建数据库表
        test.py # 写单元测试
        apps.py # app配置
        views.py # 也可以改成目录 
    manage.py # 对当前django的所有操作基于manage
```

### 模板
```
# 元素
{{user}}

# 列表元素
{{users.0}}
{{users.1}}

# 字典元素
{{users.key}}

# 循环
{% for item in users %}
    <h3>{{item}}</h3>
{% endfor %}

# 判断
{% if a==b %}
    <asdfasdf>
{% else %}
    <asfasdfadf>
{% endif %}
```

### 模态对话框Ajax
模态对话框使用form表单提交会刷新页面，为了不刷新，可以使用ajax
```html
<script src="/static/jquery-3.2.0.js/" type="text/javascript"></script>
<script>
    function ajax_add_teacher(){
        $.ajax({
            url:'/modal_add_teacher/',
            type:'POST',
            data:{'tname':$('#tname').val()},
            dataType:"JSON", //将data作为json字符串解析为json对象
            traditional:true, //如果数据中有list或dict
            success: function (data) {
                console.log(data);
                if (data == 'ok') {
                    location.href = '/teachers'; //页面跳转
                    location.reload(); //对当前页面进行刷新
                } else {
                    $('#errormsg').text(data);
                }
            }
        })
    }
</script>
```

#### 前端json
JSON.parse(字符串) -> 对象
JSON.stringify(对象) -> 字符串
通过点取值。
```js
dic = JSON.parse(args)
dic.key
```

#### js阻止默认事件发生
```html
<a href="www.baidu.com" onclick="return funca();"></a>

<script>
    function funca{
        return False;
    }    
</script>
```

#### js绑定事件
```html
<a id="xx"></a>
<script>
    $(function(){
        $("#xx").click(function(){
            
        })
    })
</script>
```

## bootstrap
常用的样式
### 样式
添加 `<link rel='stylesheet' href='/static/bootstrap.css'></link>`
添加属性 class=‘xasdf’
### 响应式
根据窗口大小，自适应布局
@media关键字
### 母版
```html
//母版文件 muban.html
{% block xx %}{% endblock %}

//文件
{% extends 'muban.html'%}
{% block xx %}
    <p></p>
{% endblock %}
```
一般css、content、js三个block

## fontawesome
常用的图标

## cookie
保存在浏览器端的键值对
```python
obj = redirect("/classes/")
obj.set_cookie("ticket","kjaskldfjaskdf",max_age=10,path="/",domain=None) //生效时间10s 推荐, path表示cookie在哪个路径生效,domain对域名的划分（默认当前域名），

import datetime
cur_time = datetime.datetime.utcnow()
time_delta = datetime.timdelta(seconds=123)
obj.set_cookie("ticket","kjaskldfjaskdf",expires=cur_time+time_delta) //知道目标时间都生效

set_salt_cookie("ticket","kjaskldfjaskdf",salt="asdf")



// 获取
request.get_signed_cookie("ticket",salt="asdf")
request.COOKIE.get("ticket")

```

## 数据库
django：路由、视图、母版、ORM()
torando：路由、视图、母版、自由(mysql,SqlAchemy)
flask：路由、视图、母版(第三方)、自由(mysql,SqlAchemy)

## 路由系统
```python
# path第一个参数为正则表达式模板
# 0
path(r"edit/",views.edit)

def edit(request):
    pass

# 00
path(r"^edit$",views.edit) # 匹配edit开头，终止的url地址

def edit(request):
    pass


# 1
path(r"edit/(w+)/(w+)/",views.edit)

def edit(request,a1,a2):
    # a1为正则表达式匹配的值，这里为(w+)中的内容，严格按照顺序从前到后
    pass

# 2
path(r"edit/(?P<a2>w+)/(?P<a1>w+)/",views.edit)

def edit(request,a1,a2):
    # a1为正则表达式匹配的值，这里为(w+)中的内容，按照<>中的进行赋值
    pass
```

### 路由分发
```python
urls.py
    from django.shortcuts import include
    path('app01',include('app01.urls'))
app01.urls.py
    path('index',views.index)
```

### 别名反向生成url
```python
# 1
from django.urls import reverse
path("login/(?P<a1>\w+)",views.login,name="n1")
v = reverse("n1",kwargs={'a1':1111})

# 2
path("login/",views.login,name="m1")
{% url 'm1' %}
```

## CBV和FBV
### CBV
一个URL对应一个类
```python
# urls.py
path(r'^login.html$', views.Login.as_view()),

# views.py
from django.views import View
class Login(View):  # 继承View类作为父类
    # #重写父类方法，该方法可作为装饰器功能
    def dispatch(self, request, *args, **kwargs):  # 自定制 dispatch方法，除了可以利用父类中原dispatch方法，还可以自定制处理逻辑
        print('before')
        obj = super(Login,self).dispatch(request, *args, **kwargs) # 传入Login对象作为参数，调用父类中的方法
        print('after')
        return obj

    def get(self,request):  # 请求为get请求时，自动调用该方法
        # return HttpResponse('Login.get')
        return render(request,'login.html')

    def post(self,request):   # 请求是POST请求时，自动调用该方法
        print(request.POST.get('user'))
        return HttpResponse('Login.post')
```

## ORM操作
### 配置
```python
 __init__.py文件
(ORM默认使用SQLlite连接数据库,需改成Mysql)

添加：
import pymysql
pymysql.install_as_MySQLdb()


# 配置setting文件中重写数数据库配置
DATABASES = {
'default': {
'ENGINE': 'django.db.backends.mysql',
'NAME':'s4day70db',
'USER': 'root',
'PASSWORD': '',
'HOST': 'localhost',
'PORT': 3306,
}
}
```

### 创建数据库
```python
# models
from django.db import models
class UserInfo(models.Model):   # 在数据库中的表名为模块名_UserInfo
    nid = models.BigAutoField(primary_key=True) #可不写，默认会生成表中的id字段 # 自增 主键
    username = models.CharField(max_length=32)
    password = models.CharField(max_length=64)

# settings配置文件中安装app01模块
INSTALLED_APPS = [
    ……
    'app01',
]

# 执行语句
python manage.py makemigrations  #生成数据表配置文件，包含生成及修改等信息
python manage.py migrate  #执行生成的配置文件
```

### 修改表/删除表
```python
# models
class UserInfo(models.Model):
    ……
    age = models.IntegerField(default=1) # 新增字段时需设置默认值/允许为空
    # age = models.IntegerField(null=True)

# 执行语句：
python manage.py makemigrations  #生成数据表配置文件，包含生成及修改等信息
python manage.py migrate  #执行生成的配置文件
```

### 创建外键关联
```python
# models

class UserGroup(models.Model): #等同于主表，需将该类写在子表前面
    title = models.CharField(max_length=32)

class UserInfo(models.Model):   # 外键所在的表等同于子表
    ……
    ug = models.ForeignKey('UserGroup',null=True,on_delete='') # 外键关联字段关联UserGroup表中的Id字段，ug在UserInfo数据表中的字段为ug_id
```

### 单表操作
```python
# 增
from app01 import models
models.UserGroup.objects.create(title='销售部')

# 删
models.UserGroup.objects.filter(id=2).delete()

# 改
 models.UserGroup.objects.filter(id=2).update(title='公关部')

 # 查
group_list = models.UserGroup.objects.all() # 所有
group_list = models.UserGroup.objects.filter(id=1)
group_list = models.UserGroup.objects.filter(id__gt=1) #大于1
group_list = models.UserGroup.objects.filter(id__lt=1) #小于1
```

### 连表操作
```python
# 创建表
from django.db import models
class UserType(models.Model):
    # 用户类型
    title = models.CharField(max_length=32)
class UserInfo(models.Model):
    # 用户表    
    name = models.CharField(max_length=16)
    age = models.IntegerField()
    ut = models.ForeignKey('UserType',on_delete='')   # 关联UserType表中的id字段

# 创建数据

# models.UserType.objects.create(title='普通用户')
# models.UserType.objects.create(title='二逼用户')
# models.UserType.objects.create(title='牛逼用户')

# models.UserInfo.objects.create(name='方少伟',age=18,ut_id=1)
# models.UserInfo.objects.create(name='由秦兵',age=18,ut_id=2)
# models.UserInfo.objects.create(name='刘庚',age=18,ut_id=2)
# models.UserInfo.objects.create(name='陈涛',age=18,ut_id=3)
# models.UserInfo.objects.create(name='王者',age=18,ut_id=3)
# models.UserInfo.objects.create(name='杨涵',age=18,ut_id=1)

# 一对多的正向操作（让存在外键的从表进行跨表，去查询关联主表中的字段）：
# 获得Queryset对象格式数据（格式：对象名.外键字段名.关联表字段名）
obj = models.UserInfo.objects.all().first()   # 获取一条数据，无需再obj[0]来获取具体的对象
print(obj.name,obj.age,obj.ut.title) # 获取跨表后的字段用obj.ut.title方式，ut是外键关联中的字段

# 获得查询结果为字典格式组合成列表类型的Queryset数据,可用list方法转换成列表格式（从表中的外键字段名__主表中的字段名）
v1 = models.UserInfo.objects.values('id','name','ut__title')

# 获得查询结果为元组格式组合成列表类型的Queryset数据,可用List转换成列表（从表中的外键字段名__主表中的字段名）
result = models.UserInfo.objects.all().values_list('id','name'.'ut__title')


# 一对多的反向操作(让主表进行跨表，去查询（有外键关联字段）从表中相应的字段作为查询条件或查询结果)：
# 获得Queryset对象格式的数据,(格式：主表的QuerySet对象名.从***表名__set.all()***)

# （一个用户类型下可以有很多用户，获得所有用户类型对应的用户信息数据）：
obj = models.UserType.objects.all().first()
for row in obj.userinfo_set.all(): 
	print(row.name,row.age)

# 获得字典格式Queryset对象格式的数据,需要转换成json字符串格式通过ajax返回数据时，可用list方法转换成列表（从表表名小写__主表字段名）
v2 = models.UserType.objects.values('id','title','userinfo__name')

# 获得元组格式Queryset对象格式的数据,需要转换成json字符串格式通过ajax返回数据时，可用list方法转换成列表。（外键字段名__从表字段名）
result = models.UserType.objects.all().values_list('id','name','userinfo__name')

# 跨表
# 正向查询：
1. q = UserInfo.objects.all().first()
q.ug.title

2. 
UserInfo.objects.values('nid','ug_id')              
UserInfo.objects.values('nid','ug_id','ug__title')  

3. UserInfo.objects.values_list('nid','ug_id','ug__title')

# 反向查询：
1. 小写的表名_set
obj = UserGroup.objects.all().first()
  result = obj.userinfo_set.all() [userinfo对象,userinfo对象,]
  
2. 小写的表名
v = UserGroup.objects.values('id','title')          
v = UserGroup.objects.values('id','title','小写的从表名称')          
v = UserGroup.objects.values('id','title','小写的从表名称__age')      
    
3. 小写的表名
v = UserGroup.objects.values_list('id','title')          
v = UserGroup.objects.values_list('id','title','小写的表名称')          
v = UserGroup.objects.values_list('id','title','小写的表名称__age')   
```

### 排序
```python
user_list = models.UserInfo.objects.all().order_by('-id','name') 
# —id代表降序，id代表升序
```

### 分组
```python
# group by
from django.db.models import Count,Sum,Max,Min
v =models.UserInfo.objects.values('ut_id').annotate(xxxx=Count('id'))
# 等价于SELECT `app01_userinfo`.`ut_id`, COUNT(`app01_userinfo`.`id`) AS `xxxx` 
# FROM
# `app01_userinfo` 
# GROUP BY `app01_userinfo`.`ut_id`
# ORDER BY NULL

# 带有having 分组条件过滤
v =models.UserInfo.objects.values('ut_id').annotate(xxxx=Count('id')).filter(xxxx__gt=2)
# 等价于SELECT `app01_userinfo`.`ut_id`, COUNT(`app01_userinfo`.`id`) AS `xxxx` 
# FROM
# `app01_userinfo` GROUP BY `app01_userinfo`.`ut_id`
# HAVING COUNT(`app01_userinfo`.`id`) > 2 
# ORDER BY NULL

v =models.UserInfo.objects.filter(id__gt=2).values('ut_id').annotate(xxxx=Count('id')).filter(xxxx__gt=2)
# 等价于SELECT `app01_userinfo`.`ut_id`, COUNT(`app01_userinfo`.`id`) AS `xxxx` 
# FROM 
# `app01_userinfo`
#  WHERE
#  `app01_userinfo`.`id` > 2 
# GROUP BY
#  `app01_userinfo`.`ut_id`
#  HAVING 
# COUNT(`app01_userinfo`.`id`) > 2 ORDER BY NULL

分组格式：model.类名.objects.values(显示的字段名).annotate(作为字段查结结果的别名=Count(字段id/1))
  # annotate依赖于values

# 条件过滤
models.UserInfo.objects.filter(id__gt=1)  # id>1
……（id__lt=1） # id<1
……(id__lte=1) #id<=1
……(id__gte=1) # id>=1
……(id__in=[1,2,3]) #id in [1,2,3]
……(name__startswith='xxxx')  #
……(name__contains='xxxx') #
……exclude(id=1)  # not in (id=1)

```

### 其他操作
- F
```python
from django.db.models import F
models.UserInfo.objects.all().update(age=F("age")+1)  # F()用来取对象中某列值
```

- Q(构造复杂的查询条件)
```python
# 对象方式(不推荐)
from django.db.models import Q
models.UserInfo.objects.filter(Q(id__gt=1))
models.UserInfo.objects.filter(Q(id=8) | Q(id=2)) # or
models.UserInfo.objects.filter(Q(id=8) & Q(id=2)) # and
```

- 方法方式
```python
from django.db.models import Q
q1 = Q()
q1.connector = 'OR'
q1.children.append(('id__gt', 1))
q1.children.append(('id', 10))
# 通过OR将3个条件进行连接组装

q2 = Q()
q2.connector = 'OR'
q2.children.append(('c1', 1))
q2.children.append(('c1', 10))

q3 = Q()
q3.connector = 'AND' #通过AND将2个条件进行连接组装
q3.children.append(('id', 1))
q3.children.append(('id', 2))
q1.add(q3,'OR') #还可将q3嵌入到q1条件组中

# 将q1和q2条件组通过AND汇总到一起，q1和q2内部分别用or组合条件
con = Q()
con.add(q1, 'AND')
con.add(q2, 'AND')
```

```python
condition_dict = {  #用户将选择的条件组合成字典格式
    'k1':[1,2,3,4],
    'k2':[1,],
}
con = Q()
for k,v in condition_dict.items():
    q = Q()
    q.connector = 'OR'
    for i in v:
        q.children.append(('id', i))
    con.add(q,'AND')
models.UserInfo.objects.filter(con)

***********************************************************************
q1 = Q()
q1.connector = 'OR'
q1.children.append(('id', 1))
q1.children.append(('id', 10))
q1.children.append(('id', 9))


q2 = Q()
q2.connector = 'OR'
q2.children.append(('c1', 1))
q2.children.append(('c1', 10))
q2.children.append(('c1', 9))

q3 = Q()
q3.connector = 'AND'
q3.children.append(('id', 1))
q3.children.append(('id', 2))

q1.add(q3,'OR')

con = Q()
con.add(q1, 'AND')
con.add(q2, 'AND')
#以上构造结果等介于(id=1 or id = 10 or id=9 or (id=1 and id=2)) and (c1=1 or c1=10 or c1=9)
```

### extra(添加额外的自定义sql语句)
```python
models.UserInfo.objects.extra(self, select=None, where=None, params=None, tables=None, order_by=None, select_params=None)
 a. 映射
 select 
 select_params=None
select 此处 from 表
 b. 条件
 where=None
 params=None,
 select * from 表 where 此处
 c. 表
 tables
 select * from 表,此处
 c. 排序
order_by=None
 select * from 表 order by 此处


 v = models.UserInfo.objects.all().extra(
    select={
        'n':"select count(1) from app01_usertype where id=%s or id=%s",
        'm':"select count(1) from app01_usertype where id=%s or id=%s",
    },
    select_params=[1,2,3,4])
for obj in v:
    print(obj.name,obj.id,obj.n) 
# 等价于将查询结果作为字段显示列：
# select
# id,
# name,
# (select count(1) from tb) as n
# from xb where ....


models.UserInfo.objects.extra(
select={'newid':'select count(1) from app01_usertype where id>%s'},
select_params=[1,],
where = ['age>%s'],
params=[18,],
order_by=['-age'],
tables=['app01_usertype']
)

# 等价于原生sql语句如下：
# select 
# app01_userinfo.id,
# (select count(1) from app01_usertype where id>1) as newid
# from app01_userinfo,app01_usertype
# where 
# app01_userinfo.age > 18
# order by 
# app01_userinfo.age desc

result = models.UserInfo.objects.filter(id__gt=1).extra(
where=['app01_userinfo.id < %s'],
params=[100,],
tables=['app01_usertype'],
order_by=['-app01_userinfo.id'],
select={'uid':1,'sw':"select count(1) from app01_userinfo"} #添加查询字段
)
# SELECT (1) AS "uid", (select count(1) from app01_userinfo) AS "sw", "app01_userinfo"."id", "app01_userinfo"."name", "app01_userinfo"."age", "app01_userinfo"."ut_id" 
# FROM
#  "app01_userinfo" , "app01_usertype" 
# WHERE
#  ("app01_userinfo"."id" > 1 AND (app01_userinfo.id < 100))
#  ORDER BY ("app01_userinfo".id) 
# DESC
```


### 原生sql语句
```python
from django.db import connection, connections
cursor = connection.cursor() 
 # cursor = connections['default'].cursor()
cursor.execute("""SELECT * from auth_user where id = %s""", [1])
row = cursor.fetchone() # fetchall()/fetchmany(..)
```

### 其他操作
```python
##################################################################
# PUBLIC METHODS THAT ALTER ATTRIBUTES AND RETURN A NEW QUERYSET #
##################################################################

def all(self)
    # 获取所有的数据对象

def filter(self, *args, **kwargs)
    # 条件查询
    # 条件可以是：参数，字典，Q

def exclude(self, *args, **kwargs)
    # 条件查询
    # 条件可以是：参数，字典，Q

def select_related(self, *fields)
     性能相关：表之间进行join连表操作，一次性获取关联的数据。
     model.tb.objects.all().select_related()
     model.tb.objects.all().select_related('外键字段')
     model.tb.objects.all().select_related('外键字段__外键字段')

def prefetch_related(self, *lookups)
    性能相关：多表连表操作时速度会慢，使用其执行多次SQL查询在Python代码中实现连表操作。
            # 获取所有用户表
            # 获取用户类型表where id in (用户表中的查到的所有用户ID)
            models.UserInfo.objects.prefetch_related('外键字段')



            from django.db.models import Count, Case, When, IntegerField
            Article.objects.annotate(
                numviews=Count(Case(
                    When(readership__what_time__lt=treshold, then=1),
                    output_field=CharField(),
                ))
            )

            students = Student.objects.all().annotate(num_excused_absences=models.Sum(
                models.Case(
                    models.When(absence__type='Excused', then=1),
                default=0,
                output_field=models.IntegerField()
            )))

def annotate(self, *args, **kwargs)
    # 用于实现聚合group by查询

    from django.db.models import Count, Avg, Max, Min, Sum

    v = models.UserInfo.objects.values('u_id').annotate(uid=Count('u_id'))
    # SELECT u_id, COUNT(ui) AS `uid` FROM UserInfo GROUP BY u_id

    v = models.UserInfo.objects.values('u_id').annotate(uid=Count('u_id')).filter(uid__gt=1)
    # SELECT u_id, COUNT(ui_id) AS `uid` FROM UserInfo GROUP BY u_id having count(u_id) > 1

    v = models.UserInfo.objects.values('u_id').annotate(uid=Count('u_id',distinct=True)).filter(uid__gt=1)
    # SELECT u_id, COUNT( DISTINCT ui_id) AS `uid` FROM UserInfo GROUP BY u_id having count(u_id) > 1

def distinct(self, *field_names)
    # 用于distinct去重
    models.UserInfo.objects.values('nid').distinct()
    # select distinct nid from userinfo

    注：只有在PostgreSQL中才能使用distinct进行去重

def order_by(self, *field_names)
    # 用于排序
    models.UserInfo.objects.all().order_by('-id','age')

def extra(self, select=None, where=None, params=None, tables=None, order_by=None, select_params=None)
    # 构造额外的查询条件或者映射，如：子查询

    Entry.objects.extra(select={'new_id': "select col from sometable where othercol > %s"}, select_params=(1,))
    Entry.objects.extra(where=['headline=%s'], params=['Lennon'])
    Entry.objects.extra(where=["foo='a' OR bar = 'a'", "baz = 'a'"])
    Entry.objects.extra(select={'new_id': "select id from tb where id > %s"}, select_params=(1,), order_by=['-nid'])

 def reverse(self):
    # 倒序
    models.UserInfo.objects.all().order_by('-nid').reverse()
    # 注：如果存在order_by，reverse则是倒序，如果多个排序则一一倒序


 def defer(self, *fields):
    models.UserInfo.objects.defer('username','id')
    或
    models.UserInfo.objects.filter(...).defer('username','id')
    #映射中排除某列数据

 def only(self, *fields):
    #仅取某个表中的数据
     models.UserInfo.objects.only('username','id')
     或
     models.UserInfo.objects.filter(...).only('username','id')

 def using(self, alias):
     指定使用的数据库，参数为别名（setting中的设置）


##################################################
# PUBLIC METHODS THAT RETURN A QUERYSET SUBCLASS #
##################################################

def raw(self, raw_query, params=None, translations=None, using=None):
    # 执行原生SQL
    models.UserInfo.objects.raw('select * from userinfo')

    # 如果SQL是其他表时，必须将名字设置为当前UserInfo对象的主键列名
    models.UserInfo.objects.raw('select id as nid from 其他表')

    # 为原生SQL设置参数
    models.UserInfo.objects.raw('select id as nid from userinfo where nid>%s', params=[12,])

    # 将获取的到列名转换为指定列名
    name_map = {'first': 'first_name', 'last': 'last_name', 'bd': 'birth_date', 'pk': 'id'}
    Person.objects.raw('SELECT * FROM some_other_table', translations=name_map)

    # 指定数据库
    models.UserInfo.objects.raw('select * from userinfo', using="default")

    ################### 原生SQL ###################
    from django.db import connection, connections
    cursor = connection.cursor()  # cursor = connections['default'].cursor()
    cursor.execute("""SELECT * from auth_user where id = %s""", [1])
    row = cursor.fetchone() # fetchall()/fetchmany(..)


def values(self, *fields):
    # 获取每行数据为字典格式

def values_list(self, *fields, **kwargs):
    # 获取每行数据为元祖

def dates(self, field_name, kind, order='ASC'):
    # 根据时间进行某一部分进行去重查找并截取指定内容
    # kind只能是："year"（年）, "month"（年-月）, "day"（年-月-日）
    # order只能是："ASC"  "DESC"
    # 并获取转换后的时间
        - year : 年-01-01
        - month: 年-月-01
        - day  : 年-月-日

    models.DatePlus.objects.dates('ctime','day','DESC')

def datetimes(self, field_name, kind, order='ASC', tzinfo=None):
    # 根据时间进行某一部分进行去重查找并截取指定内容，将时间转换为指定时区时间
    # kind只能是 "year", "month", "day", "hour", "minute", "second"
    # order只能是："ASC"  "DESC"
    # tzinfo时区对象
    models.DDD.objects.datetimes('ctime','hour',tzinfo=pytz.UTC)
    models.DDD.objects.datetimes('ctime','hour',tzinfo=pytz.timezone('Asia/Shanghai'))

    """
    pip3 install pytz
    import pytz
    pytz.all_timezones
    pytz.timezone(‘Asia/Shanghai’)
    """

def none(self):
    # 空QuerySet对象


####################################
# METHODS THAT DO DATABASE QUERIES #
####################################

def aggregate(self, *args, **kwargs):
   # 聚合函数，获取字典类型聚合结果
   from django.db.models import Count, Avg, Max, Min, Sum
   result = models.UserInfo.objects.aggregate(k=Count('u_id', distinct=True), n=Count('nid'))
   ===> {'k': 3, 'n': 4}

def count(self):
   # 获取个数

def get(self, *args, **kwargs):
   # 获取单个对象

def create(self, **kwargs):
   # 创建对象

def bulk_create(self, objs, batch_size=None):
    # 批量插入
    # batch_size表示一次插入的个数
    objs = [
        models.DDD(name='r11'),
        models.DDD(name='r22')
    ]
    models.DDD.objects.bulk_create(objs, 10)

def get_or_create(self, defaults=None, **kwargs):
    # 如果存在，则获取，否则，创建
    # defaults 指定创建时，其他字段的值
    obj, created = models.UserInfo.objects.get_or_create(username='root1', defaults={'email': '1111111','u_id': 2, 't_id': 2})

def update_or_create(self, defaults=None, **kwargs):
    # 如果存在，则更新，否则，创建
    # defaults 指定创建时或更新时的其他字段
    obj, created = models.UserInfo.objects.update_or_create(username='root1', defaults={'email': '1111111','u_id': 2, 't_id': 1})

def first(self):
   # 获取第一个

def last(self):
   # 获取最后一个

def in_bulk(self, id_list=None):
   # 根据主键ID进行查找
   id_list = [11,21,31]
   models.DDD.objects.in_bulk(id_list)

def delete(self):
   # 删除

def update(self, **kwargs):
    # 更新

def exists(self):
   # 是否有结果

# select_related:查询主动做连表，一次性获取所有连表中的数据（性能相关：数据量少的情况下使用）
q = models.UserInfo.objects.all().select_related('ut','gp')
#等价于select * from userinfo inner join usertype on ...
for row in q:
    print(row.name,row.ut.title) #采用.的形式获取连表数据

# prefetch_related：不做连表，但会做多次查询（性能相关：数据量多，查询频繁下使用）
 q = models.UserInfo.objects.all().prefetch_related('ut')
# select * from userinfo;
# Django内部：ut_id = [2,4]
# select * from usertype where id in [2,4]
for row in q:
    print(row.id,row.ut.title)
```

### 多对多操作
```python
# 手动创建第三张关联表（推荐，手动更灵活）
# models

from django.db import models
class Boy(models.Model):
    name = models.CharField(max_length=32)
class Girl(models.Model):
    nick = models.CharField(max_length=32)
class Love(models.Model):
    b = models.ForeignKey('Boy',on_delete='')
    g = models.ForeignKey('Girl',on_delete='')
class Meta: #添加联合唯一字段
    unique_together = [
        ('b','g'),
    ]


# views
# 添加数据
objs = [
    models.Boy(name='方少伟'),
    models.Boy(name='由秦兵'),
    models.Boy(name='陈涛'),
    models.Boy(name='闫龙'),
    models.Boy(name='吴彦祖'),
]
models.Boy.objects.bulk_create(objs,5) # 批量添加数据

objss = [
    models.Girl(nick='小鱼'),
    models.Girl(nick='小周'),
    models.Girl(nick='小猫'),
    models.Girl(nick='小狗'),
]
models.Girl.objects.bulk_create(objss,5)


# 1. 查询和方少伟有关系的姑娘
# 第一种查询方式：跨表反向查询，获得对象
# obj = models.Boy.objects.filter(name='方少伟').first()
# love_list = obj.love_set.all() # 反向查询，获得所有love表中的对象
# for row in love_list:
#     print(row.g.nick)   # 获得对应的姑娘数据

# 第二种查询方式：连表查询（正向查询），直接查询love表，获得对象
# love_list = models.Love.objects.filter(b__name='方少伟') #获得Queryset对象
# for row in love_list:
#     print(row.g.nick)

# （推荐）第三种查询方式：只发送一次sql连接请求，获得字典,属于正向查询
# love_list = models.Love.objects.filter(b__name='方少伟').values('g__nick')
# for item in love_list:   # 获得字典格式的列表，[{'g__nick':'xxx},]                     
#     print(item['g__nick'])

# （推荐）第四种查询方式：相当于inner join方式连表查询，只发送一次sql连接请求，获得对象
# love_list = models.Love.objects.filter(b__name='方少伟').select_related('g')
# for obj in love_list: # 获得对象
#     print(obj.g.nick)

# 总结：多对多都是先获得关联表中的数据，再进行跨表操作
# 对象格式用.（点）进行跨表     字典和元组采用_（双下划线）进行跨表

# Django自动生成第三张关联表（无法再手动添加其它字段）
# models
class Boy(models.Model):
    name = models.CharField(max_length=32)
    m = models.ManyToManyField('Girl') #自动生成关联表m，以两张表中的id字段作为关联字段
class Girl(models.Model):
    nick = models.CharField(max_length=32)
# views

# 增
# obj = models.Boy.objects.filter(name='方少伟').first()
# obj.m.add(2)
# obj.m.add(2,4) # 创建关联表的多条数据
# obj.m.add(*[1,3]) # 以列表形式创建多条数据

# 删
# obj.m.remove(1)
# obj.m.remove(2,3) # 删除关联表的多条数据
# obj.m.remove(*[4,])

# obj.m.set([1,])  # 覆盖数据库所有数据,重置
# obj = models.Boy.objects.filter(name='方少伟').first()
# obj.m.clear() # 删除所有与方少伟的关联数据

# 查
# 正向查询出单条数据
# obj = models.Boy.objects.filter(name='方少伟').first()
# # girl_list = obj.m.all() 
# girl_list = obj.m.filter(nick='小鱼') # 相当于从从表跨表到主表查询
# print(girl_list)

# 反向查询出多条数据
# obj = models.Girl.objects.filter(nick='小鱼').first()
# print(obj.id,obj.nick)
# boy_list = obj.boy_set.all()   # 相当于从主表跨表到从表反向查询


# 手动与自动结合
# models
class Boy(models.Model):
    name = models.CharField(max_length=32)
    m = models.ManyToManyField(to='Girl',through="Love",through_fields=('b','g',))
    # 可不写to关键字

class Girl(models.Model):
    nick = models.CharField(max_length=32)

class Love(models.Model):
    b = models.ForeignKey('Boy',on_delete='')
    g = models.ForeignKey('Girl',on_delete='')

    class Meta:
        unique_together = [
            ('b','g'),
        ]

# views

obj = models.Boy.objects.filter(name='方少伟').first()
obj.m.add(1)
# obj.m.remove(1)
# obj.m.clear() 可以
v = obj.m.all()
print(v)
```

### 连表操作（多对多自关联）
原理：等同于复制出一张新表
```python
class UserInfo(models.Model):
    nickname = models.CharField(max_length=32)
    username = models.CharField(max_length=32)
    password = models.CharField(max_length=64)
    gender_choices = (
        (1,'男'),
        (2,'女'),
    )
    gender = models.IntegerField(choices=gender_choices)
    m = models.ManyToManyField('UserInfo') # 多对多自关联字段，自动生成第二张表，字段分别为from_userinfo_id和to_userinfo_id; 

    # 表中的m属性不会在userinfo表中生成m字段

def test(request):
# 查男生（通过m字段查询属于正向操作）
xz = models.UserInfo.objects.filter(id=1).first() #id为1代表男生的1条数据
u = xz.m.all()
for row in u:
    print(row.nickname)

# 查女生(通过表名称_set查询属于反向操作)
xz = models.UserInfo.objects.filter(id=4).first() #id为4代表女生的1条数据
v = xz.userinfo_set.all()
for row in v:
    print(row.nickname)
return HttpResponse('...')
```
**外键自关联（常用于评论表功能）**
等同于复制出一张新表，用原表中的外键作连表操作
```python
class Comment(models.Model):
    """
    评论表
    """
    news_id = models.IntegerField()            # 新闻ID
    content = models.CharField(max_length=32)  # 评论内容
    user = models.CharField(max_length=32)     # 评论者
    reply = models.ForeignKey('Comment',null=True,blank=True,related_name='xxxx') #related_name表示反向查询时，代替 表名_set 和 表名__字段名 
"""
   新闻ID                         reply_id
1   1        别比比    root         null
2   1        就比比    root         null
3   1        瞎比比    shaowei      null
4   2        写的正好  root         null
5   1        拉倒吧    由清滨         2
6   1        拉倒吧1    xxxxx         2
7   1        拉倒吧2    xxxxx         5
"""
"""
新闻1
    别比比
    就比比
        - 拉倒吧
            - 拉倒吧2
        - 拉倒吧1
    瞎比比
新闻2：
    写的正好
"""
```


## 分页
分组获取数据`UserGroup.objects.all()[10:20]`
### 内置分页
```python
from django.core.paginator import Paginator,Page,PageNotAnInteger,EmptyPage
def index(request):
	user_list = models.UserInfo.objects.all() #获得所有数据库数据
	paginator = Paginator(user_list,10) #设置每页显示的总条数
	current_page = request.GET.get('page')# 获得当前页数
	try:
	    posts = paginator.page(current_page) #设置当前页数对应的数据
	except PageNotAnInteger as e: #当前页面数非整数
	    posts = paginator.page(1) 
	except EmptyPage as e: #当前页码数为空
	    posts = paginator.page(1)
	return render(request,'index.html',{'posts':posts})
```

```html
<h1>用户列表</h1>
<ul>
    {% for row in posts.object_list %}
        <li>{{ row.name }}</li>
    {% endfor %}
</ul>

<div>
    {% if posts.has_previous %} #是否有上一页
        <a href="/index.html?page={{ posts.previous_page_number }}">上一页</a>
    {% endif %}

    {% if posts.has_next %} #是否有下一页
        <a href="/index.html?page={{ posts.next_page_number }}">下一页</a>
    {% endif %}
</div>
```

### 自定义分页
```python
# views.py
from utils.pager import PageInfo
def custom(request):
    all_count = models.UserInfo.objects.all().count() 
    page_info = PageInfo(request.GET.get('page'),all_count,10,'/custom.html',11)
    user_list = models.UserInfo.objects.all()[page_info.start():page_info.end()]
    return render(request,'custom.html',{'user_list':user_list,'page_info':page_info})

# 
class PageInfo(object):
    def __init__(self,current_page,all_count,per_page,base_url,show_page=11):
        '''
        :param current_page:
        :param all_count: 数据库总行数
        :param per_page: 每页显示行数
        :param base_url:
        :param show_page:分页码范围，默认为11
        '''
        try:
            self.current_page = int(current_page)
        except Exception as e:
            self.current_page = 1
        self.per_page=per_page
        a,b = divmod(all_count,per_page) #返回一个包含商和余数的元组
        if b: # 数据库总数/页面总数后还有多余的数据，所以还需要加1页来显示剩余的数据
            a = a+1
        self.all_pager= a # 总页数
        self.show_page= show_page
        self.base_url= base_url

    # 页码数对应的展示数据内容
    def start(self):
        return (self.current_page-1) * self.per_page # 起始数据位置
    def end(self):
        return self.current_page * self.per_page

    # 组装分页模块
    def pager(self):
        page_list=[]

        # 计算中间页码数显示起始数和结尾数
        half = int((self.show_page-1)/2) # 中间页码数
        # 如果数据总页数 < 分页码范围11
        if self.all_pager < self.show_page:
            begin =1
            stop=self.all_pager + 1
        # 如果数据总页数 > 分页码范围11
        else:
            # 如果当前页 <=5,永远显示1,11
            if self.current_page <= half:
                begin =1
                stop = self.show_page + 1
            else:
                if self.current_page + half > self.all_pager:
                    begin = self.all_pager - self.show_page + 1
                    stop = self.all_pager + 1
                else:
                    begin = self.current_page - half
                    stop = self.current_page + half + 1

        # 组装‘上一页’选项
        if self.current_page <= 1:
            prev = "<li><a href='#'>上一页</a></li>"
        else:
            prev = "<li><a href='%s?page=%s'>上一页</a></li>"%(self.base_url,self.current_page-1,)
        page_list.append(prev)

        # 组装中间页码数显示
        for i in range(begin, stop):
            if i == self.current_page:
                temp = "<li class='active'><a  href='%s?page=%s'>%s</a></li>" % (self.base_url, i, i,)
            else:
                temp = "<li><a href='%s?page=%s'>%s</a></li>" % (self.base_url, i, i,)
            page_list.append(temp)

        # 组装'下一页'选项
        if self.current_page >= self.all_pager:
            nex = "<li><a href='#'>下一页</a></li>"
        else:
            nex = "<li><a href='%s?page=%s'>下一页</a></li>" %(self.base_url,self.current_page+1,)
        page_list.append(nex)

        return ''.join(page_list)


```
```html
<h1>用户列表</h1>
<ul>
    {% for row in user_list %}
        <li>{{ row.name }}</li>
    {% endfor %}
</ul>
<nav aria-label="Page navigation">
  <ul class="pagination">
      {{ page_info.pager|safe }}
  </ul>
</nav>
```

## XSS攻击(跨站脚本攻击)
页面script代码攻击(如评论中的scrpt代码攻击)
item中如果有script代码会自动执行
```html
<h1>评论</h1>
{% for item in msg %}
    <div>{{ item|safe}}</div> #需要给响应的值添加safe
{% endfor %}
```

## CSRF(跨站请求伪装攻击)

**方法1**
```html
<form method="POST" action="/csrf1.html">
    {% csrf_token %}   # 需添加服务器发送的csrf随机字符串，才能访问成功
    <input id="user" type="text" name="user" />
    <input type="submit" value="提交"/>
    <a onclick="submitForm();">Ajax提交</a>
</form>
```

**方法2**
```html
<script>
    function submitForm(){
        var token = $.cookie('csrftoken'); # 获得浏览器里cookies中的csrf随机字符串
        var user = $('#user').val();
        $.ajax({
            url: '/csrf1.html',
            type: 'POST',
            headers:{'X-CSRFToken': token}, # 将数据添加到请求头中，让Django去取，硬性规定
            data: { "user":user},
            success:function(arg){
                console.log(arg);
            }
        })
    }
</script>
```

## simple_tag和filter
```html
{{ name|upper }} # upper表示内置函数,将所有字母变大写
```

**自定义函数**
```python
# 步骤1 创建templatetags文件夹，再创建xx.py模块
from django import template
register = template.Library() # 规定写法，不能修改

@register.filter # 该定义的方法只能传入2个参数
def my_upper(value,arg):
    return value + arg

@register.filter  # 应用于html模版中的if判断语名
def my_bool(value):
    return False

@register.simple_tag # 推荐使用
def my_lower(value,a1,a2,a3):
    return value + a1 + a2 + a3

# 2.views
def test(request):
    return render(request,'test.html',{'name':'aaaaAA'})

# 3. html
# {% load xx %}{#导入加载xx模块#}
# <h2>filter</h2>
#     {{ name|my_upper:"666" }} # 最多支持2个参数
#     {{ name|upper }}

#     {% if name|my_bool %}
#         <h3>真</h3>
#     {% else %}
#         <h3>假</h3>
#     {% endif %}

# <h2>tag</h2>
#     {% my_lower "ALEX" "x" "SB" "V" %}

# 4. setting注册程序块
```

## include小组件
```html
// pub.html
<div>
    <h3>特别漂亮的组件</h3>
    <div class="title">标题：{{ name }}</div>
    <div class="content">内容：{{ name }}</div>
</div>

……
{% include 'pub.html' %}
……
```

## admin
admin
django amdin是django提供的一个后台管理页面，改管理页面提供完善的html和css，使得你在通过Model创建完数据库表之后，就可以对数据进行增删改查，而使用django admin 则需要以下步骤：
```python
# 创建后台管理员
# 配置url
# 注册和配置django admin后台管理页面
# 1、创建后台管理员
# python manage.py createsuperuser
# 2、配置后台管理url
# url(r'^admin/', include(admin.site.urls))
# 3、注册和配置django admin 后台管理页面

# a、在admin中执行如下配置
from django.contrib import admin
from app01 import  models
  
admin.site.register(models.UserType)
admin.site.register(models.UserInfo)
admin.site.register(models.UserGroup)
admin.site.register(models.Asset)
# b、设置数据表名称

class UserType(models.Model):
    name = models.CharField(max_length=50)
  
    class Meta:
        verbose_name = '用户类型'
        verbose_name_plural = '用户类型'
# c、打开表之后，设定默认显示，需要在model中作如下配置

class UserType(models.Model):
    name = models.CharField(max_length=50)
  
    def __unicode__(self):
        return self.name

from django.contrib import admin
  
from app01 import  models
  
class UserInfoAdmin(admin.ModelAdmin):
    list_display = ('username', 'password', 'email')
  
  
admin.site.register(models.UserType)
admin.site.register(models.UserInfo,UserInfoAdmin)
admin.site.register(models.UserGroup)
admin.site.register(models.Asset)
# d、为数据表添加搜索功能

from django.contrib import admin
from app01 import  models
  
class UserInfoAdmin(admin.ModelAdmin):
    list_display = ('username', 'password', 'email')
    search_fields = ('username', 'email')
  
admin.site.register(models.UserType)
admin.site.register(models.UserInfo,UserInfoAdmin)
admin.site.register(models.UserGroup)
admin.site.register(models.Asset)
# e、添加快速过滤
from django.contrib import admin
from app01 import  models
  
class UserInfoAdmin(admin.ModelAdmin):
    list_display = ('username', 'password', 'email')
    search_fields = ('username', 'email')
    list_filter = ('username', 'email')
      
admin.site.register(models.UserType)
admin.site.register(models.UserInfo,UserInfoAdmin)
admin.site.register(models.UserGroup)
admin.site.register(models.Asset)
```



## cookie和session
> cookie是保存在客户端浏览器上的键值对，
> Session是保存在服务端的数据（本质是键值对），用于保持会话
> 因为单独使用cookies，它会保留用户具体的明文形式（不转化成字符串的敏感信息）发送给浏览器（不安全），所以推荐使用session，
> session发送的是随机字符串，不包含用户敏感信息（安全），其中session依赖于cookies,在服务端储存的是{随机字符串：键值对}

```python
def login(request):
    if request.method == 'GET':
        return render(request,'login.html')
    else:
        u = request.POST.get('user')
        p = request.POST.get('pwd')
        obj = models.UserAdmin.objects.filter(username=u,password=p).first()
        if obj:
            # 1. 生成随机字符串
            # 2. 通过cookie发送给客户端
            # 3. 服务端保存
            # {
            #   随机字符串1: {'username':'alex','email':x''...}
            # }
            request.session['username'] = obj.username
            return redirect('/index/')
        else:
            return render(request,'login.html',{'msg':'用户名或密码错误'})

def index(request):
# 获取客户端cookies中的随机字符串，去session中查找有无该字符串， 再通过字符串去session对应key的value中查看是有username，并获得它对应的值

    v = request.session.get('username')   #  v为username对应的具体值
    if v:
        return HttpResponse('登录成功:%s' %v)
    else:
        return redirect('/login/')
```
### session的方法
```python
   def index(request):
        # 获取、设置、删除Session中数据
        request.session['k1']
        request.session.get('k1',None)
        request.session['k1'] = 123
        request.session.setdefault('k1',123) # 存在则不设置
        del request.session['k1']
 
        # 所有 键、值、键值对
        request.session.keys()
        request.session.values()
        request.session.items()
        request.session.iterkeys()
        request.session.itervalues()
        request.session.iteritems()
 
 
        # 用户session的随机字符串
        request.session.session_key
 
        # 将所有Session失效日期小于当前日期的数据删除
        request.session.clear_expired()
 
        # 检查 用户session的随机字符串 在数据库中是否
        request.session.exists("session_key")
 
        # 删除当前用户的所有Session数据
        request.session.delete("session_key")
 
        request.session.set_expiry(value)
            * 如果value是个整数，session会在些秒数后失效。
            * 如果value是个datatime或timedelta，session就会在这个时间后失效。
            * 如果value是0,用户关闭浏览器session就会失效。
            * 如果value是None,session会依赖全局session失效策略。
```

settings
```python

# SESSION_COOKIE_NAME = "sessionid"                        # Session的cookie保存在浏览器上时的key，即：sessionid＝随机字符串
# SESSION_COOKIE_PATH = "/"                                # Session的cookie保存的路径
# SESSION_COOKIE_DOMAIN = None                              # Session的cookie保存的域名
# SESSION_COOKIE_SECURE = False                             # 是否Https传输cookie
# SESSION_COOKIE_HTTPONLY = True                            # 是否Session的cookie只支持http传输
# SESSION_COOKIE_AGE = 1209600                              # Session的cookie失效日期（2周）
# SESSION_EXPIRE_AT_BROWSER_CLOSE = False                   # 是否关闭浏览器使得Session过期
# SESSION_SAVE_EVERY_REQUEST = False（推荐True）             # 是否每次请求都保存Session，默认修改之后才保存

SESSION_ENGINE = 'django.contrib.sessions.backends.db'   # 数据库（默认）
SESSION_ENGINE = 'django.contrib.sessions.backends.cached_db'   #引擎，缓存+数据库，推荐使用
SESSION_ENGINE = 'django.contrib.sessions.backends.cache'  # 缓存
SESSION_ENGINE = 'django.contrib.sessions.backends.file' # 文件
SESSION_ENGINE = 'django.contrib.sessions.backends.signed_cookies'  # cookie
SESSION_CACHE_ALLAS ='default'                             # 使用缓存别名
```

## 用户登录demo
```python
# model
from django.db import models
class Boy(models.Model):
    nickname = models.CharField(max_length=32)
    username = models.CharField(max_length=32)
    password = models.CharField(max_length=63)

class Girl(models.Model):
    nickname = models.CharField(max_length=32)
    username = models.CharField(max_length=32)
    password = models.CharField(max_length=63)

class B2G(models.Model):
    b = models.ForeignKey(to='Boy', to_field='id',on_delete='')
    g = models.ForeignKey(to='Girl', to_field='id',on_delete='')


# urls
urlpatterns = [
    url('admin/', admin.site.urls),
    url(r'^login.html$', account.login),
    url(r'^index.html$', love.index),
    url(r'^loginout.html$',account.loginout),
    url(r'^others.html$',love.others),
]

# views/account
from django.shortcuts import render,HttpResponse,redirect
from app01 import models
def login(request):
    """
    用户登陆 
    :param request: 
    :return: 
    """
    if request.method == 'GET':
        return render(request,'login.html')
    else:
        user = request.POST.get('username')
        pwd = request.POST.get('password')
        gender = request.POST.get('gender')
        rmb = request.POST.get('rmb')
        # 性别判断
        if gender == "1":
            obj = models.Boy.objects.filter(username=user,password=pwd).first()
        else:
            obj = models.Girl.objects.filter(username=user,password=pwd).first()
        if not obj:
            # 未登录
            return render(request,'login.html',{'msg': '用户名或密码错误'})
        else:
            request.session['user_info'] = {'user_id':obj.id,'gender':gender,'username':user,'nickname':obj.nickname}
            return redirect('/index.html')

def loginout(request):
    """
    注销
    :param request: 
    :return: 
    """
    if request.session.get('user_info'):
        request.session.clear() # 清除服务端数据库session（推荐）
        # request.session.delete(request.session.session_key) # 清除客户端cookie中的session
    return redirect('/login.html')

# views/love
from django.shortcuts import render,redirect,HttpResponse
from app01 import models
def index(request):
    """
    首页信息展示
    :param request: 
    :return: 
    """
    if not request.session.get('user_info'): # 获取浏览器中的session随机字符串
        return redirect('/login.html')
    else:
        # 男：女生列表
        # 女：男生列表
        gender = request.session.get('user_info').get('gender')
        if gender == '1':
            user_list  = models.Girl.objects.all()
        else:
            user_list = models.Boy.objects.all()
        return render(request,'index.html',{'user_list':user_list})

def others(request):
    """
    获取与当前用户有关系的异性
    :param request:
    :return:
    """
    current_user_id = request.session.get('user_info').get('user_id')
    gender = request.session.get('user_info').get('gender')

    if gender == '1':
        user_list = models.B2G.objects.filter(b_id=current_user_id).values('g__nickname')
    else:
        user_list = models.B2G.objects.filter(g_id=current_user_id).values('b__nickname')
    print('result', user_list)
    return render(request,'others.html',{'user_list':user_list})
```

```html
# login.html
<form method="POST" action="/login.html">
    {% csrf_token %}
    <p>用户：<input type="text" name="username" /></p>
    <p>密码：<input type="password" name="password" /></p>
    <p>
        性别：
            男<input type="radio" name="gender" value="1" />
            女<input type="radio" name="gender" value="2" />
    </p>
    <p>
        <input type="checkbox" name="rmb" value="11"  /> 一个月免登录
    </p>
    <input type="submit" value="提交" />{{ msg }}
</form>

# 建立html组件：user_header
<h1>当前用户:{{ request.session.user_info.nickname }}</h1>
<a href="/logout.html">注销</a>

# index.html
{% include 'user_header.html' %}
<h3>异性列表</h3>
<a href="/others.html">查看和我有关系的异性</a>
<ul>
    {% for row in user_list %}
        <li>{{ row.nickname }}</li>
    {% endfor %}
</ul>

# others.html
{% include 'user_header.html' %}
<h1>有关系的异性列表</h1>
<ul>
    {% for row in user_list %}
        {% if row.g__nickname %}
            <li>{{ row.g__nickname }}</li>
        {% else %}
            <li>{{ row.b__nickname }}</li>
        {% endif %}
    {% endfor %}
</ul>
```

## 中间件
![](1.png)
中间件是django路由转发之前或返回response时进行的操作。用于对所有请求或一部分请求做批量处理。
```python
class Middle1(MiddleMixin):
    def process_request(self,request):
        # 一般不要返回值，否则会阻止之后的中间件执行，从最后一个中间件的process_response返回，（旧版本从当前的request返回）
        print("asxafs")

    def process_response(self,request,response):
        # 必须有返回值
        print("print")
        return response
# process_request(self,request)
# process_view(self, request, callback, callback_args, callback_kwargs)
# process_template_response(self,request,response)  # 视图函数中有render才会执行
# process_exception(self, request, exception)
# process_response(self, request, response)
```

## MVC和MTV
文件划分方式
MVC： model(数据库，模型) views(模版) controller(业务处理)
MTV： model(数据库，模型) template(模板) views(业务逻辑)

django ： MTV

## Form组件
需要对请求的数据进行验证
一般form提交，如果不满足要求：
- 判断后无法记住上次输出，页面会刷新。

### 一般Form组件的使用
```python
from django.forms import Form,fields
# -------定义Form验证规则类
class LoginForm(Form):
    # html标签name属性=Form类的字段名
    # 正则验证: 不能为空,6-18
    username = fields.CharField(
        max_length=18,
        min_length=6,
        required=True,
        error_messages={
            'required': '用户名不能为空',
            'min_length': '太短了',
            'max_length': '太长了',
        }
    )
    # 正则验证: 不能为空，16+
    password = fields.CharField(min_length=16,required=True)

def login(request):  #Form表单提交形式
    if request.method == "GET":
        return render(request,'login.html')
    else:
       obj = LoginForm(request.POST) #将提交的数据交给Form组件验证
       if obj.is_valid():
           # 用户输入格式正确
           print(obj.cleaned_data) # 字典类型,只包含Form组件校验后的字段数据
           return redirect('http://www.baidu.com')
       else:
           # 用户输入格式错误
           print(obj.errors)
           return render(request,'login.html',{'obj':obj})
```

```html
<form method="post" action="/login/">
    {% csrf_token %}
    <p>username:<input type="text" name="username">{{obj.errors.username.0 }}</p>
    <p>password:<input type="password" name="password">{{obj.errors.password.0  }}</p>
    <input type="submit" value="submit"> 
</form>
```

### Form和Ajax提交验证（Ajax提交不会刷新，上次内容自动保留）
https://www.cnblogs.com/wupeiqi/articles/6144178.html
```html
<h1>用户登录</h1>
<form id="f1" action="/login/" method="POST">
    {% csrf_token %}
    <p>
        <input type="text" name="user" />{{ obj.errors.user.0 }}
    </p>
    <p>
        <input type="password" name="pwd" />{{ obj.errors.pwd.0 }}
    </p>
    <input type="submit" value="提交" />
    <a onclick="submitForm();">提交</a>
</form>
<script src="/static/jquery-1.12.4.js"></script>
<script>
    function submitForm(){
        $('.c1').remove();
        $.ajax({
            url: '/ajax_login/',
            type: 'POST',
            data: $('#f1').serialize(),// 序列化：user=alex&pwd=456&csrftoen=dfdf
            dataType:"JSON",
            success:function(arg){
                console.log(arg);
                if(arg.status){

                }else{
                    $.each(arg.msg,function(index,value){  #index为字段名，value为错误值                     
                        var tag = document.createElement('span');
                        tag.innerHTML = value[0];
                        tag.className = 'c1';
                        $('#f1').find('input[name="'+ index +'"]').after(tag);
                    })
                }
            }
        })
    }
</script>
```

```python
class LoginForm(Form): #定义Form组件类，用来验证请求数据
    user = fields.CharField(required=True,min_length=6)
    pwd = fields.CharField(min_length=18)
def ajax_login(request):
    import json
    ret={'status':True,'msg':None}
    obj = LoginForm(request.POST)
    if obj.is_valid():
        print(obj.cleaned_data)
    else:
        ret['status']=False
        ret['msg']=obj.errors # 获得字典，k为字段名，v为错误信息列表
    v=json.dumps(ret)
    print(obj.errors) #print时，会自动调用__str__(),在该方法中将字典组装成了<ul>标签
    return HttpResponse(v)
```

### Form组件常见字段
```python
class TestForm(Form):
    t1 = fields.CharField(
        required=True,
        max_length=8,
        min_length=2,
        error_messages={
            'required': '不能为空',
            'max_length': '太长',
            'min_length': '太短',
        }
    )
    t2 = fields.IntegerField(
        min_value=10,
        max_value=1000,
        error_messages={
            'required': 't2不能为空',
            'invalid': 't2格式错误，必须是数字',  # 格式错误
            'min_value': '必须大于10',
            'max_value': '必须小于1000',
        },
    )
    t3 = fields.EmailField(
        error_messages={
            'required': 't3不能为空',
            'invalid': 't3格式错误，必须是邮箱格式',
        }
    )

   # 为空，长度，格式，正则
    t4=fields.URLField()
    t5=fields.SlugField()
    t6=fields.GenericIPAddressField()
    t7=fields.DateField()
    t8=fields.DateTimeField()
    t9=fields.RegexField('139\d+')  # 自定义正则表达式的字段验证规则

widget=widgets.Select, # ******** 用于指定生成怎样的HTML，select，text,input/. # 插件 
label='用户名',  # 在前端可以通过obj.t1.label使用
disabled=False,              # 是否可以编辑
label_suffix='--->',            # Label内容后缀  需要在html模版中添加{{ obj.as_p }}来显示出所有Form类中定义的字段
initial='666',            # 无用，猜测有问题应该在input框中显示默认值
help_text='。。。。。。',  # 提供帮助信息
```

### Form组件之保留上次输入框内容
```html
# 采用Form组件生成的表单组件作为页面标签才能完成保留上次输入框的数据
<form action="/register/" method="POST" novalidate> #novalidate是忽略浏览器的表单验证规则
    {% csrf_token %}
    <p>
        {{ obj.user }} {{ obj.errors.user.0 }}
    </p>
    <p>
        {{ obj.email }} {{ obj.errors.email.0 }}
    </p>
    <p>
        {{ obj.password }} {{ obj.errors.password.0 }}
    </p>
    <p>
        {{ obj.phone }} {{ obj.errors.phone.0 }}
    </p>
    <input type="submit" value="提交"  />
```

```python
class RegiterForm(Form):
    user = fields.CharField(min_length=8)
    email = fields.EmailField()
    password = fields.CharField()
    phone = fields.RegexField('139\d+')
    
def register(request):  
    if request.method == 'GET':
        obj = RegiterForm()
        return render(request,'register.html',{'obj':obj}) # 只返回表单组件类的HTML文本，无数值返回
    else:
        obj = RegiterForm(request.POST) # 因用户提交了数据，Form组件会返回带有输入框值的HTML文本
        if obj.is_valid():
            print(obj.cleaned_data)
        else:
            print(obj.errors)
        return render(request,'register.html',{'obj':obj})
```