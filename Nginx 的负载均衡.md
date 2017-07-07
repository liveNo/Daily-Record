# Nginx 的负载均衡
> Nginx 服务器反向代理服务最重要也是最典型的一个用途： 实现负载均衡。


## 什么是负载均衡
负载均衡的大致原理是利用一定的分配策略将网络负载均衡的分配到网络集群的各个操作单元上，让单个重负载任务能够分配到多个单元上并行处理，或者使大量并发访问或数据流量分担到多个单元分别处理，以减少用户等待响应时间。

Nginx服务器实现的负载均衡一般认为是七层负载均衡。

负载均衡主要通过专门的硬件设备或通过软件算法两种方式实现。硬件设备的负载均衡效果好、效率高、性能稳定，但是成本较高，例如F5、citrix netscaler等。通过软件实现的负载均衡主要依赖均衡算法的选择和程序的健壮性。

#### 均衡算法的分类
* 静态负载均衡算法（比较简单，一般网络环境下也可以达到比较不错的效果）
	* 轮循算法
	* 基于比率的加权轮循算法
	* 基于优先级的轮循算法
* 动态负载均衡算法（适合比较复杂的网络环境，适应性强，效果更好）
	* 基于任务量的最少连接优先算法
	* 基于性能的最快响应优先算法
	* 预测算法
	* 动态性能分配算法

## Nginx 服务器负载均衡配置
Nginx 负载均衡实现了基于优先级的加权轮询算法，主要使用配置proxy_pass和upstream指令。

### 一般轮询规则的负载均衡

```
upstream backend{  # 配置后端服务器组
	192.168.1.101:80;
	192.168.1.102:80;
	192.168.1.103:80;
}

server {
	listen 80;
	server_name www.example.com;
	index index.php index.html;
	
	location / {
		proxy_pass http://backend;
		proxy_set_header Host $host;
	}
}
```

以上代码段中，backend服务器组的优先级全部配置为默认为的weight=1; 这样它们会按一般轮循策略依次处理请求，这种方式优点在于，当服务down掉后，可以自动去除该服务的请求，而当服务恢复好，它又可以自动去接收处理请求。

### 加权轮询规则的负载均衡

```
upstream backend{  # 配置后端服务器组
	192.168.1.101:80 weight=5;
	192.168.1.102:80 weight=2;
	192.168.1.103:80;
}

server {
	listen 80;
	server_name www.example.com;
	index index.php index.html;
	
	location / {
		proxy_pass http://backend;
		proxy_set_header Host $host;
	}
}
```

相比上一段代码，该代码段中backend服务器组中的各服务器有不同的优先级别，weight亦是的值就是轮询策略中的“权值”.其中weight=5的服务器级别最高，会优先接收和处理客户端的请求，而默认weight=1的服务器级别最低，也是接收和处理客户端请求最少的服务器。而weight=2的服务器则介入这两个服务器之间。

### 对特定的资源实现负载均衡

我们在实际使用过程中，经常会遇到对不同资源进行不现的服务请求，例如我们经常使用的静态资源服务器，我们对里面的文件静态资源和视频资源进行不现的负载均衡。例如我们再请求视频资源时使用/video/* 而对静态资源使用/file/*，下面则是实现如何让他们请求不现的服务器组。

```
upstream videoServer{  # 配置后端服务器组
	192.168.1.101:80;
	192.168.1.102:80;
	192.168.1.103:80;
}

upstream fileServer{  # 配置后端服务器组
	192.168.1.104:80;
	192.168.1.105:80;
	192.168.1.106:80;
}

server {
	listen 80;
	server_name www.example.com;
	index index.php index.html;
	
	location /video/ {
		proxy_pass http://videoServer;
		proxy_set_header Host $host;
	}
	
	location /video/ {
		proxy_pass http://fileServer;
		proxy_set_header Host $host;
		proxy_set_header X-Real-IP $remote_addr;
		...
	}
}

```

以上是Nginx服务器在不同的情况下对负载均衡的一些配置，主要是根据weight（权重）去进行负载均衡，而Nginx还提供ip_hash（访问ip）、fair(第三方模块)、url_hash(第三方模块)方式去实现负载均衡。

**ip_hash的实现：**根据每个请求ip的hash结果进行分配，这样的话，每个访问即ip会固定访问一个后端服务器，可以解决session问题。
```
upstream bakend {
	ip_hash;
	server 192.168.0.14:8800;
	server 192.168.0.15:80;
}
```

**fair实现：**这是第三方的一个模块，是按后端服务器的响应时间来分配请求，响应时间最短的优先分配，是属于动态算法的一种。
```
upstream backend {
	server server1;
	server server2;
	fair;
}
```

**url_hash实现：**这也是一个第三方的模块，是按访问url的hash结果来分配请求，每个url定向到同一个后端服务器上，类似于ip_hash的算法。

```
upstream backend{
      server 192.168.1.10:7777;
      server 192.168.1.11:8888;
      hash $request_uri;
      hash_method crc32;
}
```

以上是有关于Nginx实现负载均衡的简单介绍，如果哪块写的有误，欢迎指正。