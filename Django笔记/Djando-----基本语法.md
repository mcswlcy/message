#   Djando-----基本语法

```python
# 1添加数据二种方法 1.1
# school = School(name='吉利大学')
# school.save()
# 1.2 School.objects.create(name='吉利01')
# 2 查询表里数据 2.1查询所有
# s = School.objects.all()
# for i in s:
# print(i)
# 2.2 根据条件筛选查询 get
# s = School.objects.get(id=3)
# print(s)
# 2.3 filter
# s = School.objects.filter(name='吉利大学')
# print(s)
# 3 修改数据
# s = School.objects.filter(name='吉利大学').first()
# s.name = '武汉大学'
# s.save()
# 3.1 批量修改
# School.objects.filter().update(name='积云教育')
# 4 删除数据
# School.objects.filter(name='武汉大学').delete()
# 4.1批量删除
# School.objects.filter().delete()

# 在清华大学下添加1903A，1903B 班级,跟外建
# s = School.objects.filter(name='清华大学').first()
# grade = Grade(name='1903B')
# grade.school = s
# grade.save()

# 反向查询，班级属于哪个学校（班级作为查询条件，利用隐藏属性）
# s = School.objects.filter(grade__name='1903A').first()
# print(s)

# 正向查询， 查询学校下的所有的班级
# g = Grade.objects.filter(school__name='清华大学').all()
# for i in g:
# print(i)
# 添加学生(先查班级)
# grade = Grade.objects.filter(name='1903A').first()
# stu = Student(name='刘涛')
# stu.grade = grade
# stu.grade_id = grade.id
# stu.save()

# 反向查询，查询学生输入哪个学校的(三张表联查，除了字段都有默认的属性，利用属性查询，__都有一层递增，递减的关系)
# s = School.objects.filter(grade__student__name='胡歌').first()
# print(s)
# 正向查询，查询学校里有哪些学生
# stu = Student.objects.filter(grade__school__name='清华大学')
# for i in stu:
# print(i)

# 查询班级下的所有学生，，，，【查询的结果如果没有调用就不会数据库，只生成了查询条件】
# grade = Grade.objects.filter(name='1903A').first()
# # 正向查询的特性，__set的属性，加.all（取所有），查出所有学生
# for i in grade.student_set.all():
# print(i)
```

# Django-----上传图片

```python
# 配置静态文件夹
STATICFILES_DIRS = [os.path.join(BASE_DIR, 'static')]
# 在 form 表单一定得加   enctype="multipart/form-data"
上传图片导入俩个包
from datetime import datetime
import os
from web.settings import STATICFILES_DIRS


class Addgoods(View):
def get(self,request):
one = Onecate.objects.all()
return render(request,'add_goods.html',locals())
def post(self,request):
cid = request.POST.get('cid')
name = request.POST.get('name')
image_url = request.FILES.get('image_url')
price = request.POST.get('price')
if not all([cid,name,image_url,price]):
mes = '请输入完整信息'
else:

上传图片-------------避免重复增加了时间戳  %Y%m%d%H%M%S%f
image_url_name = datetime.now().strftime("%Y%m%d%H%M%S%f")+image_url.name
打开static的路径配置照片上传的位置，需要在static下面新建一个upload文件夹
f = open(os.path.join(settings.STATICFILES_DIRS[0],'upload',image_url_name),'wb')
二进制流写入图片
for i in image_url.chunks():
f.write(i)
f.close()
two = Twocate(name=name,image_url="/static/upload/"+image_url_name,price=price,onecate_id=cid)
two.save()
return redirect('/tnews_type')
return render(request,'add_goods.html',locals())
```

# Django-----分页展示

```python
分页导包
from django.core.paginator import Paginator
#查询数据库中的这个表的所有数据
onecate = Onecate.objects.all()
# 每页显示几条
page_count = 1
# 当前第几页
try:
p = int(request.POST.get('p'))
except:
p = 1
#利用了paginator内置函数的特性
page = Paginator(onecate,page_count)
onecatelist = page.get_page(p)
totalpage = page.num_pages

HTML代码
引入jquery包
<link rel="stylesheet" href="../../static/admin/css/jquery.pagination.css">
<script src="../../static/admin/js/jquery-1.12.4.min.js"></script>
<script src="../../static/admin/js/jquery.pagination.min.js"></script>

注意顺序
设置一个div
<div id='pagination'>
</div>
-------------------------
<script>
$(function(){
$('#pagination').pagination({
currentPage:{{p}},
totalPage:{{totalpage}},
callback:function(current){
window.location.href='/goods/my_index?p='+current
}

})
})
</script>
{{forloop.counter}}-----序号自增
<img src='{{i.image_url}}'>----图片显示

```

# Django-----路由扩展

```python
1.***在主路由里------include后面---namescape=('')
2.***在子路由里---------app_name = 'goods_app'             name=('')
3.***在函数中跳转--------跳转  return redirect{'主路由 ：子路由'}
4.***<a></a>跳转------<a href='{% url {'主路由 ：子路由'}%}'></a>

• 路由跳转
第一种跳转方式
Return  redirect("/app/hi")----->跳转地址
第二种跳转方式
#主路由 namespace      #子路由：name
Return redirect(reverse('zhu:zi'))
第三种跳转方式
#app_name  : 子路由name
Return redirect(reverse('day02_app:hihi'))


F 对象--- 用于类属性之间的比较
Q对象----用于条件之间的逻辑关系

不用路由分发---例：
from django.contrib import admin
from django.urls import path
from weblist import views

urlpatterns = [
path('admin/', admin.site.urls),
path('Index/', views.Index),
]



路由分发------例：

from django.contrib import admin
from django.urls import path,include

Include 包含子路由

urlpatterns = [
path('admin/', admin.site.urls),
# 路由分发
path('', include('weblist.urls')),
]

子路由
from django.urls import path
from booklist import views
urlpatterns = [
path('add/',views.Adduser.as_view()),
]
```

# Django-----邮件发送

```python
#邮箱发送----------需要正则验证  import re
class Index(View):
def get(self,request):
return HttpResponse('ok')
def post(self,request):
mes = {}
# 1.获取数据
email = request.POST.get('email')
# 2.验证
if not email:
mes['code']=20020
mes['message']='邮箱不能为空'
return JsonResponse(mes)
# 正则验证
ret = '^([a-zA-Z]|[0-9])(\w|\-)+@[a-zA-Z0-9]+\.([a-zA-Z]{2,4})$'
if not re.match(ret,email):
mes['code']=20030
mes['message']='邮箱不能为空格式不合法'
return JsonResponse(mes)
# 3.生成注册码
# 定义验证码的备选值
str = '1234567890QWERTYUIOPASDFGHJKLZXCVBNMqwertyuiopasdfghjklzxcvbnm' # 随机选取4个值作为验证码
rand_str = ''
for i in range(0, 4):
rand_str += str[random.randrange(0, len(str))]
# 存入session
request.session['code']=rand_str
# 4.发送邮件
title = '美多商场-用户注册'
content = '您的注册码是:{}'.format(rand_str)
# 发件人
sender = settings.EMAIL_FORM
# 收件人[]
email_list = [email]
send_mail(title,content,sender,email_list)
mes['code']=200
mes['message']='注册码已经发送邮箱，请查收'
return JsonResponse(mes)


HTML  里面
<!DOCTYPE html>
{#引入静态文件#}
{% load staticfiles %}
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta http-equiv="X-UA-Compatible" content="ie=edge">
<title>注册</title>
<script src="{% static 'admin/js/jquery-1.12.4.min.js' %}"></script>
</head>
<body>
{{mes}}
<form action="" method="POST" id='regform'> {# id 精确到表单位置#}
用户名：<input name='name'><br>
密码：<input name='passwd'><br>
邮箱：<input name='email' id='email'>
{#赋值给id 用ajax刷新数据#}
<button id='btncode'>获取注册码</button><br>
{# 格式不正确则将信息展示出来#}
<label for="" style="color:red" id='msg'></label><br>
注册码：<input name='code'><br>
<button type="submit" id='btnreg' onclick='sub()'>注册</button>
</form>
</body>
<script>
function sub(){
$('msg').text('') 
$('#regform').unbind('submit').submit()
}
$('#btncode').click(function(){ //验证码点击事件
//阻止表单默认提交
$('#regform').submit(function(event){
event.preventDefault()
})
var email = $('#email').val() //逻辑处理
var reg = new RegExp(/^([a-zA-Z]|[0-9])(\w|\-)+@[a-zA-Z0-9]+\.([a-zA-Z]{2,4})$/);
if (!reg.test(email)) //检测（邮箱的输入是否符合正则表达式）字符串是否符合正则表达式
{ //alert('邮箱格式不正确')
$('#msg').text('邮箱格式不正确')
return; //如果验证不通过直接终止函数运行
}
$.ajax({
url:'/index/', //发送地址
type:'post', //请求方式
data:{'email':email}, //参数
dataType:JSON,
success:function(res){
// console.log(res)
alert(res.mes)
}
})
})
//juery 异步刷新调用
</script>
</html>

```

# Django-----多对多

```python
多对多（二级分类，商品表，）
字段--名字---价格-----库存---简介-----详情----图片-----销售量----评论数量-----二级分类的外键----评价

下拉菜单选值俩个--多个---multiple
商品表：Class  goods(models.Models):
名字：    name = models.Charfield(max_length=50)
价格：   price = models.DecimalField(max_digits=10,decimal_place=2)
描述：    desc = models.Charfield(max_length=200)
详情：    content = models.Textfield()
图片：    image_url = models.Charfield(max_length=255)
销售量： sales = models.IntegerField(default=0)
评论数量：comment_count = models.Integer(default=0)
#二级分类外键------->多对多
外键：   Cate_list = models.ManyToManyField(Twocate)

显示表名
     class  Meta:元信息：    db_table = 'goods'

简介：ManyToMany------数据迁移成功后会单独生成一张多对多的表
如若要添加商品且要多处使用添加时的语法为-----两种方法
一、
for i in cid:
c = Twocate.objects.filter(id=i).first()
goods.cate_list.add©

二、
一次性写入
cate_list = Twocate.objects.filter(id__in=cid)
goods.cate_list.set(cate_list)
goods.save()


例子：代码演示

image_url_name = datetime.now().strftime('%Y%m%d%H%M%S%f')+image_url.name
f = open(os.path.join(settings.STATICFILES_DIRS[0],'upload',image_url_name),'wb')
for i in image_url.chunks():
f.write(i)
f.close()

goods=Goods(name=name,price=price,stock=stock,desc=desc,content=content,image_url="/static/upload/"+image_url_name)
goods.save()

需存入表里一次，再执行二次多对多操作

for i in cid:
c = Twocate.objects.filter(id=i).first()
goods.cate_list.add©

# 一次性写入
# cate_list = Twocate.objects.filter(id__in=cid)
# goods.cate_list.set(cate_list)
# goods.save()

### 多对多的基本查询用法
---------------------
创建多对多表
# 1、课程表  多对多表操作
class corsur(models.Model):
    name = models.CharField(max_length=32)
    # 加''表示指定的表，不管指定的表在那个位置。
    stu = models.ManyToManyField(to='Student')
    class Meta:
        db_table = 'corsur'
# 2、书写基础逻辑查询
# 多对多查询
# 学生选择了哪几门课程
def show2(request):
    student = Student.objects.filter(id=1).first()
    print(student.name)
    # 正向查询，学生选择了哪几门的课程
    print([i.name for i in student.corsur_set.all()])
    return HttpResponse('OK')

******假如在学生表，跟课程表中外建设置在课程表，而这时要用学生表进行关联查询、
，就需要内置的属性set进行查询
# 课程被哪些学生选择了
def show3(request):
    cc = corsur.objects.filter(id=4).first()
    print(cc.name)
    # 反向查询，是指课程被哪些学生选定了
    cc_list = cc.stu.all()
    print([i.name for i in cc_list])
    return HttpResponse('OK')
====================================================================================================
# 多对多反序列化添加
# 球员表
class Player(Base,models.Model):
    name = models.CharField(max_length=32)
    price = models.DecimalField(max_digits=7,decimal_places=2)
    class Meta:
        db_table = 'player'

#球队表
class Team(Base,models.Model):
    name = models.CharField(max_length=32)
    price = models.DecimalField(max_digits=7,decimal_places=2)
    pro_for = models.ManyToManyField(to="Player",related_name='teams')
    
    class Meta:
        db_table = 'team'
--------------------------------------------------------------------------------------------------
在反序列化py里
# 反序列化添加球员表
class PlayerUnserializer(serializers.Serializer):
    # 前端获取的字段==也是表里获取字段
    name = serializers.CharField(max_length=32)
    price = serializers.DecimalField(max_digits=7,decimal_places=2)
    tid = serializers.IntegerField()
    def create(self,data):
        # 因为表里没有该字段所有pop弹出
        tid = data.pop('tid')
        # 将数据添加入库
        playobj = Player.objects.create(**data)
        # 查询出该字段的id
        play_id = playobj.id
        # 用tid去她该字段里查询
        teamobj = Team.objects.filter(id=tid).first()
        # 对 多对多的字段进行添加
        teamobj.pro_for.add(play_id)
        return teamobj
```

# Django-----图文混排

```python
1.图文混排（富文本编辑器）
在HTML里面导入三个包
  <script type="text/javascript" src="../../static/admin/js/jquery-1.12.4.min.js"></script>
    <script src='../../static/admin/tinymce/js/tinymce/tinymce.min.js'></script>
    <script src="../../static/admin/js/tinymce_setup.js"></script>

    （1）-----jquery.1.12.4     
     (2)-------tinymce.min.js        
     (3)-------tinymce_setup.js
注意：setup里面：selector : "#rich_content"-----跟hTml 里面的 input 框 id 相对应
# 例：  <input type="text" name='content' id='rich content'>
且注意浏览器有缓存，检查源码查看action提交的位置，-----如果报错---请查看路由路径的 / 是匹配

上传图片的路径-----setup.js里----imageupload_url:路径跟form表单中action提交的位置必须相同
# 注意有没有一级路由

From datetime import datetime
Import os

def upload_img(request):
    mes={}
    #获取方式以files获取file
    image = request.FILES.get('file')
    if image:
        imgae_name = datetime.now().strftime("%Ym%d%H%M%S%f")+image.name
        f = open(os.path.join(settings.STATICFILES_DIRS[0],'upload',imgae_name),'wb')
        for i in image.chunks():
        f.write(i)
        f.close()
        mes['path']="/static/upload/"+imgae_name
        mes['error']=False
    else:
        mes['path']=''
        mes['error']=True
        return JsonResponse(mes)


有俩种返回方式    path----error-----{“error如果为false,则是上传成功”，“error为True,则是上传失败”}

select  name=‘’
option value= '{{i.id}}'
```

# Django-----删除--批量删除

```python
删除-----修改与删除等的操作，都从前台传来一个id获取后执行

class Delete_two_cate(View):
def get(self,request):
id = request.GET.get('id')
try:
	two = Twocate.objects.filter(id=id).delete()
except:
	two = {}
return redirect('/tnews_type')

# 二级分类批量删除
def Pop_two_cate(request):
try:
	id = request.POST.getlist('ids')
except:
	pass
Twocate.objects.filter(id__in=id).delete()
return redirect('/tnews_type')

注：批量删除必须要用getlist获取，相当于存入一个列表里面
批量删除的对应的前台在form表单中
1.action 提交的地址     <form action="/pop_cate/" method="POST">
2.input 框   例：<th width="10%"><input type='checkbox' name='ids' value="{{i.id}}">{{i.id}}</th>
3.button 按钮：   <tr><td><button type='submit'>批量删除</button></td></tr>

```

# Django-----文件配置

```python
生成项目文件  Django-admin startproject    项目名称
子应用        cd 到项目名称下  python manage.py startapp 子应用名称
启动服务    python manage.py runserver

配置文件样式------例：

import os
# Build paths inside the project like this: os.path.join(BASE_DIR, ...)
# 当前项目的绝对路径
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))

# Quick-start development settings - unsuitable for production
# See https://docs.djangoproject.com/en/2.0/howto/deployment/checklist/
# SECURITY WARNING: keep the secret key used in production secret!
# （密钥）*
SECRET_KEY = 'u0^23ja_uityk*d@_6wexobba7!koh4ed(!&n@w9%@lg6zj9oo'
# SECURITY WARNING: don't run with debug turned on in production!
# 调试模式
DEBUG = True
# 允许访问此网站IP,'*'---------'*'可 写 与 不 写
ALLOWED_HOSTS = []

# Application definition
# 已经安装的app，子应用关联到里面
INSTALLED_APPS = [
'django.contrib.admin',
'django.contrib.auth',
'django.contrib.contenttypes',
'django.contrib.sessions',
'django.contrib.messages',
'django.contrib.staticfiles',
'weblist.templatetags',       #过滤器
'rest_framework',            #序列化
'weblist'                    #子应用
    
]
# 中间件------- csrf 先前可以注掉，后期可以再用解决方法
MIDDLEWARE = [
'django.middleware.security.SecurityMiddleware',
'django.contrib.sessions.middleware.SessionMiddleware',
'django.middleware.common.CommonMiddleware',
# 'django.middleware.csrf.CsrfViewMiddleware',
'django.contrib.auth.middleware.AuthenticationMiddleware',
'django.contrib.messages.middleware.MessageMiddleware',
'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

# 根路由，主路由。
ROOT_URLCONF = 'web.urls'
# 模板文件夹配置
TEMPLATES = [
{
'BACKEND': 'django.template.backends.django.DjangoTemplates',
'DIRS': [os.path.join(BASE_DIR, 'templates')],
'APP_DIRS': True,
'OPTIONS': {
'context_processors': [
'django.template.context_processors.debug',
'django.template.context_processors.request',
'django.contrib.auth.context_processors.auth',
'django.contrib.messages.context_processors.messages',
],
},
},
]
WSGI_APPLICATION = 'web.wsgi.application'

# Database
# https://docs.djangoproject.com/en/2.0/ref/settings/#databases

# 数据库--------原先Django自带的数据库 sqlite3 ---使用时还的改成mysql
DATABASES = {
'default': {
'ENGINE': 'django.db.backends.mysql',
'NAME': 'my_weblist',
'HOST':'127.0.0.1',
'POST':3306,
'USER':'root',
'PASSWORD':'',
}
}

# Password validation
# https://docs.djangoproject.com/en/2.0/ref/settings/#auth-password-validators
AUTH_PASSWORD_VALIDATORS = [
{
'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
},
{
'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
},
{
'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
},
{
'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
},
]

# Internationalization
# https://docs.djangoproject.com/en/2.0/topics/i18n/
# 语言改成中文
LANGUAGE_CODE = 'zh-Hans'
# 时区（存数据库的时间不改就是8小时以前的时间）
TIME_ZONE = 'Asia/Shanghai'
USE_I18N = True
USE_L10N = True
# 修改时区：True改成False
USE_TZ = False


# Static files (CSS, JavaScript, Images)
# https://docs.djangoproject.com/en/2.0/howto/static-files/
# 静态文件夹的地址
STATIC_URL = '/static/'
# 配置静态文件夹
STATICFILES_DIRS = [os.path.join(BASE_DIR, 'static')]

# 发送邮件配置
# 1、配置文件
# 发送邮件配置
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'smtp.163.com' # 发送邮件的服务器
EMAIL_PORT = 25 # 发送邮件的端口号
# 发送邮件到邮箱
EMAIL_HOST_USER = 'liujunjie_j@163.com' # 发件人 用户名
# 在邮箱中设置客户到授权密码
EMAIL_HOST_PASSWORD = 'abc123' # 发件人密码
# 收件人看到到发件人
EMAIL_FORM = '天天生鲜<liujunjie_j@163.com>'


# 本人
# 发送邮件配置
# 1、配置文件
# 发送邮件配置
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'smtp.163.com'     # 发送邮件的服务器
EMAIL_PORT = 25                 # 发送邮件的端口号
# 发送邮件到邮箱
EMAIL_HOST_USER = 'liuguoxin_97@163.com' # 发件人 用户名
# 在邮箱中设置客户到授权密码
EMAIL_HOST_PASSWORD = '123456789lgx'           # 发件人密码
# 收件人看到到发件人
EMAIL_FORM = '天天生鲜<liuguoxin_97@163.com>'
```

# Django-----序列化--jwt

```python
安装命令
1.  pip install djangorestframework
2. from rest_framework.views import APIView
	

2.引入--rest_framework---->下划线，需要在配置文件夹app处的地方加入

序列化的类---将python数据类型转换成josn字符串
3.单写一个py文件  myserializers.py
导包：  from  rest-framework import  serializers

在 py 文件里从数据获取想要的数据后，需要通过前台vue渲染页面，所以得转为json字符串--（而序列化就是把python转成json数据的，反序列化是把json转为python）---序列化的三种方式

from mylist.serializers import * 

一、
# 原始方式----（过渡）----了解使用
class GoodsSerializer(serializers.Serializer):
id = serializers.IntegerField()
name = serializers.CharField()
price = serializers.DecimalField(max_digits=7,decimal_places=2)
comment = serializers.CharField()
		|
		|
# 第一种 了解使用
# def goods(request):
# # 序列化所有的时候，用many=True,all(),----------一条使用many=False,first()
# goods = Goods.objects.all()
# s = GoodsSerializer(goods,many=True)
# mes = {}
# mes['code']=200
# mes['message']=s.data
# return JsonResponse(mes)


二、
Class GoodsModelSerializer(serializers.ModelSerializer):
class Meta:
model = Goods
# fields = '__all__'
# 指明想要的字段
fields = ['id','name','price','comment']
			|
			|
三、
# 第二种、广泛使用
def goods(request):
# 序列化所有的时候，用many=True,all(),----------一条使用many=False,first()
goods = Goods.objects.all()
s = GoodsModelSerializer(goods,many=True)
mes = {}
mes['code']=200
mes['message']=s.data
return JsonResponse(mes)


# 第三种、原始使用

# 因向接口传送数据---vue接收的数据是json形式，因此需要to_dict()进行类型转换，code码进行传递确认
# to_dict()------->第一种方式
# def goods(request):
# goods = Goods.objects.all()
# mes = {}
# mes['code']=200
# goodslist = []
# for i in goods:
# goodslist.append(i.to_dict())
# mes['message']=goodslist
# return JsonResponse(mes)

def to_dict(self):
return {'id':self.id,'name':self.name,'price':self.price,'comment':self.comment}


--------------------------------------------------------------------------------------
# 展示购物车列表页--------将产品存入购物车后想在页面展示出来通过cart表里外建id序列化获取想要的数据
class MycartModelSerializer(serializers.ModelSerializer):
    is_checked = serializers.BooleanField(default=True)
    name = serializers.CharField(source='goods_id.name')
    image_url = serializers.CharField(source='goods_id.image_url')
    price = serializers.CharField(source='goods_id.price')

    class Meta:
        model = Cart
        fields = '__all__'

```

#Django-----跨域配置&axios

```python
一、
vue跨域配制（第一种方法）
在config文件夹下的index.js中配制
    proxyTable: {
    '/api': {  //使用"/api"来代替"http://f.apiplus.c" 
    target: 'http://127.0.0.1:8000/', //源地址 
    changeOrigin: true, //改变源 
    pathRewrite: { 
      '^/api': '' //路径重写 
      } 
  } 
},

二、
跨域（第二种方法）
用Django的第三方包 django-cors-headers 来解决跨域问题
操作步骤：
1.pip install django-cors-headers
2.在settings.py中添加'corsheaders.middleware.CorsMiddleware',在SessionMiddleware和CommonMiddleware的中间
3.在settings.py中添加CORS_ORIGIN_ALLOW_ALL = True

三、
axios使用
axios完整写法：
this.axios({
  method: 'post',
  url: '/user/12345',
  data: {
    firstName: 'Fred',
    lastName: 'Flintstone'
  }
}).then((res)=>{
      console.log(res)
}).catch((error)=>{
      console.log(error)
 });
post请求
this.axios.post('',{}).then((res)=>{}).catch((error)=>{})
get请求
axios.get('/user?ID=12345')
.then((response)=> {
  console.log(response);
})
.catch((error)=> {
  console.log(error);
});

在main.js里配置，该两行
import axios from 'axios'
Vue.prototype.axios = axios   //其它组件可以使用this.axios

```

# Django-----验证码

```python
一、需要把captcha的文件夹，复制到本Django项目目录下,utils文件夹里
from utils.captcha.captcha import captcha

# 验证码----------图片
class ImageView(View):
def get(self,request):
# text------->图片中的文本
# image---------->图片
name,text,image = captcha.generate_captcha()
# 将验证码存入session中，注册时进行比对
request.session['imagecode'] = text
return HttpResponse(image,content_type='image/jpg')


	1. vue页面-----点击图片随机出现二维码
<imgsrc="/api/web/imageview"onclick='this.src=this.src+"?"+Math.random()'alt="图形验证码"class="pic_code">
```

# Django-----多对多示例查询

```python
例--示范
# 电影表
class Move(models.Model):
name = models.CharField(max_length=50)
image_url = models.CharField(max_length=255)
class Meta:
db_table = 'move'
def __str__(self):
return self.name

# 演员表
class Action(models.Model):
name = models.CharField(max_length=50)
age = models.IntegerField()
gender = models.BooleanField(default=0)
move = models.ManyToManyField(Move,related_name='actor')
class Meta:
db_table = 'Action'
def __str__(self):
return self.name

ManyToManyField  ----写完迁移时会自动生成一张第三张表
 


	1. 借助多对多查询时用--------related_name='actor'
	2. 电影查询演员-----只需获取电影的id，用电影的id查询表first--
	# 电影id获取
	ids = request.GET.get('id')
	move = Move.objects.filter(id=ids).first()
	
	
	Html   里代码：---可以说，电影被谁主演是反查询
	{% for i in move.actor.all %}
	<tr>
	<td><a href="/goods/move_all?id={{i.id}}">			{{i.name}}</a></td>
	<td>{{i.age}}</td>
	<td>{% if i.gender == 1%}男
	{% else %}女
	{% endif %}</td>
	</tr>
	{% endfor %}
	
	
	3. 借助多对多查询时用--------move字段
	4. 查询演员拍的电影------只需要获取演员的id，用演员的id查询演员表first
	# 演员id获取
	ids = request.GET.get('id')
	action = Action.objects.filter(id=ids).first()
	
	Html   里代码：---可以说，电影被谁主演是正查询
	<h1>{{action.name}}主演的电影有</h1>
	
	{% for i in action.move.all %}
	{{i.name}}
{% endfor %}
```

# Django----模板继承&过滤器

```python
Django 模板继承
(1)、在一个完整的HTML里面写入
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta http-equiv="X-UA-Compatible" content="ie=edge">
<title>Document</title>
</head>
<body>
{% block head %}-------------->继承的格式（有开始，有结束）------（继承头部）
<a href="#">电器</a>
<a href="#">日用品</a>    可以是动态的 
<a href="#">水果</a>
{% endblock head %} -------------->(结束头部继承)
<hr>
{% block body %}-------------->继承的格式（有开始，有结束）------（继承内容）
{% endblock body %}-------------->(结束内容继承)
<hr>
{% block foot %}-------------->继承的格式（有开始，有结束）------（继承尾部）
积云教育成立三年
{% endblock foot %}-------------->(结束尾部继承)
</body>
</html>

(2)、在另一个HTML里面只需写入
1.---  {% extends 'base.html' %}------>固定格式加引号
2.--- 只需要写自己要继承哪个部分的内容即可，如-
{% block body%}
内容------内容
{% endblock body %}



二、过滤器

1、Safe:  禁用转义，应用场景---可以把带标签或者富文本编辑器的内容显示出来就可以用    例---{{ content | safe }}
2、模板内置变量：{{  forloop.counter  }} -----显示序号
3、列表（元组，字典）长度：{{ list | length}}
4、默认值：{{ abc | default:'你好--默认值'}}---abc没有的情况下

自定义过滤器   |  竖线左函数，右过滤器
1、在Django的子应用中，创建一个文件夹templatetags（templatetags的文件夹中包含__init__文件)。
2、再在templatetags的文件夹中创建一个自定义py文件
（1）：导包：from django.template import Library
（2）：创建实例--括号：register = Library()
@register.filter('addyuan')
（3）：def AddYuan(value):
			Return str(value) + '元'

在HTML里用过滤器需要{% load  myfilter  %}
myfilter代表templatetags中的自定义的一个文件，
然后就可以用别名----addyuan----例
{{123 | addyuan }}
------还需要在settings里apps部分里把templatetags导进来
weblist.templatetags



自定义标签
From datetime import datetime
1. 简单标签--------输出系统当前时间
装饰：@register.simple_tag()
括号里不指定默认---time_format
函数：def  thistime(time_format):
返回：       return datetime.now().strftime(time_format)
HTML里：{% thistime '%Y-%m-%d  %H:%M:%S%p'%}

（2）：
装饰：@register.simple_tag(takes_context=True)
Def  demo(context):
	Return  context['message']  + '|今天天气不错'
message在py文件将内容写好，在HTML输出
{% demo %}  会输出原有的+|今天天气不错
2.内含标签
装饰：@register.inclusion_tag('goods.html')
函数：def viewgoods():
			List = ['苹果','香蕉','梨']
			Return {'abc':list}
新建模板goods.html显示商品列表
显示：{% for I in abc %} {{i}} {% endfor %}
注意：{%  viewgoods %}写在继承模板的里面可以利用goods.html把数据渲染出来

```



# Django-----vue结合上传图片

```python
<div id="goods">
商品名称：<input type="text" v-model="name"><br>
商品价格：<input type="text" v-model="price"><br>
商品图片：<input type="file" id="saveimage"><br>
<button @click="addgoods()">添加</button>
</div>
 
methods:{
            addgoods:function () {
                var user_id = sessionStorage.getItem('id');
                this.user_id = user_id;
                var data = new FormData();
                data.append('name',this.name);
                data.append('price',this.price);
                data.append('id',this.user_id);
                //1.从input框里获取图片
                var img = document.getElementById('saveimage').files[0];
                //2.将图片添加到Formdata中
                data.append('file',img,img.name);
                //3.发送axios消息，请求头添加 ： Content-Type = multipart/form-data
                this.axios({
                    url:'/api/api/addgoods/',
                    method:'post',
                    data:data,
                    headers:{'Content-Type':'multipart/form-data'}
                }).then((res)=>{
                    console.log(res)
                    if(res.data.code==200){
                        this.$router.push({'path':'myindex'})
                    }
                }).catch((err)=>{
                    console.log(err)
                })
            }
        }
    
    
py文件里
# 添加商品
class Addgoods(APIView):
    def post(self,request):
        mes={}
        id = request.data.get('id')
        name = request.data.get('name')
        price = request.data.get('price')
        image = request.FILES.get('file')
        if not all([name,price,image]):
            mes['code']=22600
            mes['message']='信息不完整'
        else:
            image_name = datetime.now().strftime('%Y%m%d%H%M%S%f')+image.name
            f = open(os.path.join(settings.UPLOAD_FILE,image_name),'wb')
            for i in image.chunks():
                f.write(i)
            f.close()
            #注意图片上传的路经
            goods = Goods(name=name,price=price,image_url='http://127.0.0.1:8000/upload/'+image_name,shop_id=id) 
            goods.save()
            mes['code']=200
            mes['message']='添加成功'
        return JsonResponse(mes)
    
#配置上传图片路径
#  1、settings 里    # 图片上传目录-----自定义图片的目录不能与系统名重合
UPLOAD_FILE = os.path.join(BASE_DIR,'upload')
# 2、主路由urls里  配置   
#导两个包
from django.views.static import serve
from book import settings
#路由书写
# 将upload文件夹，共享浏览器访问
path('upload/<path>',serve,{'document_root':settings.UPLOAD_FILE})
```

 

# Django----JSON数据添加

```python
#店主表
class Shop(models.Model):
    name = models.CharField(max_length=50)
    passwd = models.CharField(max_length=255)
    class Meta:
        db_table = 'shop'
#商品表
class Goods(models.Model):
    name = models.CharField(max_length=50)
    price = models.DecimalField(max_digits=7,decimal_places=2)
    image_url = models.CharField(max_length=255)
    shop = models.ForeignKey(Shop,on_delete=models.CASCADE,related_name='goods')
    class Meta:
        db_table = 'goods'
        
#如果要查看店主表下的商品直接方法就是序列化，格式添加
#序列化商品表
class GoodsModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = Goods
        fields = '__all__'

#序列化店主表
class ShopModelSerializer(serializers.ModelSerializer):
    #goods的变量名必须跟商品表里的related_name='goods'，goods一样，直接书写商品序列化many=True,就会生成json字符串，然后直接书写业务逻辑即可
    goods = GoodsModelSerializer(many=True)
    class Meta:
        model = Shop
        fields = '__all__'

```

# Django-----一擦特渲染

```python
# 用户统计模块
from datetime import datetime,timedelta
def user_Count(request):
    # 1.一共有多少用户
    tcount = Admins.objects.count()

    # 2.当月注册用户总数，条件（create_time开始时间，这个月1号到现在）
    start_time = datetime.strftime(datetime.now(),'%Y-%m-01')
    mcount = Admins.objects.filter(create_time__gte=start_time,create_time__lte=datetime.now()).count()

    # 3.当日注册用户总数
    start_time = datetime.strftime(datetime.now(),'%Y-%m-%d')
    dcount = Admins.objects.filter(create_time__gte=start_time,create_time__lte=datetime.now()).count()
    count_list = []
    # 时间
    time_list = []
    # 4. 30天内每天用户注册的人数,(timedelta,--->日期类型(datetime))
    # strptime 字符串转成对象，strftime把对象转成字符串
    for i in range(30,0,-1):
        stime = datetime.strptime(start_time,'%Y-%m-%d') - timedelta(i)
        endtime = datetime.strptime(start_time,'%Y-%m-%d') - timedelta(i-1)
        count = Admins.objects.filter(create_time__gte=stime,create_time__lte=endtime).count()
        count_list.append(count)
        time_list.append(datetime.strftime(stime,'%Y-%m-%d'))
    return render(request,'user_count.html',locals())
```

# Djando----init配置

```python
import pymysql
pymysql.install_as_MySQLdb()

# 关系型数据库：mysql，sqlite,oracle,sqlserver,postgresql
# 非关系型数据库：redis,memcache,mongodb
```

# Django---序列化与反序列化{小}

```python
# 1. 反序列化----单表入库
class Addcate(APIView):
    def post(self,request):
        # 用postman进行存入数据
        data = request.data
        # 把定义好的反序列化拿来进行存入数据，也就是把获取的数据转成python串
        cate = CateUnserializer(data=data)
        # 判断cate值
        if cate.is_valid():
            cate.save()
            return HttpResponse('OK')
        return HttpResponse('不OK')
# 1). 反序列化  添加分类
# 序列化导包
from rest_framework import serializers
class CateUnserializer(serializers.Serializer):
    # 反序列化是 serializers.Serializer
    name = serializers.CharField(max_length=32)
    # 添加
    # data = {'name':'dsds','pwd':123}
    # **data  => name='dsds',pwd=123
    def create(self,data):
        return Cate.objects.create(**data)

---------------------------------------------------------------------------------------------------
# 2. 添加商品，且所属分类 
class AddGoods(APIView):
    def post(self,request):
        # 例，只是把data定义成一个固定的值，进行存储
        #** data = {'name':'oppo','cates':1}
        data = {'name':'oppo','cates_id':1}
        goods = GoodsUnserializer(data=data)
        if goods.is_valid():
            goods.save()
            return HttpResponse('OK')
        return HttpResponse('不OK')

#2). 反序列化 --出库----添加商品
class GoodsUnserializer(serializers.Serializer):
    name = serializers.CharField(max_length=32)
    # 在我们的商品表里有外建字段，是int类型
    #** cates = serializers.IntegerField()
    cates_id = serializers.IntegerField()

    # 添加商品、
    def create(self,data):
        # return Goods.objects.create(
            #** 
           #  cates_id = data.pop('cates'),**data)
    	 return Goods.objects.create(**data)
        
    # 修改商品---->包含三个参数（self,instance,data)
    def update(self,instance,data):
        # instance == Goods.objects.filter(id=2)
        return instance.update(**data)
    
---------------------------------------------------------------------------------------------------
# 修改制定字段的类型
    def put(self,request):
        goods = Goods.objects.filter(id=1)
        data = {'name':'iphone','cates':2}
        g = GoodsUnserializer(goods,data=data)
        if g.is_valid():
            g.save()
            return HttpResponse('YES')
        return HttpResponse('NO')
----------------------------------------------------------------------------------------------------
# 4*用序列化查询分类
===== views.py =====
class Getcate(APIView):
    def get(self,request):
        # 查询出id为1的分类
        cate = Cate.objects.filter(id=1).first()
        # 查询的结果为first()，就是many=False,也可以不用书写
        # 查询的结果为all()，就是many=True
        ser = CateSerializer(cate)
        return JsonResponse(ser.data)
# 4* 序列化--出库 ------  cate
class CateSerializer(serializers.ModelSerializer):
    class Meta:
        model = Cate
        fields = '__all__'
        # fields = ("name",) # 保证是一个元组，或列表
        # exclude = ("name",) # 除了Name之外的所有字段
---------------------------------------------------------------------------------------------------
# 用序列化查询商品，并且查询属于谁的分类
class Getgoods(APIView):
    def get(self,request):
        # 查询出id为2的商品
        goods = Goods.objects.filter(id=2).first()
        # 进行序列化转数据类型
        ser = GoodsSerializer(goods)
        return JsonResponse(ser.data)
# 商品的序列化
class GoodsSerializer(serializers.ModelSerializer):
    # 当查询的数据有外建指向，且想显示出姓名，就可以用下面方法利用外建属性直接指定
    catems = serializers.CharField(source='cates.name')
    class Meta:
        model = Goods
        fields = ('catems','id','name')
        
 # 当我们查询的需求在俩个序列化里，我们可以通过以下的方法导进来
 # goods = GoodsModelSerializer(many=True)就可以使用了,
 # 他最后的数据类型是[{}]
#class ShopModelSerializer(serializers.ModelSerializer):
    # goods = GoodsModelSerializer(many=True)
    # class Meta:
    #     model = Shop
    #     fields = '__all__'
```

# Django----邮件发送验证{小}

```python
-----------------------------------------------------------------------------------------------------
# 导入已封装好的函数-- 也成工具文件夹
from utils.sha2203 import *
import hashlib
import random
import re
# 封装sha256
def sha56(raw):
    # 实例化sha256对象
    sha56 = hashlib.sha256()
    # 把要加密的密码，进行update操作并转成utf-8,不写默认是utf-8
    sha56.update(raw.encode('utf-8'))
    # 返回一个sha56的16进制的格式
    return sha56.hexdigest()



# 加盐，随机盐
def random_safe(n=5):
    # 定义一个简单的字符串
    str = '23456789abcdefgjkmnpqrestuvwxyz'
    # 定义一个空值，将每次循环的随机数，拼接到ret上。然后返回。
    ret = ''
    for i in range(n):
        # len出字符串的长度，利用下标进行随机抽出。
        ret += str[random.randint(0,30)]
    return ret


# 正则验证邮箱
def check_email(email):
    ret = re.match(r'(\w+)@(\w+)\.(\w){2,}$',email)
    if ret:
        # 满足条件
        return True
    else:
        # 不满足条件
        return False

# 正则验证密码
def check_passwd(password):
    pattern = re.match("^(?=.*[a-zA-Z].*)(?=.*[\d].*)(?=.*_.*).{6}$",password)
    if pattern:
        return True
    else:
        return False


-----------------------------------------------------------------------------------------------------
 # 实现从前端获取的数据进行序列化操作后，执行发送邮件
    ************************ 公共部分 *****************
    if user_ser.is_valid():
            user_ser.save()
            code = random_safe()   # 发邮件验证码
            self.storge_code(eml,code)   # 把验证码存入redis里
            
            self.send_mailx(code)    # 发邮件
            return Response({'code':200,'message':'注册成功'})
----------------------------------------------------------------------------------------------------
# 1 #
settings.py配置
# 邮件参数设置 

# 邮件---
EMAIL_HOST = 'smtp.163.com'
# 设置端口号，为数字
EMAIL_PORT = 25
#设置发件人邮箱
EMAIL_HOST_USER = 'liuguoxin_97@163.com'
# 设置发件人 授权码
EMAIL_HOST_PASSWORD = '123456789lgx'
# 设置是否启用安全链接
EMAIL_USER_TLS = True
EMAIL_USER_TLS = False
DEFAULT_FROM_EMAIL = EMAIL_HOST_USER

# 2 #
views.py里
# 发邮件，导入send_mail,在Django的底层已封装好。
from django.core.mail import send_mail

# 可以在某个方法调用执行self.send_mailx()即可发送邮件。
 # 利用面向对象写邮件发送
    def send_mailx(self,code):
        # code = random_safe()   # 发邮件验证码
        # 发邮件的参数 1.主题，2.内容，3.发件人,4.收件人
        html = '您的验证码：{}'.format(code)     # 导进来的是实例，所以要加（）
        return send_mail(
            '实验楼注册邮箱验证码',
            html,
            'liuguoxin_97@163.com',
            ['1816668038@qq.com'] 
        )
 
-----------------------------------------------------------------------------------------------------

##### 如何存入redis库{redis是存储在服务器的内存中，它支持的数据类型比较多}
from django.core.cache import cache
 # 将验证码存在redis中 
    # 在storge_code函数的参数中有俩个参数（自己定义的）{也可以是多个【从上面----传来的 ---- 】}
    def storge_code(self,eml,code):
        # 怎么给redis设置，set cacha.set()   俩个值key values
        key = 'jihuo_'+eml
        cache.set(key,code)
  
    def get(self,request):
        # cache从redis用get拿出所查数据
        tmp = cache.get('jihuo_1816668038@qq.com')
        print(tmp)
        return HttpResponse(tmp)
```

# Md5加密

```python
# 加密
def md5(raw):
    import hashlib
    # md5加密实例化
    md5 = hashlib.md5()
    # 需要的话进行utf8编码
    md5.update(raw.encode('utf8'))
    return md5.hexdigest()
```

# sha256加密

```python
import hashlib
import random
import re
# 封装sha256
def sha56(raw):
    # 实例化sha256对象
    sha56 = hashlib.sha256()
    # 把要加密的密码，进行update操作并转成utf-8,不写默认是utf-8
    sha56.update(raw.encode('utf-8'))
    # 返回一个sha56的16进制的格式
    return sha56.hexdigest()



# 加盐，随机盐
def random_safe(n=5):
    # 定义一个简单的字符串
    str = '23456789abcdefgjkmnpqrestuvwxyz'
    # 定义一个空值，将每次循环的随机数，拼接到ret上。然后返回。
    ret = ''
    for i in range(n):
        # len出字符串的长度，利用下标进行随机抽出。
        ret += str[random.randint(0,30)]
    return ret


# 正则验证邮箱
def check_email(email):
    ret = re.match(r'(\w+)@(\w+)\.(\w){2,}$',email)
    if ret:
        # 满足条件
        return True
    else:
        # 不满足条件
        return False

# 正则验证密码
def check_passwd(password):
    pattern = re.match("^(?=.*[a-zA-Z].*)(?=.*[\d].*)(?=.*_.*).{6}$",password)
    if pattern:
        return True
    else:
        return False
```

# 时间戳封装

```python
import random
from datetime import datetime
def generater_img(ext='pag'):
    # 格式化时间+随机数+文件的后缀名
    return datetime.now().strftime('%Y%m%d%H%M%S') + str(random.randint(10000,99999)) + '.' + ext
```

# 删除外键关系

```python
Django取消级联删除----删除外键关系
user = models.ForeignKey(User,blank=True,null=True,on_delete=models.SET_NULL)
并且SET_NULL只有在null为True的时候,才可以使用

# 删除学生外键关系,根据id把相应的数据查询出来后,把外建改成None就OK了
class Pop_student(APIView):
    def get(self,request):
        mes={}
        id = request.GET.get('id')
        stu = Student.objects.filter(id=id).first()
        stu.teacher_id=None
        stu.save()
        mes['code']=200
        mes['message']='删除成功'
        return JsonResponse(mes)
```

















