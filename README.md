# Dailyfresh
基于`python3.7`和`Django1.8`的天天生鲜商城。

## 主要功能模块
- 用户模块
    - 用户的登录、注册、邮件激活、退出以及个人中心和个人地址  

- 商品模块
    - 分为首页、详情页、列表页、以及全局搜索功能

- 购物车模块
    - 商品的增加、删除、修改、查询

- 订单模块
    - 确认订单、提交订单

## 安装
通过下面的命令把需要用到的包打包安装下来

        ` pip install  -r requirements.txt`

## 配置
- 项目使用到了redis作为缓存， 需要自行配置一下redis
   修改`dailyfresh/setting.py` 修改缓存配置，如下所示：
   ```python
   CACHES = {
    "default": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://IP地址:6379/7",
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
        }
    }
    }
   ```


- 注册的邮件发送使用到了Celery（分布式任务队列）
    修改`dailyfresh/celery_tasks/tasks.py` 中的broker
    ```python
    app = Celery('celery_tasks.tasks', broker='redis://IP地址:6379/8')
    ```


- 上传图片使用的是Fastdfs(分布式文件系统)，自行配置
    修改`dailyfresh/setting.py` 修改
        
    ```python
    FDFS_URL = 'http://IP地址:8888/'
    ```

- 配置Nginx
    在nginx.conf 中新添任务：
    ```shell
    server {
            listen       8888;
            server_name  localhost;
            location ~/group[0-9]/ {
                ngx_fastdfs_module;
            }
            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
                root   html;
            }
        }

    ```

- 配置全文检索框架
    在`dailyfresh/setting.py` 修改
    ```python
    HAYSTACK_CONNECTIONS = {
        'default': {
            #使用whoosh引擎
            'ENGINE': 'haystack.backends.whoosh_cn_backend.WhooshEngine',
            #索引文件路径
            'PATH': os.path.join(BASE_DIR, 'whoosh_index'),
        }
    }
    ```
    我使用的是jieba分词，修改了原本whoosh_backend为whoosh_cn_backend
    如果不想修改则使用默认的whoosh_backend


## 运行

 修改`dailyfresh/setting.py` 修改数据库配置，如下所示：
    
   ```python
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.mysql',
            'NAME': 'dailyfresh',   # 你的数据库名
            'USER': 'root',     # 你的数据库账户
            'PASSWORD': 'password',     # 数据库对应的密码  
            'HOST': 'localhost',     # 本地测试添loaclhost
            'PORT': 3306,       # 默认3306
        }
    }    
   ```

### 创建数据库
- mysql创建数据库:
```sql
CREATE DATABASE `dailyfresh` 
```
- mysql导入数据库
```
mysqldump -uroot -p dailyfresh < dailyfresh.sql 
```
- 然后在终端执行
```
python manage.py makemigrations
python manage.py migrate
```

### 创建超级用户
- 终端执行
```
python manage.py createsuperuser
```
### 开始运行：
 执行：
 `python manage.py runserver`


最后一步打开浏览器输入：http://127.0.0.1:8000/  就可以看到效果啦。




### 写的比较懒散，中间可能会有什么问题，欢迎发送邮件到我的邮箱来提问：maxhjh@outlook.com
