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

Vagrant 有三种网络连接方式