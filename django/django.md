# 						 Django基础命令



在Django的使用过程中需要使用命令让Django进行一些操作，例如创建Django项目，启动Django程序，创建新的APP，数据库迁移等。



## 1、创建Django项目

新建一个文件夹来存放项目文件，切换到这个目录下，启动命令行工具，创建一个名为mysite的Django项目：

> django-admin startproject mysite

创建好项目之后，可以查看当前目录下多出一个名为mysite的文件夹，mysite的文件夹目录结构如下：

 

```
mysite``/``　　
manage.py``　　
mysite``/``　　　　　
_init_.py``　　　　　
settings.py``　　　　　
urls.py``　　　　　
wsgi.py
```



## 2、启动Django项目

启动项目的时候，需要切换到mysite目录下，执行如下命令：

> ```
> python manage.py runserver      #默认使用8000端口
> ```

命令后面还可以指定参数：

> ```
> python manage.py runserver 8888     #8888为新指定的端口
> python manage.py runserver 127.0.0.1:8000   #还可以指定IP和端口，冒号分割
> ```



## 3、创建APP

一个Django项目可以分为很多个APP，用来隔离不同功能模块的代码。
命令行创建：

> ```
> python manage.py startapp app01
> ```

执行命令后，项目目录下多出一个app01的文件夹，目录机构如下：

 

```
app01``/``  ``migrations``    ``__init__.py``  ``__init__.py``  ``admin.py``  ``apps.py``  ``models.py``  ``tests.py``  ``views.py
```

## 4、使用PyCharm创建app

可以使用PyCharm的manage.py工具来执行命名。

在主菜单栏中选择【Tools】，在下拉菜单中选择【Run [manage.py](http://manage.py/) task】,会出现如下图所示的工具对话框： 

[![img](https://img2018.cnblogs.com/blog/1456293/201811/1456293-20181118174523065-917659992.png)](https://img2018.cnblogs.com/blog/1456293/201811/1456293-20181118174523065-917659992.png)

[![img](https://img2018.cnblogs.com/blog/1456293/201811/1456293-20181118174531548-400617543.png)](https://img2018.cnblogs.com/blog/1456293/201811/1456293-20181118174531548-400617543.png)

 

在弹出的命令窗口中直接输入下面的命令便可创建app：

> startapp app01

使用PyCharm的manage.py工具执行命令时，只用输入命令及参数即可，不用再输入【python [manage.py](http://manage.py/)】了。



## 5、数据迁移

> python manage.py makemigrations
>
> python manage.py migrate

本质是将django的各种模型转化到数据库中实现



## 6、创建超级用户

> python manage.py createsuperuser

 输入以上命令后，根据提示输入用户名，邮箱，密码，确认密码。
密码的要求至少有8位，不能和邮箱太接近，两次密码需要一致。

bysms的超级账号   账号tyrion,密码root



## 7、路由映射

项目文件夹下, 非app文件夹下的urls文件

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('sales/', include("mgr.urls")),
    path('api/mgr/', include("mgr.urls")),
]
```



将各个app文件夹中的url文件引入即可



## 8、dvadmin初始化



##### 后端部分初始化

1. 进入后端项目目录:`cd dvadmin-backend`

2. 在项目根目录中，复制 `./conf/env.example.py` 文件为一份新的到 `./conf` 文件夹下，并重命名为`env.py`

3. 在 `env.py` 中配置数据库信息

4. 安装依赖环境: `pip install -r requirements.txt`

5. 执行迁移命令: `python manage.py makemigrations` `python3 manage.py migrate`

6. 初始化数据: `python manage.py init`

7. 启动项目: `python manage.py runserver 127.0.0.1:8000`(如果是服务器或虚拟机安装，把 127.0.0.1:8000 改成 0.0.0.0:8000)

8. 定时任务启动命令:

    

   ```
   celery -A application worker -B --loglevel=info
   ```

   - 注：
     - Windows 运行celery 需要安装: `pip install eventlet`
     - `celery -A application worker -P eventlet --loglevel=info`

   **备注**

   - 修改数据库的配置
   - 修改



**使用创建超级用户的命令**

```
用户账号: dfcvnl
邮箱: zhangleizl@dfcv.com.cn
Password:
Password (again):
密码长度太短。密码必须包含至少 8 个字符。
这个密码太常见了。
密码只包含数字。
Bypass password validation and create user anyway? [y/N]: y
Superuser created successfully.
```



## 9、文件脚本的持久化

目前没有比较好的脚本处理策略,只能刷一下文件夹,将数据录入到数据库中形成映射

1. 代码格式的规范
2. 代码的文件夹管理规范
3. 代码的



只能使用脚本来解析本地的文件夹获取文件目录,将对应的文件夹信息同步到数据库中

```python
import os

# 遍历文件夹
def walkFile(file):
    for root, dirs, files in os.walk(file):

        # root 表示当前正在访问的文件夹路径
        # dirs 表示该文件夹下的子目录名list
        # files 表示该文件夹下的文件list

        # 遍历文件
        for f in files:
            print(os.path.join(root, f))

        # 遍历所有的文件夹
        # for d in dirs:
        #     print(os.path.join(root, d))


def main():
    walkFile("D:/doc/各种资料讲义/重学前端/")


if __name__ == '__main__':
    main()
```





和黄强确认 

工厂内改码会不会该配置?



## 10、创建app

创建一个demo的菜单

```
python manage.py createapp demo
```



## 11、创建models模型

内容

```python
from django.conf import settings
from django.db.models import CharField, ForeignKey, CASCADE, SET_NULL

from vadmin.op_drf.models import CoreModel


# 继承框架封装的 模型类 CoreModel
class Demo(CoreModel):
    name = CharField(max_length=8, verbose_name='项目名称')
    code = CharField(max_length=8, verbose_name='项目编码')
    # 在关联用户时，建议使用 to=settings.AUTH_USER_MODEL 进行关联
    person = ForeignKey(to=settings.AUTH_USER_MODEL, null=True, verbose_name='项目负责人', related_name='project_person',
                        on_delete=SET_NULL, db_constraint=False)
    # 在普通一多一、一对多、多对多时，to='App名.模块名' 进行关联
    # on_delete=CASCADE级联删除
    # verbose_name字段名称
    # db_constraint=False 外键约束
    dept = ForeignKey(to='permission.dept', on_delete=CASCADE, verbose_name="项目所属部门", related_name='project_dept',
                      db_constraint=False)

    class Meta:
        verbose_name = '项目管理'
        verbose_name_plural = verbose_name

    def __str__(self):
        return f"{self.name} 项目"


```



PS: django**多表的使用**

```
我先定义两个模型，一个是作者，一个是作者出版的书籍，算是一对多的类型。

class Person(models.Model);
    name = models.CharField(verbose_name='作者姓名', max_length=10)
    age = models.IntegerField(verbose_name='作者年龄')


class Book(models.Model):
    person = models.ForeignKey(Person, related_name='person_book')
    title = models.CharField(verbose_name='书籍名称', max_length=10)
    pubtime = models.DateField(verbose_name='出版时间')


如果我们要查询一个作者出版了哪些书籍的话，那我们要怎么做呢？
即通过一查询出多的那方面

先查询到作者的信息
person = Person.objects.fiter(你的条件)
返回一个person对象

接下来就查询person关联的所有书籍对象，我们在前面讲过的使用基于对象的查询方式,反向查询按照表名
book = person.book_set.all()
django 默认每个主表的对象都有一个是外键的属性，可以通过它来查询到所有属于主表的子表的信息。
这个属性的名称默认是以子表的名称小写加上_set()来表示，默认返回的是一个querydict对象，你可以继续的根据情况来查询等操作。

在实际项目中，我们使用最多的还是related_name
如果你觉得上面的定义比较麻烦的话，你也可以在定义主表的外键的时候，给这个外键定义好一个名称。要用related_name比如在Book表中：
  person = models.ForeignKey(Person, related_name='person_books')
那么实现上面的需求，可以使用person.book_set.all()
也可以使用person.person_books.all()
```



导入数据模型

```python
from .demo import Demo
```
