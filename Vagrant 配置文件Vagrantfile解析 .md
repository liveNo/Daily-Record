# Vagrant 配置文件Vagrantfile解析 

## 简介
> Vagrantfile 的主要功能是描述项目所需要的机器类型及如何配置。Vagrant 运行每个项目都会有一个Vagrantfile。Vagrantfile 是一个Ruby文件。

## 配置参数说明 

**Configuration Version**

```
Vagrant.configure("2") do |config|
	# Configure ...
end
```

第一行中的`2`表示将用于该块的配置和配置对象的版本。目前，Vagrant 仅支持两种版本：`1`和`2`. `1`版本代表Vagrant 1.0.x的配置。`2`代表从1.1+版本最高到2.0.x版本的配置。

**box设置**
```
config.vm.box = "centos72 # box设置
```
当前vm使用的box镜像名称，值为本地仓库的镜像名，即使用`vagrant init boxname` 中的boxname。

**hostname设置**

```
config.vm.hostname = "bogon"
```
指定的vm的hostname，该值会覆盖vm中/etc/hostname中的设置。当有多台服务器时，可以依靠hostname来做识别。

**虚拟机网络配置**

Vagrant 有三种网络连接方式:

- 端口转发 （Forwarded port）
	
	> Vagrant 端口转发允许你通过一个端口与虚拟机端口映射来做相应的访问响应，且是通过TCP或者是UDP。当然，默认的是使用TCP协议来通信。

```
Vagrant.configure("2") do |config|
  config.vm.network "forwarded_port", guest: 80, host: 8080
end
```

以上配置是允许主机的80端口映射到虚拟机的8080端口。在上文我们也提到过可以通过Virtual Box来设置端口之间的转发，但是只是临时可用，但是当虚拟机重启后，端口的转发就会失效。

```
Vagrant.configure("2") do |config|
  config.vm.network "forwarded_port", guest: 2003, host: 12003, protocol: "tcp"
  config.vm.network "forwarded_port", guest: 2003, host: 12003, protocol: "udp"
end
```

- 私有网络 （Private Network）
	- DHCP
	- 静态ip
	- Ipv6

```
# DHCP
Vagrant.configure("2") do |config|
  config.vm.network "private_network", type: "dhcp"
end

# Static IP
Vagrant.configure("2") do |config|
  config.vm.network "private_network", ip: "192.168.50.4"
end

# IPv6
Vagrant.configure("2") do |config|
  config.vm.network "private_network", ip: "fde4:8dba:82e1::c4"
end
```	

- 公有网络 （Public Network）

	> 公用网络配置ip类似与私有网络。
