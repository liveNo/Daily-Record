## 基础环境

- VirtualBox 下载地址 `https://www.virtualbox.org/wiki/Download_Old_Builds_5_1`
- vagrant 下载地址： `https://releases.hashicorp.com/vagrant/`

- CentOs 7.2 下载地址： ` https://github.com/CommanderK5/packer-centos-template/releases/download/0.7.2/vagrant-centos-7.2.box`


## 初始化 Vagrant 环境

1. 添加box操作： `vagrant box add CentOs72 /path/vagrant-centos-7.2.box`
2. 显示当前已经添加的box列表： `vagrant box list`
3. 创建一个vagrant 开发环境目录： `mkdir ~/dev` 并 `cd ~/dev`
4. 用CentOs72 进行box的初始化： `vagrant init CentOs72`，会在该目录下生成一个`Vagrantfile`文件
5. 启动环境： `vagrant up`
6. ssh登录： `vagrant ssh`
7. 关机： `vagrant halt`
8. 删除相应的box： `vagrant box remove boxname`
9. 停止当前正在运行的虚拟机并销毁所有创建的资源： `vagrant destroy`
10. 对当前虚拟机环境进行打包： `vagrant package`
11. 重启虚拟机，主要用于重新载入配置文件： `vagrant reload`
12. 输出用于ssh连接的信息： `vagrant ssh-config`
13. 获取当前虚拟机的状态： `vagrant status`


```
# Linux CentOs7.x 初始化系统
yum -y install gcc gcc-c++ autoconf libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel zlib zlib-devel glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel ncurses ncurses-devel curl curl-devel e2fsprogs e2fsprogs-devel krb5 krb5-devel libidn libidn-devel openssl openssl-devel openldap openldap-devel nss_ldap openldap-clients openldap-servers vim-common dos2unix readline readline-devel cmake bison libtool flex pkg-config patch gd gd-devel locate libevent libevent-devel openldap.x86_64 openldap-devel.x86_64 openldap-clients.x86_64 openssh-ldap.x86_64 libtiff-devel libtiff librsvg2-devel librsvg2 perl-ExtUtils-CBuilder perl-ExtUtils-MakeMaker lrzsz mailx libzip libzip-devel
```

### 使用CentOs 7.2 不能使用`ifconfig`命令
1. `ifconfig`命令存在，则需要设置环境变量。`ifconfig`命令位置`/sbin/ifconfig`
2. `ifconfig`命令不存在，首先执行`yum upgrade`，再执行`yum install net-tools`

### CentOs 7.2 默认没有vim
使用命令`sudo yum install -y vim`安装并使用vim

## Vagrantfile配置文件详解
`Vagrantfile`主要包括三个方面的配置，虚拟机的配置、SSH配置、Vagrant的一些基础配置。


## 安装LNMP环境
### Nginx 
#### 安装Nginx
1. 查看yum源中是否有nginx 
	
	```
	[vagrant@bogon ~]$ yum info nginx
已加载插件：fastestmirror
Determining fastest mirrors
 * base: mirrors.yun-idc.com
 * epel: mirrors.tuna.tsinghua.edu.cn
 * extras: mirrors.nwsuaf.edu.cn
 * updates: mirrors.tuna.tsinghua.edu.cn
可安装的软件包
名称    ：nginx
架构    ：x86_64
时期       ：1
版本    ：1.10.2
发布    ：1.el7
大小    ：505 k
源    ：epel/x86_64
简介    ： A high performance web server and reverse proxy server
网址    ：http://nginx.org/
协议    ： BSD
描述    ： Nginx is a web server and a reverse proxy server for HTTP, SMTP, POP3 and
         : IMAP protocols, with a strong focus on high concurrency, performance and low
         : memory usage.
	```
2. 如果不存在，需要添加Nginx的下载源到yum：`sudo rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm`，指定下载源后，可以直接使用yum命令安装
3. 安装Nginx： `sudo yum install -y nginx`
4. 安装完成后，使用curl测试：

	```
	[vagrant@bogon ~]$ curl -I "http://127.0.0.1"
	HTTP/1.1 200 OK
	Server: nginx/1.10.2
	Date: Wed, 30 Aug 2017 03:49:39 GMT
	Content-Type: text/html
	Content-Length: 3700
	Last-Modified: Mon, 31 Oct 2016 12:37:02 GMT
	Connection: keep-alive
	ETag: "58173aee-e74"
	Accept-Ranges: bytes
	```

#### Nginx安装目录解析
1. nginx执行文件目录： `/usr/sbin/nginx`
2. nginx配置文件目录： `/etc/nginx`
3. nginx扩展目录配置文件：`/usr/share/nginx/modules`
4. nginx默认项目目录：`/usr/share/nginx/html`
5. 启动nginx： `systemctl start nginx`
6. 关闭nginx： `systemctl stop nginx`
7. 重启nginx： `systemctl restart nginx`
8. 查看nginx状态： `systemctl status nginx`

### PHP
#### 下载 PHP7
```
http://php.net/distributions/php-7.1.8.tar.gz
```

#### 安装依赖包
```
yum install libxml2 libxml2-devel openssl openssl-devel bzip2 bzip2-devel libcurl libcurl-devel libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel gmp gmp-devel libmcrypt libmcrypt-devel readline readline-devel libxslt libxslt-devel
```

### Mysql 


### Redis And Memcached



