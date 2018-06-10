# Linux常用命令
## 一、软件操作命令
* 软件包管理器：`aptget`
* 安装软件： `apt-get install xxx`
* 卸载软件： `apt-get remove xxx`
* 搜索软件： `apt-get serach xxx`
* 清理缓存： `apt-get autoclean`

## 二、服务器资源信息
* 内存： `free -m`
* 硬盘： `df -h`
* 负载： `w /top`
* 查看ip:
    *  `ifconfig `
    *  `ip addr`
    *  `vi /etc/syscongif/network-scripts/ifcfg-xx`
    *  `apt-get install net-tools`
* 查看配置: `cat /proc/cpuinfo`
* `fdisk`


## 三、文件操作命令
### 1. Linux文件目录
* 当前目录文件:`ls -al, ls`
* 根目录：`/`r
* 家目录:`/home`
    * 当前用户家目录：`~/`
*  临时目录`/tmp`
*  配置目录:`/etc`
*  用户程序目录`/usr`

### 2. 文件操作基本命令  
* `ls` :查看目录下的文件
* `touch` :新建文件
* `mkdir` :新建文件夹
* `cd`: 进入目录
* `rm` : 删除文件和目录
    *  `rm -r`: 删除文件夹
    *  `rm -rf`: 强制删除文件夹
* `cp thisfile tofile`: 复制
* `mv thisfile tofile`: 移动
* `pwd`: 显示当前   路径

## 系统用户操作命令
* `useradd` : 添加用户
* `userdel` : 删除用户
* `passwd`: 设置密码

## 提权和文件上传下载
* `sudo` : 提权
    * 让普通用户可以获得root用户的某些权限
    * `visudo`:root用户下给某个用户授权
* `wget,curl`: 文件下载
* `scp`: 文件上传 
    * `scp imooc@192.168.0.106:/tmp/imooc.txt ./`   从immoc服务器上下载imooc.txt文件到本地的./路径
    * `scp imooc.txt imooc@192.168.0.106:/tmp/` 把本地的imooc.txt文件上传到服务器的/tmp路径下