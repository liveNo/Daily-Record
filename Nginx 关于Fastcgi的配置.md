# Nginx 关于Fastcgi的配置

通过Nginx源码安装或者通过扩展包管理器安装的，会包括两份FastCGI的配置，分别是fastcgi_params和fastcgi.conf，而这两个配置文件只有一行不同。

我们看下这两个文件的细微区别：

```
fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
```

先了解下这行代码的作用：通知PHP应该执行哪个文件，如果没有这句话，PHP和Nginx是不能一起协同工作的。

先看下fastcgi_params使用的配置：

```
location ~ \.php$ {
	fastcgi_pass 127.0.0.1:9000;
	include fastcgi_params;
	fastcgi_param SCRIPT_FILENAME /example/path$fastcgi_script_name;
	fastcgi_index index.php;
}
```
再对比下使用fastcgi.conf使用的配置：

```
location ~ \.php$ {
	fastcgi_pass 127.0.0.1:9000;
	include fastcgi.conf;
}
```

相比来说， 一方面使用fastcgi.conf配置，更加清晰简单一些；另一方面，fastcgi_params在使用SCRIPT_FILENAME时，使用了硬编码，提高了修改的成本。也因此才会引入了fastcgi.conf配置。

那么，这会引申了另一个问题，为什么不直接在fastcgi_params上直接修改呢？原因是：fastcgi_param 指令是数组型的，和普通指令相同的是：内层替换外层；和普通指令不同的是：当在同级多次使用的时候，是新增而不是替换。也就是说，如果同级定义多次fastcgi_param ，那么会多次发送到后端，由于源安装实际上与存储库安装有所不同，所以可能会导致一些偶发的情况发生。

当然，现在Nginx社区也推荐使用fastcgi.conf的用法，但并不推荐直接将fastcgi_param 直接移到fastcgi_params文件中。

