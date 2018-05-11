# WebServer的安装和配置
## 1. Apache安装和配置
启动后就可以通过8080端口通过浏览器直接通过公网ip访问服务器(关闭防火墙的情况下)

* centos安装:  `yum install httpd`
    *  ubuntu下命令不一样,需要时再搜索
* 启动: `service httpd start`
* 停止: `service httpd stop`
* 查询进程: `ps -ef | grep httpd`
* 查看端口: `sudo netstat -anpl | grep 'http'`
* 关闭防火墙: `sudo service firewalld stop`


## 2. Apache的虚拟主机配置及伪静态操作(未配置成功)
* `cd /etc/httpd/`

#### 配置
1. `cd conf`
2.  `sudo vim httpd.conf`,进入后搜素virtual,配置`VirtualHost`标签如下:  

```
# 'Main' server configuration
#
# The directives in this section set up the values used by the 'main'
# server, which responds to any requests that aren't handled by a
# <VirtualHost> definition.  These values also provide defaults for
# any <VirtualHost> containers you may define later in the file.
#
# All of these directives may appear inside <VirtualHost> containers,
# in which case these default settings will be overridden for the
# virtual host being defined.
#


<VirtualHost *:80>
        ServerName www.zhaolizhi.aliyun
        DocumentRoot /data/www
<VirtualHost/>

```
![](https://ws1.sinaimg.cn/large/0077h8xtly1fr6kgokkaej30pi0jjaeu.jpg)

3. 重启服务器: `sudo service httpd restart`
4. 新建文件


## 3. Nginx基本操作

![](https://ws1.sinaimg.cn/large/0077h8xtly1fr6lkze2dhj30y70lo78b.jpg)  

* 默认引导页界面设置: `/usr/share/nginx/html/index.html`
* 配置文件在 `/etc/nginx`
* 自定义虚拟主机配置文件在`/etc/nginx/conf.d`,新建aliyun.conf