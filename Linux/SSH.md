# 远程服务器管理工具SSH

* 安装ssh `apt-get install openssh-server`
* 启动ssh 
    * centos: `service sshd start`
    * `sudo service ssh start`
    * `/etc/init.d/ssh start`
    * `/etc/init.d/ssh stop`
    * `/etc/init.d/ssh restart`
* 查看进程 : `ps -ef |grep ssh`
*  连接服务器: `ssh root@39.105.71.140 `
    *  ctrl + c : 终止请求


## SSH config 配置
* config存放在 `~/.ssh/config`
* SSH config语法关键字
    * Host: 别名
    * HostName: 主机名
    * Port: 端口
    * User: 用户名
    * IdentityFile: 密钥文件路径  

## SSH 免密登录
* 生成ssh key: `ssh-keygen -t rsa`
 
 
 1. 在客户端生成ssh key,包括一个公钥一个私钥
 2. 服务端进入 `~/.ssh/`,编辑`vim authorized_keys`,添加内容为客户端的公钥.
    * 打开客户端公钥的方法为`cat xxx.pub`,复制全部内容
3. 在客户端配置私钥:`ssh-add ~/.ssh/xxx`

## SSH 安全端口
* 修改默认port 22 端口,尽量避免服务器的远程连接被不法分子知道
* 修改 `/etc/ssh/sshd_config`的port一行,也可以填写多行port