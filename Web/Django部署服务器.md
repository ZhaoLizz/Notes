# 利用Nginx和Gunicorn部署Django

* 服务器环境:Ubuntu16.04
* 参考追梦人物博客: https://www.zmrenwu.com/post/20/#c3124
* 记录一下我踩的坑,**仅用作个人笔记**,方便以后配置环境

## 一.配置服务器

### **创建用户**

* **部署项目一定不能用root用户,否则会出现nginx没有权限访问root用户创建的Django项目的静态文件,导致网页的静态文件加载不出来**

```bash
# 在 root 用户下运行这条命令创建一个新用户，yangxg 是用户名
# 因为我叫杨学光，所以我取的用户名是 yangxg
# 选择一个你喜欢的用户名，不一定非得和我的相同
root@localhost:~# useradd -m -s /bin/bash zhaolizhi

# 把新创建的用户加入超级权限组
root@localhost:~# usermod -a -G sudo zhaolizhi

# 为新用户设置密码
# 注意在输密码的时候不会有字符显示，不要以为键盘坏了，正常输入即可
root@localhost:~# passwd zhaolizhi

# 切换到创建的新用户
root@localhost:~# su - zhaolizhi

# 切换成功，@符号前面已经是新用户名而不是 root 了
zhaolizhi@localhost:~$
```



### **更新一下系统**

```bash
sudo apt-get update
sudo apt-get upgrade
```



### **配置运行环境**

```bash
# 首先找到Anaconda最新版本的镜像地址:https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/
wget xxx

# 安装
bash Anaconda3-5.2.0-Linux-x86_64.sh

# 切换默认python版本
sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 100
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 150

# 如果要切换回py2
sudo update-alternatives --config python

# 安装pip
# 或者sudo apt-get install python3-pip
sudo apt-get install pip 

# 安装  git nginx virtualenv
sudo apt-get install git nginx
sudo pip install virtualenv
sudo pip install django
```



### **解析域名**

购买域名后,在阿里云控制台解析域名,然后走审核流程

### **启动Nginx服务**

Nginx用来处理**静态文件请求**.当我们访问博客文章详情页面时,服务器收到下面两种请求:

* 显示文章信息: 从数据库中获取数据
* 图片,css,js等**存在服务器某个文件夹**下的静态文件

第一种请求直接通过Django从数据库中获取,Nginx处理不了这种请求,会把这个请求转发给Django去处理.Nginx可以高效地处理静态文件请求,而Django却很慢.

```bash
sudo service nginx start
```

![](https://bkt.zmrenwu.com/Nginx%E6%AC%A2%E8%BF%8E%E9%A1%B5%E9%9D%A2.png)

出现上图就说明Nginx启动成功了



## 二.部署代码

### 修改项目的配置信息

```python
blogproject/settings.py

# 上线的时候要关闭DEBUG,不过调试阶段可以开启
# 重点是ALLOWED_HOSTS里面要加入我们的域名和公网ip
DEBUG = False
ALLOWED_HOSTS = ['127.0.0.1', 'localhost ', '.zhaolizhi.xyz','120.77.182.38']

# ...

STATIC_URL = '/static/'
# 加入下面的配置
# 这个配置指定了收集静态文件后存储的目录,我们这里指定为 项目根目录/static
STATIC_ROOT = os.path.join(BASE_DIR, 'static')

```

### **生成项目用到的所有依赖文件**

```bash
进入项目根目录,生成依赖文件

pip freeze > requirements.txt
```

### **传到github,利用github和服务器的代码保持同步**

```bash
git add .
git commit -m "message"
git push origin master
```

### 设置服务器目录结构

```
/home/zhaolizhi/
    sites/
        zhaolizhi.xyz/
            env/
            WebServer(project_name)/
```



### 创建虚拟环境,在虚拟环境下安装项目依赖

```bash
cd ~/sites/zhaolizhi.xyz

virtualenv --python=python3 env

source env/bin/activate

cd WebServer/

pip install -r requirements.txt
```

### 收集项目静态文件,生成数据库,创建admin用户

```bash
# 收集静态文件到项目根目录的static目录下
python manage.py collectstatic

python manage.py migrate

python manage.py createsuperuser
```

### 配置Nginx

```bash
# 在/etc/nginx/sites-available/ 下创建一个配置文件,文件名设置为域名
# /etc/nginx/sites-available/zhaolizhi.xyz
# 删除/etc/nginx/sites-available/default 文件

server {
    charset utf-8;
    listen 80;
    server_name zhaolizhi.xyz;

    location /static {
        alias /home/zhaolizhi/sites/zhaolizhi.xyz/WebServer/static;
    }

    location / {
        proxy_set_header Host $host;
        proxy_pass http://unix:/tmp/zhaolizhi.xyz.socket;
    }
}
```

```bash
# 然后在 /etc/nginx/sites-enabled/目录下创建一个符号链接
sudo ln -s /etc/nginx/sites-available/zhaolizhi.xyz /etc/nginx/sites-enabled/zhaolizhi.xyz
```

* 配置文件表明,所有带有`/static`的URL请求交给Nginx处理,`alias`指明了静态文件存放的目录
* 其他请求交给Django处理,`proxy_pass`后面使用了unix套接字,作用是 **防止端口冲突**
* 如果访问域名仍然显示nginx欢迎界面,删除 `/etc/nginx/sites-available/default`

### 使用Gunicorn启动服务器

```bash
# 在虚拟环境下安装Gunicorn
pip install gunicorn

# 启动服务器进程
 gunicorn --bind unix:/tmp/zhaolizhi.xyz.socket blogproject.wsgi:application
```

### 设置服务器自动启动Gunicorn

```bash
# 新建目录
sudo mkdir -p /usr/lib/systemd/system

# 新建自启动的服务文件
sudo vim /usr/lib/systemd/system/zhaolizhi.xyz.service
```

```bash
[Unit]
After=syslog.target network.target remote-fs.target nss-lookup.target
[Service]
# 你的用户
User=zhaolizhi
# 你的目录
WorkingDirectory=/home/zhaolizhi/sites/zhaolizhi.xyz/WebServer
# gunicorn启动命令
ExecStart=/home/zhaolizhi/sites/env/bin/gunicorn --bind unix:/tmp/zhaolizhi.xyz.socket blogproject.wsgi:application
Restart=on-failure
[Install]
WantedBy=multi-user.target
```

```bash
# 启动服务
sudo systemctl start zhaolizhi.xyz

# 添加服务到开机自动运行
sudo systemctl enable zhaolizhi.xyz.service

# 查看进程,看看gunicorn是否已经启动,有两个进程
ps -ef | grep gunicorn
```

